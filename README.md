# kivy-androidwidgets
Home screen android widgets for kivy

For now this repo will contain Research on creating Android widgets.
i think it only needs it's java class - broadcast listener, editing AndroidMainfest and creating some xml files.

Will release a version when or if it's successful 

---
## Research 
From AndroidMainfest point to `AppWidgetProviderInfo`
The `AppWidgetProviderInfo` XML Will contain info about the widget, should be saved in `res/xml/`

```xml
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:minWidth="40dp"
    android:minHeight="40dp"
    android:targetCellWidth="1"
    android:targetCellHeight="1"
    android:maxResizeWidth="250dp"
    android:maxResizeHeight="120dp"
    android:updatePeriodMillis="86400000"
    android:description="@string/example_appwidget_description"
    android:previewLayout="@layout/example_appwidget_preview"
    android:initialLayout="@layout/example_loading_appwidget"
    android:configure="com.example.android.ExampleAppWidgetConfigurationActivity"
    android:resizeMode="horizontal|vertical"
    android:widgetCategory="home_screen"
    android:widgetFeatures="reconfigurable|configuration_optional">
</appwidget-provider>
```
The `AppWidgetProvider` class handles widget broadcasts and updates the widget in response to widget lifecycle events.
Tags:

receiver
- `android:name` specifys `AppWidgetProvider` which should be combination of app name and package name,
  In my case `org.laner.lan_ft.ExampleAppWidgetProvider"` according to [my last working receiver](https://github.com/Fector101/Laner/wiki/Dev#register-listener)
- `android:exported` remains False unless another process communicates with Listener( Not sure what it means i think another process apart from app)

intent-filter:
- `android:name="android.appwidget.action.APPWIDGET_UPDATE` is required, specifies Broad Listener accepts the `ACTION_APPWIDGET_UPDATE` broadcast.

meta-data:
- `meta-data android:name`
- `android:resource` points to location of `AppWidgetProviderInfo` in resources folder
```xml
<receiver android:name="ExampleAppWidgetProvider"
                 android:exported="false">
    <intent-filter>
        <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
    </intent-filter>
    <meta-data android:name="android.appwidget.provider"
               android:resource="@xml/example_appwidget_info" />
</receiver>
```

AppWidgetProvider class extends BroadcastReceiver class, i.e it's a child class:

It receives only the event broadcasts when widget is updated, deleted, enabled, and disabled. 
Use method `onUpdate` which is called in every `updatePeriodMillis`

Widget layout - What will display
Need to learn more about [layouts](https://developer.android.com/guide/topics/ui/declaring-layout).
Needs to be saved in `res/layout`

From this [stackoverflow answer](https://stackoverflow.com/a/6265265/19961621) `AppWidgetProvider` calls Service

---
## Resources:
[BroadCast Listener and XML Adding](https://github.com/Fector101/Laner/wiki/Dev#how-to-create-broadcast-listener-kivy)

[android docs](https://developer.android.com/develop/ui/views/appwidgets)

[StackoverFlow AppWidgetProvider call service](https://stackoverflow.com/a/6265265/19961621)
