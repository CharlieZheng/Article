这东西是在 Android 5.0（API level 21）及以上才支持的
[AnimatedVectorDrawable](https://developer.android.com/guide/topics/graphics/vector-drawable-resources)
方式一：用三种 xml 文件定义
avd.xml
```
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
   android:drawable="@drawable/vd" >
     <target
         android:name="rotationGroup"
         android:animation="@anim/rotation" />
     <target
         android:name="vectorPath"
         android:animation="@anim/path_morph" />
</animated-vector>
```
vd.xml
```
<vector xmlns:android="http://schemas.android.com/apk/res/android"
   android:height="64dp"
   android:width="64dp"
   android:viewportHeight="600"
   android:viewportWidth="600" >
   <group
      android:name="rotationGroup"
      android:pivotX="300.0"
      android:pivotY="300.0"
      android:rotation="45.0" >
      <path
         android:name="vectorPath"
         android:fillColor="#000000"
         android:pathData="M300,70 l 0,-70 70,70 0,0 -70,70z" />
   </group>
</vector>
```
rotation.xml
```
<objectAnimator
   android:duration="6000"
   android:propertyName="rotation"
   android:valueFrom="0"
   android:valueTo="360" />
```
path_morph.xml
```
<set xmlns:android="http://schemas.android.com/apk/res/android">
   <objectAnimator
      android:duration="3000"
      android:propertyName="pathData"
      android:valueFrom="M300,70 l 0,-70 70,70 0,0   -70,70z"
      android:valueTo="M300,70 l 0,-70 70,0  0,140 -70,0 z"
      android:valueType="pathType"/>
</set>
```

方式二：用单一文件来定义
```
<animated-vector
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:aapt="http://schemas.android.com/aapt">
    <aapt:attr name="android:drawable">
        <vector
            android:width="24dp"
            android:height="24dp"
            android:viewportWidth="24"
            android:viewportHeight="24">
            <path
                android:name="root"
                android:strokeWidth="2"
                android:strokeLineCap="square"
                android:strokeColor="?android:colorControlNormal"
                android:pathData="M4.8,13.4 L9,17.6 M10.4,16.2 L19.6,7" />
        </vector>
    </aapt:attr>
    <target android:name="root">
        <aapt:attr name="android:animation">
            <objectAnimator
                android:propertyName="pathData"
                android:valueFrom="M4.8,13.4 L9,17.6 M10.4,16.2 L19.6,7"
                android:valueTo="M6.4,6.4 L17.6,17.6 M6.4,17.6 L17.6,6.4"
                android:duration="300"
                android:interpolator="@android:interpolator/fast_out_slow_in"
                android:valueType="pathType" />
        </aapt:attr>
    </target>
</animated-vector>
```
#### 兼容包支持
VectorDrawableCompat：可以兼容到 Android 2.1（API level 7）及以上
AnimatedVectorDrawableCompat：兼容到 Android 3.0（API level 11）及以上
```
//For Gradle Plugin 2.0+
 android {
   defaultConfig {
     vectorDrawables.useSupportLibrary = true
    }
 }
```

```
//For Gradle Plugin 1.5 or below
android {
  defaultConfig {
    // Stops the Gradle plugin’s automatic rasterization of vectors
    generatedDensities = []
  }
  // Flag notifies aapt to keep the attribute IDs around
  aaptOptions {
    additionalParameters "--no-version-vectors"
  }
}
 
```

怎么用：
```
<ImageView
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  app:srcCompat="@drawable/ic_add" />
```
app:srcCompat 可以当 android:src 来用

Support Library 25.4.0 支持以下的特性：
 - Path Morphing (PathType evaluator)
 - Path Interpolation
Support Library 26.0.0-beta1 支持以下的特性：
 - Move along path
 
 
兼容包的使用

方式一：几个文件
avd.xml
```
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
   android:drawable="@drawable/vd" >
     <target
         android:name="rotationGroup"
         android:animation="@anim/rotation" />
</animated-vector>
```
vd.xml
```
<vector xmlns:android="http://schemas.android.com/apk/res/android"
   android:height="64dp"
   android:width="64dp"
   android:viewportHeight="600"
   android:viewportWidth="600" >
   <group
      android:name="rotationGroup"
      android:pivotX="300.0"
      android:pivotY="300.0"
      android:rotation="45.0" >
      <path
         android:name="vectorPath"
         android:fillColor="#000000"
         android:pathData="M300,70 l 0,-70 70,70 0,0 -70,70z" />
   </group>
</vector>
```
rotation.xml
```
<objectAnimator
   android:duration="6000"
   android:propertyName="rotation"
   android:valueFrom="0"
   android:valueTo="360" />
```

方式二：单一文件
```
<animated-vector
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:aapt="http://schemas.android.com/aapt">
    <aapt:attr name="android:drawable">
        <vector xmlns:android="http://schemas.android.com/apk/res/android"
            android:width="64dp"
            android:height="64dp"
            android:viewportWidth="600"
            android:viewportHeight="600">
            <group
                android:name="rotationGroup"
                android:pivotX="300"
                android:pivotY="300"
                android:roation="45.0" >
                <path
                    android:name="vectorPath"
                    android:fillColor="#000000"
                    android:pathData="M300,70 l 0,-70 70,70 0,0 -70,70z" />
            </group>
        </vector>
    </aapt:attr>
    <target android:name="rotationGroup">
        <aapt:attr name="android:animation">
            <objectAnimator
                android:propertyName="rotation"
                android:valueFrom="0"
                android:valueTo="360"
                android:duration="6000"
                android:interpolator="@android:interpolator/fast_out_slow_in" />
        </aapt:attr>
    </target>
</animated-vector>
```
aapt 标签会创建方式一中的几个文件出来
需要 Build Tools 24 或以上
