/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/AndroidManifest.xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"
        android:maxSdkVersion="28" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.MANAGE_EXTERNAL_STORAGE"
        tools:ignore="ScopedStorage" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />
    <uses-permission android:name="android.permission.INSTALL_PACKAGES"
        tools:ignore="ProtectedPermissions" />

    <application
        android:name=".MyApplication"
        android:allowBackup="true"
        android:fullBackupContent="@xml/backup_rules"
        android:label="Terraria Loader"
        android:icon="@mipmap/ic_launcher"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.MaterialComponents.DayNight.DarkActionBar"
        android:requestLegacyExternalStorage="true">

        <provider
            android:name="androidx.core.content.FileProvider"
            android:authorities="${applicationId}.provider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/paths" />
        </provider>

        <!-- Main launcher activity -->
        <activity android:name=".MainActivity" android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <!-- Core activities -->
        <activity android:name=".TerrariaSpecificActivity" android:exported="false"/>
        <activity android:name=".AboutActivity" android:exported="false"/>
        <activity android:name=".UniversalActivity" android:exported="false"/>
        <activity android:name=".SpecificSelectionActivity" android:exported="false"/>
        <activity android:name=".ui.OfflineDiagnosticActivity" android:exported="false"/>

        <!-- NEW: Unified Loader Activity (replaces TerrariaActivity and DllModActivity) -->
        <activity android:name=".ui.UnifiedLoaderActivity" android:exported="false"/>
        
        <!-- Updated: Pure mod management only -->
        <activity android:name=".ui.ModManagementActivity" android:exported="false"/>
        <activity android:name=".ui.ModListActivity" android:exported="false"/>
        
        <!-- Updated: Setup guide with offline ZIP import -->
        <activity android:name=".ui.SetupGuideActivity" android:exported="false"/>
        
        <!-- Utility activities -->
        <activity android:name=".ui.SettingsActivity" android:exported="false"/>
        <activity android:name=".ui.LogViewerActivity" android:exported="false"/>
        <activity android:name=".ui.InstructionsActivity" android:exported="false"/>
        <activity android:name=".SandboxActivity" android:exported="false"/>
        
        
    </application>
</manifest>

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/AboutActivity.java
package com.terrarialoader;

import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;

public class AboutActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_about);

        setTitle("About");
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/LogActivity.java
package com.terrarialoader;

import android.content.Intent;
import android.os.Bundle;
import android.view.MenuItem;
import android.widget.ScrollView;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.content.FileProvider;

import com.terrarialoader.util.LogUtils;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;

public class LogActivity extends AppCompatActivity {

    private TextView logText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setTitle("Logs");
        setContentView(R.layout.activity_log);

        logText = findViewById(R.id.log_text);
        ScrollView scrollView = findViewById(R.id.log_scroll);

        logText.setText(LogUtils.getLogs());
        scrollView.post(() -> scrollView.fullScroll(ScrollView.FOCUS_DOWN));

        findViewById(R.id.export_logs_button).setOnClickListener(v -> exportLogs());
    }

    private void exportLogs() {
        try {
            File logDir = new File(getExternalFilesDir(null), "exports");
            if (!logDir.exists()) logDir.mkdirs();

            File logFile = new File(logDir, "logs.txt");
            FileWriter writer = new FileWriter(logFile);
            writer.write(LogUtils.getLogs());
            writer.close();

            Intent shareIntent = new Intent(Intent.ACTION_SEND);
            shareIntent.setType("text/plain");
            shareIntent.putExtra(Intent.EXTRA_STREAM,
                    FileProvider.getUriForFile(this, getPackageName() + ".provider", logFile));
            shareIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
            startActivity(Intent.createChooser(shareIntent, "Share Logs"));

            Toast.makeText(this, "Logs exported", Toast.LENGTH_SHORT).show();
        } catch (IOException e) {
            e.printStackTrace();
            Toast.makeText(this, "Failed to export logs", Toast.LENGTH_SHORT).show();
        }
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        finish();
        return true;
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/MainActivity.java
// File: MainActivity.java (Activity Class) - Fixed Navigation Structure
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/MainActivity.java

package com.terrarialoader;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;

import com.terrarialoader.util.LogUtils;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        try {
            stopService(new Intent().setClassName(getPackageName(), "com.itsaky.androidide.logsender.LogSenderService"));
        } catch (Exception ignored) {}

        LogUtils.initialize(getApplicationContext());
        LogUtils.logDebug("MainActivity.onCreate() started");

        Thread.setDefaultUncaughtExceptionHandler((thread, throwable) -> {
            LogUtils.logDebug("FATAL CRASH: " + throwable);
            for (StackTraceElement e : throwable.getStackTrace()) {
                LogUtils.logDebug("    at " + e.toString());
            }
        });

        LogUtils.logDebug("Setting up main navigation");

        setContentView(R.layout.activity_main);
        setTitle("Terraria Loader - Main Menu");

        Button universalButton = findViewById(R.id.universal_button);
        Button specificButton = findViewById(R.id.specific_button);

        universalButton.setOnClickListener(v -> {
            LogUtils.logUser("Universal Mode selected");
            startActivity(new Intent(MainActivity.this, UniversalActivity.class));
        });

        specificButton.setOnClickListener(v -> {
            LogUtils.logUser("Specific Mode selected - showing app selection");
            startActivity(new Intent(MainActivity.this, SpecificSelectionActivity.class));
        });
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions,
                                           @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        LogUtils.logDebug("Permission result callback triggered");
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/MyApplication.java
// File: MyApplication.java (FIXED) - Initialize Logs and Handle Migration
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/MyApplication.java

package com.terrarialoader;

import android.app.Application;
import android.os.Environment;
import com.terrarialoader.util.LogUtils;
import com.terrarialoader.loader.MelonLoaderManager;
import com.terrarialoader.installer.ModInstaller;
import java.io.File;

public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        
        // FIXED: Initialize LogUtils first
        LogUtils.initialize(getApplicationContext());
        
        // FIXED: Initialize app startup logging
        LogUtils.initializeAppStartup();
        
        // Auto-create TerrariaLoader directory structure on first run
        initializeTerrariaLoaderStructure();
        
        // FIXED: Handle migration from legacy structure
        handleMigration();
    }
    
    private void initializeTerrariaLoaderStructure() {
        try {
            LogUtils.logUser("🚀 Initializing TerrariaLoader directory structure...");
            
            // The primary and most reliable location for app files.
            File baseDir = getExternalFilesDir(null);
            if (baseDir == null) {
                LogUtils.logDebug("❌ External storage is not available or app-specific directory is null.");
                return;
            }

            // FIXED: Use new directory structure
            File terrariaLoaderDir = new File(baseDir, "TerrariaLoader");
            File gameDir = new File(terrariaLoaderDir, "com.and.games505.TerrariaPaid");

            if (!gameDir.exists()) {
                if (gameDir.mkdirs()) {
                    LogUtils.logUser("✅ Created base TerrariaLoader directory at: " + gameDir.getAbsolutePath());
                } else {
                    LogUtils.logDebug("❌ Failed to create base directory: " + gameDir.getAbsolutePath());
                    return; // Stop if we can't create the main folder
                }
            }
            
            // Create Terraria-specific directories manually inside the new base directory
            createTerrariaDirectories(gameDir);
            
        } catch (Exception e) {
            LogUtils.logDebug("Directory initialization error: " + e.getMessage());
        }
    }
    
    private void createTerrariaDirectories(File gameBaseDir) {
        try {
            String basePath = gameBaseDir.getAbsolutePath();
            
            // FIXED: Updated directory structure with correct paths
            String[] directories = {
                basePath,
                basePath + "/Mods",
                basePath + "/Mods/DEX",      // FIXED: DEX mods directory
                basePath + "/Mods/DLL",      // FIXED: DLL mods directory
                basePath + "/Config",
                basePath + "/Logs",          // FIXED: Game logs (MelonLoader logs)
                basePath + "/AppLogs",       // FIXED: App logs (TerrariaLoader logs)
                basePath + "/Backups",
                basePath + "/Loaders",
                basePath + "/Loaders/MelonLoader",
                basePath + "/Loaders/MelonLoader/net8",
                basePath + "/Loaders/MelonLoader/net35",
                basePath + "/Loaders/MelonLoader/Dependencies",
                basePath + "/Loaders/MelonLoader/Dependencies/SupportModules",
                basePath + "/Loaders/MelonLoader/Dependencies/CompatibilityLayers",
                basePath + "/Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator",
                basePath + "/Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/Cpp2IL/cpp2il_out",
                basePath + "/Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/UnityDependencies",
                basePath + "/Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/Il2CppInterop/Il2CppAssemblies",
                // FIXED: Plugins and UserLibs at game root level, not inside MelonLoader
                basePath + "/Plugins",       // FIXED: Plugins at root level
                basePath + "/UserLibs"       // FIXED: UserLibs at root level
            };
            
            int createdCount = 0;
            for (String dirPath : directories) {
                File dir = new File(dirPath);
                if (!dir.exists()) {
                    if (dir.mkdirs()) {
                        createdCount++;
                        LogUtils.logDebug("✅ Created: " + dirPath);
                    } else {
                        LogUtils.logDebug("❌ Failed: " + dirPath);
                    }
                } else {
                    LogUtils.logDebug("📁 Exists: " + dirPath);
                }
            }
            
            if (createdCount > 0) {
                LogUtils.logUser("✅ TerrariaLoader directory structure created successfully");
                LogUtils.logUser("📁 Path: " + basePath);
                LogUtils.logUser("📊 Created " + createdCount + " directories");
                createInfoFiles(basePath);
            } else {
                LogUtils.logUser("📁 TerrariaLoader directories already exist");
            }
            
        } catch (Exception e) {
            LogUtils.logDebug("Manual directory creation error: " + e.getMessage());
        }
    }
    
    private void createInfoFiles(String basePath) {
        try {
            // FIXED: Create README files in correct directories
            
            // DEX mods README
            File dexReadme = new File(basePath + "/Mods/DEX", "README.txt");
            if (!dexReadme.exists()) {
                try (java.io.FileWriter writer = new java.io.FileWriter(dexReadme)) {
                    writer.write("=== TerrariaLoader - DEX/JAR Mods ===\n\n");
                    writer.write("Place your .dex and .jar mod files here.\n");
                    writer.write("Supported formats:\n");
                    writer.write("• .dex files (enabled)\n");
                    writer.write("• .jar files (enabled)\n");
                    writer.write("• .dex.disabled files (disabled)\n");
                    writer.write("• .jar.disabled files (disabled)\n\n");
                    writer.write("Path: " + dexReadme.getAbsolutePath() + "\n");
                }
                LogUtils.logDebug("✅ Created DEX mods README");
            }
            
            // DLL mods README
            File dllReadme = new File(basePath + "/Mods/DLL", "README.txt");
            if (!dllReadme.exists()) {
                try (java.io.FileWriter writer = new java.io.FileWriter(dllReadme)) {
                    writer.write("=== TerrariaLoader - DLL Mods ===\n\n");
                    writer.write("Place your .dll mod files here.\n");
                    writer.write("Requires MelonLoader to be installed.\n");
                    writer.write("Supported formats:\n");
                    writer.write("• .dll files (enabled)\n");
                    writer.write("• .dll.disabled files (disabled)\n\n");
                    writer.write("Path: " + dllReadme.getAbsolutePath() + "\n");
                }
                LogUtils.logDebug("✅ Created DLL mods README");
            }
            
            // FIXED: Plugins README
            File pluginsReadme = new File(basePath + "/Plugins", "README.txt");
            if (!pluginsReadme.exists()) {
                try (java.io.FileWriter writer = new java.io.FileWriter(pluginsReadme)) {
                    writer.write("=== MelonLoader Plugins Directory ===\n\n");
                    writer.write("This directory contains MelonLoader plugins.\n");
                    writer.write("Plugins extend MelonLoader functionality.\n\n");
                    writer.write("Files you might see here:\n");
                    writer.write("• .dll plugin files\n");
                    writer.write("• Plugin configuration files\n\n");
                    writer.write("Path: " + pluginsReadme.getAbsolutePath() + "\n");
                }
                LogUtils.logDebug("✅ Created Plugins README");
            }
            
            // FIXED: UserLibs README
            File userlibsReadme = new File(basePath + "/UserLibs", "README.txt");
            if (!userlibsReadme.exists()) {
                try (java.io.FileWriter writer = new java.io.FileWriter(userlibsReadme)) {
                    writer.write("=== MelonLoader UserLibs Directory ===\n\n");
                    writer.write("This directory contains user libraries.\n");
                    writer.write("Libraries that mods depend on go here.\n\n");
                    writer.write("Files you might see here:\n");
                    writer.write("• .dll library files\n");
                    writer.write("• Shared mod dependencies\n\n");
                    writer.write("Path: " + userlibsReadme.getAbsolutePath() + "\n");
                }
                LogUtils.logDebug("✅ Created UserLibs README");
            }
            
            // FIXED: Game logs README
            File gameLogsReadme = new File(basePath + "/Logs", "README.txt");
            if (!gameLogsReadme.exists()) {
                try (java.io.FileWriter writer = new java.io.FileWriter(gameLogsReadme)) {
                    writer.write("=== MelonLoader Game Logs ===\n\n");
                    writer.write("This directory contains logs from MelonLoader and mods.\n");
                    writer.write("These are generated when running the patched game.\n\n");
                    writer.write("Log files follow rotation:\n");
                    writer.write("• Log.txt (current log)\n");
                    writer.write("• Log1.txt to Log5.txt (previous logs)\n");
                    writer.write("• Oldest logs are automatically deleted\n\n");
                    writer.write("Path: " + gameLogsReadme.getAbsolutePath() + "\n");
                }
                LogUtils.logDebug("✅ Created Game logs README");
            }
            
            // FIXED: App logs README
            File appLogsReadme = new File(basePath + "/AppLogs", "README.txt");
            if (!appLogsReadme.exists()) {
                try (java.io.FileWriter writer = new java.io.FileWriter(appLogsReadme)) {
                    writer.write("=== TerrariaLoader App Logs ===\n\n");
                    writer.write("This directory contains logs from TerrariaLoader app.\n");
                    writer.write("These are generated when using this app.\n\n");
                    writer.write("Log files follow rotation:\n");
                    writer.write("• AppLog.txt (current log)\n");
                    writer.write("• AppLog1.txt to AppLog5.txt (previous logs)\n");
                    writer.write("• Oldest logs are automatically deleted\n\n");
                    writer.write("Path: " + appLogsReadme.getAbsolutePath() + "\n");
                }
                LogUtils.logDebug("✅ Created App logs README");
            }
            
        } catch (Exception e) {
            LogUtils.logDebug("Info file creation error: " + e.getMessage());
        }
    }
    
    // FIXED: Handle migration from legacy structure
    private void handleMigration() {
        try {
            LogUtils.logUser("🔍 Checking for legacy structure migration...");
            
            // Check if migration is needed for mods
            if (ModInstaller.needsMigration(this)) {
                LogUtils.logUser("📦 Legacy mods found - starting migration...");
                if (ModInstaller.migrateLegacyMods(this)) {
                    LogUtils.logUser("✅ Successfully migrated legacy mods");
                } else {
                    LogUtils.logUser("⚠️ Migration completed with some issues");
                }
            }
            
            // Check if migration is needed for app logs
            migrateLegacyAppLogs();
            
            LogUtils.logUser("🔍 Migration check completed");
            
        } catch (Exception e) {
            LogUtils.logDebug("Migration error: " + e.getMessage());
        }
    }
    
    // FIXED: Migrate legacy app logs
    private void migrateLegacyAppLogs() {
        try {
            File legacyLogsDir = new File(getExternalFilesDir(null), "logs");
            if (!legacyLogsDir.exists()) {
                return; // No legacy logs to migrate
            }
            
            File[] legacyLogFiles = legacyLogsDir.listFiles((dir, name) -> 
                name.startsWith("auto_save_") || name.endsWith(".txt"));
            
            if (legacyLogFiles == null || legacyLogFiles.length == 0) {
                return; // No log files to migrate
            }
            
            LogUtils.logUser("📋 Migrating " + legacyLogFiles.length + " legacy log files...");
            
            // Create new app logs directory
            File gameBaseDir = new File(getExternalFilesDir(null), "TerrariaLoader/com.and.games505.TerrariaPaid");
            File newAppLogsDir = new File(gameBaseDir, "AppLogs");
            if (!newAppLogsDir.exists()) {
                newAppLogsDir.mkdirs();
            }
            
            int migratedCount = 0;
            for (File legacyLogFile : legacyLogFiles) {
                // Rename to new format
                String newName = "AppLog" + (migratedCount + 1) + ".txt";
                File newLocation = new File(newAppLogsDir, newName);
                
                if (legacyLogFile.renameTo(newLocation)) {
                    migratedCount++;
                    LogUtils.logDebug("Migrated log: " + legacyLogFile.getName() + " -> " + newName);
                }
            }
            
            LogUtils.logUser("✅ Migrated " + migratedCount + " log files to new structure");
            
            // Remove legacy logs directory if empty
            if (legacyLogsDir.listFiles() == null || legacyLogsDir.listFiles().length == 0) {
                legacyLogsDir.delete();
                LogUtils.logDebug("Removed empty legacy logs directory");
            }
            
        } catch (Exception e) {
            LogUtils.logDebug("Legacy logs migration error: " + e.getMessage());
        }
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/SandboxActivity.java
package com.terrarialoader;

import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.widget.TextView;

import com.terrarialoader.loader.ModManager;

public class SandboxActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        TextView view = new TextView(this);
        view.setPadding(40, 40, 40, 40);
        view.setText("🔬 Sandbox Mode:\nRunning mods without Terraria...\n\nCheck logcat for mod output.");
        setContentView(view);

        try {
            Log.i("Sandbox", "Loading mods in sandbox...");
            ModManager.loadMods(getApplicationContext());
            Log.i("Sandbox", "Mods loaded.");
        } catch (Exception e) {
            Log.e("Sandbox", "Error during sandbox test", e);
        }
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/SpecificSelectionActivity.java
// File: SpecificSelectionActivity.java (Fixed Activity Class) - Fixed App Selection
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/SpecificSelectionActivity.java

package com.terrarialoader;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

import com.terrarialoader.util.LogUtils;

public class SpecificSelectionActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_specific_selection);
        setTitle("Select Game/App");

        LogUtils.logDebug("SpecificSelectionActivity started");

        // Add header text
        TextView headerText = findViewById(R.id.headerText);
        if (headerText != null) {
            headerText.setText("Choose which game/app to mod:");
        }

        // Setup Terraria button - FIXED: Changed to TerrariaSpecificActivity
        Button terrariaBtn = findViewById(R.id.terraria_button);
        terrariaBtn.setOnClickListener(v -> {
            LogUtils.logUser("Terraria selected for specific modding");
            Intent intent = new Intent(this, TerrariaSpecificActivity.class);
            startActivity(intent);
        });

        // Add future games section
        LinearLayout futureGamesSection = findViewById(R.id.futureGamesSection);
        if (futureGamesSection != null) {
            TextView futureText = new TextView(this);
            futureText.setText("More games coming soon:\n• Minecraft PE\n• Among Us\n• Other Unity Games");
            futureText.setTextSize(14);
            futureText.setPadding(16, 16, 16, 16);
            futureText.setTextColor(getColor(android.R.color.darker_gray));
            futureGamesSection.addView(futureText);
        }
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/TerrariaSpecificActivity.java
// File: TerrariaSpecificActivity.java (Updated) - Elegant UI with Dynamic Theming
// Path: /main/java/com/terrarialoader/TerrariaSpecificActivity.java

package com.terrarialoader;

import android.content.Intent;
import android.graphics.Color;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;
import androidx.cardview.widget.CardView;

import com.terrarialoader.R;
import com.terrarialoader.ui.UnifiedLoaderActivity;
import com.terrarialoader.ui.ModListActivity;
import com.terrarialoader.ui.SettingsActivity;
import com.terrarialoader.ui.LogViewerActivity;
import com.terrarialoader.ui.InstructionsActivity;
import com.terrarialoader.SandboxActivity;
import com.terrarialoader.loader.MelonLoaderManager;
import com.terrarialoader.util.LogUtils;
import com.terrarialoader.ui.OfflineDiagnosticActivity;


public class TerrariaSpecificActivity extends AppCompatActivity {

    private LinearLayout rootLayout;
    private TextView loaderStatusText;
    private CardView setupCard;
    private CardView modManagementCard;
    private CardView toolsCard;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_terraria_specific_updated);
        
        setTitle("🌍 Terraria Mod Loader");
        
        LogUtils.logUser("Terraria Menu Activity opened");
        
        initializeViews();
        applyTerrariaTheme();
        setupUI();
        updateLoaderStatus();
    }

    private void initializeViews() {
        rootLayout = findViewById(R.id.rootLayout);
        loaderStatusText = findViewById(R.id.loaderStatusText);
        setupCard = findViewById(R.id.setupCard);
        modManagementCard = findViewById(R.id.modManagementCard);
        toolsCard = findViewById(R.id.toolsCard);
    }

    private void applyTerrariaTheme() {
        // Dynamic Terraria-inspired green/blue theme
        int primaryGreen = Color.parseColor("#4CAF50");
        int secondaryBlue = Color.parseColor("#2196F3");
        int accentGreen = Color.parseColor("#81C784");
        
        // Apply theme to root layout
        rootLayout.setBackgroundColor(Color.parseColor("#E8F5E8"));
        
        // Apply theme to cards
        setupCard.setCardBackgroundColor(Color.parseColor("#F1F8E9"));
        modManagementCard.setCardBackgroundColor(Color.parseColor("#E3F2FD"));
        toolsCard.setCardBackgroundColor(Color.parseColor("#F3E5F5"));
        
        // Set status bar color
        if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.LOLLIPOP) {
            getWindow().setStatusBarColor(primaryGreen);
        }
    }

    private void setupUI() {
        // === SETUP SECTION ===
        
        // Unified Loader Setup (NEW - replaces multiple buttons)
        Button unifiedSetupBtn = findViewById(R.id.unifiedSetupButton);
        unifiedSetupBtn.setOnClickListener(v -> {
            LogUtils.logUser("Opening Unified MelonLoader Setup");
            Intent intent = new Intent(this, UnifiedLoaderActivity.class);
            startActivity(intent);
        });
        
        // Quick Setup Guide
        Button setupGuideBtn = findViewById(R.id.setupGuideButton);
        setupGuideBtn.setOnClickListener(v -> {
            LogUtils.logUser("Opening Setup Guide");
            Intent intent = new Intent(this, com.terrarialoader.ui.SetupGuideActivity.class);
            startActivity(intent);
        });
        
        // Manual Instructions (fallback)
        Button manualInstructionsBtn = findViewById(R.id.manualInstructionsButton);
        manualInstructionsBtn.setOnClickListener(v -> {
            LogUtils.logUser("Opening Manual Installation Instructions");
            Intent intent = new Intent(this, InstructionsActivity.class);
            startActivity(intent);
        });
        
        // === MOD MANAGEMENT SECTION ===
        
        // DEX/JAR Mod Manager
        Button dexModManagerBtn = findViewById(R.id.dexModManagerButton);
        dexModManagerBtn.setOnClickListener(v -> {
            LogUtils.logUser("Opening DEX/JAR Mod Manager");
            Intent intent = new Intent(this, ModListActivity.class);
            startActivity(intent);
        });
        
        // DLL Mod Manager (redirects to unified system)
        Button dllModManagerBtn = findViewById(R.id.dllModManagerButton);
        dllModManagerBtn.setOnClickListener(v -> {
            LogUtils.logUser("Opening DLL Mod Management via Unified Loader");
            
            // Check if loader is installed
            if (MelonLoaderManager.isMelonLoaderInstalled(this) || MelonLoaderManager.isLemonLoaderInstalled(this)) {
                // If loader is installed, go directly to mod management
                Intent intent = new Intent(this, com.terrarialoader.ui.ModManagementActivity.class);
                startActivity(intent);
            } else {
                // If no loader, start unified setup wizard
                Intent intent = new Intent(this, UnifiedLoaderActivity.class);
                startActivity(intent);
            }
        });
        
        // === TOOLS SECTION ===
        
        // Log Viewer
        Button logViewerBtn = findViewById(R.id.logViewerButton);
        logViewerBtn.setOnClickListener(v -> {
            LogUtils.logUser("Opening Log Viewer");
            Intent intent = new Intent(this, LogViewerActivity.class);
            startActivity(intent);
        });
        
        // Settings
        Button settingsBtn = findViewById(R.id.settingsButton);
        settingsBtn.setOnClickListener(v -> {
            LogUtils.logUser("Opening Settings");
            Intent intent = new Intent(this, SettingsActivity.class);
            startActivity(intent);
        });
        
        // Sandbox Mode
        Button sandboxBtn = findViewById(R.id.sandboxButton);
        sandboxBtn.setOnClickListener(v -> {
            LogUtils.logUser("Opening Sandbox Mode");
            Intent intent = new Intent(this, SandboxActivity.class);
            startActivity(intent);
            
        });    
            
        // Diagnostic Tool
        Button diagnosticBtn = findViewById(R.id.diagnosticButton);
        diagnosticBtn.setOnClickListener(v -> {
            LogUtils.logUser("Opening Offline Diagnostic Tool");
            Intent intent = new Intent(this, OfflineDiagnosticActivity.class);
            startActivity(intent);
   
        });
        
        // === NAVIGATION ===
        
        // Back to App Selection
        Button backBtn = findViewById(R.id.backButton);
        backBtn.setOnClickListener(v -> {
            LogUtils.logUser("Returning to app selection");
            finish();
        });
    }

    private void updateLoaderStatus() {
        boolean melonInstalled = MelonLoaderManager.isMelonLoaderInstalled(this);
        boolean lemonInstalled = MelonLoaderManager.isLemonLoaderInstalled(this);
        
        String statusText;
        int statusColor;
        
        if (melonInstalled) {
            statusText = "✅ MelonLoader " + MelonLoaderManager.getInstalledLoaderVersion() + " ready for DLL mods";
            statusColor = Color.parseColor("#4CAF50"); // Green
        } else if (lemonInstalled) {
            statusText = "✅ LemonLoader " + MelonLoaderManager.getInstalledLoaderVersion() + " ready for DLL mods";
            statusColor = Color.parseColor("#4CAF50"); // Green
        } else {
            statusText = "⚠️ No loader installed - Use 'Complete Setup Wizard' to install MelonLoader";
            statusColor = Color.parseColor("#FF9800"); // Orange
        }
        
        loaderStatusText.setText(statusText);
        loaderStatusText.setTextColor(statusColor);
        
        // Update button states based on loader status
        updateButtonStates(melonInstalled || lemonInstalled);
    }

    private void updateButtonStates(boolean loaderInstalled) {
        Button dllModManagerBtn = findViewById(R.id.dllModManagerButton);
        
        if (loaderInstalled) {
            dllModManagerBtn.setText("🔧 Manage DLL Mods");
            dllModManagerBtn.setBackgroundColor(Color.parseColor("#4CAF50"));
        } else {
            dllModManagerBtn.setText("🔧 Setup DLL Support");
            dllModManagerBtn.setBackgroundColor(Color.parseColor("#FF9800"));
        }
    }

    @Override
    protected void onResume() {
        super.onResume();
        // Refresh loader status when returning to this activity
        updateLoaderStatus();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        LogUtils.logUser("Terraria Menu Activity closed");
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/UniversalActivity.java
package com.terrarialoader;

import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;

import androidx.activity.result.ActivityResultLauncher;
import androidx.activity.result.contract.ActivityResultContracts;
import androidx.appcompat.app.AppCompatActivity;

import com.terrarialoader.util.LogUtils;

public class UniversalActivity extends AppCompatActivity {

    private Uri apkUri;
    private Uri zipUri;

    private TextView statusText;

    private final ActivityResultLauncher<Intent> apkPickerLauncher =
            registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), result -> {
                if (result.getResultCode() == RESULT_OK && result.getData() != null) {
                    apkUri = result.getData().getData();
                    statusText.setText("Universal APK selected:\n" + apkUri.getPath());
                    LogUtils.logUser("Universal - Selected APK: " + apkUri);
                }
            });

    private final ActivityResultLauncher<Intent> zipPickerLauncher =
            registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), result -> {
                if (result.getResultCode() == RESULT_OK && result.getData() != null) {
                    zipUri = result.getData().getData();
                    statusText.setText("Loader ZIP selected:\n" + zipUri.getPath());
                    LogUtils.logUser("Universal - Selected ZIP: " + zipUri);
                }
            });

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_universal);

        LogUtils.logDebug("UniversalActivity launched");

        Button selectApkBtn = findViewById(R.id.select_apk_button);
        Button selectZipBtn = findViewById(R.id.select_zip_button);
        Button injectBtn = findViewById(R.id.inject_button);
        statusText = findViewById(R.id.status_text);

        selectApkBtn.setOnClickListener(v -> openApkPicker());
        selectZipBtn.setOnClickListener(v -> openZipPicker());

        injectBtn.setOnClickListener(v -> {
            if (apkUri == null || zipUri == null) {
                Toast.makeText(this, "Please select both APK and ZIP", Toast.LENGTH_SHORT).show();
                return;
            }
            injectLoader();
        });
    }

    private void openApkPicker() {
        Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
        intent.setType("application/vnd.android.package-archive");
        apkPickerLauncher.launch(Intent.createChooser(intent, "Select APK"));
    }

    private void openZipPicker() {
        Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
        intent.setType("application/zip");
        zipPickerLauncher.launch(Intent.createChooser(intent, "Select Loader ZIP"));
    }

    private void injectLoader() {
        LogUtils.logUser("Universal - Injecting loader...");
        statusText.setText("Injecting loader into APK...\n(This is a placeholder operation)");

        Toast.makeText(this, "Injection (mocked)", Toast.LENGTH_SHORT).show();
        LogUtils.logUser("Universal - Injection completed (simulated)");
    }

    private void exportModifiedApk() {
        Toast.makeText(this, "Exporting modified APK (not implemented)", Toast.LENGTH_SHORT).show();
    }

    private void exportLogs() {
        Toast.makeText(this, "Exporting logs (not implemented)", Toast.LENGTH_SHORT).show();
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.main_menu, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();

        if (id == R.id.action_log) {
            startActivity(new Intent(this, LogActivity.class));
            return true;
        } else if (id == R.id.action_about) {
            startActivity(new Intent(this, AboutActivity.class));
            return true;
        } else if (id == R.id.action_dark_mode) {
            Toast.makeText(this, "Toggle dark mode (not implemented)", Toast.LENGTH_SHORT).show();
            return true;
        } else if (id == R.id.action_export_apk) {
            exportModifiedApk();
            return true;
        } else if (id == R.id.action_export_logs) {
            exportLogs();
            return true;
        }

        return super.onOptionsItemSelected(item);
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/Diagnostic/DiagnosticManager.java
package com.terrarialoader.diagnostic;

import android.content.Context;
import android.os.Environment;
import com.terrarialoader.util.LogUtils;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.*;

public class DiagnosticManager {
    private final Context context;
    private final File gameBaseDir;
    private final List<String> reportLines = new ArrayList<>();
    private final Map<String, Boolean> checks = new LinkedHashMap<>();
    private final String gamePackage = "com.and.games505.TerrariaPaid";

    public DiagnosticManager(Context context) {
        this.context = context;
        this.gameBaseDir = new File(context.getExternalFilesDir(null), "TerrariaLoader/" + gamePackage);
    }

    public String runFullDiagnostics() {
        log("🔧 Starting Offline Diagnostics...");
        reportLines.clear();
        checks.clear();

        checkDirectoryStructure();
        checkLoaderPresence();
        checkFileIntegrity();
        checkPermissions();
        checkLogRotation();
        checkReadmes();

        log("📊 Diagnostics completed.");
        generateReport();
        return getFormattedReport();
    }

    public boolean attemptSelfRepair() {
        log("🛠️ Starting repair process...");
        boolean success = true;

        for (String path : getExpectedDirectories()) {
            File dir = new File(path);
            if (!dir.exists()) {
                if (dir.mkdirs()) {
                    log("✅ Created: " + dir.getAbsolutePath());
                } else {
                    log("❌ Failed to create: " + dir.getAbsolutePath());
                    success = false;
                }
            }
        }

        createReadmes();
        rotateLogsIfNeeded();

        log(success ? "✅ Repair completed successfully." : "⚠️ Repair completed with issues.");
        return success;
    }

    private void checkDirectoryStructure() {
        log("📂 Checking directory structure...");
        for (String path : getExpectedDirectories()) {
            File f = new File(path);
            boolean exists = f.exists() && f.isDirectory();
            checks.put("Directory: " + f.getName(), exists);
        }
    }

    private void checkLoaderPresence() {
        log("🛠️ Checking loader status...");
        File melonDir = new File(gameBaseDir, "Loaders/MelonLoader");
        checks.put("MelonLoader Installed", melonDir.exists());

        File net8 = new File(melonDir, "net8");
        File net35 = new File(melonDir, "net35");
        File deps = new File(melonDir, "Dependencies");
        checks.put("ML/net8 Present", net8.exists());
        checks.put("ML/net35 Present", net35.exists());
        checks.put("ML/Dependencies Present", deps.exists());
    }

    private void checkFileIntegrity() {
        log("🔍 Checking file integrity...");
        File logsDir = new File(gameBaseDir, "Logs");
        File log = new File(logsDir, "Log.txt");
        checks.put("Log.txt Exists", log.exists());

        File[] oldLogs = logsDir.listFiles((dir, name) -> name.matches("Log[1-5]\\.txt"));
        checks.put("Old Logs Present (1-5)", oldLogs != null && oldLogs.length > 0);
    }

    private void checkPermissions() {
        log("🔐 Checking permissions...");
        File test = new File(context.getExternalFilesDir(null), "perm_test.txt");
        try {
            FileWriter writer = new FileWriter(test);
            writer.write("test");
            writer.close();
            checks.put("Write Permission", true);
            test.delete();
        } catch (IOException e) {
            checks.put("Write Permission", false);
        }
    }

    private void checkLogRotation() {
        log("🕒 Checking log rotation...");
        File logsDir = new File(gameBaseDir, "AppLogs");
        File[] logFiles = logsDir.listFiles((dir, name) -> name.startsWith("AppLog"));
        checks.put("AppLogs Present", logFiles != null && logFiles.length > 0);
    }

    private void checkReadmes() {
        log("📘 Checking README files...");
        File[] dirs = new File[]{
            new File(gameBaseDir, "Mods/DEX"),
            new File(gameBaseDir, "Mods/DLL"),
            new File(gameBaseDir, "Plugins"),
            new File(gameBaseDir, "UserLibs"),
            new File(gameBaseDir, "Logs"),
            new File(gameBaseDir, "AppLogs")
        };

        for (File dir : dirs) {
            File readme = new File(dir, "README.txt");
            checks.put(dir.getName() + "/README Present", readme.exists());
        }
    }

    private void rotateLogsIfNeeded() {
        File logsDir = new File(gameBaseDir, "AppLogs");
        File currentLog = new File(logsDir, "AppLog.txt");
        if (currentLog.exists()) {
            for (int i = 5; i >= 1; i--) {
                File prev = new File(logsDir, "AppLog" + i + ".txt");
                if (prev.exists() && i == 5) prev.delete();
                else if (prev.exists())
                    prev.renameTo(new File(logsDir, "AppLog" + (i + 1) + ".txt"));
            }
            currentLog.renameTo(new File(logsDir, "AppLog1.txt"));
        }
    }

    private void createReadmes() {
        String[] dirs = {
            "Mods/DEX", "Mods/DLL", "Plugins", "UserLibs", "Logs", "AppLogs"
        };

        for (String relPath : dirs) {
            File dir = new File(gameBaseDir, relPath);
            File readme = new File(dir, "README.txt");
            if (!readme.exists()) {
                try {
                    FileWriter writer = new FileWriter(readme);
                    writer.write("README for " + relPath + "\nThis directory is used by TerrariaLoader.");
                    writer.close();
                    log("📘 Created README in: " + relPath);
                } catch (IOException e) {
                    log("❌ Failed to write README in: " + relPath);
                }
            }
        }
    }

    private void generateReport() {
        reportLines.add("=== TerrariaLoader Offline Diagnostic Report ===");
        reportLines.add("Timestamp: " + new SimpleDateFormat("yyyy-MM-dd HH:mm:ss", Locale.getDefault()).format(new Date()));
        reportLines.add("Base Directory: " + gameBaseDir.getAbsolutePath());
        reportLines.add("\nChecks:");

        for (Map.Entry<String, Boolean> entry : checks.entrySet()) {
            String icon = entry.getValue() ? "✅" : "❌";
            reportLines.add(icon + " " + entry.getKey());
        }

        reportLines.add("\nSuggestions:");
        for (Map.Entry<String, Boolean> entry : checks.entrySet()) {
            if (!entry.getValue()) {
                reportLines.add("• Fix: " + entry.getKey());
            }
        }
    }

    public boolean exportReportToFile(File file) {
        try (FileWriter writer = new FileWriter(file)) {
            for (String line : reportLines) {
                writer.write(line + "\n");
            }
            log("✅ Report exported: " + file.getAbsolutePath());
            return true;
        } catch (IOException e) {
            log("❌ Failed to export report: " + e.getMessage());
            return false;
        }
    }

    public String getFormattedReport() {
        StringBuilder sb = new StringBuilder();
        for (String line : reportLines) {
            sb.append(line).append("\n");
        }
        return sb.toString();
    }

    private void log(String message) {
        LogUtils.logDebug("[DiagnosticManager] " + message);
    }

    private String[] getExpectedDirectories() {
        String base = gameBaseDir.getAbsolutePath();
        return new String[]{
            base,
            base + "/Mods", base + "/Mods/DEX", base + "/Mods/DLL",
            base + "/Config", base + "/Logs", base + "/AppLogs", base + "/Backups",
            base + "/Loaders/MelonLoader", base + "/Loaders/MelonLoader/net8",
            base + "/Loaders/MelonLoader/net35", base + "/Loaders/MelonLoader/Dependencies",
            base + "/Plugins", base + "/UserLibs"
        };
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/installer/ModInstaller.java
// File: ModInstaller.java (FIXED) - Updated to use new directory structure
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/installer/ModInstaller.java

package com.terrarialoader.installer;

import android.content.Context;
import android.net.Uri;
import com.terrarialoader.util.FileUtils;
import com.terrarialoader.util.LogUtils;
import com.terrarialoader.util.PathManager;
import com.terrarialoader.loader.ModManager;
import java.io.File;

public class ModInstaller {
    // Enhanced file extension whitelist
    private static final String[] ALLOWED_EXTENSIONS = {
        ".dex", ".jar", ".dll", ".dex.disabled", ".jar.disabled", ".dll.disabled"
    };
    
    // Maximum file size (50MB)
    private static final long MAX_MOD_SIZE = 50 * 1024 * 1024;

    public static boolean installMod(Context context, Uri sourceUri, String filename) {
        LogUtils.logUser("Starting mod installation: " + filename);
        
        // Enhanced input validation
        if (context == null) {
            LogUtils.logUser("❌ Installation failed: context is null");
            return false;
        }
        
        if (sourceUri == null) {
            LogUtils.logUser("❌ Installation failed: source URI is null");
            return false;
        }
        
        if (filename == null || filename.trim().isEmpty()) {
            LogUtils.logUser("❌ Installation failed: filename is invalid");
            return false;
        }

        // Sanitize filename
        filename = sanitizeFilename(filename);
        
        // Check file extension
        if (!isValidModFile(filename)) {
            LogUtils.logUser("❌ Invalid mod file type: " + filename);
            LogUtils.logDebug("Allowed extensions: " + String.join(", ", ALLOWED_EXTENSIONS));
            return false;
        }

        // FIXED: Determine mod directory based on file type using new structure
        File modDir = getModDirectoryByType(context, filename);
        if (modDir == null) {
            LogUtils.logUser("❌ Cannot determine mod directory for file type");
            return false;
        }

        // Ensure mod directory exists
        if (!PathManager.ensureDirectoryExists(modDir)) {
            LogUtils.logUser("❌ Cannot create mod directory: " + modDir.getAbsolutePath());
            return false;
        }

        // Check for existing mod with same name
        File outputFile = new File(modDir, filename);
        if (outputFile.exists()) {
            LogUtils.logUser("⚠️ Mod already exists: " + filename);
            // Create backup of existing mod
            File backupFile = new File(modDir, filename + ".backup." + System.currentTimeMillis());
            if (outputFile.renameTo(backupFile)) {
                LogUtils.logUser("📦 Created backup: " + backupFile.getName());
            }
        }

        // Validate file size before copying
        try {
            long fileSize = context.getContentResolver().openInputStream(sourceUri).available();
            if (fileSize > MAX_MOD_SIZE) {
                LogUtils.logUser("❌ Mod file too large: " + formatFileSize(fileSize) + " (max: " + formatFileSize(MAX_MOD_SIZE) + ")");
                return false;
            }
            LogUtils.logDebug("Mod file size: " + formatFileSize(fileSize));
        } catch (Exception e) {
            LogUtils.logDebug("Could not determine file size: " + e.getMessage());
        }

        // Install the mod
        boolean success = FileUtils.copyUriToFile(context, sourceUri, outputFile);
        
        if (success) {
            // Validate the copied file
            if (validateInstalledMod(outputFile)) {
                LogUtils.logUser("✅ Successfully installed: " + filename);
                LogUtils.logUser("📁 Location: " + outputFile.getAbsolutePath());
                
                // Auto-enable mod if it was installed with .disabled extension and user wants it enabled
                if (filename.endsWith(".disabled")) {
                    LogUtils.logUser("ℹ️ Mod installed as disabled. Enable it in mod manager to use.");
                } else {
                    LogUtils.logUser("ℹ️ Mod installed and ready to use.");
                }
                
                // Refresh mod list
                ModManager.loadMods(context);
                
                return true;
            } else {
                LogUtils.logUser("❌ Installed mod file validation failed");
                outputFile.delete(); // Clean up invalid file
                return false;
            }
        } else {
            LogUtils.logUser("❌ Failed to install: " + filename);
            return false;
        }
    }

    /**
     * FIXED: Get appropriate mod directory based on file type using new structure
     */
    private static File getModDirectoryByType(Context context, String filename) {
        String gamePackage = "com.and.games505.TerrariaPaid"; // Default to Terraria
        String lowerFilename = filename.toLowerCase();
        
        if (lowerFilename.endsWith(".dll") || lowerFilename.endsWith(".dll.disabled")) {
            // DLL mods go to DLL directory
            return PathManager.getDllModsDir(context, gamePackage);
        } else if (lowerFilename.endsWith(".dex") || lowerFilename.endsWith(".dex.disabled") ||
                   lowerFilename.endsWith(".jar") || lowerFilename.endsWith(".jar.disabled")) {
            // DEX/JAR mods go to DEX directory
            return PathManager.getDexModsDir(context, gamePackage);
        }
        
        return null; // Unknown file type
    }

    // Enhanced mod installation with auto-detection
    public static boolean installModAuto(Context context, Uri sourceUri) {
        try {
            // Try to get filename from URI
            String filename = FileUtils.getFilenameFromUri(context, sourceUri);
            if (filename == null) {
                filename = "mod_" + System.currentTimeMillis() + ".dex";
                LogUtils.logDebug("Auto-generated filename: " + filename);
            }
            
            return installMod(context, sourceUri, filename);
            
        } catch (Exception e) {
            LogUtils.logUser("❌ Auto-installation failed: " + e.getMessage());
            LogUtils.logDebug("Auto-install error: " + e.toString());
            return false;
        }
    }

    // Batch mod installation
    public static int installMultipleMods(Context context, Uri[] sourceUris, String[] filenames) {
        if (sourceUris == null || filenames == null || sourceUris.length != filenames.length) {
            LogUtils.logUser("❌ Batch install failed: invalid parameters");
            return 0;
        }
        
        int successCount = 0;
        LogUtils.logUser("Starting batch installation of " + sourceUris.length + " mods");
        
        for (int i = 0; i < sourceUris.length; i++) {
            if (installMod(context, sourceUris[i], filenames[i])) {
                successCount++;
            }
        }
        
        LogUtils.logUser("Batch installation complete: " + successCount + "/" + sourceUris.length + " mods installed");
        return successCount;
    }

    private static boolean isValidModFile(String filename) {
        String lowerFilename = filename.toLowerCase();
        for (String ext : ALLOWED_EXTENSIONS) {
            if (lowerFilename.endsWith(ext)) {
                return true;
            }
        }
        return false;
    }

    private static String sanitizeFilename(String filename) {
        // Remove dangerous characters and limit length
        filename = filename.replaceAll("[^a-zA-Z0-9._-]", "_");
        if (filename.length() > 100) {
            filename = filename.substring(0, 100);
        }
        return filename;
    }

    private static boolean validateInstalledMod(File modFile) {
        if (!modFile.exists()) {
            LogUtils.logDebug("Validation failed: file does not exist");
            return false;
        }
        
        if (modFile.length() == 0) {
            LogUtils.logDebug("Validation failed: file is empty");
            return false;
        }
        
        if (modFile.length() > MAX_MOD_SIZE) {
            LogUtils.logDebug("Validation failed: file too large");
            return false;
        }
        
        // Basic file header validation for .dex files
        if (modFile.getName().toLowerCase().endsWith(".dex")) {
            try {
                byte[] header = new byte[8];
                java.io.FileInputStream fis = new java.io.FileInputStream(modFile);
                int bytesRead = fis.read(header);
                fis.close();
                
                if (bytesRead >= 8) {
                    // Check DEX magic number
                    String magic = new String(header, 0, 4);
                    if (!"dex\n".equals(magic)) {
                        LogUtils.logDebug("Validation failed: invalid DEX magic number");
                        return false;
                    }
                }
            } catch (Exception e) {
                LogUtils.logDebug("Validation warning: could not read file header: " + e.getMessage());
                // Don't fail validation just because we can't read header
            }
        }
        
        LogUtils.logDebug("Mod validation passed: " + modFile.getName());
        return true;
    }

    private static String formatFileSize(long bytes) {
        if (bytes < 1024) return bytes + " B";
        if (bytes < 1024 * 1024) return String.format("%.1f KB", bytes / 1024.0);
        if (bytes < 1024 * 1024 * 1024) return String.format("%.1f MB", bytes / (1024.0 * 1024.0));
        return String.format("%.1f GB", bytes / (1024.0 * 1024.0 * 1024.0));
    }

    // FIXED: Uninstall mod from correct directory
    public static boolean uninstallMod(Context context, String filename) {
        String gamePackage = "com.and.games505.TerrariaPaid";
        
        // Try to find the mod in appropriate directory
        File modFile = findModFile(context, gamePackage, filename);
        
        if (modFile == null || !modFile.exists()) {
            LogUtils.logUser("❌ Mod not found: " + filename);
            return false;
        }
        
        if (modFile.delete()) {
            LogUtils.logUser("🗑️ Uninstalled mod: " + filename);
            LogUtils.logUser("📁 From: " + modFile.getParent());
            ModManager.loadMods(context); // Refresh mod list
            return true;
        } else {
            LogUtils.logUser("❌ Failed to uninstall: " + filename);
            return false;
        }
    }

    /**
     * FIXED: Find mod file in appropriate directory based on file type
     */
    private static File findModFile(Context context, String gamePackage, String filename) {
        String lowerFilename = filename.toLowerCase();
        
        if (lowerFilename.endsWith(".dll") || lowerFilename.endsWith(".dll.disabled")) {
            File dllDir = PathManager.getDllModsDir(context, gamePackage);
            File modFile = new File(dllDir, filename);
            if (modFile.exists()) {
                return modFile;
            }
        }
        
        if (lowerFilename.endsWith(".dex") || lowerFilename.endsWith(".dex.disabled") ||
            lowerFilename.endsWith(".jar") || lowerFilename.endsWith(".jar.disabled")) {
            File dexDir = PathManager.getDexModsDir(context, gamePackage);
            File modFile = new File(dexDir, filename);
            if (modFile.exists()) {
                return modFile;
            }
        }
        
        // Fallback: check legacy directory for backward compatibility
        File legacyDir = PathManager.getLegacyModsDir(context);
        File modFile = new File(legacyDir, filename);
        if (modFile.exists()) {
            LogUtils.logDebug("Found mod in legacy directory: " + filename);
            return modFile;
        }
        
        return null;
    }

    // Get installation status
    public static String[] getSupportedExtensions() {
        return ALLOWED_EXTENSIONS.clone();
    }

    public static long getMaxModSize() {
        return MAX_MOD_SIZE;
    }

    /**
     * FIXED: Check if migration is needed from legacy structure
     */
    public static boolean needsMigration(Context context) {
        File legacyModsDir = PathManager.getLegacyModsDir(context);
        if (!legacyModsDir.exists()) {
            return false;
        }
        
        File[] legacyMods = legacyModsDir.listFiles((dir, name) -> {
            String lowerName = name.toLowerCase();
            for (String ext : ALLOWED_EXTENSIONS) {
                if (lowerName.endsWith(ext)) {
                    return true;
                }
            }
            return false;
        });
        
        return legacyMods != null && legacyMods.length > 0;
    }

    /**
     * FIXED: Migrate legacy mods to new directory structure
     */
    public static boolean migrateLegacyMods(Context context) {
        if (!needsMigration(context)) {
            LogUtils.logDebug("No legacy mods to migrate");
            return true;
        }
        
        LogUtils.logUser("🔄 Migrating legacy mods to new structure...");
        
        String gamePackage = "com.and.games505.TerrariaPaid";
        File legacyModsDir = PathManager.getLegacyModsDir(context);
        
        // Ensure new directories exist
        File dexModsDir = PathManager.getDexModsDir(context, gamePackage);
        File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
        
        if (!PathManager.ensureDirectoryExists(dexModsDir) || 
            !PathManager.ensureDirectoryExists(dllModsDir)) {
            LogUtils.logUser("❌ Failed to create new mod directories");
            return false;
        }
        
        File[] legacyMods = legacyModsDir.listFiles((dir, name) -> {
            String lowerName = name.toLowerCase();
            for (String ext : ALLOWED_EXTENSIONS) {
                if (lowerName.endsWith(ext)) {
                    return true;
                }
            }
            return false;
        });
        
        if (legacyMods == null || legacyMods.length == 0) {
            LogUtils.logDebug("No legacy mod files found");
            return true;
        }
        
        int migratedCount = 0;
        int dexCount = 0;
        int dllCount = 0;
        
        for (File legacyMod : legacyMods) {
            String filename = legacyMod.getName();
            String lowerFilename = filename.toLowerCase();
            
            File targetDir;
            if (lowerFilename.endsWith(".dll") || lowerFilename.endsWith(".dll.disabled")) {
                targetDir = dllModsDir;
                dllCount++;
            } else {
                targetDir = dexModsDir;
                dexCount++;
            }
            
            File newLocation = new File(targetDir, filename);
            if (legacyMod.renameTo(newLocation)) {
                migratedCount++;
                LogUtils.logDebug("Migrated: " + filename + " -> " + targetDir.getName());
            } else {
                LogUtils.logDebug("Failed to migrate: " + filename);
            }
        }
        
        LogUtils.logUser("✅ Migration completed:");
        LogUtils.logUser("📊 Total migrated: " + migratedCount + "/" + legacyMods.length);
        LogUtils.logUser("📊 DEX/JAR mods: " + dexCount);
        LogUtils.logUser("📊 DLL mods: " + dllCount);
        
        // Remove legacy directory if it's empty
        File[] remainingFiles = legacyModsDir.listFiles();
        if (remainingFiles == null || remainingFiles.length == 0) {
            if (legacyModsDir.delete()) {
                LogUtils.logDebug("Removed empty legacy mods directory");
            }
        }
        
        return migratedCount > 0;
    }

    /**
     * Get mod installation statistics
     */
    public static String getInstallationStats(Context context) {
        String gamePackage = "com.and.games505.TerrariaPaid";
        
        File dexModsDir = PathManager.getDexModsDir(context, gamePackage);
        File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
        
        int dexCount = 0, dllCount = 0;
        int dexEnabled = 0, dllEnabled = 0;
        
        // Count DEX mods
        if (dexModsDir.exists()) {
            File[] dexMods = dexModsDir.listFiles((dir, name) -> {
                String lowerName = name.toLowerCase();
                return lowerName.endsWith(".dex") || lowerName.endsWith(".jar") ||
                       lowerName.endsWith(".dex.disabled") || lowerName.endsWith(".jar.disabled");
            });
            
            if (dexMods != null) {
                dexCount = dexMods.length;
                for (File mod : dexMods) {
                    if (!mod.getName().endsWith(".disabled")) {
                        dexEnabled++;
                    }
                }
            }
        }
        
        // Count DLL mods
        if (dllModsDir.exists()) {
            File[] dllMods = dllModsDir.listFiles((dir, name) -> {
                String lowerName = name.toLowerCase();
                return lowerName.endsWith(".dll") || lowerName.endsWith(".dll.disabled");
            });
            
            if (dllMods != null) {
                dllCount = dllMods.length;
                for (File mod : dllMods) {
                    if (!mod.getName().endsWith(".disabled")) {
                        dllEnabled++;
                    }
                }
            }
        }
        
        StringBuilder stats = new StringBuilder();
        stats.append("=== Mod Installation Statistics ===\n");
        stats.append("DEX/JAR Mods: ").append(dexEnabled).append("/").append(dexCount).append(" enabled\n");
        stats.append("DLL Mods: ").append(dllEnabled).append("/").append(dllCount).append(" enabled\n");
        stats.append("Total Mods: ").append(dexEnabled + dllEnabled).append("/").append(dexCount + dllCount).append(" enabled\n");
        stats.append("\nDirectories:\n");
        stats.append("DEX: ").append(dexModsDir.getAbsolutePath()).append("\n");
        stats.append("DLL: ").append(dllModsDir.getAbsolutePath()).append("\n");
        
        return stats.toString();
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/loader/LoaderFileManager.java
// File: LoaderFileManager.java (Fixed Component) - Complete Path Management Fix
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/loader/LoaderFileManager.java

package com.terrarialoader.loader;

import android.content.Context;
import com.terrarialoader.util.LogUtils;
import com.terrarialoader.util.FileUtils;
import com.terrarialoader.util.PathManager;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.util.ArrayList;
import java.util.List;

public class LoaderFileManager {
    
    // Install a DLL mod into standardized structure
    public boolean installDllMod(Context context, File dllFile, String gamePackage) {
        if (!isMelonLoaderInstalled(context, gamePackage)) {
            LogUtils.logUser("❌ No MelonLoader installed for: " + gamePackage);
            return false;
        }
        
        try {
            File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
            PathManager.ensureDirectoryExists(dllModsDir);
            
            File targetFile = new File(dllModsDir, dllFile.getName());
            
            if (copyFile(dllFile, targetFile)) {
                LogUtils.logUser("✅ DLL mod installed: " + dllFile.getName());
                LogUtils.logUser("📁 Location: " + targetFile.getAbsolutePath());
                return true;
            } else {
                LogUtils.logUser("❌ Failed to install DLL mod: " + dllFile.getName());
                return false;
            }
            
        } catch (Exception e) {
            LogUtils.logDebug("DLL mod installation failed: " + e.getMessage());
            return false;
        }
    }

    // Get list of installed DLL mods for a game
    public File[] getInstalledDllMods(Context context, String gamePackage) {
        File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
        
        if (!dllModsDir.exists()) {
            return new File[0];
        }
        
        File[] dllFiles = dllModsDir.listFiles((dir, name) -> 
            name.toLowerCase().endsWith(".dll") && !name.toLowerCase().endsWith(".disabled"));
        
        return dllFiles != null ? dllFiles : new File[0];
    }
    
    // Get list of installed DEX mods for a game
    public File[] getInstalledDexMods(Context context, String gamePackage) {
        File dexModsDir = PathManager.getDexModsDir(context, gamePackage);
        
        if (!dexModsDir.exists()) {
            // Check legacy location for backward compatibility
            File legacyModsDir = PathManager.getLegacyModsDir(context);
            if (legacyModsDir.exists()) {
                LogUtils.logDebug("Using legacy mods directory for DEX mods");
                File[] legacyFiles = legacyModsDir.listFiles((dir, name) -> {
                    String lowerName = name.toLowerCase();
                    return (lowerName.endsWith(".dex") || lowerName.endsWith(".jar")) && 
                           !lowerName.endsWith(".disabled");
                });
                return legacyFiles != null ? legacyFiles : new File[0];
            }
            return new File[0];
        }
        
        File[] dexFiles = dexModsDir.listFiles((dir, name) -> {
            String lowerName = name.toLowerCase();
            return (lowerName.endsWith(".dex") || lowerName.endsWith(".jar")) && 
                   !lowerName.endsWith(".disabled");
        });
        
        return dexFiles != null ? dexFiles : new File[0];
    }

    // Enable/disable DLL mod
    public boolean toggleDllMod(Context context, String gamePackage, String modName, boolean enable) {
        File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
        
        if (enable) {
            // Enable: Remove .disabled extension
            File disabledFile = new File(dllModsDir, modName + ".disabled");
            File enabledFile = new File(dllModsDir, modName);
            
            if (disabledFile.exists()) {
                return disabledFile.renameTo(enabledFile);
            }
        } else {
            // Disable: Add .disabled extension
            File enabledFile = new File(dllModsDir, modName);
            File disabledFile = new File(dllModsDir, modName + ".disabled");
            
            if (enabledFile.exists()) {
                return enabledFile.renameTo(disabledFile);
            }
        }
        
        return false;
    }

    // Delete DLL  mod
    public boolean deleteDllMod(Context context, String gamePackage, String modName) {
        File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
        File modFile = new File(dllModsDir, modName);
        File disabledFile = new File(dllModsDir, modName + ".disabled");
        
        boolean deleted = false;
        
        if (modFile.exists()) {
            deleted = modFile.delete();
        }
        
        if (disabledFile.exists()) {
            deleted = disabledFile.delete() || deleted;
        }
        
        if (deleted) {
            LogUtils.logUser("🗑️ Deleted DLL mod: " + modName);
        }
        
        return deleted;
    }

    // Get all available mods (both DLL and DEX) with legacy support
    public List<File> getAllMods(Context context, String gamePackage) {
        List<File> allMods = new ArrayList<>();
        
        // Add DLL mods
        File[] dllMods = getInstalledDllMods(context, gamePackage);
        for (File mod : dllMods) {
            allMods.add(mod);
        }
        
        // Add disabled DLL mods
        File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
        if (dllModsDir.exists()) {
            File[] disabledDllMods = dllModsDir.listFiles((dir, name) -> 
                name.toLowerCase().endsWith(".dll.disabled"));
            if (disabledDllMods != null) {
                for (File mod : disabledDllMods) {
                    allMods.add(mod);
                }
            }
        }
        
        // Add DEX mods (with legacy support)
        File[] dexMods = getInstalledDexMods(context, gamePackage);
        for (File mod : dexMods) {
            allMods.add(mod);
        }
        
        // Add disabled DEX mods
        File dexModsDir = PathManager.getDexModsDir(context, gamePackage);
        if (dexModsDir.exists()) {
            File[] disabledDexMods = dexModsDir.listFiles((dir, name) -> {
                String lowerName = name.toLowerCase();
                return lowerName.endsWith(".dex.disabled") || lowerName.endsWith(".jar.disabled");
            });
            if (disabledDexMods != null) {
                for (File mod : disabledDexMods) {
                    allMods.add(mod);
                }
            }
        } else {
            // Check legacy location for disabled DEX mods
            File legacyModsDir = PathManager.getLegacyModsDir(context);
            if (legacyModsDir.exists()) {
                File[] disabledDexMods = legacyModsDir.listFiles((dir, name) -> {
                    String lowerName = name.toLowerCase();
                    return lowerName.endsWith(".dex.disabled") || lowerName.endsWith(".jar.disabled");
                });
                if (disabledDexMods != null) {
                    for (File mod : disabledDexMods) {
                        allMods.add(mod);
                    }
                }
            }
        }
        
        return allMods;
    }

    // Check if mod is enabled
    public boolean isModEnabled(File modFile) {
        return !modFile.getName().toLowerCase().endsWith(".disabled");
    }

    // Get mod type from file
    public String getModType(File modFile) {
        String fileName = modFile.getName().toLowerCase();
        if (fileName.endsWith(".dll") || fileName.endsWith(".dll.disabled")) {
            return "DLL";
        } else if (fileName.endsWith(".dex") || fileName.endsWith(".dex.disabled")) {
            return "DEX";
        } else if (fileName.endsWith(".jar") || fileName.endsWith(".jar.disabled")) {
            return "JAR";
        }
        return "Unknown";
    }

    // Toggle mod enabled/disabled state
    public boolean toggleMod(Context context, String gamePackage, File modFile) {
        boolean isEnabled = isModEnabled(modFile);
        String modType = getModType(modFile);
        
        if ("DLL".equals(modType)) {
            return toggleDllMod(context, gamePackage, modFile.getName(), !isEnabled);
        } else if ("DEX".equals(modType) || "JAR".equals(modType)) {
            // Handle DEX/JAR toggling through FileUtils
            return FileUtils.toggleModFile(modFile);
        }
        
        return false;
    }

    // Get formatted mod info
    public String getModInfo(File modFile) {
        StringBuilder info = new StringBuilder();
        info.append("Name: ").append(modFile.getName()).append("\n");
        info.append("Type: ").append(getModType(modFile)).append("\n");
        info.append("Size: ").append(FileUtils.formatFileSize(modFile.length())).append("\n");
        info.append("Status: ").append(isModEnabled(modFile) ? "Enabled" : "Disabled").append("\n");
        info.append("Path: ").append(modFile.getAbsolutePath()).append("\n");
        
        if (modFile.exists()) {
            info.append("Last Modified: ").append(new java.util.Date(modFile.lastModified()).toString()).append("\n");
        }
        
        return info.toString();
    }

    // Backup existing mods before operations
    public boolean backupMods(Context context, String gamePackage) {
        try {
            File modsDir = PathManager.getGameBaseDir(context, gamePackage);
            if (modsDir == null || !modsDir.exists()) {
                LogUtils.logDebug("No game directory to backup for: " + gamePackage);
                return true;
            }
            
            File backupDir = new File(PathManager.getBackupsDir(context, gamePackage), "mods_" + System.currentTimeMillis());
            PathManager.ensureDirectoryExists(backupDir.getParentFile());
            
            return copyDirectory(modsDir, backupDir);
            
        } catch (Exception e) {
            LogUtils.logDebug("Mod backup failed: " + e.getMessage());
            return false;
        }
    }

    // Helper method to copy directory
    private boolean copyDirectory(File source, File target) {
        try {
            if (source.isDirectory()) {
                if (!target.exists()) {
                    target.mkdirs();
                }
                
                File[] files = source.listFiles();
                if (files != null) {
                    for (File file : files) {
                        File targetFile = new File(target, file.getName());
                        if (!copyDirectory(file, targetFile)) {
                            return false;
                        }
                    }
                }
            } else {
                return copyFile(source, target);
            }
            return true;
        } catch (Exception e) {
            LogUtils.logDebug("Directory copy failed: " + e.getMessage());
            return false;
        }
    }

    // Helper method to copy files
    private boolean copyFile(File source, File target) {
        try (FileInputStream in = new FileInputStream(source);
             FileOutputStream out = new FileOutputStream(target)) {
            
            byte[] buffer = new byte[8192];
            int bytesRead;
            while ((bytesRead = in.read(buffer)) != -1) {
                out.write(buffer, 0, bytesRead);
            }
            return true;
            
        } catch (Exception e) {
            LogUtils.logDebug("File copy failed: " + e.getMessage());
            return false;
        }
    }

    // Calculate directory size
    private long calculateDirectorySize(File dir) {
        long size = 0;
        if (dir != null && dir.isDirectory()) {
            File[] files = dir.listFiles();
            if (files != null) {
                for (File file : files) {
                    if (file.isDirectory()) {
                        size += calculateDirectorySize(file);
                    } else {
                        size += file.length();
                    }
                }
            }
        } else if (dir != null && dir.isFile()) {
            size = dir.length();
        }
        return size;
    }

    // Count files recursively
    private int countFiles(File dir) {
        int count = 0;
        if (dir != null && dir.isDirectory()) {
            File[] files = dir.listFiles();
            if (files != null) {
                for (File file : files) {
                    if (file.isDirectory()) {
                        count += countFiles(file);
                    } else {
                        count++;
                    }
                }
            }
        } else if (dir != null && dir.isFile()) {
            count = 1;
        }
        return count;
    }

    // Get file statistics for a game package
    public FileStatistics getFileStatistics(Context context, String gamePackage) {
        FileStatistics stats = new FileStatistics();
        stats.gamePackage = gamePackage;
        
        File baseDir = PathManager.getGameBaseDir(context, gamePackage);
        if (baseDir != null && baseDir.exists()) {
            stats.totalFiles = countFiles(baseDir);
            stats.totalSize = calculateDirectorySize(baseDir);
            
            // Count mods specifically
            File[] dllMods = getInstalledDllMods(context, gamePackage);
            File[] dexMods = getInstalledDexMods(context, gamePackage);
            List<File> allMods = getAllMods(context, gamePackage);
            
            stats.dllModCount = dllMods != null ? dllMods.length : 0;
            stats.dexModCount = dexMods != null ? dexMods.length : 0;
            stats.totalModCount = allMods != null ? allMods.size() : 0;
            
            // Count enabled vs disabled
            if (allMods != null) {
                for (File mod : allMods) {
                    if (isModEnabled(mod)) {
                        stats.enabledModCount++;
                    } else {
                        stats.disabledModCount++;
                    }
                }
            }
        }
        
        return stats;
    }

    // Clean up old backup files
    public void cleanupOldBackups(Context context, String gamePackage, int maxBackups) {
        try {
            File backupDir = PathManager.getBackupsDir(context, gamePackage);
            if (!backupDir.exists()) {
                return;
            }
            
            File[] backupFiles = backupDir.listFiles((dir, name) -> name.startsWith("mods_"));
            if (backupFiles == null || backupFiles.length <= maxBackups) {
                return;
            }
            
            // Sort by timestamp (oldest first)
            java.util.Arrays.sort(backupFiles, (a, b) -> Long.compare(a.lastModified(), b.lastModified()));
            
            // Delete oldest backups
            int toDelete = backupFiles.length - maxBackups;
            for (int i = 0; i < toDelete; i++) {
                deleteDirectory(backupFiles[i]);
                LogUtils.logDebug("Cleaned up old backup: " + backupFiles[i].getName());
            }
            
        } catch (Exception e) {
            LogUtils.logDebug("Backup cleanup failed: " + e.getMessage());
        }
    }

    // Helper method to delete directory recursively
    private boolean deleteDirectory(File dir) {
        if (dir == null) return false;
        
        if (dir.isDirectory()) {
            File[] files = dir.listFiles();
            if (files != null) {
                for (File file : files) {
                    deleteDirectory(file);
                }
            }
        }
        return dir.delete();
    }

    // Check if MelonLoader is installed (uses PathManager)
    private boolean isMelonLoaderInstalled(Context context, String gamePackage) {
        File loaderDir = PathManager.getMelonLoaderDir(context, gamePackage);
        File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
        return loaderDir != null && loaderDir.exists() && dllModsDir != null && dllModsDir.exists();
    }

    // File Statistics helper class
    public static class FileStatistics {
        public String gamePackage;
        public int totalFiles = 0;
        public long totalSize = 0;
        public int dllModCount = 0;
        public int dexModCount = 0;
        public int totalModCount = 0;
        public int enabledModCount = 0;
        public int disabledModCount = 0;
        
        @Override
        public String toString() {
            return String.format("FileStats[%s]: %d files, %s, %d mods (%d enabled, %d disabled)", 
                               gamePackage != null ? gamePackage : "null", 
                               totalFiles,
                               FileUtils.formatFileSize(totalSize),
                               totalModCount, 
                               enabledModCount, 
                               disabledModCount);
        }
        
        public String getDetailedInfo() {
            StringBuilder info = new StringBuilder();
            info.append("=== File Statistics for ").append(gamePackage != null ? gamePackage : "Unknown").append(" ===\n");
            info.append("Total Files: ").append(totalFiles).append("\n");
            info.append("Total Size: ").append(FileUtils.formatFileSize(totalSize)).append("\n");
            info.append("DLL Mods: ").append(dllModCount).append("\n");
            info.append("DEX Mods: ").append(dexModCount).append("\n");
            info.append("Total Mods: ").append(totalModCount).append("\n");
            info.append("Enabled Mods: ").append(enabledModCount).append("\n");
            info.append("Disabled Mods: ").append(disabledModCount).append("\n");
            return info.toString();
        }
    }

    // Export mod list to text file
    public boolean exportModList(Context context, String gamePackage, File outputFile) {
        try {
            List<File> allMods = getAllMods(context, gamePackage);
            
            try (java.io.FileWriter writer = new java.io.FileWriter(outputFile)) {
                writer.write("=== TerrariaLoader Mod List ===\n");
                writer.write("Game Package: " + gamePackage + "\n");
                writer.write("Export Date: " + new java.util.Date().toString() + "\n");
                writer.write("Total Mods: " + (allMods != null ? allMods.size() : 0) + "\n\n");
                
                if (allMods != null) {
                    for (File mod : allMods) {
                        writer.write("Mod: " + mod.getName() + "\n");
                        writer.write("  Type: " + getModType(mod) + "\n");
                        writer.write("  Status: " + (isModEnabled(mod) ? "Enabled" : "Disabled") + "\n");
                        writer.write("  Size: " + FileUtils.formatFileSize(mod.length()) + "\n");
                        writer.write("  Path: " + mod.getAbsolutePath() + "\n\n");
                    }
                }
            }
            
            LogUtils.logUser("✅ Mod list exported to: " + outputFile.getName());
            return true;
            
        } catch (Exception e) {
            LogUtils.logDebug("Mod list export failed: " + e.getMessage());
            return false;
        }
    }

    // Validate mod directory structure
    public boolean validateModDirectories(Context context, String gamePackage) {
        try {
            File baseDir = PathManager.getGameBaseDir(context, gamePackage);
            File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
            File dexModsDir = PathManager.getDexModsDir(context, gamePackage);
            
            boolean valid = true;
            
            if (baseDir == null) {
                LogUtils.logDebug("Base directory is null for: " + gamePackage);
                return false;
            }
            
            if (!baseDir.exists()) {
                LogUtils.logDebug("Creating base directory: " + baseDir.getAbsolutePath());
                if (!PathManager.ensureDirectoryExists(baseDir)) {
                    valid = false;
                }
            }
            
            if (dllModsDir == null || !dllModsDir.exists()) {
                LogUtils.logDebug("Creating DLL mods directory");
                if (dllModsDir != null && !PathManager.ensureDirectoryExists(dllModsDir)) {
                    valid = false;
                }
            }
            
            if (dexModsDir == null || !dexModsDir.exists()) {
                LogUtils.logDebug("Creating DEX mods directory");
                if (dexModsDir != null && !PathManager.ensureDirectoryExists(dexModsDir)) {
                    valid = false;
                }
            }
            
            return valid;
            
        } catch (Exception e) {
            LogUtils.logDebug("Directory validation failed: " + e.getMessage());
            return false;
        }
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/loader/LoaderInstaller.java
// File: LoaderInstaller.java (Fixed Component) - Uses Correct App-Specific Paths
// Path: /storage/emulated/0/AndroidIDEProjects/main/java/com/terrarialoader/loader/LoaderInstaller.java

package com.terrarialoader.loader;

import android.content.Context;
import com.terrarialoader.util.LogUtils;
import com.terrarialoader.util.FileUtils;
import com.terrarialoader.util.PathManager;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.zip.ZipEntry;
import com.terrarialoader.util.ApkPatcher;
import java.util.zip.ZipInputStream;

public class LoaderInstaller {
    public static final String TERRARIA_PACKAGE = "com.and.games505.TerrariaPaid";
    
    // FIXED: Complete directory structure using PathManager (app-specific paths)
    private static final String[] LOADER_DIRECTORIES = {
        // Core Runtime Directories  
        "Loaders/MelonLoader/net8",                              // .NET 8 runtime files
        "Loaders/MelonLoader/net35",                             // .NET 3.5 runtime files (compatibility)
        
        // Dependencies Structure (from MelonLoader_File_List.txt)
        "Loaders/MelonLoader/Dependencies",
        "Loaders/MelonLoader/Dependencies/SupportModules",       // Core support DLLs
        "Loaders/MelonLoader/Dependencies/CompatibilityLayers",  // Compatibility DLLs
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator",      // Assembly generation tools
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/Cpp2IL",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/Cpp2IL/cpp2il_out",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/Il2CppInterop",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/Il2CppInterop/Il2CppAssemblies",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/UnityDependencies",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/runtimes",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/runtimes/linux-arm64/native",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/runtimes/linux-arm/native",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/runtimes/linux-x64/native",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/runtimes/linux-x86/native",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/runtimes/osx-arm64/native",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/runtimes/osx-x64/native",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/runtimes/win-arm64/native",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/runtimes/win-x64/native",
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator/runtimes/win-x86/native",
        
        // User Directories
        "Mods/DLL",                                              // DLL mods go here
        "Mods/DEX",                                              // DEX/JAR mods go here
        "Loaders/MelonLoader/UserLibs",                          // User libraries
        "Loaders/MelonLoader/Plugins",                           // Plugin files
        "Logs",                                                  // All logs (both MelonLoader and TerrariaLoader)
        
        // Backup and Config
        "Backups",                                               // APK backups
        "Config"                                                 // Configuration files
    };

    // FIXED: Create complete directory structure using app-specific paths
    public boolean createLoaderStructure(Context context, String gamePackage, MelonLoaderManager.LoaderType loaderType) {
        LogUtils.logUser("Creating " + loaderType.getDisplayName() + " structure for: " + gamePackage);
        
        try {
            // FIXED: Use app-specific directory via PathManager
            File baseDir = PathManager.getGameBaseDir(context, gamePackage);
            LogUtils.logDebug("Using base directory: " + baseDir.getAbsolutePath());
            
            // Create all required directories
            for (String dirPath : LOADER_DIRECTORIES) {
                File dir = new File(baseDir, dirPath);
                if (!dir.exists() && !dir.mkdirs()) {
                    LogUtils.logDebug("Failed to create directory: " + dir.getAbsolutePath());
                    return false;
                }
            }
            
            // Create informational files
            createInfoFiles(baseDir, loaderType);
            
            LogUtils.logUser("✅ " + loaderType.getDisplayName() + " structure created successfully");
            LogUtils.logUser("📁 Base path: " + baseDir.getAbsolutePath());
            
            return true;
            
        } catch (Exception e) {
            LogUtils.logDebug("Failed to create loader structure: " + e.getMessage());
            return false;
        }
    }
    
    // Create helpful info files
    private void createInfoFiles(File baseDir, MelonLoaderManager.LoaderType loaderType) throws IOException {
        // DLL Mods directory README
        File dllModsReadme = new File(baseDir, "Mods/DLL/README.txt");
        try (java.io.FileWriter writer = new java.io.FileWriter(dllModsReadme)) {
            writer.write("=== TerrariaLoader - DLL Mods Directory ===\n\n");
            writer.write("Place your .dll mod files here.\n");
            writer.write("Mods will be loaded automatically when Terraria starts.\n\n");
            writer.write("Supported formats:\n");
            writer.write("• .dll files implementing MelonMod (enabled)\n");
            writer.write("• .dll.disabled files (disabled mods)\n\n");
            writer.write("Loader Type: " + loaderType.getDisplayName() + "\n");
            writer.write("Unity Version: " + MelonLoaderManager.TERRARIA_UNITY_VERSION + "\n");
            writer.write("Game Version: " + MelonLoaderManager.TERRARIA_GAME_VERSION + "\n\n");
            writer.write("Examples of DLL mods:\n");
            writer.write("• MyTerrariaMod.dll (enabled)\n");
            writer.write("• SomeOtherMod.dll.disabled (disabled)\n");
        }
        
        // DEX Mods directory README
        File dexModsReadme = new File(baseDir, "Mods/DEX/README.txt");
        try (java.io.FileWriter writer = new java.io.FileWriter(dexModsReadme)) {
            writer.write("=== TerrariaLoader - DEX/JAR Mods Directory ===\n\n");
            writer.write("Place your .dex and .jar mod files here.\n");
            writer.write("These are Java-based mods for Android.\n\n");
            writer.write("Supported formats:\n");
            writer.write("• .dex files (enabled)\n");
            writer.write("• .jar files (enabled)\n");
            writer.write("• .dex.disabled files (disabled)\n");
            writer.write("• .jar.disabled files (disabled)\n\n");
            writer.write("Examples of DEX mods:\n");
            writer.write("• MyJavaMod.dex (enabled)\n");
            writer.write("• SomeJarMod.jar (enabled)\n");
            writer.write("• DisabledMod.dex.disabled (disabled)\n");
        }
        
        // Config directory info
        File configReadme = new File(baseDir, "Config/README.txt");
        try (java.io.FileWriter writer = new java.io.FileWriter(configReadme)) {
            writer.write("=== TerrariaLoader Configuration Directory ===\n\n");
            writer.write("This directory contains configuration files for TerrariaLoader and MelonLoader.\n");
            writer.write("Mod-specific config files will appear here automatically.\n\n");
            writer.write("File types you might see:\n");
            writer.write("• .cfg files (MelonLoader configs)\n");
            writer.write("• .json files (TerrariaLoader configs)\n");
            writer.write("• .txt files (Various settings)\n");
        }
    }

    // FIXED: Install from LemonLoader installer APK using correct paths
    public boolean installFromLemonLoaderApk(Context context, File installerApk, MelonLoaderManager.LoaderType loaderType) {
        LogUtils.logUser("Installing " + loaderType.getDisplayName() + " from LemonLoader installer APK...");
        
        try {
            // First create the directory structure using PathManager
            if (!createLoaderStructure(context, TERRARIA_PACKAGE, loaderType)) {
                return false;
            }
            
            // Extract files from LemonLoader installer APK
            return extractAndInstallFiles(context, installerApk, loaderType);
            
        } catch (Exception e) {
            LogUtils.logDebug(loaderType.getDisplayName() + " installation from APK failed: " + e.getMessage());
            return false;
        }
    }
    
    // FIXED: Extract files from LemonLoader installer APK and place them correctly using PathManager
    private boolean extractAndInstallFiles(Context context, File installerApk, MelonLoaderManager.LoaderType loaderType) {
        try {
            // FIXED: Use PathManager for correct app-specific paths
            File baseDir = PathManager.getGameBaseDir(context, TERRARIA_PACKAGE);
            File loaderDir = PathManager.getMelonLoaderDir(context, TERRARIA_PACKAGE);
            int installedCount = 0;
            
            LogUtils.logDebug("Extracting to: " + loaderDir.getAbsolutePath());
            
            try (ZipInputStream zis = new ZipInputStream(new FileInputStream(installerApk))) {
                ZipEntry entry;
                while ((entry = zis.getNextEntry()) != null) {
                    if (entry.isDirectory()) continue;
                    
                    String entryName = entry.getName();
                    String targetPath = getTargetPath(entryName, loaderType);
                    
                    if (targetPath != null) {
                        File outputFile = new File(loaderDir, targetPath);
                        outputFile.getParentFile().mkdirs();
                        
                        try (FileOutputStream fos = new FileOutputStream(outputFile)) {
                            byte[] buffer = new byte[8192];
                            int bytesRead;
                            while ((bytesRead = zis.read(buffer)) != -1) {
                                fos.write(buffer, 0, bytesRead);
                            }
                        }
                        
                        installedCount++;
                        LogUtils.logDebug("Installed: " + targetPath);
                    }
                }
            }
            
            LogUtils.logUser("✅ Installed " + installedCount + " " + loaderType.getDisplayName() + " files");
            return installedCount > 0;
            
        } catch (Exception e) {
            LogUtils.logDebug("File extraction failed: " + e.getMessage());
            return false;
        }
    }
    
    // Enhanced file path mapping based on MelonLoader_File_List.txt
    private String getTargetPath(String entryName, MelonLoaderManager.LoaderType loaderType) {
        // Remove common prefixes
        entryName = entryName.replaceFirst("^(assets/|lib/|)", "");
        
        // Handle DLL files
        if (entryName.endsWith(".dll")) {
            String fileName = new File(entryName).getName();
            
            // Core loader files - map to appropriate runtime
            if (isCoreDll(fileName)) {
                if (loaderType == MelonLoaderManager.LoaderType.MELONLOADER_NET8) {
                    return "net8/" + fileName;
                } else {
                    return "net35/" + fileName;
                }
            }
            
            // Support modules
            if (isSupportModule(fileName)) {
                return "Dependencies/SupportModules/" + fileName;
            }
            
            // Assembly generator files
            if (isAssemblyGeneratorFile(fileName)) {
                return "Dependencies/Il2CppAssemblyGenerator/" + fileName;
            }
            
            // Unity dependencies
            if (isUnityDependency(fileName)) {
                return "Dependencies/Il2CppAssemblyGenerator/UnityDependencies/" + fileName;
            }
            
            // Compatibility layers
            if (isCompatibilityLayer(fileName)) {
                return "Dependencies/CompatibilityLayers/" + fileName;
            }
        }
        
        // Handle other file types
        if (entryName.endsWith(".json") || entryName.endsWith(".runtimeconfig.json")) {
            String fileName = new File(entryName).getName();
            if (loaderType == MelonLoaderManager.LoaderType.MELONLOADER_NET8) {
                return "net8/" + fileName;
            } else {
                return "net35/" + fileName;
            }
        }
        
        // Handle native libraries
        if (entryName.contains("libcapstone.so") || entryName.contains("capstone.dll")) {
            if (entryName.contains("arm64")) {
                return "Dependencies/Il2CppAssemblyGenerator/runtimes/linux-arm64/native/" + new File(entryName).getName();
            } else if (entryName.contains("arm")) {
                return "Dependencies/Il2CppAssemblyGenerator/runtimes/linux-arm/native/" + new File(entryName).getName();
            }
        }
        
        // Configuration files
        if (entryName.endsWith(".cfg")) {
            return "../../Config/" + new File(entryName).getName();
        }
        
        return null; // Skip this file
    }
    
    // Helper methods for file classification based on MelonLoader_File_List.txt
    private boolean isCoreDll(String fileName) {
        return fileName.equals("MelonLoader.dll") ||
               fileName.equals("0Harmony.dll") ||
               fileName.startsWith("MonoMod.") ||
               fileName.equals("Il2CppInterop.Runtime.dll");
    }
    
    private boolean isSupportModule(String fileName) {
        return fileName.startsWith("Il2CppInterop.") ||
               fileName.equals("Il2Cpp.dll") ||
               fileName.equals("Preload.dll") ||
               fileName.equals("Mono.dll");
    }
    
    private boolean isAssemblyGeneratorFile(String fileName) {
        return fileName.contains("Il2CppAssemblyGenerator") ||
               fileName.contains("Cpp2IL") ||
               fileName.contains("LibCpp2IL") ||
               fileName.equals("AsmResolver.dll") ||
               fileName.equals("Disarm.dll") ||
               fileName.equals("Iced.dll");
    }
    
    private boolean isUnityDependency(String fileName) {
        return fileName.startsWith("UnityEngine.");
    }
    
    private boolean isCompatibilityLayer(String fileName) {
        return fileName.equals("Demeo.dll") ||
               fileName.equals("EOS.dll") ||
               fileName.equals("IPA.dll") ||
               fileName.equals("Muse_Dash_Mono.dll") ||
               fileName.equals("Stress_Level_Zero_Il2Cpp.dll");
    }

    // FIXED: Install MelonLoader into APK using REAL injection
    public boolean installMelonLoader(Context context, File inputApk, File outputApk) {
        LogUtils.logUser("Installing MelonLoader into APK...");
        
        try {
            // First ensure MelonLoader files exist
            if (!createLoaderStructure(context, TERRARIA_PACKAGE, MelonLoaderManager.LoaderType.MELONLOADER_NET8)) {
                LogUtils.logUser("❌ Failed to create loader structure");
                return false;
            }
            
            // Check if MelonLoader files are actually installed
            if (!MelonLoaderManager.isMelonLoaderInstalled(context, TERRARIA_PACKAGE)) {
                LogUtils.logUser("❌ MelonLoader files not found!");
                LogUtils.logUser("💡 Use 'Automated Installation' first to download MelonLoader files");
                return false;
            }
            
            // FIXED: Use real APK injection instead of just copying
            boolean success = ApkPatcher.injectMelonLoaderIntoApk(context, inputApk, outputApk, 
                MelonLoaderManager.LoaderType.MELONLOADER_NET8);
            
            if (success) {
                LogUtils.logUser("✅ MelonLoader successfully injected into APK");
                return true;
            } else {
                LogUtils.logUser("❌ MelonLoader injection failed");
                return false;
            }
            
        } catch (Exception e) {
            LogUtils.logDebug("MelonLoader installation failed: " + e.getMessage());
            LogUtils.logUser("❌ MelonLoader installation failed: " + e.getMessage());
            return false;
        }
    }

    // FIXED: Install LemonLoader into APK using REAL injection
    public boolean installLemonLoader(Context context, File inputApk, File outputApk) {
        LogUtils.logUser("Installing LemonLoader into APK...");
        
        try {
            // First ensure LemonLoader files exist
            if (!createLoaderStructure(context, TERRARIA_PACKAGE, MelonLoaderManager.LoaderType.MELONLOADER_NET35)) {
                LogUtils.logUser("❌ Failed to create loader structure");
                return false;
            }
            
            // Check if LemonLoader files are actually installed
            if (!MelonLoaderManager.isLemonLoaderInstalled(context, TERRARIA_PACKAGE)) {
                LogUtils.logUser("❌ LemonLoader files not found!");
                LogUtils.logUser("💡 Use 'Automated Installation' first to download LemonLoader files");
                return false;
            }
            
            // FIXED: Use real APK injection instead of just copying
            boolean success = ApkPatcher.injectMelonLoaderIntoApk(context, inputApk, outputApk, 
                MelonLoaderManager.LoaderType.MELONLOADER_NET35);
            
            if (success) {
                LogUtils.logUser("✅ LemonLoader successfully injected into APK");
                return true;
            } else {
                LogUtils.logUser("❌ LemonLoader injection failed");
                return false;
            }
            
        } catch (Exception e) {
            LogUtils.logDebug("LemonLoader installation failed: " + e.getMessage());
            LogUtils.logUser("❌ LemonLoader installation failed: " + e.getMessage());
            return false;
        }
    }

    // Helper method to copy files
    private boolean copyFile(File source, File target) {
        try (java.io.FileInputStream in = new java.io.FileInputStream(source);
             java.io.FileOutputStream out = new java.io.FileOutputStream(target)) {
            
            byte[] buffer = new byte[8192];
            int bytesRead;
            while ((bytesRead = in.read(buffer)) != -1) {
                out.write(buffer, 0, bytesRead);
            }
            return true;
            
        } catch (Exception e) {
            LogUtils.logDebug("File copy failed: " + e.getMessage());
            return false;
        }
    }

    // Helper method to delete directory recursively
    private boolean deleteDirectory(File dir) {
        if (dir.isDirectory()) {
            File[] files = dir.listFiles();
            if (files != null) {
                for (File file : files) {
                    deleteDirectory(file);
                }
            }
        }
        return dir.delete();
    }
    
    // FIXED: Clean up method for removing loader installation using correct paths
    public boolean uninstallLoader(String gamePackage) {
        // FIXED: This should use Context to get proper paths, but kept for compatibility
        LogUtils.logDebug("Uninstall called without context - this method is deprecated");
        return false;
    }
    
    // NEW: Proper uninstall method with context
    public boolean uninstallLoader(Context context, String gamePackage) {
        File loaderDir = PathManager.getGameBaseDir(context, gamePackage);
        
        if (!loaderDir.exists()) {
            LogUtils.logUser("No loader installation found for: " + gamePackage);
            return true;
        }
        
        try {
            boolean success = deleteDirectory(loaderDir);
            if (success) {
                LogUtils.logUser("✅ Loader uninstalled successfully");
            } else {
                LogUtils.logUser("❌ Failed to uninstall loader");
            }
            return success;
        } catch (Exception e) {
            LogUtils.logDebug("Loader uninstallation error: " + e.getMessage());
            return false;
        }
    }
}

/storage/emulated/0/AndroidIDEProjects/TerrariaMLmain/java/com/terrarialoader/loader/LoaderValidator.java
// File: LoaderValidator.java (Fixed Component) - Complete Path Management Fix
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/loader/LoaderValidator.java

package com.terrarialoader.loader;

import android.content.Context;
import com.terrarialoader.util.LogUtils;
import com.terrarialoader.util.PathManager;
import java.io.File;
import java.io.FileInputStream;

public class LoaderValidator {
    private static final String MELONLOADER_VERSION = "0.6.5";
    
    // Essential files mapping based on MelonLoader_File_List.txt
    private static final String[] MELONLOADER_NET8_FILES = {
        "net8/MelonLoader.dll",
        "net8/MelonLoader.deps.json",
        "net8/MelonLoader.runtimeconfig.json",
        "net8/MelonLoader.xml",
        "net8/MelonLoader.NativeHost.deps.json",
        "net8/MelonLoader.NativeHost.dll",
        "net8/0Harmony.dll",
        "net8/0Harmony.pdb",
        "net8/0Harmony.xml",
        "net8/Il2CppInterop.Runtime.dll",
        "net8/Il2CppInterop.Runtime.xml",
        "net8/MonoMod.RuntimeDetour.dll",
        "net8/MonoMod.RuntimeDetour.pdb",
        "net8/MonoMod.RuntimeDetour.xml",
        "net8/MonoMod.Utils.dll",
        "net8/MonoMod.Utils.pdb",
        "net8/MonoMod.Utils.xml"
    };
    
    private static final String[] MELONLOADER_NET35_FILES = {
        "net35/MelonLoader.dll",
        "net35/MelonLoader.xml",
        "net35/0Harmony.dll",
        "net35/0Harmony.pdb",
        "net35/0Harmony.xml",
        "net35/MonoMod.RuntimeDetour.dll",
        "net35/MonoMod.RuntimeDetour.pdb",
        "net35/MonoMod.RuntimeDetour.xml",
        "net35/MonoMod.Utils.dll",
        "net35/MonoMod.Utils.pdb",
        "net35/MonoMod.Utils.xml"
    };
    
    private static final String[] MELONLOADER_SUPPORT_FILES = {
        "Dependencies/SupportModules/Il2Cpp.dll",
        "Dependencies/SupportModules/Il2Cpp.deps.json",
        "Dependencies/SupportModules/Il2CppInterop.Runtime.dll",
        "Dependencies/SupportModules/Il2CppInterop.Runtime.xml",
        "Dependencies/SupportModules/Il2CppInterop.HarmonySupport.dll",
        "Dependencies/SupportModules/Il2CppInterop.HarmonySupport.xml",
        "Dependencies/Il2CppAssemblyGenerator/Il2CppAssemblyGenerator.dll",
        "Dependencies/Il2CppAssemblyGenerator/Il2CppAssemblyGenerator.deps.json"
    };

    // Enhanced detection with PathManager
    public boolean isMelonLoaderInstalled(Context context, String gamePackage) {
        if (context == null || gamePackage == null) {
            LogUtils.logDebug("Context or gamePackage is null");
            return false;
        }
        
        // Check if directories need to be initialized
        if (PathManager.needsMigration(context)) {
            LogUtils.logUser("Migrating from legacy directory structure...");
            PathManager.migrateLegacyStructure(context);
        }
        
        // Auto-initialize directories if they don't exist
        if (!PathManager.initializeGameDirectories(context, gamePackage)) {
            LogUtils.logDebug("Failed to initialize game directories for: " + gamePackage);
        }
        
        File loaderDir = PathManager.getMelonLoaderDir(context, gamePackage);
        File net8Dir = PathManager.getMelonLoaderNet8Dir(context, gamePackage);
        File net35Dir = PathManager.getMelonLoaderNet35Dir(context, gamePackage);
        File depsDir = PathManager.getMelonLoaderDependenciesDir(context, gamePackage);
        File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
        
        // Check for core files (either net8 or net35)
        boolean hasNet8 = checkCoreFiles(loaderDir, MELONLOADER_NET8_FILES);
        boolean hasNet35 = checkCoreFiles(loaderDir, MELONLOADER_NET35_FILES);
        boolean hasSupportFiles = checkCoreFiles(loaderDir, MELONLOADER_SUPPORT_FILES);
        
        boolean installed = loaderDir != null && loaderDir.exists() && 
                           depsDir != null && depsDir.exists() && 
                           dllModsDir != null && dllModsDir.exists() && 
                           (hasNet8 || hasNet35) && hasSupportFiles;
        
        if (installed) {
            LogUtils.logDebug("MelonLoader detected for: " + gamePackage + 
                             " (NET8: " + hasNet8 + ", NET35: " + hasNet35 + ")");
        } else {
            LogUtils.logDebug("MelonLoader not detected for: " + gamePackage);
            LogUtils.logDebug("  Loader dir exists: " + (loaderDir != null && loaderDir.exists()));
            LogUtils.logDebug("  Dependencies exist: " + (depsDir != null && depsDir.exists()));
            LogUtils.logDebug("  DLL mods dir exists: " + (dllModsDir != null && dllModsDir.exists()));
            LogUtils.logDebug("  Has NET8: " + hasNet8);
            LogUtils.logDebug("  Has NET35: " + hasNet35);
            LogUtils.logDebug("  Has support files: " + hasSupportFiles);
        }
        
        return installed;
    }
    
    // Helper method to check if core files exist
    private boolean checkCoreFiles(File baseDir, String[] filePaths) {
        if (baseDir == null || !baseDir.exists() || filePaths == null) {
            return false;
        }
        
        int foundFiles = 0;
        int requiredFiles = Math.max(1, filePaths.length / 2); // At least half the files should exist
        
        for (String filePath : filePaths) {
            File file = new File(baseDir, filePath);
            if (file.exists() && file.length() > 0) {
                foundFiles++;
            }
        }
        
        return foundFiles >= requiredFiles;
    }

    // Validate DLL mod file
    public boolean validateDllMod(File dllFile) {
        if (dllFile == null || !dllFile.exists() || !dllFile.getName().toLowerCase().endsWith(".dll")) {
            return false;
        }
        
        if (dllFile.length() == 0) {
            LogUtils.logDebug("DLL file is empty: " + dllFile.getName());
            return false;
        }
        
        // Basic PE header validation
        try (FileInputStream fis = new FileInputStream(dllFile)) {
            byte[] header = new byte[2];
            if (fis.read(header) == 2) {
                // Check for PE signature (MZ)
                return header[0] == 0x4D && header[1] == 0x5A;
            }
        } catch (Exception e) {
            LogUtils.logDebug("DLL validation error: " + e.getMessage());
        }
        
        return false;
    }

    // Check loader type availability
    public boolean isNet8Available(Context context, String gamePackage) {
        if (context == null || gamePackage == null) return false;
        
        File loaderDir = PathManager.getMelonLoaderDir(context, gamePackage);
        return checkCoreFiles(loaderDir, MELONLOADER_NET8_FILES);
    }

    public boolean isNet35Available(Context context, String gamePackage) {
        if (context == null || gamePackage == null) return false;
        
        File loaderDir = PathManager.getMelonLoaderDir(context, gamePackage);
        return checkCoreFiles(loaderDir, MELONLOADER_NET35_FILES);
    }

    // Get active loader type
    public MelonLoaderManager.LoaderType getActiveLoaderType(Context context, String gamePackage) {
        if (context == null || gamePackage == null) return null;
        
        if (isNet8Available(context, gamePackage)) {
            return MelonLoaderManager.LoaderType.MELONLOADER_NET8;
        } else if (isNet35Available(context, gamePackage)) {
            return MelonLoaderManager.LoaderType.MELONLOADER_NET35;
        }
        return null;
    }

    // Comprehensive validation of loader installation
    public ValidationResult validateLoaderInstallation(Context context, String gamePackage) {
        ValidationResult result = new ValidationResult();
        result.gamePackage = gamePackage;
        
        if (context == null || gamePackage == null) {
            result.isValid = false;
            result.issues.add("Context or game package is null");
            return result;
        }
        
        File baseDir = PathManager.getGameBaseDir(context, gamePackage);
        result.basePathExists = baseDir != null && baseDir.exists();
        
        if (!result.basePathExists) {
            result.isValid = false;
            result.issues.add("Base directory does not exist: " + (baseDir != null ? baseDir.getAbsolutePath() : "null"));
            
            // Try to create the directory structure
            LogUtils.logUser("Attempting to create missing directory structure...");
            if (PathManager.initializeGameDirectories(context, gamePackage)) {
                LogUtils.logUser("✅ Directory structure created successfully");
                result.basePathExists = true;
                // Re-check after creation
                baseDir = PathManager.getGameBaseDir(context, gamePackage);
            } else {
                LogUtils.logUser("❌ Failed to create directory structure");
                return result;
            }
        }
        
        File loaderDir = PathManager.getMelonLoaderDir(context, gamePackage);
        result.loaderDirExists = loaderDir != null && loaderDir.exists();
        
        if (!result.loaderDirExists && loaderDir != null) {
            result.issues.add("Loader directory does not exist: " + loaderDir.getAbsolutePath());
        }
        
        // Check runtime availability
