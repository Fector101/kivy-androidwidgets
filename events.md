## Tap widget to open App


```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/widget_root"
    android:orientation="vertical"
    android:gravity="center"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:clickable="true"
    android:focusable="true" >

    <ImageView
        android:id="@+id/test_image"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:scaleType="centerCrop"/>


</LinearLayout>
```
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

public class CarouselWidgetProvider extends AppWidgetProvider {

    private static final String TAG = "CarouselWidgetProvider";

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
                views.setOnClickPendingIntent(R.id.test_image, pendingIntent);
                Log.d(TAG, "PendingIntent attached to R.id.test_image and widget");
            }


                appWidgetManager.updateAppWidget(appWidgetId, views);
    }
}
```
