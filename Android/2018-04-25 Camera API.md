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
      5. 
