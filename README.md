 import retrofit2.Call;
import retrofit2.http.GET;

public interface ApiService {
    @GET("content") // Your API endpoint
    Call<List<ContentMetadata>> getContent();
}
// DownloadService.java
import android.app.IntentService;
import android.content.Intent;
import android.util.Log;

import androidx.annotation.Nullable;

import java.io.File;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class DownloadService extends IntentService {
    private static final String TAG = "DownloadService";

    public DownloadService() {
        super("DownloadService");
    }

    @Override
    protected void onHandleIntent(@Nullable Intent intent) {
        String fileUrl = intent.getStringExtra("url");
        String filePath = intent.getStringExtra("path");

        try {
            URL url = new URL(fileUrl);
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
            connection.setRequestMethod("GET");
            connection.setDoOutput(true);
            connection.connect();

            File file = new File(filePath);
            FileOutputStream outputStream = new FileOutputStream(file);

            InputStream inputStream = connection.getInputStream();
            byte[] buffer = new byte[1024];
            int bytesRead;

            while ((bytesRead = inputStream.read(buffer)) != -1) {
                outputStream.write(buffer, 0, bytesRead);
            }

            outputStream.close();
            inputStream.close();
        } catch (Exception e) {
            Log.e(TAG, "Download failed", e);
        }
    }
}
// AppDatabase.java
import android.content.Context;
import androidx.room.Database;
import androidx.room.Room;
import androidx.room.RoomDatabase;

@Database(entities = {Content.class}, version = 1)
public abstract class AppDatabase extends RoomDatabase {
    public abstract ContentDao contentDao();

    private static volatile AppDatabase INSTANCE;

    public static AppDatabase getDatabase(final Context context) {
        if (INSTANCE == null) {
            synchronized (AppDatabase.class) {
                if (INSTANCE == null) {
                    INSTANCE = Room.databaseBuilder(context.getApplicationContext(),
                            AppDatabase.class, "content_database")
                            .build();
                }
            }
        }
        return INSTANCE;
    }
}
// ApiService.java
import retrofit2.Call;
import retrofit2.http.GET;

public interface ApiService {
    @GET("content") // Your API endpoint
    Call<List<ContentMetadata>> getContent();
}
// DownloadService.java
import android.app.IntentService;
import android.content.Intent;
import android.util.Log;

import androidx.annotation.Nullable;

import java.io.File;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class DownloadService extends IntentService {
    private static final String TAG = "DownloadService";

    public DownloadService() {
        super("DownloadService");
    }

    @Override
    protected void onHandleIntent(@Nullable Intent intent) {
        String fileUrl = intent.getStringExtra("url");
        String filePath = intent.getStringExtra("path");

        try {
            URL url = new URL(fileUrl);
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
            connection.setRequestMethod("GET");
            connection.setDoOutput(true);
            connection.connect();

            File file = new File(filePath);
            FileOutputStream outputStream = new FileOutputStream(file);

            InputStream inputStream = connection.getInputStream();
            byte[] buffer = new byte[1024];
            int bytesRead;

            while ((bytesRead = inputStream.read(buffer)) != -1) {
                outputStream.write(buffer, 0, bytesRead);
            }

            outputStream.close();
            inputStream.close();
        } catch (Exception e) {
            Log.e(TAG, "Download failed", e);
        }
    }
}
// DownloadService.java
import android.app.IntentService;
import android.content.Intent;
import android.util.Log;

import androidx.annotation.Nullable;

import java.io.File;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.net.HttpURLConnection;
import java.net.URL;

public class DownloadService extends IntentService {
    private static final String TAG = "DownloadService";

    public DownloadService() {
        super("DownloadService");
    }

    @Override
    protected void onHandleIntent(@Nullable Intent intent) {
        String fileUrl = intent.getStringExtra("url");
        String filePath = intent.getStringExtra("path");

        try {
            URL url = new URL(fileUrl);
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
            connection.setRequestMethod("GET");
            connection.setDoOutput(true);
            connection.connect();

            File file = new File(filePath);
            FileOutputStream outputStream = new FileOutputStream(file);

            InputStream inputStream = connection.getInputStream();
            byte[] buffer = new byte[1024];
            int bytesRead;

            while ((bytesRead = inputStream.read(buffer)) != -1) {
                outputStream.write(buffer, 0, bytesRead);
            }

            outputStream.close();
            inputStream.close();
        } catch (Exception e) {
            Log.e(TAG, "Download failed", e);
        }
    }
}
// In your activity or fragment
Intent intent = new Intent(this, DownloadService.class);
intent.putExtra("url", "https://example.com/file.mp4");
intent.putExtra("path", getExternalFilesDir(null) + "/file.mp4");
startService(intent);
<!-- In your activity_main.xml layout -->
<com.google.android.exoplayer2.ui.PlayerView
    android:id="@+id/player_view"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
// MainActivity.java
import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;
import com.google.android.exoplayer2.ExoPlayer;
import com.google.android.exoplayer2.MediaItem;
import com.google.android.exoplayer2.Player;
import com.google.android.exoplayer2.ui.PlayerView;

public class MainActivity extends AppCompatActivity {

    private ExoPlayer player;
    private PlayerView playerView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        playerView = findViewById(R.id.player_view);
        player = new ExoPlayer.Builder(this).build();
        playerView.setPlayer(player);

        MediaItem mediaItem = MediaItem.fromUri(getExternalFilesDir(null) + "/file.mp4");
        player.setMediaItem(mediaItem);
        player.prepare();
        player.play();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        player.release();
    }
}
// MainActivity.java (continued)
import androidx.lifecycle.Observer;
import androidx.lifecycle.ViewModelProvider;
import androidx.recyclerview.widget.RecyclerView;
import java.util.List;

public class MainActivity extends AppCompatActivity {

    private RecyclerView recyclerView;
    private ContentAdapter contentAdapter;
    private AppDatabase appDatabase;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        appDatabase = AppDatabase.getDatabase(this);
        recyclerView = findViewById(R.id.recycler_view);
        contentAdapter = new ContentAdapter();
        recyclerView.setAdapter(contentAdapter);

        appDatabase.contentDao().getAllContent().observe(this, new Observer<List<Content>>() {
            @Override
            public void onChanged(List<Content> contents) {
                contentAdapter.setContent(contents);
            }
        });
    }
}
// ContentAdapter.java
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;
import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;
import java.util.List;

public class ContentAdapter extends RecyclerView.Adapter<ContentAdapter.ContentViewHolder> {
    private List<Content> contentList;

    public static class ContentViewHolder extends RecyclerView.ViewHolder {
        public TextView title;

        public ContentViewHolder(View view) {
            super(view);
            title = view.findViewById(R.id.content_title);
        }
    }

    @NonNull
    @Override
    public ContentViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.content_item, parent, false);
        return new ContentViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull ContentViewHolder holder, int position) {
        Content content = contentList.get(position);
        holder.title.setText(content.title);
    }

    @Override
    public int getItemCount() {
        return contentList.size();
    }

    public void setContent(List<Content> content) {
        this.contentList = content;
        notifyDataSetChanged();
    }
}
<!-- content_item.xml -->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="16dp">

    <TextView
        android:id="@+id/content_title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="18sp" />
</LinearLayout>
// SyncWorker.java
import android.content.Context;
import androidx.annotation.NonNull;
import androidx.work.Worker;
import androidx.work.WorkerParameters;

public class SyncWorker extends Worker {

    public SyncWorker(@NonNull Context context, @NonNull WorkerParameters params) {
        super(context, params);
    }

    @NonNull
    @Override
    public Result doWork() {
        // Perform synchronization with server
        return Result.success();
    }
}
// In your activity or fragment
import androidx.work.OneTimeWorkRequest;
import androidx.work.WorkManager;

OneTimeWorkRequest syncRequest = new OneTimeWorkRequest.Builder(SyncWorker.class).build();
WorkManager.getInstance(this).enqueue(syncRequest);
