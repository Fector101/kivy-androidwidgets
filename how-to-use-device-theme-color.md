On Android 12+ the accent color of the device changes with the current wallpaper these are the steps to go about using it
- Path: `buildozer.spec`
```ini
android.gradle_dependencies = com.google.android.material:material:1.11.0
```

- Path: `res/layout/simple_widget.xml`
- `?attr/colorPrimary` contains the bright theme color
- Optional atttribute for background `android:background="@drawable/widget_bg"`
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:gravity="center"
    android:background="@drawable/widget_bg"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:theme="@style/MyWidgetTheme">

    <TextView
        android:id="@+id/widget_text"
        android:text="Loading..."
        android:textSize="18sp"
        android:padding="8dp"
        android:textColor="?attr/colorPrimary"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

</LinearLayout>

```

Path: `res/values/styles.xml`
```xml
<resources>
    <style name="MyWidgetTheme" parent="Theme.Material3.DayNight">
    </style>
</resources>
```

Path: `res/values-v31/styles.xml`
```xml
<resources>
    <style name="MyWidgetTheme" parent="Theme.Material3.DynamicColors.DayNight">
    </style>
</resources>
```

Optional background
- Path: `res/drawable/widget_bg.xml`
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle">
    <solid android:color="?attr/colorSurface"/>
    <corners android:radius="16dp"/>
</shape>

```
Source: https://developer.android.com/develop/ui/views/appwidgets/enhance
