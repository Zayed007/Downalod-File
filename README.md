# Downalod-File

##### Create DownloadFile Class , use the following code

```
import android.app.DownloadManager;
import android.content.Context;
import android.net.Uri;
import android.os.Environment;
import android.widget.Toast;
import java.io.File;

import static android.content.Context.DOWNLOAD_SERVICE;

public class DownloadFile {

    public static long downloadID;

    public static void downloadFileFromUrl(Context context, String url, String fileName) {

        String filePath = Environment.getExternalStorageDirectory() + File.separator + "PrizeBond";
        File folder = new File(filePath);

        if (!folder.exists()) {
            folder.mkdirs();
        }

        try {

            Uri downloadUri = Uri.parse(url);
            DownloadManager.Request request = new DownloadManager.Request(downloadUri);

            request.allowScanningByMediaScanner();
            request.setDestinationInExternalPublicDir(context.getString(R.string.label_path), fileName);
            request.setNotificationVisibility(DownloadManager.Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED);
            request.setVisibleInDownloadsUi(false);
            request.setAllowedOverMetered(true);
            request.setAllowedOverRoaming(true);
            DownloadManager downloadManager = (DownloadManager) context.getSystemService(DOWNLOAD_SERVICE);
            downloadID = downloadManager.enqueue(request);
            Toast.makeText(context, fileName, Toast.LENGTH_LONG).show();
            Toast.makeText(context, filePath, Toast.LENGTH_LONG).show();

        } catch (Exception ex) {
            Toast.makeText(context, ex.toString(), Toast.LENGTH_LONG).show();
        }
    }
}
```

##### Create the broadcast receiver inside MainActivity to notify when download is complete

```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        registerReceiver(onDownloadComplete,new IntentFilter(DownloadManager.ACTION_DOWNLOAD_COMPLETE));
    }
    
    private BroadcastReceiver onDownloadComplete = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            //Fetching the download id received with the broadcast
            long id = intent.getLongExtra(DownloadManager.EXTRA_DOWNLOAD_ID, -1);
            //Checking if the received broadcast is for our enqueued download by matching download id
            if (downloadID == id) {
                Toast.makeText(MainActivity.this, "Download Completed", Toast.LENGTH_SHORT).show();
            }
        }
    };

    @Override
    public void onDestroy() {
        super.onDestroy();
        unregisterReceiver(onDownloadComplete);
    }
```
