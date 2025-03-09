# KiteTricksApp
package com.example.kitetricks;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button btnBeginner = findViewById(R.id.btnBeginner);
        Button btnMedium = findViewById(R.id.btnMedium);
        Button btnAdvanced = findViewById(R.id.btnAdvanced);
        Button btnExpert = findViewById(R.id.btnExpert);

        View.OnClickListener categoryClickListener = new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String category = ((Button) v).getText().toString();
                Intent intent = new Intent(MainActivity.this, TrickListActivity.class);
                intent.putExtra("CATEGORY", category);
                startActivity(intent);
            }
        };

        btnBeginner.setOnClickListener(categoryClickListener);
        btnMedium.setOnClickListener(categoryClickListener);
        btnAdvanced.setOnClickListener(categoryClickListener);
        btnExpert.setOnClickListener(categoryClickListener);
    }
}
package com.example.kitetricks;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.ListView;
import androidx.appcompat.app.AppCompatActivity;
import java.util.ArrayList;

public class TrickListActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_trick_list);

        String category = getIntent().getStringExtra("CATEGORY");
        ListView trickListView = findViewById(R.id.trickListView);

        ArrayList<String> tricks = getTricksForCategory(category);
        ArrayAdapter<String> adapter = new ArrayAdapter<>(this, android.R.layout.simple_list_item_1, tricks);
        trickListView.setAdapter(adapter);

        trickListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                String trick = tricks.get(position);
                Intent intent = new Intent(TrickListActivity.this, UploadVideoActivity.class);
                intent.putExtra("TRICK_NAME", trick);
                startActivity(intent);
            }
        });
    }

    private ArrayList<String> getTricksForCategory(String category) {
        ArrayList<String> tricks = new ArrayList<>();
        switch (category) {
            case "Beginner":
                tricks.add("Waterstart");
                tricks.add("First Jump");
                break;
            case "Medium":
                tricks.add("Backroll");
                tricks.add("Frontroll");
                break;
            case "Avancé":
                tricks.add("Kiteloop");
                tricks.add("Darkslide");
                break;
            case "Expert":
                tricks.add("Handle Pass");
                tricks.add("Board Off");
                break;
        }
        return tricks;
    }
}
package com.example.kitetricks;

import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;

public class UploadVideoActivity extends AppCompatActivity {
    private static final int PICK_VIDEO_REQUEST = 1;
    private Uri videoUri;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_upload_video);

        String trickName = getIntent().getStringExtra("TRICK_NAME");
        TextView trickTextView = findViewById(R.id.trickTextView);
        trickTextView.setText("Trick sélectionné : " + trickName);

        Button btnSelectVideo = findViewById(R.id.btnSelectVideo);
        Button btnUploadVideo = findViewById(R.id.btnUploadVideo);

        btnSelectVideo.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("video/*");
                startActivityForResult(intent, PICK_VIDEO_REQUEST);
            }
        });

        btnUploadVideo.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (videoUri != null) {
                    if (validateVideo(videoUri)) {
                        Toast.makeText(UploadVideoActivity.this, "Vidéo envoyée !", Toast.LENGTH_SHORT).show();
                    } else {
                        Toast.makeText(UploadVideoActivity.this, "Vidéo invalide (format/durée incorrects)", Toast.LENGTH_SHORT).show();
                    }
                } else {
                    Toast.makeText(UploadVideoActivity.this, "Veuillez sélectionner une vidéo", Toast.LENGTH_SHORT).show();
                }
            }
        });
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_VIDEO_REQUEST && resultCode == RESULT_OK && data != null) {
            videoUri = data.getData();
            Toast.makeText(this, "Vidéo sélectionnée", Toast.LENGTH_SHORT).show();
        }
    }

    private boolean validateVideo(Uri videoUri) {
        // Ici, on pourrait vérifier la durée et le format
        return true; // Simplifié pour le moment
    }
}
