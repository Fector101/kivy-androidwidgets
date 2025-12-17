This is how to customise more stuff in widgets.
---
Like: Title, description and curved corners
### Prerequisite
- You should have already gone through the README.md file

## Adding Title
In the `p4a\hook.py` file add this line to your receiver
`android:label="Simple Text"`
so the receiver becomes 
```xml
<receiver android:name="{package}.SimpleWidget"
              android:enabled="true"
              android:exported="false"
              android:label="Simple Text">
        <intent-filter>
            <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
        </intent-filter>
        <meta-data android:name="android.appwidget.provider"
               android:resource="@xml/widgetproviderinfo" />
    </receiver>
```

---

## Adding Description
Create `res/values/widgets.xml` this will contain strings for the description,   
Take note of the `name` property you use it'll used to reference the string, In this case it's `widget_simple_demo_description`

> [!WARNING]
> Don't use `strings.xml` it gets overwritten by Kivy, create `res/values/widgets.xml` instead.
```xml
<resources>
    <string name="widget_simple_demo_description">
        A simple text widget showing a greeting message
    </string>
</resources>
```
Then in `res/xml/widgetproviderinfo.xml` use the name `property` from `widgets.xml` to reference description.  
line: `android:description="@string/widget_simple_demo_description"`
Complete file;
```xml
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:minWidth="120dp"
    android:minHeight="60dp"
    android:updatePeriodMillis="1800000"
    android:initialLayout="@layout/simple_widget"
    android:previewImage="@drawable/ic_launcher_foreground"
    android:resizeMode="horizontal|vertical"
    android:widgetCategory="home_screen"
    android:description="@string/widget_simple_demo_description" >
</appwidget-provider>
```
Sample of proper widget with Title:

Sample of Bad widget without Title and Description: 

---

## How to make your widget background curved 
This is simply using another layout as background 
Create `res/drawable/widget_bg.xml`
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">

    <solid android:color="#FFFFFF"/>

    <corners android:radius="16dp"/>

    <padding
        android:left="10dp"
        android:top="10dp"
        android:right="10dp"
        android:bottom="10dp"/>
</shape>
```
Then in `res/layout/simple_widget.xml` point to widget as background.   
Line: `android:background="@drawable/widget_bg"`
Complete file:
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:gravity="center"
    android:background="@drawable/widget_bg"
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
