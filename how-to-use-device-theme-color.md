On Android 12+ the accent color of the device changes with the current wallpaper these are the steps to go about using it
- Path: `buildozer.spec`
```ini
android.gradle_dependencies = com.google.android.material:material:1.11.0
```

- Path: `res/layout/carousel_widget.xml`
- `?attr/colorPrimary` contains the bright theme color
```xml
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/widget_root"
    android:orientation="vertical"
    android:gravity="center"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:theme="@style/MyWidgetTheme"
    android:background="?attr/colorSurface">

    <TextView
        android:id="@+id/placeholder_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="waiting for\nwallpaper change"
        android:gravity="center"
        android:textSize="16sp"
        android:textColor="?attr/colorPrimary"
        android:padding="8dp"
        android:textStyle="bold" />

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

Source: https://developer.android.com/develop/ui/views/appwidgets/enhance
