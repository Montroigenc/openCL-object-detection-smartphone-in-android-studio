# openCL-object-detection-in-android-studio
Implementation of an object detection algorithm in an android studio emulator using mobileNet deep neural network and OpenCV.

![source: https://www.microsoft.com/developerblog/2017/04/10/end-end-object-detection-box/](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/CatObjectDetection.jpg)

This tutorial was originally written by OpenCV developers (https://docs.opencv.org/3.4/d0/d6c/tutorial_dnn_android.html), but some tricks are added here in order to solve some installation problems and enable the use of an emulated device using Android Studio.

This tutorial was written for the following versions of corresponding software:
- Android Studio 3.1.4
- penCV 3.4.3
- Ubuntu 18.04


# Requirements
Download and install Android Studio from https://developer.android.com/studio.
Get the latest pre-built OpenCV for Android release from https://github.com/opencv/opencv/releases and unpack it (for example, opencv-3.4.3-android-sdk.zip).

Download MobileNet object detection model (MobileNetSSD_deploy.caffemodel and MobileNetSSD_deploy.prototxt) from https://github.com/chuanqi305/MobileNet-SSD.


# Installation
## 1. Create an empty Android Studio project
Open Android Studio. Start a new project. Let's address it as mobilenet_openCV_v3 and set company domain to mobilenet_example.opencv.org, as could be seen in the following image. You can chose different app name and company domain, but then you will have to take more care to respect your own name when copy-pasting code from this tutorial to your app.

![img](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/createAndroidProject.png)

For the next new project setting steps, just leave it with the default settings.

![img](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/createNewProject2.png)

![img](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/cnp3.png)

![img](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/cnp4.png)

Great! Now we have our project prepared to follow next steps.

## 2. Import OpenCV module
Go to **File->New->Import module** and provide a path to **unpacked_OpenCV_package/sdk/java**, where unpacked_OpenCV_package is the directory where you have unpacked the previously downloaded the last openCV version. The name of module is set automatically as **openCVLibraryXXX**, where XXX is your openCV version.

![](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/importOpencvDependency.png)
Click next and left all the settings in the next window as they come by default.

## 3. Add OpenCV dependency
To work with the OpenCV Android library, you have to add it to your app module as a dependency. To easily do this on Android Studio, click on **File -> Project Structure**.

When the project structure dialog opens, click on the app module and click on the **+** green sign in the right window side  and select **Module dependency**. When the choose modules dialog opens, select your **OpenCV library module** and click on **OK**.

Automatically you will return to the dependencies page, where you should see that the openCV module has been added as a dependency. Then click **OK** to finish this step.

Once you have added the dependency, the openCV dependency should have been added to your project structure, as seen in the following image.

![](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/importOpencvDependency2.png)

Now Android Studio might be showing that there are errors in the project, any worries, we will solve it in the next step.

## 4. Adapting the files
Go to Android Studio and look at your project structure, you should have a directory named **Gradle Scripts** (image below), open two files of this directory:
1. build.gradle(Module:app)
2. build.gradle(openCVLibraryXXX), where XXX is your openCV version (my version is 343).

![](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/compilesdkversion.png)

Copy compileSdkVersion from the first file to the second one. In my case compileSdkVersion is 28:

**compileSdkVersion 14 -> compileSdkVersion 28**

Copy the compileSdkVersion to all targetSdkVersion in **both files**.

**targetSdkVersion 14 -> targetSdkVersion 28**

In Android Studio, select **Build -> Make Project**. If any error occurr, it should be because you have forget to do any of the previous steps.

## 5. Add Native Libraries
On your file explorer, navigate to the folder where you extracted the content of the OpenCV Android library zip file. Open the **sdk/native** directory.
Copy the **libs** folder in the native folder over to your project app module main folder (Usually ProjectName/app/src/main) and rename the libs folder you just copied into your project to **jniLibs**.

## 6. Add pretrained MobileNet deep neural network to the project
Put previously downloaded **MobileNetSSD_deploy.prototxt** and **MobileNetSSD_deploy.caffemodel** into your project folder **app/build/intermediates/assets/debug**.

## 7. Set main code and add camera permissions
Following we will change the content of some files in your android project. First of all, we need to add a necessary widget which displays processed frames. Modify **app/src/main/res/layout/activity_main.xml**:
```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
     <org.opencv.android.JavaCameraView
        android:id="@+id/CameraView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:visibility="visible" />
</FrameLayout>
```

Modify **/app/src/main/AndroidManifest.xml** to enable full-screen mode, set up a correct screen orientation and allow to use a camera. **BEFORE REPLACING THE CODE MAKE SURE THAT package (line 3 in the following code) MATCH YOUR ORIGINAL PACKAGE!!! IF NOT, COPY YOUR ORIGINAL package line AND REPLACE IT IN THE CODE I PROVIDE.**

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="org.opencv.mobilenet_example.mobilenet_opencv_v3">
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.AppCompat.NoActionBar">  <!--Full screen mode--> <!--android:theme="@style/AppTheme"-->
        <activity android:name=".MainActivity">
            android:screenOrientation="landscape">  <!--Screen orientation-->
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <!--Allow to use a camera-->
    <uses-permission android:name="android.permission.CAMERA"/>
    <uses-feature android:name="android.hardware.camera" android:required="false"/>
    <uses-feature android:name="android.hardware.camera.autofocus" android:required="false"/>
    <uses-feature android:name="android.hardware.camera.front" android:required="false"/>
    <uses-feature android:name="android.hardware.camera.front.autofocus" android:required="false"/>
</manifest>
```

Replace content of **app/src/main/java/org/opencv/samples/opencv_mobilenet/MainActivity.java**, here it happens the same again, check that your original package name stands in the first line of the code, **you should only replace your original code from line 2 on**:

```
package org.opencv.mobilenet_example.mobilenet_opencv_v3;
import android.content.Context;
import android.content.res.AssetManager;
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.util.Log;
import org.opencv.android.BaseLoaderCallback;
import org.opencv.android.CameraBridgeViewBase;
import org.opencv.android.CameraBridgeViewBase.CvCameraViewFrame;
import org.opencv.android.CameraBridgeViewBase.CvCameraViewListener2;
import org.opencv.android.LoaderCallbackInterface;
import org.opencv.android.OpenCVLoader;
import org.opencv.core.Core;
import org.opencv.core.Mat;
import org.opencv.core.Point;
import org.opencv.core.Scalar;
import org.opencv.core.Size;
import org.opencv.dnn.Net;
import org.opencv.dnn.Dnn;
import org.opencv.imgproc.Imgproc;
import java.io.BufferedInputStream;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
public class MainActivity extends AppCompatActivity implements CvCameraViewListener2 {
    // Initialize OpenCV manager.
    private BaseLoaderCallback mLoaderCallback = new BaseLoaderCallback(this) {
        @Override
        public void onManagerConnected(int status) {
            switch (status) {
                case LoaderCallbackInterface.SUCCESS: {
                    Log.i(TAG, "OpenCV loaded successfully");
                    mOpenCvCameraView.enableView();
                    break;
                }
                default: {
                    super.onManagerConnected(status);
                    break;
                }
            }
        }
    };
    @Override
    public void onResume() {
        super.onResume();
        OpenCVLoader.initAsync(OpenCVLoader.OPENCV_VERSION, this, mLoaderCallback);
    }
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        // Set up camera listener.
        mOpenCvCameraView = (CameraBridgeViewBase)findViewById(R.id.CameraView);
        mOpenCvCameraView.setVisibility(CameraBridgeViewBase.VISIBLE);
        mOpenCvCameraView.setCvCameraViewListener(this);
    }
    // Load a network.
    public void onCameraViewStarted(int width, int height) {
        String proto = getPath("MobileNetSSD_deploy.prototxt", this);
        String weights = getPath("MobileNetSSD_deploy.caffemodel", this);
        net = Dnn.readNetFromCaffe(proto, weights);
        Log.i(TAG, "Network loaded successfully");
    }
    public Mat onCameraFrame(CvCameraViewFrame inputFrame) {
        final int IN_WIDTH = 300;
        final int IN_HEIGHT = 300;
        final float WH_RATIO = (float)IN_WIDTH / IN_HEIGHT;
        final double IN_SCALE_FACTOR = 0.007843;
        final double MEAN_VAL = 127.5;
        final double THRESHOLD = 0.2;
        // Get a new frame
        Mat frame = inputFrame.rgba();
        Imgproc.cvtColor(frame, frame, Imgproc.COLOR_RGBA2RGB);
        // Forward image through network.
        Mat blob = Dnn.blobFromImage(frame, IN_SCALE_FACTOR,
                new Size(IN_WIDTH, IN_HEIGHT),
                new Scalar(MEAN_VAL, MEAN_VAL, MEAN_VAL), false);
        net.setInput(blob);
        Mat detections = net.forward();
        int cols = frame.cols();
        int rows = frame.rows();
        Size cropSize;
        if ((float)cols / rows > WH_RATIO) {
            cropSize = new Size(rows * WH_RATIO, rows);
        } else {
            cropSize = new Size(cols, cols / WH_RATIO);
        }
        int y1 = (int)(rows - cropSize.height) / 2;
        int y2 = (int)(y1 + cropSize.height);
        int x1 = (int)(cols - cropSize.width) / 2;
        int x2 = (int)(x1 + cropSize.width);
        Mat subFrame = frame.submat(y1, y2, x1, x2);
        cols = subFrame.cols();
        rows = subFrame.rows();
        detections = detections.reshape(1, (int)detections.total() / 7);
        for (int i = 0; i < detections.rows(); ++i) {
            double confidence = detections.get(i, 2)[0];
            if (confidence > THRESHOLD) {
                int classId = (int)detections.get(i, 1)[0];
                int xLeftBottom = (int)(detections.get(i, 3)[0] * cols);
                int yLeftBottom = (int)(detections.get(i, 4)[0] * rows);
                int xRightTop   = (int)(detections.get(i, 5)[0] * cols);
                int yRightTop   = (int)(detections.get(i, 6)[0] * rows);
                // Draw rectangle around detected object.
                Imgproc.rectangle(subFrame, new Point(xLeftBottom, yLeftBottom),
                        new Point(xRightTop, yRightTop),
                        new Scalar(0, 255, 0));
                String label = classNames[classId] + ": " + confidence;
                int[] baseLine = new int[1];
                Size labelSize = Imgproc.getTextSize(label, Core.FONT_HERSHEY_SIMPLEX, 0.5, 1, baseLine);
                // Draw background for label.
                Imgproc.rectangle(subFrame, new Point(xLeftBottom, yLeftBottom - labelSize.height),
                        new Point(xLeftBottom + labelSize.width, yLeftBottom + baseLine[0]),
                        new Scalar(255, 255, 255), Core.FILLED);
                // Write class name and confidence.
                Imgproc.putText(subFrame, label, new Point(xLeftBottom, yLeftBottom),
                        Core.FONT_HERSHEY_SIMPLEX, 0.5, new Scalar(0, 0, 0));
            }
        }
        return frame;
    }
    public void onCameraViewStopped() {}
    // Upload file to storage and return a path.
    private static String getPath(String file, Context context) {
        AssetManager assetManager = context.getAssets();
        BufferedInputStream inputStream = null;
        try {
            // Read data from assets.
            inputStream = new BufferedInputStream(assetManager.open(file));
            byte[] data = new byte[inputStream.available()];
            inputStream.read(data);
            inputStream.close();
            // Create copy file in storage.
            File outFile = new File(context.getFilesDir(), file);
            FileOutputStream os = new FileOutputStream(outFile);
            os.write(data);
            os.close();
            // Return a path to file which may be read in common way.
            return outFile.getAbsolutePath();
        } catch (IOException ex) {
            Log.i(TAG, "Failed to upload a file");
        }
        return "";
    }
    private static final String TAG = "OpenCV/Sample/MobileNet";
    private static final String[] classNames = {"background",
            "aeroplane", "bicycle", "bird", "boat",
            "bottle", "bus", "car", "cat", "chair",
            "cow", "diningtable", "dog", "horse",
            "motorbike", "person", "pottedplant",
            "sheep", "sofa", "train", "tvmonitor"};
    private Net net;
    private CameraBridgeViewBase mOpenCvCameraView;
}
```

## Smartphone emulator setting
To create our own smartphone emulator go to Android Studio, select **Tools -> AVG Manager**. A new window listing your current virtual devices will pop up. If you want to create a new one, select **Create Virtual Device...**

![img](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/AVGmanager.png)

Once the Select Hardware window pops up, we have to select **Phone** Category and we could choose **Nexus 5X** as our target emulation device. Click on next and in the **Recommended** sheet, assure that you have your target API downloaded, in my case it is the API 28. If you don't have it, click on the blue **Download** link just in the right of the Release Name you want. 

In the next window (Verify Configuration), click on **Show Advanced Settings**. Scroll down to the Camera settings. Here you can decide if you want to use your PC camera or you prefer a VirtualScene. Make your choice and click on the **Finish** button.

AWESOME! Now we have our smartphone emulator ready!

To start it, open the AVG Manager window if you have not opened yet and click on the green play button on the Actions box.

To allow the use of openCV in you emulated smartphone, you shoud install appropriate OpenCV manager. To do it, you have to open a terminal window in unpacked_OpenCV_package/apk, where you have different apk versions, **make sure you are executing the one that matches your emulated smartphone settings (in my case OpenCV_3.4.3_Manager_3.43_x86_64.apk)**, and run:

**adb install OpenCV_3.4.3_Manager_3.43_x86_64.apk**

In the prompt window a **SUCCESS** message should appear, if not, probably you have not selected the appropiate apk version.

Once your device had been completely started, you should drag your cursor in the smartphone main screen to open the app menu. Then select **Settings** and following **Apps & notifications**.

Now go again to Android Studio and go to **Run -> Run 'app'**, select your smartphone emulator device (should be currently running, in the case of this example it is the Nexus 5X).

While the app is starting in your emulated smartphone, hour app name should appear in the **Apps & notifications** folder we have opened. Once you can see the name of your app, click on it and go to **Permissions**, here you could give to your app the permission to use the smartphone camera.

Great!! You have done all the configuration steps, and when you app starts in your emulator you could play identifying your surrounding objects, yourself or your favourite pet!
