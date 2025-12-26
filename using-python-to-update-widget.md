## This is a working sample, Don't have time to write explanation for now, will surely do later. Dropping it now cause i think it's very useful

### Worked in my service file and it should work for UI thread too.
```python
from android_notify.config import get_python_service, get_python_activity_context

    def worked_changed_widget_from_service(self):
        # This worked for Changing widget text
        AppWidgetManager = autoclass('android.appwidget.AppWidgetManager')
        ComponentName = autoclass('android.content.ComponentName')
        RemoteViews = autoclass('android.widget.RemoteViews')

        context = get_python_activity_context()  # PythonActivity.mActivity.getApplicationContext()
        resources = context.getResources()
        package_name = context.getPackageName()

        # IMPORTANT: use CLASS NAME STRING, NOT autoclass
        component = ComponentName(
            context,
            'org.wally.waller.Image1'
        )

        appWidgetManager = AppWidgetManager.getInstance(context)
        ids = appWidgetManager.getAppWidgetIds(component)

        text_layout = resources.getIdentifier("image_test_widget", "layout", package_name)
        title_id = resources.getIdentifier("widget_text", "id", package_name)

        views = RemoteViews(package_name, text_layout)
        views.setTextViewText(title_id, AndroidString("Frm Python"))
        appWidgetManager.updateAppWidget(ids, views)
        print('got------- here')

    def update_widget_image(self, wallpaper_path):
        Bitmap = autoclass('android.graphics.Bitmap')
        BitmapConfig = autoclass('android.graphics.Bitmap$Config')
        Canvas = autoclass('android.graphics.Canvas')
        Paint = autoclass('android.graphics.Paint')
        Rect = autoclass('android.graphics.Rect')
        RectF = autoclass('android.graphics.RectF')
        PorterDuffMode = autoclass('android.graphics.PorterDuff$Mode')
        PorterDuffXfermode = autoclass('android.graphics.PorterDuffXfermode')

        BitmapFactory = autoclass('android.graphics.BitmapFactory')
        BitmapFactoryOptions = autoclass('android.graphics.BitmapFactory$Options')

        AppWidgetManager = autoclass('android.appwidget.AppWidgetManager')
        ComponentName = autoclass('android.content.ComponentName')
        RemoteViews = autoclass('android.widget.RemoteViews')

        context = get_python_activity_context()
        resources = context.getResources()
        package_name = context.getPackageName()

        image_file = os.path.join(
            context.getFilesDir().getAbsolutePath(),
            "app",
            wallpaper_path
        )

        if not os.path.exists(image_file):
            self.__log(f"Image not found: {image_file}", "ERROR")
            return

        opts = BitmapFactoryOptions()
        opts.inSampleSize = 4  # widget-safe memory usage
        src = BitmapFactory.decodeFile(image_file, opts)

        if src is None:
            self.__log("Bitmap decode failed", "ERROR")
            return

        # Crop bitmap to square
        size = min(src.getWidth(), src.getHeight())
        x = (src.getWidth() - size) // 2
        y = (src.getHeight() - size) // 2
        square = Bitmap.createBitmap(src, x, y, size, size)

        # Scale bitmap to widget size
        widget_dp = 120  # widget layout width/height in dp
        density = context.getResources().getDisplayMetrics().density
        widget_px = int(widget_dp * density)  # convert dp to pixels

        scaled_bitmap = Bitmap.createScaledBitmap(square, widget_px, widget_px, True)

        # Create rounded bitmap using Canvas
        output = Bitmap.createBitmap(widget_px, widget_px, BitmapConfig.ARGB_8888)
        canvas = Canvas(output)

        paint = Paint()
        paint.setAntiAlias(True)

        rect = Rect(0, 0, widget_px, widget_px)
        rectF = RectF(rect)

        corner_radius_px = 16 * density  # 16dp corners
        canvas.drawARGB(0, 0, 0, 0)
        canvas.drawRoundRect(rectF, corner_radius_px, corner_radius_px, paint)

        paint.setXfermode(PorterDuffXfermode(PorterDuffMode.SRC_IN))
        canvas.drawBitmap(scaled_bitmap, rect, rect, paint)

        # Update widget
        layout_id = resources.getIdentifier("image_test_widget", "layout", package_name)
        image_id = resources.getIdentifier("test_image", "id", package_name)

        views = RemoteViews(package_name, layout_id)
        views.setImageViewBitmap(image_id, output)

        component = ComponentName(context, f"{package_name}.Image1")
        appWidgetManager = AppWidgetManager.getInstance(context)
        ids = appWidgetManager.getAppWidgetIds(component)
        appWidgetManager.updateAppWidget(ids, views)

        self.__log(f"Changed Home Screen Widget: {wallpaper_path}", "SUCCESS")

```
