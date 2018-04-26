注意：这篇文章所使用的 SDK 接口都是过时的。


#### [清单文件定义](https://developer.android.com/guide/topics/media/camera)
 - 相机权限
```
<uses-permission android:name="android.permission.CAMERA" />
```

如果你是使用现有的相机程序来使用相机的话，则不用声明此权限

 - 相机功能
```
<uses-feature android:name="android.hardware.camera" android:required="false" />
```
required="false" 表示非必需，Google Play 不会对没有相机功能的设备隐藏你的应用

 - 存储权限
```
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```
 - 录音权限
```
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```
- 定位权限
```
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
...
<!-- Needed only if your app targets Android 5.0 (API level 21) or higher. -->
<uses-feature android:name="android.hardware.location.gps" />
```

#### 使用现有相机程序

[拍照](https://developer.android.com/training/camera/photobasics)

[录像](https://developer.android.com/training/camera/videobasics)

#### 构建一个相机程序

为了程序看起来像个相机或者提供特殊的功能，有些开发者需要一个定制的相机 UI。

开发步骤：
1. 检查并申请访问相机
2. 创建一个Preview（一个自定义SurfaceView）
3. 创建一个引用 Preview 的布局 Layout
4. 监听布局上的按钮
5. 拍照并保存
6. 释放相机
#### 检测相机硬件
没有在清单文件中声明需要使用相机功能的话
需要动态检测设备是否具备该功能
```
/** Check if this device has a camera */
private boolean checkCameraHardware(Context context) {
    if (context.getPackageManager().hasSystemFeature(PackageManager.FEATURE_CAMERA)){
        // this device has a camera
        return true;
    } else {
        // no camera on this device
        return false;
    }
}
```
Android 2.3（API Level 9）及以上提供相机数量接口返回设备具有多少个可用的相机
```
Camera.getNumberOfCameras()
```

#### 访问相机

申请访问相机的过程是实例化一个 Camera 对象
不用 requestPermissions
```
/** A safe way to get an instance of the Camera object. */
public static Camera getCameraInstance(){
    Camera c = null;
    try {
        c = Camera.open(); // attempt to get a Camera instance
    }
    catch (Exception e){
        // Camera is not available (in use or does not exist)
    }
    return c; // returns null if camera is unavailable
}
```
Android 2.3（API Level 9）及以上提供 Camera.open(int) 可以访问不同的摄像头
不带 int 参数的老接口将访问第一个后置摄像头。

#### 检查相机功能
```Camera.Parameters Camera.getParameters()```

API 9 及以上使用：```Camera.getCameraInfo()```

#### 预览
```
/** A basic Camera preview class */
public class CameraPreview extends SurfaceView implements SurfaceHolder.Callback {
    private SurfaceHolder mHolder;
    private Camera mCamera;

    public CameraPreview(Context context, Camera camera) {
        super(context);
        mCamera = camera;

        // Install a SurfaceHolder.Callback so we get notified when the
        // underlying surface is created and destroyed.
        mHolder = getHolder();
        mHolder.addCallback(this);
        // deprecated setting, but required on Android versions prior to 3.0
        mHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
    }

    public void surfaceCreated(SurfaceHolder holder) {
        // The Surface has been created, now tell the camera where to draw the preview.
        try {
            mCamera.setPreviewDisplay(holder);
            mCamera.startPreview();
        } catch (IOException e) {
            Log.d(TAG, "Error setting camera preview: " + e.getMessage());
        }
    }

    public void surfaceDestroyed(SurfaceHolder holder) {
        // empty. Take care of releasing the Camera preview in your activity.
    }

    public void surfaceChanged(SurfaceHolder holder, int format, int w, int h) {
        // If your preview can change or rotate, take care of those events here.
        // Make sure to stop the preview before resizing or reformatting it.

        if (mHolder.getSurface() == null){
          // preview surface does not exist
          return;
        }

        // stop preview before making changes
        try {
            mCamera.stopPreview();
        } catch (Exception e){
          // ignore: tried to stop a non-existent preview
        }

        // set preview size and make any resize, rotate or
        // reformatting changes here
        // 使用 getSupportedPreviewSizes() 返回的列表中的 size 来指定
        // 预览窗口的大小，不可随意指定
        // 纵横比不会与 Activity 相同


        // start preview with new settings
        try {
            mCamera.setPreviewDisplay(mHolder);
            mCamera.startPreview();

        } catch (Exception e){
            Log.d(TAG, "Error starting camera preview: " + e.getMessage());
        }
    }
}
```
#### 把预览放到布局中
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
  <FrameLayout
    android:id="@+id/camera_preview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:layout_weight="1"
    />

  <Button
    android:id="@+id/button_capture"
    android:text="Capture"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="center"
    />
</LinearLayout>
```
 - 设置横屏
```
<activity android:name=".CameraActivity"
          android:label="@string/app_name"

          android:screenOrientation="landscape">
          <!-- configure this activity to use landscape orientation -->

          <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

当用户旋转屏幕的时候，你可以调用```setDisplayOrientation()```
来改变预览的方向
在预览类中的 ```surfaceChanged()``` 方法中先调用 ```Camera.stopPreview()```
再调用 ```Camera.startPreview()```

 - 连接 Activity
```
public class CameraActivity extends Activity {

    private Camera mCamera;
    private CameraPreview mPreview;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        // Create an instance of Camera
        mCamera = getCameraInstance();

        // Create our Preview view and set it as the content of our activity.
        mPreview = new CameraPreview(this, mCamera);
        FrameLayout preview = (FrameLayout) findViewById(R.id.camera_preview);
        preview.addView(mPreview);
    }
}
```
在 Activity 的 onPause 方法中需要释放 Camera

 - 点击按钮拍照

```
private PictureCallback mPicture = new PictureCallback() {

    @Override
    public void onPictureTaken(byte[] data, Camera camera) {

        File pictureFile = getOutputMediaFile(MEDIA_TYPE_IMAGE);
        if (pictureFile == null){
            Log.d(TAG, "Error creating media file, check storage permissions: " +
                e.getMessage());
            return;
        }

        try {
            FileOutputStream fos = new FileOutputStream(pictureFile);
            fos.write(data);
            fos.close();
        } catch (FileNotFoundException e) {
            Log.d(TAG, "File not found: " + e.getMessage());
        } catch (IOException e) {
            Log.d(TAG, "Error accessing file: " + e.getMessage());
        }
    }
};
// Add a listener to the Capture button
Button captureButton = (Button) findViewById(id.button_capture);
captureButton.setOnClickListener(
    new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            // get an image from the camera
            mCamera.takePicture(null, null, mPicture);
        }
    }
);
```
#### 视频录制

MediaRecorder 得和 Camera 协调使用
使用 ```Camera.lock()``` ```Camera.unlock()``` ```Camera.open()``` ```Camera.release()```
对 Camera 进行管理以与 MediaRecorder 进行协调
Android 4.0（API Level 14）及以上会自动为您管理 Camera.lock() 和 Camera.unlock() 的调用

次序：
1. 打开摄像头 ```Camera.open()```
2. 连接预览，使用 ```Camera.setPreviewDisplay()``` 把摄像头和一个预览连接。拍照功能没用到这个接口
3. 开始预览 ```Camera.startPreview()```
4. 开始录制视频
    1. 解锁 Camera ```Camera.unlock()```
    2. 配置 MediaRecorder
    
        1. ```setCamera()```
        
        2. 设置音频源 ```setAudioSource()```
        
        3. 设置视频源 ```setVideoSource()```
        
        4. 设置视频输出格式和编码格式
            在 Android 2.2（API Level 8）及之后的 SDK 版本中 ```MediaRecorder.setProfile(CamcorderProfile.get())```
            在之前的版本中：
            1. 设置输出格式 ```setOutputFormat()```
            2. 设置音频编码 ```setAudioEncoder()```
            3. 设置视频编码 ```setVideoEncoder()```
        5. 设置输出到哪个文件 ```setOutputFile(getOutputMediaFile(MEDIA_TYPE_VIDEO).toString())```
        6. 指定预览的布局节点，使用在第 2 步（连接预览）中的对象 ```setPreviewDisplay()```
    3. 准备 MediaRecorder ```MediaRecorder.prepare()```
    4. 开始 MediaRecorder ```MediaRecorder.start()```
5. 停止视频录制
    1. 停止 MediaRecorder ```MediaRecorder.stop()```
    2. 可选的操作，重置 MediaRecorder ```MediaRecorder.reset()```
    3. 释放 MediaRecorder ```MediaRecorder.release()```
    4. 锁住 Camera ```Camera.lock()```，这一步在 Android 4.0（API level 14）及以上是可选的，除非 MediaRecorder.prepare() 失败
6. 停止预览 ```Camera.stopPreview()```
7. 释放 Camera ```Camera.release()```

不开预览也可以进行视频录制，此处不讨论

如果你的应用通常被用来视频录制，可以在开始预览之前调用 ```setRecordingHint(true)```
来减少启动录制的时间

#### 配置 MediaRecorder
```
private boolean prepareVideoRecorder(){

    mCamera = getCameraInstance();
    mMediaRecorder = new MediaRecorder();

    // Step 1: Unlock and set camera to MediaRecorder
    mCamera.unlock();
    mMediaRecorder.setCamera(mCamera);

    // Step 2: Set sources
    mMediaRecorder.setAudioSource(MediaRecorder.AudioSource.CAMCORDER);
    mMediaRecorder.setVideoSource(MediaRecorder.VideoSource.CAMERA);

    // Step 3: Set a CamcorderProfile (requires API Level 8 or higher)
    mMediaRecorder.setProfile(CamcorderProfile.get(CamcorderProfile.QUALITY_HIGH));

    // Step 4: Set output file
    mMediaRecorder.setOutputFile(getOutputMediaFile(MEDIA_TYPE_VIDEO).toString());

    // Step 5: Set the preview output
    mMediaRecorder.setPreviewDisplay(mPreview.getHolder().getSurface());

    // Step 6: Prepare configured MediaRecorder
    try {
        mMediaRecorder.prepare();
    } catch (IllegalStateException e) {
        Log.d(TAG, "IllegalStateException preparing MediaRecorder: " + e.getMessage());
        releaseMediaRecorder();
        return false;
    } catch (IOException e) {
        Log.d(TAG, "IOException preparing MediaRecorder: " + e.getMessage());
        releaseMediaRecorder();
        return false;
    }
    return true;
}
```
在 Android 2.2（API Level 8）之前使用下面的代码设置输出格式和编码格式
```
    // Step 3: Set output format and encoding (for versions prior to API Level 8)
    mMediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.MPEG_4);
    mMediaRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.DEFAULT);
    mMediaRecorder.setVideoEncoder(MediaRecorder.VideoEncoder.DEFAULT);
```

下面这些设置一般保持默认就可以了
```
setVideoEncodingBitRate()
setVideoSize()
setVideoFrameRate()
setAudioEncodingBitRate()
setAudioChannels()
setAudioSamplingRate()
```

#### 开始和停止 MediaRecorder

当进行这两步之前你必须遵循下面的特定步骤

1. 解锁 Camera ```Camera.unlock()```
2. 配置 MediaRecorder，参照上面一节
3. 开始录制 ```MediaRecorder.start()```
4. 录制
5. 停止录制 ```MediaRecorder.stop()```
6. 释放 ```MediaRecorder.stop()```
7. 锁住 Camera ```Camera.lock()```

当完成录制后不要释放你的 Camera，否则你的预览将停止

开始和停止录制
```private boolean isRecording = false;

// Add a listener to the Capture button
Button captureButton = (Button) findViewById(id.button_capture);
captureButton.setOnClickListener(
    new View.OnClickListener() {
        @Override
        public void onClick(View v) {
            if (isRecording) {
                // stop recording and release camera
                mMediaRecorder.stop();  // stop the recording
                releaseMediaRecorder(); // release the MediaRecorder object
                mCamera.lock();         // take camera access back from MediaRecorder

                // inform the user that recording has stopped
                setCaptureButtonText("Capture");
                isRecording = false;
            } else {
                // initialize video camera
                if (prepareVideoRecorder()) {
                    // Camera is available and unlocked, MediaRecorder is prepared,
                    // now you can start recording
                    mMediaRecorder.start();

                    // inform the user that recording has started
                    setCaptureButtonText("Stop");
                    isRecording = true;
                } else {
                    // prepare didn't work, release the camera
                    releaseMediaRecorder();
                    // inform user
                }
            }
        }
    }
);
```

#### 释放相机

```
public class CameraActivity extends Activity {
    private Camera mCamera;
    private SurfaceView mPreview;
    private MediaRecorder mMediaRecorder;

    ...

    @Override
    protected void onPause() {
        super.onPause();
        releaseMediaRecorder();       // if you are using MediaRecorder, release it first
        releaseCamera();              // release the camera immediately on pause event
    }

    private void releaseMediaRecorder(){
        if (mMediaRecorder != null) {
            mMediaRecorder.reset();   // clear recorder configuration
            mMediaRecorder.release(); // release the recorder object
            mMediaRecorder = null;
            mCamera.lock();           // lock camera for later use
        }
    }

    private void releaseCamera(){
        if (mCamera != null){
            mCamera.release();        // release the camera for other applications
            mCamera = null;
        }
    }
}
```
#### 保存媒体文件

很多路径可以存储这些文件，而开发者应该考虑的只能是下面的两种标准路径
1. Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES)
  这个接口在 Android 2.2（API Level 8）及以上提供，在老版本中使用 ```Environment.getExternalStorageDirectory()```
2. Context.getExternalFilesDir(Environment.DIRECTORY_PICTURES)
  当你的应用被卸载的时候，这些文件也会被清楚。其它应用可以访问到该路径下的文件。
  
```
public static final int MEDIA_TYPE_IMAGE = 1;
public static final int MEDIA_TYPE_VIDEO = 2;

/** Create a file Uri for saving an image or video */
private static Uri getOutputMediaFileUri(int type){
      return Uri.fromFile(getOutputMediaFile(type));
}

/** Create a File for saving an image or video */
private static File getOutputMediaFile(int type){
    // To be safe, you should check that the SDCard is mounted
    // using Environment.getExternalStorageState() before doing this.

    File mediaStorageDir = new File(Environment.getExternalStoragePublicDirectory(
              Environment.DIRECTORY_PICTURES), "MyCameraApp");
    // This location works best if you want the created images to be shared
    // between applications and persist after your app has been uninstalled.

    // Create the storage directory if it does not exist
    if (! mediaStorageDir.exists()){
        if (! mediaStorageDir.mkdirs()){
            Log.d("MyCameraApp", "failed to create directory");
            return null;
        }
    }

    // Create a media file name
    String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
    File mediaFile;
    if (type == MEDIA_TYPE_IMAGE){
        mediaFile = new File(mediaStorageDir.getPath() + File.separator +
        "IMG_"+ timeStamp + ".jpg");
    } else if(type == MEDIA_TYPE_VIDEO) {
        mediaFile = new File(mediaStorageDir.getPath() + File.separator +
        "VID_"+ timeStamp + ".mp4");
    } else {
        return null;
    }

    return mediaFile;
}
```

无论是使用系统相机拍摄还是你自己的应用进行拍摄，存储媒体的功能都是上面的代码所能完成的

要使 URI 支持工作概要文件，得先将文件 URI 转换为内容 URI，然后将内容 URI 添加到 Intent 的 EXTRA_OUTPUT 中

#### 相机功能

##### 检查功能是否可用
下面的代码展示了检查相机是否支持自动对焦功能
```
// get Camera parameters
Camera.Parameters params = mCamera.getParameters();

List<String> focusModes = params.getSupportedFocusModes();
if (focusModes.contains(Camera.Parameters.FOCUS_MODE_AUTO)) {
  // Autofocus mode is supported
}
```

在清单文件中声明的一些相机特性如若设备无法提供的话，则那些设备安装不上这个 app，之前说会在 Google Play 上对这类设备不可见的说法应该是译者理解错误吧
##### 使用功能
```
// get Camera parameters
Camera.Parameters params = mCamera.getParameters();
// set the focus mode
params.setFocusMode(Camera.Parameters.FOCUS_MODE_AUTO);
// set Camera parameters
mCamera.setParameters(params);
```
有些功能参数不能随意改变

相机预览的大小和方向需要先停止预览然后才可设置

Android 4.0（API Level 14）及以上改变方向不需要重启预览

##### 计量并对焦某区域

 - 设置两个测光区域
```
// Create an instance of Camera
mCamera = getCameraInstance();

// set Camera parameters
Camera.Parameters params = mCamera.getParameters();

if (params.getMaxNumMeteringAreas() > 0){ // check that metering areas are supported
    List<Camera.Area> meteringAreas = new ArrayList<Camera.Area>();

    Rect areaRect1 = new Rect(-100, -100, 100, 100);    // specify an area in center of image
    meteringAreas.add(new Camera.Area(areaRect1, 600)); // set weight to 60%
    Rect areaRect2 = new Rect(800, -1000, 1000, -800);  // specify an area in upper right of image
    meteringAreas.add(new Camera.Area(areaRect2, 400)); // set weight to 40%
    params.setMeteringAreas(meteringAreas);
}

mCamera.setParameters(params);
```

[img](./camera-area-coordinates.png)
##### 面部检测

