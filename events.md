## Tap widget to open App

### In the Layout xml used to display widget
ID's must be added for later use
- `android:id="@+id/widget_root"` to select layout container
- `android:id="@+id/widget_text"` to select text

Path: `res/layout/simple_widget.xml`
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/widget_root"
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

### In the `AppWidgetProvider`
path: `src/SimpleWidget.java`
- Change first line `org.wally.waller` to data in your buildozer.spec `package.domain+'.'+package.name`
- Apply Same Change to `org.wally.waller.R` and leave `.R` as is.
```java
package org.wally.waller;

import android.appwidget.AppWidgetManager;
import android.appwidget.AppWidgetProvider;
import android.app.PendingIntent;
import android.content.Intent;
import android.content.Context;
import android.util.Log;
import android.widget.RemoteViews;


import org.wally.waller.R;

public class SimpleWidget extends AppWidgetProvider {

    private static final String TAG = "SimpleWidget";

    @Override
    public void onUpdate(
            Context context,
            AppWidgetManager appWidgetManager,
            int[] appWidgetIds
    ) {

        Log.d(TAG, "onUpdate() called, widget count=" + appWidgetIds.length);

        for (int appWidgetId : appWidgetIds) {

            Log.d(TAG, "Updating widgetId=" + appWidgetId);

            RemoteViews views = new RemoteViews(
                    context.getPackageName(),
                    R.layout.carousel_widget
            );

            // WIDGET CLICK → APP LAUNCH
            Log.d(TAG, "Resolving launch intent");

            Intent intent = context.getPackageManager().getLaunchIntentForPackage(context.getPackageName());

            if (intent == null) {
                Log.e(TAG, "Launch intent is NULL – app has no LAUNCHER activity");
            } else {
                Log.d(TAG, "Launch intent resolved: " + intent.getComponent());

                intent.setFlags(
                        Intent.FLAG_ACTIVITY_NEW_TASK |
                        Intent.FLAG_ACTIVITY_CLEAR_TOP
                );

                intent.putExtra("from_widget", true);
                Log.d(TAG, "Extra 'from_widget' added");

                PendingIntent pendingIntent = PendingIntent.getActivity(
                        context,
                        appWidgetId,
                        intent,
                        PendingIntent.FLAG_UPDATE_CURRENT | PendingIntent.FLAG_IMMUTABLE
                );

                Log.d(TAG, "PendingIntent created");

                views.setOnClickPendingIntent(R.id.widget_root, pendingIntent);
                views.setOnClickPendingIntent(R.id.widget_text, pendingIntent);
                Log.d(TAG, "PendingIntent attached to R.id.widget_text and widget");
            }
            appWidgetManager.updateAppWidget(appWidgetId, views);
    }
}
```
