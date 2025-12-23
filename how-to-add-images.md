# Steps to Add an Image in Your Widget

Prerequisite
- You should have gone through [README.md](https://github.com/Fector101/kivy-androidwidgets/blob/main/README.md)

Things to Look out for
- Make Sure you have permission to path for Image, You don't need permission for images in `app` path
- If testing with images in app `assets` path and installing with `adb install *.apk` it doesn't add your new images to `assets`, so you'll have to install manually
- When testing you'll have to laucher app so it can setup `app` path, else it'll display a blank box like sample below. It's a one time thing after this App does NOT have to be open for path to exist.

Four Steps to Have WOrking Sample
---
This is my app `tree` for more context  

```sh
.
├── res
│   ├── layout
│   │   └── image_test_widget.xml
│   ├── xml
│   │    └── image_test_widget_info.xml
├── src
│   └── ImageTestWidget.java
├── assets
│   └── images
│       └── test.jpg
├── main.py
└── buildozer.spec
```

Step 1: Creating Layout to display on Home Screen  

In path `res/layout/image_test_widget.xml`

The id `test_image` in `android:id="@+id/test_image"` will be used later to reference image, It's like a variable it can be named anything.
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:padding="10dp"
    android:background="#FFFFFF"
    android:gravity="center"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">

    <ImageView
        android:id="@+id/test_image"
        android:layout_width="120dp"
        android:layout_height="120dp"
        android:scaleType="centerCrop"/>

</LinearLayout>

```

Step 2: Create xml that gives Info about widget to android

In path `res/xml/image_test_widget_info.xml`

The id `image_test_widget` from `android:initialLayout="@layout/image_test_widget"` should be the file name of your widget from **Step 1** without `.xml`

```xml
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:minWidth="120dp"
    android:minHeight="160dp"
    android:updatePeriodMillis="900000"
    android:initialLayout="@layout/image_test_widget"
    android:resizeMode="horizontal|vertical"
    android:widgetCategory="home_screen">

</appwidget-provider>

```
Step 3: Create a AppWidgetProvider  

This will be used to add image once widget is added to screen

In path `src/ButtonWidget.java`

- Change `relative_path` variable to your image path relative to `main.py`
- Make sure `image_test_widget` matches file name from **Step 1**
- Lastly `test_image` should match `ImageView.android:id` from **Step 1**

```java
package org.wally.waller; // Change here from buildozer.spec package.domain+package.name

import android.appwidget.AppWidgetManager;
import android.appwidget.AppWidgetProvider;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.widget.RemoteViews;
import android.util.Log;
import java.io.File;

import org.wally.waller.R; // Change here from buildozer.spec package.domain+package.name

public class ImageTestWidget extends AppWidgetProvider {

    @Override
    public void onUpdate(Context context, AppWidgetManager appWidgetManager,  int[] appWidgetIds) {

        for (int appWidgetId : appWidgetIds) {

            RemoteViews views = new RemoteViews( context.getPackageName(),  R.layout.image_test_widget );


            String relative_path = "assets/images/test1.jpg"; //<---- Change This Relative to your App path
            File imageFile = new File(  context.getFilesDir().getAbsolutePath()  + "/app/" + relative_path  );

            Log.d("ImageTestWidget", "Image path: " + imageFile.getAbsolutePath());
            if (imageFile.exists()) {
                Log.d("ImageTestWidget", "Image exists at path: " + imageFile.getAbsolutePath());
            }

            if (imageFile.exists() && imageFile.length() > 0) {

                BitmapFactory.Options opts = new BitmapFactory.Options();
                opts.inSampleSize = 4;

                Bitmap bitmap = BitmapFactory.decodeFile(  imageFile.getAbsolutePath(), opts );

                if (bitmap != null) {
                    views.setImageViewBitmap(  R.id.test_image,  bitmap  );
                }
            }

            appWidgetManager.updateAppWidget( appWidgetId, views );
        }
    }
}

```

Step 4: Automate Injecting Reciecver to `AndroidManifest.xml`

Change these:
- `package` variable to match `package.domain` and `package.name` from `buildozer.spec`
- `image_test_widget_info` to match file name from **step 2**

```py
from pathlib import Path
from pythonforandroid.toolchain import ToolchainCL


def after_apk_build(toolchain: ToolchainCL):
    manifest_file = Path(toolchain._dist.dist_dir) / "src" / "main" / "AndroidManifest.xml"
    text = manifest_file.read_text(encoding="utf-8")

    package = "org.wally.waller" # Change here from buildozer.spec package.domain+package.name

    receiver_xml = f'''    
<receiver android:name="{package}.ImageTestWidget"
          android:enabled="true"
          android:exported="false">
    <intent-filter>
        <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
    </intent-filter>
    <meta-data android:name="android.appwidget.provider"
           android:resource="@xml/image_test_widget_info" />
</receiver>
    '''

    if receiver_xml.strip() not in text:
        if "</application>" in text:
            text = text.replace("</application>", f"{receiver_xml}\n</application>")
            print("Receiver added")
        else: 
            print("Could not find </application> to insert receiver")
    else: 
        print("Receiver already exists in manifest")




    # Save final manifest back
    manifest_file.write_text(text, encoding="utf-8")
    print("Successfully_101: Manifest update completed successfully!")

```


Sample Image  

![widget with img in it](https://raw.githubusercontent.com/Fector101/kivy-androidwidgets/main/imgs/adding-img-in-widget.jpg)

- Blank box is from adding image when it doesn't exist

### For Questions: [open_issue](https://github.com/Fector101/kivy-androidwidgets/issues/new)
