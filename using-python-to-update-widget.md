## Using Python to Make Changes in Home Screen widget


Prerequisite
- You should have gone through [README.md](https://github.com/Fector101/kivy-androidwidgets/blob/main/README.md) <-- Important

### Required Things
- A `App Widget Provider` java file, even if it's empty, [How to add](https://github.com/Fector101/kivy-androidwidgets/blob/main/README.md#step-3-create-a-appwidgetprovider-its-used-to-receive-events-for-widget)

## Example - Changing Text

### Actual Widget

- Important to mark `widget_text` which is the Text id, would be used to select later

Stored in: `res/layout/simple_widget.xml`
This a simple widget with a text
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:padding="10dp"
    android:background="#FFFFFF"
    android:gravity="center"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">

    <TextView
        android:id="@+id/widget_text"
        android:text="Loading..."
        android:textSize="18sp"
        android:textColor="#000"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

</LinearLayout>
```


### An App Widget Provider

path: `src/SimpleWidget.java`.  

- Can be Empty Since we using python to make Changes
- Useful For receiving event like Taps and Swipes

```java
package org.wally.waller; // Change here from buildozer.spec package.domain+package.name

import android.appwidget.AppWidgetManager;
import android.appwidget.AppWidgetProvider;
import android.content.Context;
import android.widget.RemoteViews;

import org.wally.waller.R; // Change here from buildozer.spec package.domain+package.name
import android.app.PendingIntent;
import android.content.Intent;

public class SimpleWidget extends AppWidgetProvider {

    @Override
    public void onUpdate(Context context, AppWidgetManager appWidgetManager, int[] appWidgetIds) {
        for (int appWidgetId : appWidgetIds) {

            RemoteViews views = new RemoteViews(context.getPackageName(), R.layout.simple_widget);

            // Set text
            views.setTextViewText(R.id.widget_text, "Hello frm Java!");

            // Update widget
            appWidgetManager.updateAppWidget(appWidgetId, views);
        }
    }
}
```
### Create an xml containing the info about the widget.  
Like: size, preview icon and others   
path: `res/xml/widgetproviderinfo.xml`
```xml
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:minWidth="120dp"
    android:minHeight="60dp"
    android:updatePeriodMillis="1800000"
    android:initialLayout="@layout/simple_widget"
    android:previewImage="@drawable/ic_launcher_foreground"
    android:resizeMode="horizontal|vertical"
    android:widgetCategory="home_screen">
</appwidget-provider>
```
### Automate injecting Receiver in XML

> [!IMPORTANT]  
> This is where we link `App Widget Provider` to `Actual Widget xml`  
> And Provide Info about widget too.

```py
from pathlib import Path
from pythonforandroid.toolchain import ToolchainCL


def after_apk_build(toolchain: ToolchainCL):
    manifest_file = Path(toolchain._dist.dist_dir) / "src" / "main" / "AndroidManifest.xml"
    text = manifest_file.read_text(encoding="utf-8")

    package = "org.wally.waller" # Change here to yours
    receiver_xml = f'''
    <receiver android:name="{package}.SimpleWidget"
              android:enabled="true"
              android:exported="false">
        <intent-filter>
            <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
        </intent-filter>
        <meta-data android:name="android.appwidget.provider"
               android:resource="@xml/widgetproviderinfo" />
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

    manifest_file.write_text(text, encoding="utf-8")
    print("Successfully_101: Manifest update completed successfully!")

```

### Changing Text with Python

Things needed
- `App Widget Provider` Class Name, In our case `SimpleWidget`
- `Widget File name`, In our case `simple_widget`
- `Text id`, In our case `widget_text`

```python
from jnius import autoclass

AppWidgetManager = autoclass('android.appwidget.AppWidgetManager')
ComponentName = autoclass('android.content.ComponentName')
RemoteViews = autoclass('android.widget.RemoteViews')
AndroidString = autoclass("java.lang.String")

context =  PythonActivity.mActivity.getApplicationContext()
resources = context.getResources()
package_name = context.getPackageName()

component = ComponentName( context, f'{package_name}.SimpleWidget' )
appWidgetManager = AppWidgetManager.getInstance(context)
ids = appWidgetManager.getAppWidgetIds(component) # In tests would be empty if Java file doesn't exist

text_layout_id = resources.getIdentifier("simple_widget", "layout", package_name) # In tests would return "0" if layout doesn't exist
views = RemoteViews(package_name, text_layout_id)

title_id = resources.getIdentifier("widget_text", "id", package_name) # In tests would return "0" if id doesn't exist
views.setTextViewText(title_id, AndroidString("Hello Frm Python"))
appWidgetManager.updateAppWidget(ids, views)


```


### For Questions: [open_issue](https://github.com/Fector101/kivy-androidwidgets/issues/new)
