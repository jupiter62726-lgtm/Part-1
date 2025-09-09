
/ModLoader/build.gradle

// Root-level build.gradle (for Gradle 7.0+ and AGP 8.0+)

plugins {
    id 'com.android.application' version '8.0.0' apply false
    id 'com.android.library' version '8.0.0' apply false
    // No IDE or LogSender related plugins
}

tasks.register("clean", Delete) {
    delete rootProject.buildDir
}

/ModLoader/settings.gradle

pluginManagement {
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
    }
}

dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.PREFER_SETTINGS)
    repositories {
        google()
        mavenCentral()
    }
}

rootProject.name = "ModLoader"
include ':app'


/ModLoader/app/build.gradle

plugins {
    id 'com.android.application'
}

repositories {
    google()
    mavenCentral()
}

android {
    namespace 'com.modloader'
    compileSdk 34

    defaultConfig {
        applicationId "com.modloader"
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"

        vectorDrawables {
            useSupportLibrary true
        }
    }

    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
        debug {
            debuggable true
        }
    }

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }

    buildFeatures {
        viewBinding true
        dataBinding true
    }
}

dependencies {

    // AndroidX Core
    implementation 'androidx.swiperefreshlayout:swiperefreshlayout:1.1.0'
    implementation 'androidx.appcompat:appcompat:1.6.1'
    implementation 'com.google.android.material:material:1.9.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.1.4'
    implementation 'androidx.recyclerview:recyclerview:1.3.1'
    implementation 'androidx.cardview:cardview:1.0.0'

    // NEW: Shizuku Dependencies for Enhanced Permissions
    implementation 'dev.rikka.shizuku:api:13.1.5'
    implementation 'dev.rikka.shizuku:provider:13.1.5'

    // Testing
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.5'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.5.1'
}

/ModLoader/app/src/main/AndroidManifest.xml

<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <!-- ENHANCED PERMISSIONS - Fixed APK installation issues -->
    
    <!-- Storage permissions -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"
        android:maxSdkVersion="28" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.MANAGE_EXTERNAL_STORAGE"
        tools:ignore="ScopedStorage" />
    
    <!-- Network permissions -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    
    <!-- APK Installation permissions (FIXED) -->
    <uses-permission android:name="android.permission.REQUEST_INSTALL_PACKAGES" />
    <uses-permission android:name="android.permission.INSTALL_PACKAGES"
        tools:ignore="ProtectedPermissions" />
    
    <!-- Package management permissions -->
    <uses-permission android:name="android.permission.QUERY_ALL_PACKAGES"
        tools:ignore="QueryAllPackagesPermission" />
    
    <!-- Wake lock for long operations -->
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    
    <!-- Vibration for notifications -->
    <uses-permission android:name="android.permission.VIBRATE" />

    <application
        android:name=".MyApplication"
        android:allowBackup="true"
        android:fullBackupContent="@xml/backup_rules"
        android:label="Mod Loader"
        android:icon="@mipmap/ic_launcher"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.MaterialComponents.DayNight.DarkActionBar"
        android:requestLegacyExternalStorage="true"
        android:preserveLegacyExternalStorage="true"
        android:largeHeap="true">

        <!-- ENHANCED FILE PROVIDER - Fixed APK installation -->
        <provider
            android:name="androidx.core.content.FileProvider"
            android:authorities="${applicationId}.provider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_provider_paths" />
        </provider>

        <!-- Main launcher activity -->
        <activity 
            android:name=".MainActivity" 
            android:exported="true"
            android:launchMode="singleTop">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <!-- Core activities -->
        <activity android:name=".TerrariaSpecificActivity" 
            android:exported="false"
            android:parentActivityName=".SpecificSelectionActivity"/>
        <activity android:name=".AboutActivity" 
            android:exported="false"
            android:parentActivityName=".MainActivity"/>
        <activity android:name=".UniversalActivity" 
            android:exported="false"
            android:parentActivityName=".MainActivity"/>
        <activity android:name=".SpecificSelectionActivity" 
            android:exported="false"
            android:parentActivityName=".MainActivity"/>

        <!-- Diagnostic and utility activities -->
        <activity android:name=".ui.OfflineDiagnosticActivity" 
            android:exported="false"
            android:parentActivityName=".TerrariaSpecificActivity"/>

        <!-- NEW: Unified Loader Activity (replaces TerrariaActivity and DllModActivity) -->
        <activity android:name=".ui.UnifiedLoaderActivity" 
            android:exported="false"
            android:parentActivityName=".TerrariaSpecificActivity"
            android:windowSoftInputMode="adjustResize"/>

        <!-- Updated: Pure mod management only -->
        <activity android:name=".ui.ModManagementActivity" 
            android:exported="false"
            android:parentActivityName=".TerrariaSpecificActivity"/>
        <activity android:name=".ui.ModListActivity" 
            android:exported="false"
            android:parentActivityName=".TerrariaSpecificActivity"/>

        <!-- Updated: Setup guide with offline ZIP import -->
        <activity android:name=".ui.SetupGuideActivity" 
            android:exported="false"
            android:parentActivityName=".TerrariaSpecificActivity"/>

        <!-- Utility activities -->
        <activity android:name=".ui.SettingsActivity" 
            android:exported="false"
            android:parentActivityName=".TerrariaSpecificActivity"/>

        <!-- ENHANCED: Advanced Log Viewer -->
        <activity android:name=".ui.LogViewerActivity" 
            android:exported="false"
            android:parentActivityName=".TerrariaSpecificActivity"
            android:windowSoftInputMode="adjustResize"/>

        <activity android:name=".ui.InstructionsActivity" 
            android:exported="false"
            android:parentActivityName=".TerrariaSpecificActivity"/>

        <!-- Testing and development -->
        <activity android:name=".SandboxActivity" 
            android:exported="false"
            android:parentActivityName=".TerrariaSpecificActivity"/>

        <!-- NEW: APK Management Activity -->
        <activity android:name=".ui.ApkManagerActivity"
            android:exported="false"
            android:parentActivityName=".ui.UnifiedLoaderActivity"/>

        <!-- Intent filters for APK files (ENHANCED) -->
        <activity-alias
            android:name=".ApkHandler"
            android:targetActivity=".ui.ApkManagerActivity"
            android:exported="true">
            <intent-filter android:priority="50">
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="file" />
                <data android:mimeType="application/vnd.android.package-archive" />
            </intent-filter>
            <intent-filter android:priority="50">
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="content" />
                <data android:mimeType="application/vnd.android.package-archive" />
            </intent-filter>
        </activity-alias>

        <!-- Service for background operations -->
        <service
            android:name=".service.LoaderService"
            android:enabled="true"
            android:exported="false" />

        <!-- Broadcast receiver for package events -->
        <receiver
            android:name=".receiver.PackageReceiver"
            android:enabled="true"
            android:exported="false">
            <intent-filter android:priority="1000">
                <action android:name="android.intent.action.PACKAGE_ADDED" />
                <action android:name="android.intent.action.PACKAGE_REMOVED" />
                <action android:name="android.intent.action.PACKAGE_REPLACED" />
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

    </application>

</manifest>

/ModLoader/app/src/main/java/com/modloader/AboutActivity.java

package com.modloader;

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

/ModLoader/app/src/main/java/com/modloader/IntegrationManager.java

package com.modloader;

public class IntegrationManager {
}


/ModLoader/app/src/main/java/com/modloader/LogActivity.java

package com.modloader;

import android.content.Intent;
import android.os.Bundle;
import android.view.MenuItem;
import android.widget.ScrollView;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.content.FileProvider;

import com.modloader.util.LogUtils;

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

/ModLoader/app/src/main/java/com/modloader/MainActivity.java

// File: MainActivity.java (Activity Class) - Fixed Navigation Structure
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/MainActivity.java

package com.modloader;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;

import com.modloader.util.LogUtils;

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
        setTitle("Mod Loader - Main Menu");

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

/ModLoader/app/src/main/java/com/modloader/MyApplication.java

// File: MyApplication.java (FIXED) - Initialize Logs and Handle Migration
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/MyApplication.java

package com.modloader;

import android.app.Application;
import android.os.Environment;
import com.modloader.util.LogUtils;
import com.modloader.loader.MelonLoaderManager;
import com.modloader.installer.ModInstaller;
import java.io.File;

public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        
        // FIXED: Initialize LogUtils first
        LogUtils.initialize(getApplicationContext());
        
        // FIXED: Initialize app startup logging
        LogUtils.initializeAppStartup();
        
        // Auto-create ModLoader directory structure on first run
        initializeModLoaderStructure();
        
        // FIXED: Handle migration from legacy structure
        handleMigration();
    }
    
    private void initializeModLoaderStructure() {
        try {
            LogUtils.logUser("🚀 Initializing ModLoader directory structure...");
            
            // The primary and most reliable location for app files.
            File baseDir = getExternalFilesDir(null);
            if (baseDir == null) {
                LogUtils.logDebug("❌ External storage is not available or app-specific directory is null.");
                return;
            }

            // FIXED: Use new directory structure
            File terrariaLoaderDir = new File(baseDir, "ModLoader");
            File gameDir = new File(terrariaLoaderDir, "com.and.games505.TerrariaPaid");

            if (!gameDir.exists()) {
                if (gameDir.mkdirs()) {
                    LogUtils.logUser("✅ Created base ModLoader directory at: " + gameDir.getAbsolutePath());
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
                basePath + "/AppLogs",       // FIXED: App logs (ModLoader logs)
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
                LogUtils.logUser("✅ ModLoader directory structure created successfully");
                LogUtils.logUser("📁 Path: " + basePath);
                LogUtils.logUser("📊 Created " + createdCount + " directories");
                createInfoFiles(basePath);
            } else {
                LogUtils.logUser("📁 ModLoader directories already exist");
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
                    writer.write("=== ModLoader - DEX/JAR Mods ===\n\n");
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
                    writer.write("=== ModLoader - DLL Mods ===\n\n");
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
                    writer.write("=== ModLoader App Logs ===\n\n");
                    writer.write("This directory contains logs from ModLoader app.\n");
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
            File gameBaseDir = new File(getExternalFilesDir(null), "ModLoader/com.and.games505.TerrariaPaid");
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

/ModLoader/app/src/main/java/com/modloader/SandboxActivity.java

package com.modloader;

import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.widget.TextView;

import com.modloader.loader.ModManager;

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

/ModLoader/app/src/main/java/com/modloader/SpecificSelectionActivity.java

// File: SpecificSelectionActivity.java (Fixed Activity Class) - Fixed App Selection
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/SpecificSelectionActivity.java

package com.modloader;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

import com.modloader.util.LogUtils;

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

/ModLoader/app/src/main/java/com/modloader/TerrariaSpecificActivity.java

// File: TerrariaSpecificActivity.java (Updated) - Elegant UI with Dynamic Theming
// Path: /main/java/com/modloader/TerrariaSpecificActivity.java

package com.modloader;

import android.content.Intent;
import android.graphics.Color;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;
import androidx.cardview.widget.CardView;

import com.modloader.R;
import com.modloader.ui.UnifiedLoaderActivity;
import com.modloader.ui.ModListActivity;
import com.modloader.ui.SettingsActivity;
import com.modloader.ui.LogViewerActivity;
import com.modloader.ui.InstructionsActivity;
import com.modloader.SandboxActivity;
import com.modloader.loader.MelonLoaderManager;
import com.modloader.util.LogUtils;
import com.modloader.ui.OfflineDiagnosticActivity;


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
            Intent intent = new Intent(this, com.modloader.ui.SetupGuideActivity.class);
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
                Intent intent = new Intent(this, com.modloader.ui.ModManagementActivity.class);
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

/ModLoader/app/src/main/java/com/modloader/UniversalActivity.java

package com.modloader;

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

import com.modloader.util.LogUtils;

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

/ModLoader/app/src/main/java/com/modloader/Diagnostic/ApkProcessLogger.java

package com.modloader.Diagnostic;

public class ApkProcessLogger {
}


/ModLoader/app/src/main/java/com/modloader/Diagnostic/DiagnosticManager.java

package com.modloader.diagnostic;

import android.content.Context;
import android.os.Environment;
import com.modloader.util.LogUtils;

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
        this.gameBaseDir = new File(context.getExternalFilesDir(null), "ModLoader/" + gamePackage);
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
                    writer.write("README for " + relPath + "\nThis directory is used by ModLoader.");
                    writer.close();
                    log("📘 Created README in: " + relPath);
                } catch (IOException e) {
                    log("❌ Failed to write README in: " + relPath);
                }
            }
        }
    }

    private void generateReport() {
        reportLines.add("=== ModLoader Offline Diagnostic Report ===");
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

/ModLoader/app/src/main/java/com/modloader/installer/ModInstaller.java

// File: ModInstaller.java (FIXED) - Updated to use new directory structure
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/installer/ModInstaller.java

package com.modloader.installer;

import android.content.Context;
import android.net.Uri;
import com.modloader.util.FileUtils;
import com.modloader.util.LogUtils;
import com.modloader.util.PathManager;
import com.modloader.loader.ModManager;
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

/ModLoader/app/src/main/java/com/modloader/loader/LoaderFileManager.java

// File: LoaderFileManager.java (Fixed Component) - Complete Path Management Fix
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/loader/LoaderFileManager.java

package com.modloader.loader;

import android.content.Context;
import com.modloader.util.LogUtils;
import com.modloader.util.FileUtils;
import com.modloader.util.PathManager;
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

    // Delete DLL mod
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
                writer.write("=== ModLoader Mod List ===\n");
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

/ModLoader/app/src/main/java/com/modloader/loader/LoaderInstaller.java

// File: LoaderInstaller.java (Complete Fixed Version) - Uses Correct App-Specific Paths
// Path: /storage/emulated/0/AndroidIDEProjects/ModLoader/app/src/main/java/com/modloader/loader/LoaderInstaller.java

package com.modloader.loader;

import android.content.Context;
import com.modloader.util.LogUtils;
import com.modloader.util.FileUtils;
import com.modloader.util.PathManager;
import com.modloader.util.ApkPatcher;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.zip.ZipEntry;
import java.util.zip.ZipInputStream;

public class LoaderInstaller {
    public static final String TERRARIA_PACKAGE = "com.and.games505.TerrariaPaid";

    // FIXED: Complete directory structure using PathManager (app-specific paths)
    private static final String[] LOADER_DIRECTORIES = {
        // Core Runtime Directories
        "Loaders/MelonLoader/net8", // .NET 8 runtime files
        "Loaders/MelonLoader/net35", // .NET 3.5 runtime files (compatibility)
        // Dependencies Structure (from MelonLoader_File_List.txt)
        "Loaders/MelonLoader/Dependencies",
        "Loaders/MelonLoader/Dependencies/SupportModules", // Core support DLLs
        "Loaders/MelonLoader/Dependencies/CompatibilityLayers", // Compatibility DLLs
        "Loaders/MelonLoader/Dependencies/Il2CppAssemblyGenerator", // Assembly generation tools
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
        "Mods/DLL", // DLL mods go here
        "Mods/DEX", // DEX/JAR mods go here
        "Loaders/MelonLoader/UserLibs", // User libraries
        "Loaders/MelonLoader/Plugins", // Plugin files
        "Logs", // All logs (both MelonLoader and ModLoader)
        // Backup and Config
        "Backups", // APK backups
        "Config" // Configuration files
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
            writer.write("=== ModLoader - DLL Mods Directory ===\n\n");
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
            writer.write("=== ModLoader - DEX/JAR Mods Directory ===\n\n");
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
            writer.write("=== ModLoader Configuration Directory ===\n\n");
            writer.write("This directory contains configuration files for ModLoader and MelonLoader.\n");
            writer.write("Mod-specific config files will appear here automatically.\n\n");
            writer.write("File types you might see:\n");
            writer.write("• .cfg files (MelonLoader configs)\n");
            writer.write("• .json files (ModLoader configs)\n");
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

    // FIXED: Install MelonLoader into APK using ApkPatcher.PatchResult
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

            // FIXED: Use ApkPatcher's own PatchResult type
            ApkPatcher.PatchResult patchResult = ApkPatcher.injectMelonLoaderIntoApk(context, inputApk, outputApk,
                    MelonLoaderManager.LoaderType.MELONLOADER_NET8);
            boolean success = patchResult.success; // Access the success field directly
            
            if (success) {
                LogUtils.logUser("✅ MelonLoader successfully injected into APK");
                if (patchResult.outputPath != null) {
                    LogUtils.logUser("📁 Output: " + patchResult.outputPath);
                }
                return true;
            } else {
                String errorMsg = patchResult.errorMessage != null ? patchResult.errorMessage : "Unknown error";
                LogUtils.logUser("❌ MelonLoader injection failed: " + errorMsg);
                return false;
            }
        } catch (Exception e) {
            LogUtils.logDebug("MelonLoader installation failed: " + e.getMessage());
            LogUtils.logUser("❌ MelonLoader installation failed: " + e.getMessage());
            return false;
        }
    }

    // FIXED: Install LemonLoader into APK using ApkPatcher.PatchResult
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

            // FIXED: Use ApkPatcher's own PatchResult type
            ApkPatcher.PatchResult patchResult = ApkPatcher.injectMelonLoaderIntoApk(context, inputApk, outputApk,
                    MelonLoaderManager.LoaderType.MELONLOADER_NET35);
            boolean success = patchResult.success; // Access the success field directly
            
            if (success) {
                LogUtils.logUser("✅ LemonLoader successfully injected into APK");
                if (patchResult.outputPath != null) {
                    LogUtils.logUser("📁 Output: " + patchResult.outputPath);
                }
                return true;
            } else {
                String errorMsg = patchResult.errorMessage != null ? patchResult.errorMessage : "Unknown error";
                LogUtils.logUser("❌ LemonLoader injection failed: " + errorMsg);
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

/ModLoader/app/src/main/java/com/modloader/loader/LoaderValidator.java

// File: LoaderValidator.java (Fixed Component) - Complete Path Management Fix
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/loader/LoaderValidator.java

package com.modloader.loader;

import android.content.Context;
import com.modloader.util.LogUtils;
import com.modloader.util.PathManager;
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
        result.hasNet8 = isNet8Available(context, gamePackage);
        result.hasNet35 = isNet35Available(context, gamePackage);
        
        if (!result.hasNet8 && !result.hasNet35) {
            result.issues.add("No valid runtime found (neither NET8 nor NET35)");
        }
        
        // Check support files
        result.hasSupportFiles = checkCoreFiles(loaderDir, MELONLOADER_SUPPORT_FILES);
        if (!result.hasSupportFiles) {
            result.issues.add("Missing support files in Dependencies directory");
        }
        
        // Check mod directories
        File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
        File dexModsDir = PathManager.getDexModsDir(context, gamePackage);
        result.dllModsDirExists = dllModsDir != null && dllModsDir.exists();
        result.dexModsDirExists = dexModsDir != null && dexModsDir.exists();
        
        if (!result.dllModsDirExists) {
            result.issues.add("DLL mods directory does not exist");
        }
        if (!result.dexModsDirExists) {
            result.issues.add("DEX mods directory does not exist");
        }
        
        // Overall validation
        result.isValid = result.basePathExists && result.loaderDirExists && 
                        (result.hasNet8 || result.hasNet35) && result.hasSupportFiles &&
                        result.dllModsDirExists && result.dexModsDirExists;
        
        if (result.isValid) {
            result.activeLoaderType = getActiveLoaderType(context, gamePackage);
        }
        
        return result;
    }

    // Get detailed information about loader installation
    public String getValidationReport(Context context, String gamePackage) {
        ValidationResult result = validateLoaderInstallation(context, gamePackage);
        StringBuilder report = new StringBuilder();
        
        report.append("=== Loader Validation Report ===\n");
        report.append("Game Package: ").append(gamePackage != null ? gamePackage : "null").append("\n");
        report.append("Overall Status: ").append(result.isValid ? "✅ VALID" : "❌ INVALID").append("\n\n");
        
        report.append("Directory Structure:\n");
        report.append("- Base Path: ").append(result.basePathExists ? "✅" : "❌").append("\n");
        report.append("- Loader Dir: ").append(result.loaderDirExists ? "✅" : "❌").append("\n");
        report.append("- DLL Mods Dir: ").append(result.dllModsDirExists ? "✅" : "❌").append("\n");
        report.append("- DEX Mods Dir: ").append(result.dexModsDirExists ? "✅" : "❌").append("\n\n");
        
        report.append("Runtime Support:\n");
        report.append("- NET8 Runtime: ").append(result.hasNet8 ? "✅" : "❌").append("\n");
        report.append("- NET35 Runtime: ").append(result.hasNet35 ? "✅" : "❌").append("\n");
        report.append("- Support Files: ").append(result.hasSupportFiles ? "✅" : "❌").append("\n\n");
        
        if (result.activeLoaderType != null) {
            report.append("Active Loader: ").append(result.activeLoaderType.getDisplayName()).append("\n\n");
        }
        
        if (!result.issues.isEmpty()) {
            report.append("Issues Found:\n");
            for (String issue : result.issues) {
                report.append("- ").append(issue).append("\n");
            }
        }
        
        // Add path information
        if (context != null && gamePackage != null) {
            report.append("\n").append(PathManager.getPathInfo(context, gamePackage));
        }
        
        return report.toString();
    }

    // Get installed loader version
    public String getInstalledLoaderVersion() {
        return MELONLOADER_VERSION;
    }

    // Helper class for validation results
    public static class ValidationResult {
        public String gamePackage;
        public boolean isValid = false;
        public boolean basePathExists = false;
        public boolean loaderDirExists = false;
        public boolean dllModsDirExists = false;
        public boolean dexModsDirExists = false;
        public boolean hasNet8 = false;
        public boolean hasNet35 = false;
        public boolean hasSupportFiles = false;
        public MelonLoaderManager.LoaderType activeLoaderType = null;
        public java.util.List<String> issues = new java.util.ArrayList<>();
        
        @Override
        public String toString() {
            return String.format("ValidationResult[%s]: %s (%d issues)", 
                               gamePackage != null ? gamePackage : "null", 
                               isValid ? "VALID" : "INVALID",
                               issues.size());
        }
    }

    // Quick validation methods for backward compatibility
    public boolean isLemonLoaderInstalled(Context context, String gamePackage) {
        return isMelonLoaderInstalled(context, gamePackage);
    }

    public boolean isLemonLoaderInstalled(Context context) {
        return isMelonLoaderInstalled(context, MelonLoaderManager.TERRARIA_PACKAGE);
    }

    // File integrity check
    public boolean checkFileIntegrity(File file) {
        if (file == null || !file.exists()) {
            return false;
        }
        
        if (file.length() == 0) {
            LogUtils.logDebug("File is empty: " + file.getName());
            return false;
        }
        
        // Check if file is readable
        try (FileInputStream fis = new FileInputStream(file)) {
            byte[] buffer = new byte[1024];
            int bytesRead = fis.read(buffer);
            return bytesRead > 0;
        } catch (Exception e) {
            LogUtils.logDebug("File integrity check failed: " + e.getMessage());
            return false;
        }
    }

    // Repair corrupted installation
    public boolean attemptRepair(Context context, String gamePackage) {
        LogUtils.logUser("Attempting to repair loader installation for: " + gamePackage);
        
        if (context == null || gamePackage == null) {
            LogUtils.logUser("❌ Cannot repair: context or gamePackage is null");
            return false;
        }
        
        ValidationResult result = validateLoaderInstallation(context, gamePackage);
        
        if (result.isValid) {
            LogUtils.logUser("Installation is already valid, no repair needed");
            return true;
        }
        
        boolean repaired = false;
        
        // Try to create missing directories
        if (!result.basePathExists || !result.dllModsDirExists || !result.dexModsDirExists) {
            LogUtils.logDebug("Creating missing directories");
            if (PathManager.initializeGameDirectories(context, gamePackage)) {
                LogUtils.logDebug("Successfully created missing directories");
                repaired = true;
            }
        }
        
        // Re-validate after repair attempts
        ValidationResult newResult = validateLoaderInstallation(context, gamePackage);
        
        if (newResult.isValid) {
            LogUtils.logUser("✅ Loader installation repaired successfully");
            return true;
        } else {
            LogUtils.logUser("❌ Could not fully repair installation");
            LogUtils.logDebug("Remaining issues: " + newResult.issues.size());
            for (String issue : newResult.issues) {
                LogUtils.logDebug("  - " + issue);
            }
            return repaired; // Return true if we made some progress
        }
    }
}

/ModLoader/app/src/main/java/com/modloader/loader/MainLoader.java

package com.loader;

import android.app.Application;
import android.util.Log;
import com.modloader.loader.ModManager;
import java.io.*;

public class MainLoader extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        Log.i("LoaderDex", "MainLoader has started in Terraria");

        if (shouldEnableMods()) {
            ModManager.loadMods(getApplicationContext());
        }
    }

    private boolean shouldEnableMods() {
        File config = new File(getExternalFilesDir(null), "config.txt");
        if (!config.exists()) return false;
        try (BufferedReader reader = new BufferedReader(new FileReader(config))) {
            String line;
            while ((line = reader.readLine()) != null) {
                if (line.trim().equalsIgnoreCase("enable_mods=true")) {
                    return true;
                }
            }
        } catch (IOException e) {
            Log.e("LoaderDex", "Error reading config.txt", e);
        }
        return false;
    }
}

/ModLoader/app/src/main/java/com/modloader/loader/MelonLoaderManager.java

// File: MelonLoaderManager.java (Fixed Facade) - Updated with PathManager
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/loader/MelonLoaderManager.java

package com.modloader.loader;

import android.content.Context;
import com.modloader.util.LogUtils;
import com.modloader.util.FileUtils;
import com.modloader.util.PathManager;
import java.io.File;
import java.util.List;

/**
 * MelonLoaderManager serves as a facade that delegates to specialized components:
 * - LoaderInstaller: Handles installation of MelonLoader/LemonLoader
 * - LoaderValidator: Handles detection and validation of loader installations
 * - LoaderFileManager: Handles file operations for mods and loaders
 * 
 * This maintains backward compatibility while organizing code into focused components.
 * Updated to use PathManager for consistent directory structure.
 */
public class MelonLoaderManager {
    // Static components
    private static final LoaderInstaller installer = new LoaderInstaller();
    private static final LoaderValidator validator = new LoaderValidator();
    private static final LoaderFileManager fileManager = new LoaderFileManager();
    
    // Constants for Terraria specifically (from your logs)
    public static final String TERRARIA_PACKAGE = "com.and.games505.TerrariaPaid";
    public static final String TERRARIA_UNITY_VERSION = "2021.3.35f1";
    public static final String TERRARIA_GAME_VERSION = "1.4.4.9.6";

    // Enum for loader types
    public enum LoaderType {
        MELONLOADER_NET8("MelonLoader .NET 8", ".dll"),
        MELONLOADER_NET35("MelonLoader .NET 3.5", ".dll");
        
        private final String displayName;
        private final String extension;
        
        LoaderType(String displayName, String extension) {
            this.displayName = displayName;
            this.extension = extension;
        }
        
        public String getDisplayName() { return displayName; }
        public String getExtension() { return extension; }
    }

    // Enhanced status class
    public static class LoaderStatus {
        public String gamePackage;
        public boolean melonLoaderInstalled;
        public LoaderType activeLoader;
        public String version;
        public String basePath;
        public File[] installedDllMods;
        public File[] installedDexMods;
        public File modsDirectory;
        public int totalFiles;
        public long totalSize;
        public boolean isNet8Available;
        public boolean isNet35Available;
        
        @Override
        public String toString() {
            return String.format("Loader[%s]: %s (%d DLL mods, %d DEX mods, %d files)", 
                               gamePackage != null ? gamePackage : "null", 
                               activeLoader != null ? "Installed " + activeLoader.getDisplayName() + " v" + version : "Not Installed",
                               installedDllMods != null ? installedDllMods.length : 0,
                               installedDexMods != null ? installedDexMods.length : 0,
                               totalFiles);
        }
    }

    // === DETECTION AND VALIDATION ===
    public static boolean isMelonLoaderInstalled(Context context, String gamePackage) {
        return validator.isMelonLoaderInstalled(context, gamePackage);
    }
    
    public static boolean isMelonLoaderInstalled(Context context) {
        return validator.isMelonLoaderInstalled(context, TERRARIA_PACKAGE);
    }
    
    public static boolean isLemonLoaderInstalled(Context context, String gamePackage) {
        return validator.isLemonLoaderInstalled(context, gamePackage);
    }
    
    public static boolean isLemonLoaderInstalled(Context context) {
        return validator.isLemonLoaderInstalled(context);
    }
    
    public static String getInstalledLoaderVersion() {
        return validator.getInstalledLoaderVersion();
    }

    // === INSTALLATION ===
    public static boolean createLoaderStructure(Context context, String gamePackage, LoaderType loaderType) {
        return installer.createLoaderStructure(context, gamePackage, loaderType);
    }
    
    public static boolean installFromLemonLoaderApk(Context context, File installerApk, LoaderType loaderType) {
        return installer.installFromLemonLoaderApk(context, installerApk, loaderType);
    }
    
    public static boolean installMelonLoader(Context context, File inputApk, File outputApk) {
        return installer.installMelonLoader(context, inputApk, outputApk);
    }

    public static boolean installLemonLoader(Context context, File inputApk, File outputApk) {
        return installer.installLemonLoader(context, inputApk, outputApk);
    }

    public static boolean uninstallLoader(Context context, String gamePackage) {
        return installer.uninstallLoader(gamePackage);
    }

    // === FILE MANAGEMENT ===
    public static boolean installDllMod(Context context, File dllFile, String gamePackage) {
        return fileManager.installDllMod(context, dllFile, gamePackage);
    }

    public static File[] getInstalledDllMods(Context context, String gamePackage) {
        return fileManager.getInstalledDllMods(context, gamePackage);
    }
    
    public static File[] getInstalledDexMods(Context context, String gamePackage) {
        return fileManager.getInstalledDexMods(context, gamePackage);
    }

    public static boolean toggleDllMod(Context context, String gamePackage, String modName, boolean enable) {
        return fileManager.toggleDllMod(context, gamePackage, modName, enable);
    }

    public static boolean deleteDllMod(Context context, String gamePackage, String modName) {
        return fileManager.deleteDllMod(context, gamePackage, modName);
    }

    public static List<File> getAllMods(Context context, String gamePackage) {
        return fileManager.getAllMods(context, gamePackage);
    }

    public static boolean isModEnabled(File modFile) {
        return fileManager.isModEnabled(modFile);
    }

    public static String getModType(File modFile) {
        return fileManager.getModType(modFile);
    }

    public static boolean toggleMod(Context context, String gamePackage, File modFile) {
        return fileManager.toggleMod(context, gamePackage, modFile);
    }

    public static String getModInfo(File modFile) {
        return fileManager.getModInfo(modFile);
    }

    public static boolean backupMods(Context context, String gamePackage) {
        return fileManager.backupMods(context, gamePackage);
    }

    // === VALIDATION ===
    public static boolean validateDllMod(File dllFile) {
        return validator.validateDllMod(dllFile);
    }

    public static LoaderValidator.ValidationResult validateLoaderInstallation(Context context, String gamePackage) {
        return validator.validateLoaderInstallation(context, gamePackage);
    }

    public static String getValidationReport(Context context, String gamePackage) {
        return validator.getValidationReport(context, gamePackage);
    }

    public static boolean attemptRepair(Context context, String gamePackage) {
        return validator.attemptRepair(context, gamePackage);
    }

    // === STATUS INFORMATION ===
    public static LoaderStatus getStatus(Context context, String gamePackage) {
        LoaderStatus status = new LoaderStatus();
        status.gamePackage = gamePackage;
        
        if (context == null || gamePackage == null) {
            status.melonLoaderInstalled = false;
            return status;
        }
        
        status.melonLoaderInstalled = validator.isMelonLoaderInstalled(context, gamePackage);
        
        if (status.melonLoaderInstalled) {
            // Check which runtime versions are available
            status.isNet8Available = validator.isNet8Available(context, gamePackage);
            status.isNet35Available = validator.isNet35Available(context, gamePackage);
            status.activeLoader = validator.getActiveLoaderType(context, gamePackage);
            status.version = validator.getInstalledLoaderVersion();
            
            File baseDir = PathManager.getGameBaseDir(context, gamePackage);
            status.basePath = baseDir != null ? baseDir.getAbsolutePath() : "null";
            status.installedDllMods = fileManager.getInstalledDllMods(context, gamePackage);
            status.installedDexMods = fileManager.getInstalledDexMods(context, gamePackage);
            
            File modsDir = PathManager.getDllModsDir(context, gamePackage);
            status.modsDirectory = modsDir;
            
            // Get file statistics
            LoaderFileManager.FileStatistics stats = fileManager.getFileStatistics(context, gamePackage);
            status.totalFiles = stats.totalFiles;
            status.totalSize = stats.totalSize;
        }
        
        return status;
    }

    // === DEBUG INFORMATION ===
    public static String getDebugInfo(Context context, String gamePackage) {
        StringBuilder info = new StringBuilder();
        info.append("=== MelonLoaderManager Debug Info (Facade Pattern) ===\n");
        info.append("Components: LoaderInstaller, LoaderValidator, LoaderFileManager\n");
        info.append("PathManager: Centralized path management\n\n");
        
        // Validation info
        info.append(validator.getValidationReport(context, gamePackage)).append("\n");
        
        // File statistics
        LoaderFileManager.FileStatistics stats = fileManager.getFileStatistics(context, gamePackage);
        info.append(stats.getDetailedInfo()).append("\n");
        
        // Status summary
        LoaderStatus status = getStatus(context, gamePackage);
        info.append("Status Summary: ").append(status.toString()).append("\n");
        
        return info.toString();
    }

    public static String getSummary(Context context, String gamePackage) {
        LoaderStatus status = getStatus(context, gamePackage);
        StringBuilder summary = new StringBuilder();
        
        summary.append("=== ModLoader Summary ===\n");
        summary.append("Game: ").append(gamePackage != null ? gamePackage : "null").append("\n");
        summary.append("Loader Status: ").append(status.melonLoaderInstalled ? "Installed" : "Not Installed").append("\n");
        
        if (status.melonLoaderInstalled) {
            summary.append("Loader Type: ").append(status.activeLoader != null ? status.activeLoader.getDisplayName() : "Unknown").append("\n");
            summary.append("Version: ").append(status.version != null ? status.version : "Unknown").append("\n");
            summary.append("DLL Mods: ").append(status.installedDllMods != null ? status.installedDllMods.length : 0).append("\n");
            summary.append("DEX Mods: ").append(status.installedDexMods != null ? status.installedDexMods.length : 0).append("\n");
            summary.append("Total Files: ").append(status.totalFiles).append("\n");
            summary.append("Total Size: ").append(FileUtils.formatFileSize(status.totalSize)).append("\n");
            summary.append("Base Path: ").append(status.basePath != null ? status.basePath : "null").append("\n");
        }
        
        return summary.toString();
    }

    // === COMPONENT ACCESS (for advanced usage) ===
    public static LoaderInstaller getInstaller() {
        return installer;
    }

    public static LoaderValidator getValidator() {
        return validator;
    }

    public static LoaderFileManager getFileManager() {
        return fileManager;
    }

    // === BACKWARD COMPATIBILITY ===
    // These methods maintain compatibility with existing code that expects the old interface

    // Backward compatibility enum values
    public enum LoaderTypeCompat {
        MELONLOADER("MelonLoader", ".dll"),
        LEMONLOADER("LemonLoader", ".dll");

        private final String displayName;
        private final String extension;

        LoaderTypeCompat(String displayName, String extension) {
            this.displayName = displayName;
            this.extension = extension;
        }

        public String getDisplayName() { return displayName; }
        public String getExtension() { return extension; }

        public LoaderType toNewLoaderType() {
            switch (this) {
                case MELONLOADER:
                    return LoaderType.MELONLOADER_NET8;
                case LEMONLOADER:
                    return LoaderType.MELONLOADER_NET35;
                default:
                    return LoaderType.MELONLOADER_NET8;
            }
        }
    }

    // === UTILITY METHODS ===
    public static boolean validateModDirectories(Context context, String gamePackage) {
        return fileManager.validateModDirectories(context, gamePackage);
    }

    public static boolean exportModList(Context context, String gamePackage, File outputFile) {
        return fileManager.exportModList(context, gamePackage, outputFile);
    }

    public static LoaderFileManager.FileStatistics getFileStatistics(Context context, String gamePackage) {
        return fileManager.getFileStatistics(context, gamePackage);
    }

    public static void cleanupOldBackups(Context context, String gamePackage, int maxBackups) {
        fileManager.cleanupOldBackups(context, gamePackage, maxBackups);
    }

    // === INITIALIZATION AND CLEANUP ===
    public static void initialize(Context context) {
        LogUtils.logDebug("MelonLoaderManager initialized with facade pattern and PathManager");
        
        // Check for migration needs
        if (PathManager.needsMigration(context)) {
            LogUtils.logUser("Legacy directory structure detected, migrating...");
            PathManager.migrateLegacyStructure(context);
        }
        
        // Validate directory structure on startup
        validateModDirectories(context, TERRARIA_PACKAGE);
    }

    public static void cleanup(Context context, String gamePackage) {
        // Clean up old backups (keep last 5)
        cleanupOldBackups(context, gamePackage, 5);
        LogUtils.logDebug("MelonLoaderManager cleanup completed for: " + gamePackage);
    }

    // === HEALTH CHECK ===
    public static boolean performHealthCheck(Context context, String gamePackage) {
        LogUtils.logDebug("Performing health check for: " + gamePackage);
        
        if (context == null || gamePackage == null) {
            LogUtils.logDebug("Health check failed: null parameters");
            return false;
        }
        
        boolean healthy = true;
        
        // Check if directories exist and are writable
        if (!validateModDirectories(context, gamePackage)) {
            LogUtils.logDebug("Health check failed: mod directories invalid");
            healthy = false;
        }
        
        // Check if loader is properly installed (if supposed to be)
        if (isMelonLoaderInstalled(context, gamePackage)) {
            LoaderValidator.ValidationResult result = validateLoaderInstallation(context, gamePackage);
            if (!result.isValid) {
                LogUtils.logDebug("Health check failed: loader installation invalid");
                healthy = false;
            }
        }
        
        LogUtils.logDebug("Health check result: " + (healthy ? "PASS" : "FAIL"));
        return healthy;
    }

    // === MIGRATION SUPPORT ===
    public static boolean migrateFromOldStructure(Context context, String gamePackage) {
        LogUtils.logUser("Checking for old structure to migrate...");
        
        if (context == null || gamePackage == null) {
            LogUtils.logDebug("Migration failed: null parameters");
            return false;
        }
        
        // Check if migration is needed
        if (PathManager.needsMigration(context)) {
            return PathManager.migrateLegacyStructure(context);
        } else {
            // Just ensure new structure exists
            return validateModDirectories(context, gamePackage);
        }
    }

    // === PATH INFORMATION ===
    public static String getPathInfo(Context context, String gamePackage) {
        if (context == null || gamePackage == null) {
            return "Path info unavailable: null parameters";
        }
        return PathManager.getPathInfo(context, gamePackage);
    }

    // === AUTO-REPAIR FUNCTIONALITY ===
    public static boolean autoRepairInstallation(Context context, String gamePackage) {
        LogUtils.logUser("🔧 Auto-repairing installation for: " + gamePackage);
        
        if (context == null || gamePackage == null) {
            LogUtils.logUser("❌ Auto-repair failed: invalid parameters");
            return false;
        }
        
        boolean repaired = false;
        
        // Step 1: Initialize directories if missing
        if (!PathManager.initializeGameDirectories(context, gamePackage)) {
            LogUtils.logDebug("Failed to initialize directories during auto-repair");
        } else {
            repaired = true;
        }
        
        // Step 2: Attempt validation repair
        if (attemptRepair(context, gamePackage)) {
            repaired = true;
        }
        
        // Step 3: Migrate legacy structure if needed
        if (migrateFromOldStructure(context, gamePackage)) {
            repaired = true;
        }
        
        // Step 4: Final health check
        boolean healthy = performHealthCheck(context, gamePackage);
        
        if (healthy) {
            LogUtils.logUser("✅ Auto-repair completed successfully");
        } else if (repaired) {
            LogUtils.logUser("⚠️ Auto-repair made improvements but issues remain");
        } else {
            LogUtils.logUser("❌ Auto-repair could not fix the installation");
        }
        
        return healthy || repaired;
    }

    // === LEGACY COMPATIBILITY METHODS ===
    // These maintain compatibility with old method signatures
    
    @Deprecated
    public static boolean isMelonLoaderInstalled(String gamePackage) {
        LogUtils.logDebug("Using deprecated method - context required for proper functionality");
        return false; // Cannot function without context
    }
    
    @Deprecated
    public static boolean isLemonLoaderInstalled() {
        LogUtils.logDebug("Using deprecated method - context required for proper functionality");  
        return false; // Cannot function without context
    }
    
    @Deprecated
    public static LoaderStatus getStatus(String gamePackage) {
        LogUtils.logDebug("Using deprecated method - context required for proper functionality");
        LoaderStatus status = new LoaderStatus();
        status.gamePackage = gamePackage;
        status.melonLoaderInstalled = false;
        return status;
    }
}

/ModLoader/app/src/main/java/com/modloader/loader/ModBase.java

// File: ModBase.java (Interface Class) - Phase 4 Enhanced with DLL Support
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/loader/ModBase.java

package com.modloader.loader;

import android.content.Context;

public interface ModBase {
    void onLoad(Context context);
    
    // Optional metadata methods (Phase 1 features preserved)
    default String getModName() {
        return "Unknown Mod";
    }
    
    default String getModVersion() {
        return "1.0.0";
    }
    
    default String getModDescription() {
        return "No description provided";
    }
    
    default String getModAuthor() {
        return "Unknown Author";
    }
    
    default String[] getDependencies() {
        return new String[0]; // No dependencies by default
    }
    
    default String getMinGameVersion() {
        return "1.0.0";
    }
    
    default String getMaxGameVersion() {
        return "999.0.0";
    }
    
    default boolean isCompatibleWith(String gameVersion) {
        return true; // Compatible by default
    }
    
    // Configuration support (Phase 1 features preserved)
    default void onConfigurationChanged(String key, Object value) {
        // Override to handle configuration changes
    }
    
    default void onUnload() {
        // Override to handle cleanup
    }
    
    // NEW PHASE 4: DLL Support Methods
    default ModType getModType() {
        return ModType.DEX; // Default to DEX for existing mods
    }
    
    default String[] getSupportedPlatforms() {
        return new String[]{"Android"}; // Default platform support
    }
    
    default boolean requiresMelonLoader() {
        return getModType() == ModType.DLL;
    }
    
    default String getMelonLoaderVersion() {
        return "0.6.1"; // Default MelonLoader version
    }
    
    default String[] getRequiredAssemblies() {
        return new String[0]; // No additional assemblies by default
    }
    
    // Hybrid mod support
    default boolean isHybridMod() {
        return false; // Most mods are single-type
    }
    
    default String getCompanionModPath() {
        return null; // Path to companion .dex/.dll file for hybrid mods
    }
    
    // Unity-specific methods for DLL mods
    default String getUnityVersion() {
        return "2021.3.21f1"; // Default Unity version for Terraria
    }
    
    default boolean requiresIl2Cpp() {
        return true; // Most Unity games use IL2CPP
    }
    
    default String[] getHarmonyPatches() {
        return new String[0]; // Harmony patch classes
    }
    
    // Mod type enumeration
    enum ModType {
        DEX("Java/Android DEX", ".dex"),
        JAR("Java Archive", ".jar"), 
        DLL("C# Dynamic Library", ".dll"),
        HYBRID("Hybrid Java+C#", ".hybrid");
        
        private final String displayName;
        private final String extension;
        
        ModType(String displayName, String extension) {
            this.displayName = displayName;
            this.extension = extension;
        }
        
        public String getDisplayName() { return displayName; }
        public String getExtension() { return extension; }
        
        public static ModType fromFileName(String fileName) {
            if (fileName.endsWith(".dex") || fileName.endsWith(".dex.disabled")) {
                return DEX;
            } else if (fileName.endsWith(".jar") || fileName.endsWith(".jar.disabled")) {
                return JAR;
            } else if (fileName.endsWith(".dll") || fileName.endsWith(".dll.disabled")) {
                return DLL;
            } else if (fileName.endsWith(".hybrid") || fileName.endsWith(".hybrid.disabled")) {
                return HYBRID;
            }
            return DEX; // Default
        }
    }
}

/ModLoader/app/src/main/java/com/modloader/loader/ModConfiguration.java

package com.modloader.loader;

import android.content.Context;
import android.content.SharedPreferences;
import com.modloader.util.LogUtils;
import org.json.JSONException;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;

public class ModConfiguration {
    private static final String PREFS_PREFIX = "mod_config_";
    private final String modName;
    private final Context context;
    private final SharedPreferences prefs;
    private final Map<String, Object> defaultValues;

    public ModConfiguration(Context context, String modName) {
        this.context = context;
        this.modName = modName;
        this.prefs = context.getSharedPreferences(PREFS_PREFIX + modName, Context.MODE_PRIVATE);
        this.defaultValues = new HashMap<>();
        
        LogUtils.logDebug("Created configuration for mod: " + modName);
    }

    // Set default values
    public void setDefault(String key, Object defaultValue) {
        defaultValues.put(key, defaultValue);
    }

    public void setDefaults(Map<String, Object> defaults) {
        defaultValues.putAll(defaults);
    }

    // Get values with type safety
    public String getString(String key, String defaultValue) {
        return prefs.getString(key, (String) defaultValues.getOrDefault(key, defaultValue));
    }

    public int getInt(String key, int defaultValue) {
        return prefs.getInt(key, (Integer) defaultValues.getOrDefault(key, defaultValue));
    }

    public boolean getBoolean(String key, boolean defaultValue) {
        return prefs.getBoolean(key, (Boolean) defaultValues.getOrDefault(key, defaultValue));
    }

    public float getFloat(String key, float defaultValue) {
        return prefs.getFloat(key, (Float) defaultValues.getOrDefault(key, defaultValue));
    }

    public long getLong(String key, long defaultValue) {
        return prefs.getLong(key, (Long) defaultValues.getOrDefault(key, defaultValue));
    }

    // Set values
    public void setString(String key, String value) {
        prefs.edit().putString(key, value).apply();
        LogUtils.logDebug("Set " + modName + " config: " + key + " = " + value);
    }

    public void setInt(String key, int value) {
        prefs.edit().putInt(key, value).apply();
        LogUtils.logDebug("Set " + modName + " config: " + key + " = " + value);
    }

    public void setBoolean(String key, boolean value) {
        prefs.edit().putBoolean(key, value).apply();
        LogUtils.logDebug("Set " + modName + " config: " + key + " = " + value);
    }

    public void setFloat(String key, float value) {
        prefs.edit().putFloat(key, value).apply();
        LogUtils.logDebug("Set " + modName + " config: " + key + " = " + value);
    }

    public void setLong(String key, long value) {
        prefs.edit().putLong(key, value).apply();
        LogUtils.logDebug("Set " + modName + " config: " + key + " = " + value);
    }

    // Check if key exists
    public boolean contains(String key) {
        return prefs.contains(key) || defaultValues.containsKey(key);
    }

    // Remove key
    public void remove(String key) {
        prefs.edit().remove(key).apply();
        LogUtils.logDebug("Removed " + modName + " config: " + key);
    }

    // Clear all configuration
    public void clear() {
        prefs.edit().clear().apply();
        LogUtils.logUser("Cleared all configuration for mod: " + modName);
    }

    // Reset to defaults
    public void resetToDefaults() {
        prefs.edit().clear().apply();
        
        // Apply default values
        SharedPreferences.Editor editor = prefs.edit();
        for (Map.Entry<String, Object> entry : defaultValues.entrySet()) {
            Object value = entry.getValue();
            if (value instanceof String) {
                editor.putString(entry.getKey(), (String) value);
            } else if (value instanceof Integer) {
                editor.putInt(entry.getKey(), (Integer) value);
            } else if (value instanceof Boolean) {
                editor.putBoolean(entry.getKey(), (Boolean) value);
            } else if (value instanceof Float) {
                editor.putFloat(entry.getKey(), (Float) value);
            } else if (value instanceof Long) {
                editor.putLong(entry.getKey(), (Long) value);
            }
        }
        editor.apply();
        
        LogUtils.logUser("Reset " + modName + " configuration to defaults");
    }

    // Export configuration as JSON
    public String exportToJson() {
        JSONObject json = new JSONObject();
        try {
            Map<String, ?> allPrefs = prefs.getAll();
            for (Map.Entry<String, ?> entry : allPrefs.entrySet()) {
                json.put(entry.getKey(), entry.getValue());
            }
        } catch (JSONException e) {
            LogUtils.logDebug("Failed to export config to JSON: " + e.getMessage());
        }
        return json.toString();
    }

    // Import configuration from JSON
    public boolean importFromJson(String jsonString) {
        try {
            JSONObject json = new JSONObject(jsonString);
            SharedPreferences.Editor editor = prefs.edit();
            
            Iterator<String> keys = json.keys();
            while (keys.hasNext()) {
                String key = keys.next();
                Object value = json.get(key);
                
                if (value instanceof String) {
                    editor.putString(key, (String) value);
                } else if (value instanceof Integer) {
                    editor.putInt(key, (Integer) value);
                } else if (value instanceof Boolean) {
                    editor.putBoolean(key, (Boolean) value);
                } else if (value instanceof Double) {
                    editor.putFloat(key, ((Double) value).floatValue());
                } else if (value instanceof Long) {
                    editor.putLong(key, (Long) value);
                }
            }
            
            editor.apply();
            LogUtils.logUser("Imported configuration for mod: " + modName);
            return true;
            
        } catch (JSONException e) {
            LogUtils.logDebug("Failed to import config from JSON: " + e.getMessage());
            return false;
        }
    }

    // Get all configuration keys
    public String[] getAllKeys() {
        Map<String, ?> allPrefs = prefs.getAll();
        return allPrefs.keySet().toArray(new String[0]);
    }

    // Get configuration summary
    public String getConfigSummary() {
        Map<String, ?> allPrefs = prefs.getAll();
        StringBuilder summary = new StringBuilder();
        summary.append("Configuration for ").append(modName).append(":\n");
        
        for (Map.Entry<String, ?> entry : allPrefs.entrySet()) {
            summary.append("- ").append(entry.getKey()).append(": ").append(entry.getValue()).append("\n");
        }
        
        if (allPrefs.isEmpty()) {
            summary.append("No configuration set (using defaults)\n");
        }
        
        return summary.toString();
    }
}

/ModLoader/app/src/main/java/com/modloader/loader/ModController.java

// File: ModController.java (Extracted Component) - Handles mod state management
// Path: /storage/emulated/0/AndroidIDEProjects/main/java/com/modloader/loader/ModController.java

package com.modloader.loader;

import android.content.Context;
import com.modloader.util.LogUtils;
import java.io.File;

public class ModController {
    private ModLoader modLoader;
    private ModRepository modRepository;

    public ModController() {
        this.modLoader = new ModLoader();
        this.modRepository = new ModRepository();
    }

    // Allow injection of components for testing/customization
    public ModController(ModLoader modLoader, ModRepository modRepository) {
        this.modLoader = modLoader;
        this.modRepository = modRepository;
    }

    public boolean enableMod(Context context, File modFile) {
        if (!modFile.exists()) {
            LogUtils.logDebug("Cannot enable non-existent mod: " + modFile.getName());
            return false;
        }

        if (isModEnabled(modFile)) {
            LogUtils.logDebug("Mod already enabled: " + modFile.getName());
            return true;
        }

        String currentName = modFile.getName();
        String newName = currentName.replace(".disabled", "");
        File enabledFile = new File(modFile.getParentFile(), newName);

        if (modFile.renameTo(enabledFile)) {
            LogUtils.logUser("✅ Enabled mod: " + newName);
            refreshMods(context);
            return true;
        } else {
            LogUtils.logDebug("Failed to enable mod: " + currentName);
            return false;
        }
    }

    public boolean disableMod(Context context, File modFile) {
        if (!modFile.exists()) {
            LogUtils.logDebug("Cannot disable non-existent mod: " + modFile.getName());
            return false;
        }

        if (!isModEnabled(modFile)) {
            LogUtils.logDebug("Mod already disabled: " + modFile.getName());
            return true;
        }

        String currentName = modFile.getName();
        String newName = currentName + ".disabled";
        File disabledFile = new File(modFile.getParentFile(), newName);

        if (modFile.renameTo(disabledFile)) {
            LogUtils.logUser("⏸️ Disabled mod: " + currentName);
            refreshMods(context);
            return true;
        } else {
            LogUtils.logDebug("Failed to disable mod: " + currentName);
            return false;
        }
    }

    public boolean deleteMod(Context context, File modFile) {
        if (!modFile.exists()) {
            LogUtils.logDebug("Cannot delete non-existent mod: " + modFile.getName());
            return false;
        }

        String modName = modFile.getName();
        
        // Clean up configuration
        String configModName = modName.replace(".dex", "").replace(".jar", "")
                                     .replace(".dll", "").replace(".disabled", "");
        
        // For DLL mods, also clean up MelonLoader directory
        if (modName.toLowerCase().endsWith(".dll") || modName.toLowerCase().endsWith(".dll.disabled")) {
            modLoader.cleanupDllMod(context, modFile);
        }

        if (modFile.delete()) {
            LogUtils.logUser("🗑️ Deleted mod: " + modName);
            
            // Remove from repository
            modRepository.removeMod(configModName);
            
            refreshMods(context);
            return true;
        } else {
            LogUtils.logDebug("Failed to delete mod: " + modName);
            return false;
        }
    }

    // Batch operations
    public void enableAllMods(Context context) {
        java.util.List<ModMetadata> sortedMods = modRepository.resolveDependencies();
        int enabledCount = 0;
        
        for (ModMetadata metadata : sortedMods) {
            if (!isModEnabled(metadata.getModFile())) {
                if (enableMod(context, metadata.getModFile())) {
                    enabledCount++;
                }
            }
        }
        
        LogUtils.logUser("Batch enabled " + enabledCount + " mods (dependency order)");
    }

    public void disableAllMods(Context context) {
        int disabledCount = 0;
        java.util.List<File> modsToDisable = new java.util.ArrayList<>(modRepository.getAvailableMods());
        
        for (File mod : modsToDisable) {
            if (isModEnabled(mod)) {
                if (disableMod(context, mod)) {
                    disabledCount++;
                }
            }
        }
        
        LogUtils.logUser("Batch disabled " + disabledCount + " mods");
    }

    // Toggle mod state
    public boolean toggleMod(Context context, File modFile) {
        if (isModEnabled(modFile)) {
            return disableMod(context, modFile);
        } else {
            return enableMod(context, modFile);
        }
    }

    // Validate mod before operations
    public boolean validateMod(File modFile) {
        if (!modFile.exists() || !modFile.isFile()) {
            LogUtils.logDebug("Mod file does not exist or is not a file: " + modFile.getName());
            return false;
        }

        if (modFile.length() == 0) {
            LogUtils.logDebug("Mod file is empty: " + modFile.getName());
            return false;
        }

        String fileName = modFile.getName().toLowerCase();
        String[] supportedExtensions = ModRepository.getSupportedExtensions();
        
        for (String ext : supportedExtensions) {
            if (fileName.endsWith(ext)) {
                return true;
            }
        }

        LogUtils.logDebug("Unsupported mod file type: " + modFile.getName());
        return false;
    }

    // Create backup before mod operations
    public File createModBackup(File modFile) {
        if (!modFile.exists()) {
            return null;
        }

        try {
            String backupName = modFile.getName() + ".backup." + System.currentTimeMillis();
            File backupFile = new File(modFile.getParentFile(), backupName);

            java.io.FileInputStream in = new java.io.FileInputStream(modFile);
            java.io.FileOutputStream out = new java.io.FileOutputStream(backupFile);

            byte[] buffer = new byte[8192];
            int bytesRead;
            while ((bytesRead = in.read(buffer)) != -1) {
                out.write(buffer, 0, bytesRead);
            }

            in.close();
            out.close();

            LogUtils.logDebug("Created backup: " + backupName);
            return backupFile;

        } catch (Exception e) {
            LogUtils.logDebug("Failed to create backup: " + e.getMessage());
            return null;
        }
    }

    // Refresh mods after changes
    public void refreshMods(Context context) {
        modRepository.scanForMods(context);
        modLoader.loadMods(context, modRepository.getAvailableMods(), modRepository);
    }

    // Status checks
    private boolean isModEnabled(File file) {
        String fileName = file.getName().toLowerCase();
        return !fileName.endsWith(".disabled");
    }

    public String getModStatus(File modFile) {
        if (!modFile.exists()) {
            return "Not Found";
        }
        
        if (isModEnabled(modFile)) {
            return "Enabled";
        } else {
            return "Disabled";
        }
    }

    public ModBase.ModType getModType(File modFile) {
        return ModBase.ModType.fromFileName(modFile.getName());
    }

    // Cleanup operations
    public void cleanupTemporaryFiles(Context context) {
        try {
            File cacheDir = context.getCacheDir();
            if (cacheDir != null && cacheDir.exists()) {
                File[] tempFiles = cacheDir.listFiles((dir, name) -> 
                    name.startsWith("mod_temp_") || name.endsWith(".tmp"));
                
                if (tempFiles != null) {
                    int deletedCount = 0;
                    for (File tempFile : tempFiles) {
                        if (tempFile.delete()) {
                            deletedCount++;
                        }
                    }
                    
                    if (deletedCount > 0) {
                        LogUtils.logDebug("Cleaned up " + deletedCount + " temporary mod files");
                    }
                }
            }
        } catch (Exception e) {
            LogUtils.logDebug("Error cleaning up temporary files: " + e.getMessage());
        }
    }

    // Repair operations
    public boolean repairModFile(Context context, File modFile) {
        LogUtils.logDebug("Attempting to repair mod file: " + modFile.getName());
        
        // Create backup first
        File backup = createModBackup(modFile);
        if (backup == null) {
            LogUtils.logDebug("Cannot repair - backup creation failed");
            return false;
        }

        try {
            // Basic repair operations
            if (!validateMod(modFile)) {
                LogUtils.logDebug("Mod validation failed during repair");
                return false;
            }

            // Additional repair logic can be added here
            LogUtils.logUser("✅ Mod repair completed: " + modFile.getName());
            return true;

        } catch (Exception e) {
            LogUtils.logDebug("Mod repair failed: " + e.getMessage());
            
            // Restore from backup if repair failed
            try {
                java.nio.file.Files.copy(backup.toPath(), modFile.toPath(), 
                    java.nio.file.StandardCopyOption.REPLACE_EXISTING);
                LogUtils.logDebug("Restored from backup after failed repair");
            } catch (Exception restoreError) {
                LogUtils.logDebug("Failed to restore from backup: " + restoreError.getMessage());
            }
            
            return false;
        } finally {
            // Clean up backup
            if (backup.exists()) {
                backup.delete();
            }
        }
    }

    // Getters for components (for advanced usage)
    public ModLoader getModLoader() {
        return modLoader;
    }

    public ModRepository getModRepository() {
        return modRepository;
    }
}

/ModLoader/app/src/main/java/com/modloader/loader/ModLoader.java

// File: ModLoader.java (Complete Fixed Component) - Updated Method Calls with Context
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/loader/ModLoader.java

package com.modloader.loader;

import android.content.Context;
import android.util.Log;
import com.modloader.util.LogUtils;
import com.modloader.ui.SettingsActivity;
import dalvik.system.DexClassLoader;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.util.ArrayList;
import java.util.List;

public class ModLoader {
    private static final String TAG = "ModLoader";
    private final List<ModBase> loadedDexMods = new ArrayList<>();
    private final List<File> loadedDllMods = new ArrayList<>();

    public void loadMods(Context context, List<File> availableMods, ModRepository repository) {
        if (context == null) {
            LogUtils.logDebug("Context is null, cannot load mods");
            return;
        }

        loadedDexMods.clear();
        loadedDllMods.clear();

        if (!SettingsActivity.isModsEnabled(context)) {
            LogUtils.logUser("Mod loading disabled in settings");
            return;
        }

        if (availableMods == null || availableMods.isEmpty()) {
            LogUtils.logUser("No mods found to load");
            return;
        }

        // Check loader requirements
        checkLoaderRequirements(context, availableMods);
        
        // Load mods by type and dependency order
        List<ModMetadata> sortedMods = repository != null ? repository.resolveDependencies() : new ArrayList<>();
        int dexLoaded = 0, dllLoaded = 0;

        for (ModMetadata metadata : sortedMods) {
            if (metadata != null && metadata.getModFile() != null && isModEnabled(metadata.getModFile())) {
                ModBase.ModType type = ModBase.ModType.fromFileName(metadata.getModFile().getName());
                switch (type) {
                    case DEX:
                    case JAR:
                        if (loadDexMod(context, metadata)) {
                            dexLoaded++;
                        }
                        break;
                    case DLL:
                        if (loadDllMod(context, metadata)) {
                            dllLoaded++;
                        }
                        break;
                    case HYBRID:
                        if (loadHybridMod(context, metadata)) {
                            dexLoaded++;
                            dllLoaded++;
                        }
                        break;
                }
            }
        }

        LogUtils.logUser("Loaded " + dexLoaded + " DEX/JAR mods and " + dllLoaded + " DLL mods");
        LogUtils.logUser("Total: " + (dexLoaded + dllLoaded) + " out of " + availableMods.size() + " mods");
    }

    private void checkLoaderRequirements(Context context, List<File> availableMods) {
        if (context == null || availableMods == null) {
            return;
        }

        boolean needsMelonLoader = false;
        for (File mod : availableMods) {
            if (mod != null && isModEnabled(mod)) {
                ModBase.ModType type = ModBase.ModType.fromFileName(mod.getName());
                if (type == ModBase.ModType.DLL || type == ModBase.ModType.HYBRID) {
                    needsMelonLoader = true;
                    break;
                }
            }
        }

        // FIXED: Pass context parameter to MelonLoaderManager method
        if (needsMelonLoader && !MelonLoaderManager.isMelonLoaderInstalled(context)) {
            LogUtils.logUser("⚠️ DLL mods found but MelonLoader not installed");
            LogUtils.logUser("💡 Use 'Inject Loader' to install MelonLoader for DLL mod support");
        }
    }

    private boolean loadDexMod(Context context, ModMetadata metadata) {
        if (context == null || metadata == null || metadata.getModFile() == null) {
            LogUtils.logDebug("Invalid parameters for DEX mod loading");
            return false;
        }

        File file = metadata.getModFile();
        try {
            String optimizedDir = context.getCodeCacheDir().getAbsolutePath();
            DexClassLoader loader = new DexClassLoader(
                file.getAbsolutePath(),
                optimizedDir,
                null,
                context.getClassLoader()
            );
            
            String[] possibleClassNames = {
                "com.mod.MyMod",
                "com.mod.MainMod",
                "com.terrariamod.Main",
                "mod.Main",
                "Main"
            };
            
            Class<?> modClass = null;
            String foundClassName = null;

            for (String className : possibleClassNames) {
                try {
                    modClass = loader.loadClass(className);
                    foundClassName = className;
                    break;
                } catch (ClassNotFoundException e) {
                    // Try next class name
                }
            }

            if (modClass == null) {
                LogUtils.logDebug("No valid mod class found in: " + file.getName());
                return false;
            }

            if (!ModBase.class.isAssignableFrom(modClass)) {
                LogUtils.logDebug("Class " + foundClassName + " does not implement ModBase interface");
                return false;
            }

            ModBase mod = (ModBase) modClass.newInstance();
            metadata.updateFromModBase(mod);

            if (SettingsActivity.isSandboxMode(context)) {
                LogUtils.logDebug("Loading DEX mod in sandbox mode: " + file.getName());
            }

            mod.onLoad(context);
            loadedDexMods.add(mod);

            LogUtils.logUser("✅ Loaded DEX mod: " + metadata.getName() + " v" + metadata.getVersion() +
                           " (class: " + foundClassName + ")");
            return true;

        } catch (Exception e) {
            String errorMsg = "Failed to load DEX mod: " + file.getName() + " - " + e.getMessage();
            LogUtils.logDebug(errorMsg);
            Log.e(TAG, errorMsg, e);
            return false;
        }
    }

    private boolean loadDllMod(Context context, ModMetadata metadata) {
        if (context == null || metadata == null || metadata.getModFile() == null) {
            LogUtils.logDebug("Invalid parameters for DLL mod loading");
            return false;
        }

        File file = metadata.getModFile();
        try {
            // FIXED: Pass context parameter to MelonLoaderManager method
            if (!MelonLoaderManager.isMelonLoaderInstalled(context)) {
                LogUtils.logDebug("Cannot load DLL mod - no loader installed: " + file.getName());
                return false;
            }

            // Create mods directory for MelonLoader using proper path management
            File melonModsDir = new File(context.getExternalFilesDir(null), "ModLoader/com.and.games505.TerrariaPaid/Mods/DLL");
            if (!melonModsDir.exists()) {
                melonModsDir.mkdirs();
            }

            // Copy DLL to MelonLoader mods directory
            File targetFile = new File(melonModsDir, file.getName().replace(".disabled", ""));
            if (!targetFile.exists()) {
                if (!copyFile(file, targetFile)) {
                    LogUtils.logDebug("Failed to copy DLL mod: " + file.getName());
                    return false;
                }
            }

            // Validate DLL
            if (!validateDllMod(targetFile)) {
                LogUtils.logDebug("DLL validation failed: " + file.getName());
                return false;
            }

            loadedDllMods.add(file);
            LogUtils.logUser("✅ Registered DLL mod: " + metadata.getName() + " v" + metadata.getVersion() +
                           " (will load via MelonLoader on game startup)");
            return true;

        } catch (Exception e) {
            String errorMsg = "Failed to register DLL mod: " + file.getName() + " - " + e.getMessage();
            LogUtils.logDebug(errorMsg);
            Log.e(TAG, errorMsg, e);
            return false;
        }
    }

    private boolean loadHybridMod(Context context, ModMetadata metadata) {
        if (context == null || metadata == null) {
            LogUtils.logDebug("Invalid parameters for hybrid mod loading");
            return false;
        }

        LogUtils.logDebug("Loading hybrid mod: " + metadata.getName());
        // For hybrid mods, we need to load both components
        boolean dexLoaded = loadDexMod(context, metadata);
        boolean dllLoaded = loadDllMod(context, metadata);

        if (dexLoaded || dllLoaded) {
            LogUtils.logUser("✅ Loaded hybrid mod: " + metadata.getName() +
                           " (DEX: " + dexLoaded + ", DLL: " + dllLoaded + ")");
            return true;
        }

        return false;
    }

    private boolean isModEnabled(File file) {
        if (file == null || !file.exists()) {
            return false;
        }
        String fileName = file.getName().toLowerCase();
        return !fileName.endsWith(".disabled");
    }

    private boolean copyFile(File source, File target) {
        if (source == null || target == null || !source.exists()) {
            return false;
        }

        try (FileInputStream in = new FileInputStream(source);
             FileOutputStream out = new FileOutputStream(target)) {

            byte[] buffer = new byte[8192];
            int bytesRead;
            while ((bytesRead = in.read(buffer)) != -1) {
                out.write(buffer, 0, bytesRead);
            }
            LogUtils.logDebug("Successfully copied: " + source.getName() + " -> " + target.getName());
            return true;
        } catch (Exception e) {
            LogUtils.logDebug("File copy failed: " + e.getMessage());
            return false;
        }
    }

    private boolean validateDllMod(File dllFile) {
        if (dllFile == null || !dllFile.exists()) {
            return false;
        }

        try {
            // Basic DLL validation - check file size and header
            if (dllFile.length() == 0) {
                LogUtils.logDebug("DLL file is empty: " + dllFile.getName());
                return false;
            }
            
            // Check for PE header (basic DLL validation)
            try (FileInputStream fis = new FileInputStream(dllFile)) {
                byte[] header = new byte[2];
                if (fis.read(header) == 2) {
                    // Check for MZ signature (PE executable)
                    if (header[0] == 0x4D && header[1] == 0x5A) {
                        LogUtils.logDebug("DLL validation passed: " + dllFile.getName());
                        return true;
                    } else {
                        LogUtils.logDebug("Invalid PE header in DLL: " + dllFile.getName());
                        return false;
                    }
                }
            }
            LogUtils.logDebug("Could not read DLL header: " + dllFile.getName());
            return false;
        } catch (Exception e) {
            LogUtils.logDebug("DLL validation error: " + e.getMessage());
            return false;
        }
    }

    // Cleanup method for DLL mods
    public void cleanupDllMod(Context context, File modFile) {
        if (context == null || modFile == null) {
            return;
        }

        try {
            File melonModsDir = new File(context.getExternalFilesDir(null), "ModLoader/com.and.games505.TerrariaPaid/Mods/DLL");
            File targetFile = new File(melonModsDir, modFile.getName().replace(".disabled", ""));
            if (targetFile.exists()) {
                if (targetFile.delete()) {
                    LogUtils.logDebug("Cleaned up DLL from MelonLoader directory: " + targetFile.getName());
                } else {
                    LogUtils.logDebug("Failed to cleanup DLL: " + targetFile.getName());
                }
            }
        } catch (Exception e) {
            LogUtils.logDebug("DLL cleanup error: " + e.getMessage());
        }
    }

    // Enhanced cleanup for all temporary files
    public void cleanupAllTemporaryFiles(Context context) {
        if (context == null) {
            return;
        }

        try {
            File cacheDir = context.getCacheDir();
            if (cacheDir != null && cacheDir.exists()) {
                File[] tempFiles = cacheDir.listFiles((dir, name) -> 
                    name.startsWith("mod_temp_") || 
                    name.startsWith("input_") || 
                    name.endsWith(".tmp"));
                
                if (tempFiles != null) {
                    int deletedCount = 0;
                    for (File tempFile : tempFiles) {
                        if (tempFile.delete()) {
                            deletedCount++;
                        }
                    }
                    
                    if (deletedCount > 0) {
                        LogUtils.logDebug("Cleaned up " + deletedCount + " temporary mod files");
                    }
                }
            }
        } catch (Exception e) {
            LogUtils.logDebug("Error cleaning up temporary files: " + e.getMessage());
        }
    }

    // Getters for loaded mods with null safety
    public List<ModBase> getLoadedDexMods() {
        return new ArrayList<>(loadedDexMods);
    }

    public List<File> getLoadedDllMods() {
        return new ArrayList<>(loadedDllMods);
    }

    // Get loading statistics
    public int getLoadedDexModCount() {
        return loadedDexMods.size();
    }

    public int getLoadedDllModCount() {
        return loadedDllMods.size();
    }

    public int getTotalLoadedModCount() {
        return loadedDexMods.size() + loadedDllMods.size();
    }

    // Check if any mods are loaded
    public boolean hasLoadedMods() {
        return !loadedDexMods.isEmpty() || !loadedDllMods.isEmpty();
    }

    // Get mod loading status
    public String getLoadingStatus() {
        return "Loaded " + getLoadedDexModCount() + " DEX/JAR mods and " + 
               getLoadedDllModCount() + " DLL mods (" + getTotalLoadedModCount() + " total)";
    }

    // Clear all loaded mods
    public void clearLoadedMods() {
        loadedDexMods.clear();
        loadedDllMods.clear();
        LogUtils.logDebug("Cleared all loaded mods from memory");
    }

    // Unload specific mod (for DEX mods)
    public boolean unloadDexMod(ModBase mod) {
        if (mod == null) {
            return false;
        }

        try {
            mod.onUnload();
            boolean removed = loadedDexMods.remove(mod);
            if (removed) {
                LogUtils.logDebug("Unloaded DEX mod: " + mod.getModName());
            }
            return removed;
        } catch (Exception e) {
            LogUtils.logDebug("Error unloading DEX mod: " + e.getMessage());
            return false;
        }
    }
}

/ModLoader/app/src/main/java/com/modloader/loader/ModManager.java

// File: ModManager.java (Fixed Facade) - Updated with PathManager
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/loader/ModManager.java

package com.modloader.loader;

import android.content.Context;
import com.modloader.util.PathManager;
import java.io.File;
import java.util.List;

/**
 * ModManager serves as a facade that delegates to specialized components:
 * - ModLoader: Handles the actual loading of mods
 * - ModRepository: Manages mod storage and metadata
 * - ModController: Handles mod state changes (enable/disable/delete)
 * 
 * This maintains backward compatibility while organizing code into focused components.
 * Updated to use PathManager for consistent directory structure.
 */
public class ModManager {
    private static final ModLoader modLoader = new ModLoader();
    private static final ModRepository modRepository = new ModRepository();
    private static final ModController modController = new ModController(modLoader, modRepository);

    // === LOADING OPERATIONS ===
    public static void loadMods(Context context) {
        if (context == null) {
            return;
        }
        
        // Check for migration needs first
        if (PathManager.needsMigration(context)) {
            PathManager.migrateLegacyStructure(context);
        }
        
        // Initialize directory structure
        PathManager.initializeGameDirectories(context, MelonLoaderManager.TERRARIA_PACKAGE);
        
        modRepository.scanForMods(context);
        modLoader.loadMods(context, modRepository.getAvailableMods(), modRepository);
    }

    // === RETRIEVAL OPERATIONS ===
    public static List<ModBase> getLoadedMods() {
        return modLoader.getLoadedDexMods();
    }

    public static List<File> getLoadedDllMods() {
        return modLoader.getLoadedDllMods();
    }

    public static List<File> getAvailableMods() {
        return modRepository.getAvailableMods();
    }

    public static List<File> getModsByType(ModBase.ModType type) {
        return modRepository.getModsByType(type);
    }

    public static List<ModMetadata> getModMetadata() {
        return modRepository.getModMetadata();
    }

    public static ModMetadata getMetadata(String modName) {
        return modRepository.getMetadata(modName);
    }

    public static ModConfiguration getConfiguration(Context context, String modName) {
        return modRepository.getConfiguration(context, modName);
    }

    // === MOD CONTROL OPERATIONS ===
    public static boolean enableMod(Context context, File modFile) {
        return modController.enableMod(context, modFile);
    }

    public static boolean disableMod(Context context, File modFile) {
        return modController.disableMod(context, modFile);
    }

    public static boolean deleteMod(Context context, File modFile) {
        return modController.deleteMod(context, modFile);
    }

    public static boolean toggleMod(Context context, File modFile) {
        return modController.toggleMod(context, modFile);
    }

    // === BATCH OPERATIONS ===
    public static void enableAllMods(Context context) {
        modController.enableAllMods(context);
    }

    public static void disableAllMods(Context context) {
        modController.disableAllMods(context);
    }

    // === STATISTICS ===
    public static int getEnabledModCount() {
        return modRepository.getEnabledModCount();
    }

    public static int getDisabledModCount() {
        return modRepository.getDisabledModCount();
    }

    public static int getTotalModCount() {
        return modRepository.getTotalModCount();
    }

    public static int getDexModCount() {
        return modRepository.getDexModCount();
    }

    public static int getDllModCount() {
        return modRepository.getDllModCount();
    }

    public static int getHybridModCount() {
        return modRepository.getHybridModCount();
    }

    // === UTILITY OPERATIONS ===
    public static boolean validateMod(File modFile) {
        return modController.validateMod(modFile);
    }

    public static String getModStatus(File modFile) {
        return modController.getModStatus(modFile);
    }

    public static ModBase.ModType getModType(File modFile) {
        return modController.getModType(modFile);
    }

    // === MAINTENANCE OPERATIONS ===
    public static void cleanupTemporaryFiles(Context context) {
        modController.cleanupTemporaryFiles(context);
    }

    public static boolean repairModFile(Context context, File modFile) {
        return modController.repairModFile(context, modFile);
    }

    public static File createModBackup(File modFile) {
        return modController.createModBackup(modFile);
    }

    // === DEBUG OPERATIONS ===
    public static String getDebugInfo() {
        StringBuilder info = new StringBuilder();
        info.append("=== ModManager Debug Info (Facade Pattern) ===\n");
        info.append("Components: ModLoader, ModRepository, ModController\n");
        info.append("PathManager: Centralized path management\n\n");
        info.append(modRepository.getDebugInfo());
        
        info.append("\nLoaded Components:\n");
        info.append("- DEX Mods Loaded: ").append(modLoader.getLoadedDexMods().size()).append("\n");
        info.append("- DLL Mods Loaded: ").append(modLoader.getLoadedDllMods().size()).append("\n");
        
        return info.toString();
    }

    // === COMPONENT ACCESS (for advanced usage) ===
    public static ModLoader getModLoader() {
        return modLoader;
    }

    public static ModRepository getModRepository() {
        return modRepository;
    }

    public static ModController getModController() {
        return modController;
    }

    // === REFRESH OPERATION ===
    public static void refreshMods(Context context) {
        modController.refreshMods(context);
    }

    // === DEPENDENCY RESOLUTION ===
    public static List<ModMetadata> resolveDependencies() {
        return modRepository.resolveDependencies();
    }

    // === PATH-AWARE OPERATIONS ===
    public static String getModsDirectoryPath(Context context) {
        if (context == null) return "Context is null";
        
        File dexModsDir = PathManager.getDexModsDir(context, MelonLoaderManager.TERRARIA_PACKAGE);
        return dexModsDir != null ? dexModsDir.getAbsolutePath() : "Path unavailable";
    }

    public static String getDllModsDirectoryPath(Context context) {
        if (context == null) return "Context is null";
        
        File dllModsDir = PathManager.getDllModsDir(context, MelonLoaderManager.TERRARIA_PACKAGE);
        return dllModsDir != null ? dllModsDir.getAbsolutePath() : "Path unavailable";
    }

    public static boolean initializeModDirectories(Context context) {
        if (context == null) return false;
        
        return PathManager.initializeGameDirectories(context, MelonLoaderManager.TERRARIA_PACKAGE);
    }

    // === MIGRATION SUPPORT ===
    public static boolean migrateModsToNewStructure(Context context) {
        if (context == null) return false;
        
        if (PathManager.needsMigration(context)) {
            return PathManager.migrateLegacyStructure(context);
        }
        return true; // No migration needed
    }

    // === DIRECTORY VALIDATION ===
    public static boolean validateModDirectories(Context context) {
        if (context == null) return false;
        
        return MelonLoaderManager.validateModDirectories(context, MelonLoaderManager.TERRARIA_PACKAGE);
    }

    // === HEALTH CHECK ===
    public static boolean performHealthCheck(Context context) {
        if (context == null) return false;
        
        boolean healthy = true;
        
        // Check directory structure
        if (!validateModDirectories(context)) {
            healthy = false;
        }
        
        // Check for migration needs
        if (PathManager.needsMigration(context)) {
            migrateModsToNewStructure(context);
        }
        
        // Validate mod files
        List<File> availableMods = getAvailableMods();
        if (availableMods != null) {
            for (File mod : availableMods) {
                if (!validateMod(mod)) {
                    healthy = false;
                    break;
                }
            }
        }
        
        return healthy;
    }

    // === INITIALIZATION ===
    public static void initialize(Context context) {
        if (context == null) return;
        
        // Initialize directory structure
        initializeModDirectories(context);
        
        // Perform migration if needed
        migrateModsToNewStructure(context);
        
        // Load initial mods
        loadMods(context);
    }

    // === CLEANUP ===
    public static void cleanup(Context context) {
        if (context == null) return;
        
        cleanupTemporaryFiles(context);
    }

    // === LEGACY COMPATIBILITY ===
    // These methods are deprecated but maintained for backward compatibility
    
    @Deprecated
    public static void loadMods() {
        // Cannot function without context
        throw new UnsupportedOperationException("Context is required. Use loadMods(Context context) instead.");
    }
    
    @Deprecated
    public static boolean enableMod(File modFile) {
        // Cannot function without context
        throw new UnsupportedOperationException("Context is required. Use enableMod(Context context, File modFile) instead.");
    }
    
    @Deprecated
    public static boolean disableMod(File modFile) {
        // Cannot function without context
        throw new UnsupportedOperationException("Context is required. Use disableMod(Context context, File modFile) instead.");
    }
    
    @Deprecated
    public static boolean deleteMod(File modFile) {
        // Cannot function without context
        throw new UnsupportedOperationException("Context is required. Use deleteMod(Context context, File modFile) instead.");
    }
}

/ModLoader/app/src/main/java/com/modloader/loader/ModMetadata.java

// File: ModMetadata.java (Fixed Class) - Enhanced Null Safety
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/loader/ModMetadata.java

package com.modloader.loader;

import android.content.Context;
import com.modloader.util.LogUtils;
import org.json.JSONArray;
import org.json.JSONObject;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class ModMetadata {
    private String name;
    private String version;
    private String description;
    private String author;
    private List<String> dependencies;
    private String minGameVersion;
    private String maxGameVersion;
    private File modFile;
    private boolean isValid;
    private ModBase.ModType modType;

    // FIXED: Constructor with enhanced null safety
    public ModMetadata(File modFile) {
        this.modFile = modFile;
        this.dependencies = new ArrayList<>();
        this.isValid = false;
        
        // FIXED: Safe mod type detection
        try {
            this.modType = ModBase.ModType.fromFileName(modFile.getName());
        } catch (Exception e) {
            LogUtils.logDebug("Error detecting mod type: " + e.getMessage());
            this.modType = ModBase.ModType.DEX; // Default fallback
        }
        
        // Set safe defaults
        this.name = modFile.getName();
        this.version = "1.0.0";
        this.description = "No description available";
        this.author = "Unknown";
        this.minGameVersion = "1.0.0";
        this.maxGameVersion = "999.0.0";
        
        loadMetadata();
    }

    // Constructor with explicit ModType (for advanced usage)
    public ModMetadata(File modFile, ModBase.ModType modType) {
        this.modFile = modFile;
        this.dependencies = new ArrayList<>();
        this.isValid = false;
        this.modType = modType != null ? modType : ModBase.ModType.DEX; // Null safety
        
        // Set safe defaults
        this.name = modFile.getName();
        this.version = "1.0.0";
        this.description = "No description available";
        this.author = "Unknown";
        this.minGameVersion = "1.0.0";
        this.maxGameVersion = "999.0.0";
        
        loadMetadata();
    }

    private void loadMetadata() {
        try {
            // Try to load from mod.json file in same directory
            File metadataFile = new File(modFile.getParentFile(), 
                modFile.getName().replace(".dex", ".json")
                                  .replace(".jar", ".json")
                                  .replace(".dll", ".json")
                                  .replace(".hybrid", ".json")
                                  .replace(".disabled", "")); // Remove .disabled if present
            
            if (metadataFile.exists()) {
                loadFromJsonFile(metadataFile);
            } else {
                // Use defaults and mark as valid
                this.isValid = true;
                LogUtils.logDebug("Using default metadata for: " + name);
            }
        } catch (Exception e) {
            LogUtils.logDebug("Error loading metadata: " + e.getMessage());
            this.isValid = true; // Still mark as valid with defaults
        }
    }

    private void loadFromJsonFile(File jsonFile) {
        try {
            byte[] jsonData = new byte[(int) jsonFile.length()];
            FileInputStream fis = new FileInputStream(jsonFile);
            fis.read(jsonData);
            fis.close();
            
            String jsonString = new String(jsonData);
            JSONObject json = new JSONObject(jsonString);
            
            this.name = json.optString("name", this.name);
            this.version = json.optString("version", this.version);
            this.description = json.optString("description", this.description);
            this.author = json.optString("author", this.author);
            this.minGameVersion = json.optString("minGameVersion", this.minGameVersion);
            this.maxGameVersion = json.optString("maxGameVersion", this.maxGameVersion);
            
            // Load dependencies
            JSONArray depsArray = json.optJSONArray("dependencies");
            if (depsArray != null) {
                for (int i = 0; i < depsArray.length(); i++) {
                    dependencies.add(depsArray.getString(i));
                }
            }
            
            this.isValid = true;
            LogUtils.logDebug("Loaded metadata from JSON: " + name);
            
        } catch (Exception e) {
            LogUtils.logDebug("Failed to load metadata from JSON: " + e.getMessage());
            this.isValid = true; // Still mark as valid with defaults
        }
    }

    // FIXED: Update metadata from loaded ModBase instance with null safety
    public void updateFromModBase(ModBase modInstance) {
        if (modInstance != null) {
            try {
                this.name = modInstance.getModName() != null ? modInstance.getModName() : this.name;
                this.version = modInstance.getModVersion() != null ? modInstance.getModVersion() : this.version;
                this.description = modInstance.getModDescription() != null ? modInstance.getModDescription() : this.description;
                this.author = modInstance.getModAuthor() != null ? modInstance.getModAuthor() : this.author;
                this.minGameVersion = modInstance.getMinGameVersion() != null ? modInstance.getMinGameVersion() : this.minGameVersion;
                this.maxGameVersion = modInstance.getMaxGameVersion() != null ? modInstance.getMaxGameVersion() : this.maxGameVersion;
                
                String[] deps = modInstance.getDependencies();
                if (deps != null) {
                    this.dependencies = Arrays.asList(deps);
                }
                
                LogUtils.logDebug("Updated metadata from ModBase: " + name);
            } catch (Exception e) {
                LogUtils.logDebug("Error updating from ModBase: " + e.getMessage());
            }
        }
    }

    // Getters with null safety
    public String getName() { return name != null ? name : "Unknown"; }
    public String getVersion() { return version != null ? version : "1.0.0"; }
    public String getDescription() { return description != null ? description : "No description"; }
    public String getAuthor() { return author != null ? author : "Unknown"; }
    public List<String> getDependencies() { return dependencies != null ? dependencies : new ArrayList<>(); }
    public String getMinGameVersion() { return minGameVersion != null ? minGameVersion : "1.0.0"; }
    public String getMaxGameVersion() { return maxGameVersion != null ? maxGameVersion : "999.0.0"; }
    public File getModFile() { return modFile; }
    public boolean isValid() { return isValid; }
    public ModBase.ModType getModType() { return modType != null ? modType : ModBase.ModType.DEX; }

    // Dependency checking
    public boolean hasDependencies() {
        return dependencies != null && !dependencies.isEmpty();
    }

    public boolean isDependencySatisfied(List<ModMetadata> availableMods) {
        if (!hasDependencies()) return true;
        
        for (String dependency : dependencies) {
            boolean found = false;
            for (ModMetadata mod : availableMods) {
                if (mod != null && dependency.equals(mod.getName())) {
                    found = true;
                    break;
                }
            }
            if (!found) {
                return false;
            }
        }
        return true;
    }

    // Version compatibility
    public boolean isCompatibleWithGameVersion(String gameVersion) {
        try {
            return compareVersions(gameVersion, minGameVersion) >= 0 &&
                   compareVersions(gameVersion, maxGameVersion) <= 0;
        } catch (Exception e) {
            LogUtils.logDebug("Version comparison failed: " + e.getMessage());
            return true; // Default to compatible
        }
    }

    private int compareVersions(String version1, String version2) {
        try {
            String[] v1Parts = version1.split("\\.");
            String[] v2Parts = version2.split("\\.");
            
            int maxLength = Math.max(v1Parts.length, v2Parts.length);
            
            for (int i = 0; i < maxLength; i++) {
                int v1Part = i < v1Parts.length ? Integer.parseInt(v1Parts[i]) : 0;
                int v2Part = i < v2Parts.length ? Integer.parseInt(v2Parts[i]) : 0;
                
                if (v1Part != v2Part) {
                    return Integer.compare(v1Part, v2Part);
                }
            }
            
            return 0;
        } catch (Exception e) {
            LogUtils.logDebug("Version parsing error: " + e.getMessage());
            return 0; // Assume equal if parsing fails
        }
    }

    @Override
    public String toString() {
        return getName() + " v" + getVersion() + " by " + getAuthor() + 
               " (Type: " + (getModType() != null ? getModType().getDisplayName() : "Unknown") + ")";
    }
}

/ModLoader/app/src/main/java/com/modloader/loader/ModRepository.java

// File: ModRepository.java (Fixed Component) - Updated Method Calls
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/loader/ModRepository.java

package com.modloader.loader;

import android.content.Context;
import com.modloader.util.LogUtils;
import java.io.File;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class ModRepository {
    private final List<File> availableMods = new ArrayList<>();
    private final Map<String, ModMetadata> modMetadataMap = new HashMap<>();
    private final Map<String, ModConfiguration> modConfigMap = new HashMap<>();
    
    // Enhanced file extensions for DLL support
    private static final String[] SUPPORTED_EXTENSIONS = {
        ".dex", ".jar", ".dll", ".dex.disabled", ".jar.disabled", ".dll.disabled", ".hybrid", ".hybrid.disabled"
    };

    public void scanForMods(Context context) {
        availableMods.clear();
        modMetadataMap.clear();

        File modDir = new File(context.getExternalFilesDir(null), "mods");
        if (!modDir.exists() && !modDir.mkdirs()) {
            LogUtils.logDebug("Failed to create mods directory");
            return;
        }

        // Enhanced file filtering for DLL support
        File[] modFiles = modDir.listFiles((dir, name) -> {
            String lowerName = name.toLowerCase();
            for (String ext : SUPPORTED_EXTENSIONS) {
                if (lowerName.endsWith(ext)) {
                    return true;
                }
            }
            return false;
        });

        if (modFiles != null) {
            LogUtils.logUser("Found " + modFiles.length + " mod files");
            
            // Load metadata for all mods
            for (File file : modFiles) {
                availableMods.add(file);
                ModMetadata metadata = new ModMetadata(file);
                modMetadataMap.put(metadata.getName(), metadata);
            }
        }
    }

    public List<File> getAvailableMods() {
        return new ArrayList<>(availableMods);
    }

    public List<File> getModsByType(ModBase.ModType type) {
        List<File> typedMods = new ArrayList<>();
        for (File mod : availableMods) {
            if (ModBase.ModType.fromFileName(mod.getName()) == type) {
                typedMods.add(mod);
            }
        }
        return typedMods;
    }

    public List<ModMetadata> getModMetadata() {
        return new ArrayList<>(modMetadataMap.values());
    }

    public ModMetadata getMetadata(String modName) {
        return modMetadataMap.get(modName);
    }

    public ModConfiguration getConfiguration(Context context, String modName) {
        if (!modConfigMap.containsKey(modName)) {
            modConfigMap.put(modName, new ModConfiguration(context, modName));
        }
        return modConfigMap.get(modName);
    }

    // Dependency resolution
    public List<ModMetadata> resolveDependencies() {
        List<ModMetadata> allMods = new ArrayList<>(modMetadataMap.values());
        List<ModMetadata> sortedMods = new ArrayList<>();
        List<ModMetadata> remaining = new ArrayList<>(allMods);
        
        while (!remaining.isEmpty()) {
            boolean progress = false;
            for (int i = remaining.size() - 1; i >= 0; i--) {
                ModMetadata mod = remaining.get(i);
                if (mod.isDependencySatisfied(sortedMods)) {
                    sortedMods.add(mod);
                    remaining.remove(i);
                    progress = true;
                    LogUtils.logDebug("Dependencies satisfied for: " + mod.getName());
                }
            }

            if (!progress && !remaining.isEmpty()) {
                for (ModMetadata mod : remaining) {
                    LogUtils.logDebug("Dependency issue with mod: " + mod.getName());
                    sortedMods.add(mod);
                }
                remaining.clear();
            }
        }

        return sortedMods;
    }

    // Statistics methods
    public int getEnabledModCount() {
        int count = 0;
        for (File mod : availableMods) {
            if (isModEnabled(mod)) {
                count++;
            }
        }
        return count;
    }

    public int getDisabledModCount() {
        return availableMods.size() - getEnabledModCount();
    }

    public int getTotalModCount() {
        return availableMods.size();
    }

    public int getDexModCount() {
        return getModsByType(ModBase.ModType.DEX).size() + getModsByType(ModBase.ModType.JAR).size();
    }

    public int getDllModCount() {
        return getModsByType(ModBase.ModType.DLL).size();
    }

    public int getHybridModCount() {
        return getModsByType(ModBase.ModType.HYBRID).size();
    }

    private boolean isModEnabled(File file) {
        String fileName = file.getName().toLowerCase();
        return !fileName.endsWith(".disabled");
    }

    // Clean up method to remove mod from repository
    public void removeMod(String modName) {
        // Remove from metadata map
        modMetadataMap.remove(modName);
        
        // Remove from available mods list
        availableMods.removeIf(file -> {
            String fileName = file.getName();
            String cleanName = fileName.replace(".dex", "").replace(".jar", "")
                                      .replace(".dll", "").replace(".disabled", "");
            return cleanName.equals(modName);
        });
        
        // Clean up configuration
        if (modConfigMap.containsKey(modName)) {
            modConfigMap.get(modName).clear();
            modConfigMap.remove(modName);
        }
    }

    // Debug information
    public String getDebugInfo() {
        StringBuilder info = new StringBuilder();
        info.append("=== ModRepository Debug Info ===\n");
        info.append("Total mods: ").append(availableMods.size()).append("\n");
        info.append("DEX/JAR mods: ").append(getDexModCount()).append("\n");
        info.append("DLL mods: ").append(getDllModCount()).append("\n");
        info.append("Hybrid mods: ").append(getHybridModCount()).append("\n");
        info.append("Metadata entries: ").append(modMetadataMap.size()).append("\n");
        info.append("Configuration entries: ").append(modConfigMap.size()).append("\n");
        info.append("NOTE: MelonLoader status requires Context parameter\n");
        info.append("\nMod details:\n");
        
        for (ModMetadata metadata : modMetadataMap.values()) {
            ModBase.ModType type = ModBase.ModType.fromFileName(metadata.getModFile().getName());
            info.append("- ").append(metadata.toString()).append(" [").append(type.getDisplayName()).append("]\n");
            if (metadata.hasDependencies()) {
                info.append("  Dependencies: ").append(metadata.getDependencies()).append("\n");
            }
        }
        
        return info.toString();
    }

    // Supported extensions getter
    public static String[] getSupportedExtensions() {
        return SUPPORTED_EXTENSIONS.clone();
    }
}

/ModLoader/app/src/main/java/com/modloader/loader/debugger/ApkProcessTracker.java

// File: ApkProcessTracker.java - Real-time APK operation monitoring and debugging
// Path: app/src/main/java/com/modloader/loader/debug/ApkProcessTracker.java

package com.modloader.loader.debug;

import android.content.Context;
import com.modloader.util.LogUtils;
import com.modloader.util.ApkValidator;
import com.modloader.util.ApkPatcher;
import java.io.*;
import java.util.*;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicLong;

/**
 * ApkProcessTracker - Real-time monitoring and debugging of APK operations
 * Single responsibility: Track, monitor, and debug APK patching processes in real-time
 */
public class ApkProcessTracker {
    private static final String TAG = "APKTracker";
    private final Context context;
    private final Map<String, ActiveProcess> activeProcesses = new ConcurrentHashMap<>();
    private final AtomicLong processIdCounter = new AtomicLong(0);
    private final List<ProcessListener> listeners = new ArrayList<>();
    
    public ApkProcessTracker(Context context) {
        this.context = context;
        LogUtils.logDebug("ApkProcessTracker initialized");
    }
    
    /**
     * Start tracking an APK process with detailed monitoring
     */
    public String startProcess(String operation, File inputApk, File outputApk) {
        String processId = "APK_" + System.currentTimeMillis() + "_" + processIdCounter.incrementAndGet();
        
        ActiveProcess process = new ActiveProcess(processId, operation, inputApk, outputApk);
        activeProcesses.put(processId, process);
        
        LogUtils.logApkProcessStart(operation, inputApk != null ? inputApk.getName() : "Unknown");
        LogUtils.logDebug("Started tracking process: " + processId);
        
        // Notify listeners
        notifyListeners(listener -> listener.onProcessStarted(process));
        
        return processId;
    }
    
    /**
     * Log a detailed step with performance metrics
     */
    public void logStep(String processId, String stepName, String details) {
        ActiveProcess process = activeProcesses.get(processId);
        if (process == null) {
            LogUtils.logDebug("Process not found for step logging: " + processId);
            return;
        }
        
        ProcessStep step = new ProcessStep(stepName, details);
        process.addStep(step);
        
        LogUtils.logApkProcessStep(stepName, details);
        LogUtils.logDebug(String.format("[%s] Step: %s (%dms since start)", 
                                      processId, stepName, step.getElapsedTime()));
        
        // Notify listeners
        notifyListeners(listener -> listener.onStepCompleted(process, step));
    }
    
    /**
     * Log step with file size information
     */
    public void logStepWithSize(String processId, String stepName, String details, long fileSize) {
        String enhancedDetails = details + " (Size: " + formatFileSize(fileSize) + ")";
        logStep(processId, stepName, enhancedDetails);
        
        // Track size changes
        ActiveProcess process = activeProcesses.get(processId);
        if (process != null) {
            process.trackSizeChange(stepName, fileSize);
        }
    }
    
    /**
     * Log step with timing information
     */
    public void logStepWithTiming(String processId, String stepName, String details, long durationMs) {
        String enhancedDetails = details + " (Duration: " + durationMs + "ms)";
        logStep(processId, stepName, enhancedDetails);
        
        // Track performance
        ActiveProcess process = activeProcesses.get(processId);
        if (process != null) {
            process.trackPerformance(stepName, durationMs);
        }
    }
    
    /**
     * Log validation results for the process
     */
    public void logValidationResults(String processId, ApkValidator.ValidationResult validation) {
        ActiveProcess process = activeProcesses.get(processId);
        if (process == null) return;
        
        process.validationResult = validation;
        
        String status = validation.isValid ? "PASSED" : "FAILED";
        String details = String.format("Validation %s - Size: %s, Entries: %d, Issues: %d", 
                                     status, formatFileSize(validation.fileSize), 
                                     validation.totalEntries, validation.issues.size());
        
        logStep(processId, "APK Validation", details);
        
        if (!validation.isValid) {
            for (String issue : validation.issues) {
                LogUtils.logError("Validation issue: " + issue);
            }
        }
    }
    
    /**
     * Log patching results for the process
     */
    public void logPatchingResults(String processId, ApkPatcher.PatchResult patchResult) {
        ActiveProcess process = activeProcesses.get(processId);
        if (process == null) return;
        
        process.patchResult = patchResult;
        
        String status = patchResult.success ? "SUCCESS" : "FAILED";
        String details = String.format("Patching %s - Added: %d files, Modified: %d files, Size change: %s", 
                                     status, patchResult.addedFiles, patchResult.modifiedFiles,
                                     formatFileSize(patchResult.patchedSize - patchResult.originalSize));
        
        logStep(processId, "APK Patching", details);
        
        if (!patchResult.success && patchResult.errorMessage != null) {
            LogUtils.logError("Patching error: " + patchResult.errorMessage);
        }
    }
    
    /**
     * Complete a process with final results
     */
    public void completeProcess(String processId, boolean success, String result) {
        ActiveProcess process = activeProcesses.get(processId);
        if (process == null) {
            LogUtils.logDebug("Process not found for completion: " + processId);
            return;
        }
        
        process.complete(success, result);
        
        LogUtils.logApkProcessComplete(success, result);
        LogUtils.logDebug(String.format("Process completed: %s (%s, %dms total)", 
                                       processId, success ? "SUCCESS" : "FAILED", 
                                       process.getTotalDuration()));
        
        // Notify listeners
        notifyListeners(listener -> listener.onProcessCompleted(process));
        
        // Remove from active processes
        activeProcesses.remove(processId);
    }
    
    /**
     * Get detailed process report
     */
    public String getProcessReport(String processId) {
        ActiveProcess process = activeProcesses.get(processId);
        if (process == null) {
            return "Process not found: " + processId;
        }
        
        return process.generateDetailedReport();
    }
    
    /**
     * Get summary of all active processes
     */
    public String getActiveProcessesSummary() {
        if (activeProcesses.isEmpty()) {
            return "No active APK processes";
        }
        
        StringBuilder summary = new StringBuilder();
        summary.append("=== Active APK Processes ===\n");
        summary.append("Total: ").append(activeProcesses.size()).append("\n\n");
        
        for (ActiveProcess process : activeProcesses.values()) {
            summary.append(process.getSummary()).append("\n");
        }
        
        return summary.toString();
    }
    
    /**
     * Get performance statistics
     */
    public ProcessStatistics getStatistics() {
        ProcessStatistics stats = new ProcessStatistics();
        
        for (ActiveProcess process : activeProcesses.values()) {
            stats.totalProcesses++;
            stats.totalSteps += process.steps.size();
            
            if (process.isCompleted) {
                if (process.success) {
                    stats.successfulProcesses++;
                } else {
                    stats.failedProcesses++;
                }
                
                stats.totalDuration += process.getTotalDuration();
                
                // Track step performance
                for (ProcessStep step : process.steps) {
                    stats.stepPerformance.merge(step.name, step.getElapsedTime(), Long::sum);
                }
            }
        }
        
        return stats;
    }
    
    /**
     * Kill a running process (if possible)
     */
    public boolean killProcess(String processId) {
        ActiveProcess process = activeProcesses.get(processId);
        if (process == null) {
            return false;
        }
        
        process.killed = true;
        logStep(processId, "Process Killed", "Process terminated by user");
        completeProcess(processId, false, "Process killed by user");
        
        LogUtils.logUser("Killed APK process: " + processId);
        return true;
    }
    
    /**
     * Clean up completed processes older than specified time
     */
    public int cleanupOldProcesses(long maxAgeMs) {
        int cleaned = 0;
        long cutoff = System.currentTimeMillis() - maxAgeMs;
        
        Iterator<Map.Entry<String, ActiveProcess>> iterator = activeProcesses.entrySet().iterator();
        while (iterator.hasNext()) {
            Map.Entry<String, ActiveProcess> entry = iterator.next();
            ActiveProcess process = entry.getValue();
            
            if (process.isCompleted && process.startTime < cutoff) {
                iterator.remove();
                cleaned++;
            }
        }
        
        LogUtils.logDebug("Cleaned up " + cleaned + " old APK processes");
        return cleaned;
    }
    
    /**
     * Add a process listener for real-time updates
     */
    public void addProcessListener(ProcessListener listener) {
        synchronized (listeners) {
            listeners.add(listener);
        }
    }
    
    /**
     * Remove a process listener
     */
    public void removeProcessListener(ProcessListener listener) {
        synchronized (listeners) {
            listeners.remove(listener);
        }
    }
    
    // === PRIVATE HELPER METHODS ===
    
    private void notifyListeners(ListenerAction action) {
        synchronized (listeners) {
            for (ProcessListener listener : listeners) {
                try {
                    action.execute(listener);
                } catch (Exception e) {
                    LogUtils.logError("Process listener error", e);
                }
            }
        }
    }
    
    private String formatFileSize(long bytes) {
        if (bytes < 1024) return bytes + " B";
        if (bytes < 1024 * 1024) return String.format("%.1f KB", bytes / 1024.0);
        return String.format("%.1f MB", bytes / (1024.0 * 1024.0));
    }
    
    // === INTERFACES AND CLASSES ===
    
    @FunctionalInterface
    private interface ListenerAction {
        void execute(ProcessListener listener);
    }
    
    public interface ProcessListener {
        void onProcessStarted(ActiveProcess process);
        void onStepCompleted(ActiveProcess process, ProcessStep step);
        void onProcessCompleted(ActiveProcess process);
    }
    
    public static class ActiveProcess {
        public final String processId;
        public final String operation;
        public final File inputApk;
        public final File outputApk;
        public final long startTime;
        public final List<ProcessStep> steps = new ArrayList<>();
        public final Map<String, Long> sizeChanges = new HashMap<>();
        public final Map<String, Long> performanceMetrics = new HashMap<>();
        
        public boolean isCompleted = false;
        public boolean success = false;
        public boolean killed = false;
        public String result;
        public long completionTime;
        public ApkValidator.ValidationResult validationResult;
        public ApkPatcher.PatchResult patchResult;
        
        public ActiveProcess(String processId, String operation, File inputApk, File outputApk) {
            this.processId = processId;
            this.operation = operation;
            this.inputApk = inputApk;
            this.outputApk = outputApk;
            this.startTime = System.currentTimeMillis();
        }
        
        public void addStep(ProcessStep step) {
            synchronized (steps) {
                steps.add(step);
            }
        }
        
        public void complete(boolean success, String result) {
            this.isCompleted = true;
            this.success = success;
            this.result = result;
            this.completionTime = System.currentTimeMillis();
        }
        
        public void trackSizeChange(String stepName, long size) {
            sizeChanges.put(stepName, size);
        }
        
        public void trackPerformance(String stepName, long durationMs) {
            performanceMetrics.put(stepName, durationMs);
        }
        
        public long getTotalDuration() {
            long endTime = isCompleted ? completionTime : System.currentTimeMillis();
            return endTime - startTime;
        }
        
        public String getSummary() {
            return String.format("[%s] %s: %s (%d steps, %dms)", 
                               processId, operation, 
                               isCompleted ? (success ? "SUCCESS" : "FAILED") : "RUNNING",
                               steps.size(), getTotalDuration());
        }
        
        public String generateDetailedReport() {
            StringBuilder report = new StringBuilder();
            
            report.append("=== APK Process Report ===\n");
            report.append("Process ID: ").append(processId).append("\n");
            report.append("Operation: ").append(operation).append("\n");
            report.append("Input APK: ").append(inputApk != null ? inputApk.getName() : "None").append("\n");
            report.append("Output APK: ").append(outputApk != null ? outputApk.getName() : "None").append("\n");
            report.append("Started: ").append(new Date(startTime)).append("\n");
            
            if (isCompleted) {
                report.append("Completed: ").append(new Date(completionTime)).append("\n");
                report.append("Duration: ").append(getTotalDuration()).append("ms\n");
                report.append("Status: ").append(success ? "SUCCESS" : "FAILED").append("\n");
                if (result != null) {
                    report.append("Result: ").append(result).append("\n");
                }
            } else {
                report.append("Status: RUNNING (").append(getTotalDuration()).append("ms elapsed)\n");
            }
            
            if (killed) {
                report.append("NOTE: Process was killed\n");
            }
            
            report.append("\n=== Process Steps ===\n");
            synchronized (steps) {
                if (steps.isEmpty()) {
                    report.append("No steps recorded\n");
                } else {
                    for (int i = 0; i < steps.size(); i++) {
                        ProcessStep step = steps.get(i);
                        report.append(String.format("Step %d: %s\n", i + 1, step.getDetailedString()));
                        if (step.details != null) {
                            report.append("  Details: ").append(step.details).append("\n");
                        }
                        report.append("  Elapsed: ").append(step.getElapsedTime()).append("ms\n");
                        
                        // Add performance metrics if available
                        Long performance = performanceMetrics.get(step.name);
                        if (performance != null) {
                            report.append("  Duration: ").append(performance).append("ms\n");
                        }
                        
                        // Add size information if available
                        Long size = sizeChanges.get(step.name);
                        if (size != null) {
                            report.append("  File Size: ").append(formatSize(size)).append("\n");
                        }
                        
                        report.append("\n");
                    }
                }
            }
            
            // Add validation results
            if (validationResult != null) {
                report.append("=== Validation Results ===\n");
                report.append("Valid: ").append(validationResult.isValid).append("\n");
                report.append("File Size: ").append(formatSize(validationResult.fileSize)).append("\n");
                report.append("ZIP Entries: ").append(validationResult.totalEntries).append("\n");
                if (validationResult.packageName != null) {
                    report.append("Package: ").append(validationResult.packageName).append("\n");
                }
                if (!validationResult.issues.isEmpty()) {
                    report.append("Issues:\n");
                    for (String issue : validationResult.issues) {
                        report.append("  - ").append(issue).append("\n");
                    }
                }
                report.append("\n");
            }
            
            // Add patching results
            if (patchResult != null) {
                report.append("=== Patching Results ===\n");
                report.append("Success: ").append(patchResult.success).append("\n");
                report.append("Original Size: ").append(formatSize(patchResult.originalSize)).append("\n");
                report.append("Patched Size: ").append(formatSize(patchResult.patchedSize)).append("\n");
                report.append("Size Change: ").append(formatSize(patchResult.patchedSize - patchResult.originalSize)).append("\n");
                report.append("Files Added: ").append(patchResult.addedFiles).append("\n");
                report.append("Files Modified: ").append(patchResult.modifiedFiles).append("\n");
                
                if (!patchResult.injectedFiles.isEmpty()) {
                    report.append("Injected Files:\n");
                    for (String file : patchResult.injectedFiles) {
                        report.append("  + ").append(file).append("\n");
                    }
                }
                
                if (patchResult.errorMessage != null) {
                    report.append("Error: ").append(patchResult.errorMessage).append("\n");
                }
                report.append("\n");
            }
            
            return report.toString();
        }
        
        private String formatSize(long bytes) {
            if (bytes < 1024) return bytes + " B";
            if (bytes < 1024 * 1024) return String.format("%.1f KB", bytes / 1024.0);
            return String.format("%.1f MB", bytes / (1024.0 * 1024.0));
        }
    }
    
    public static class ProcessStep {
        public final String name;
        public final String details;
        public final long timestamp;
        public final long startTime;
        
        public ProcessStep(String name, String details) {
            this.name = name;
            this.details = details;
            this.timestamp = System.currentTimeMillis();
            this.startTime = System.currentTimeMillis();
        }
        
        public long getElapsedTime() {
            return timestamp - startTime;
        }
        
        public String getDetailedString() {
            return String.format("[%s] %s", 
                               new java.text.SimpleDateFormat("HH:mm:ss.SSS", Locale.getDefault()).format(new Date(timestamp)),
                               name);
        }
    }
    
    public static class ProcessStatistics {
        public int totalProcesses = 0;
        public int successfulProcesses = 0;
        public int failedProcesses = 0;
        public int totalSteps = 0;
        public long totalDuration = 0;
        public Map<String, Long> stepPerformance = new HashMap<>();
        
        public double getSuccessRate() {
            int completed = successfulProcesses + failedProcesses;
            return completed > 0 ? (double) successfulProcesses / completed * 100 : 0;
        }
        
        public long getAverageDuration() {
            return totalProcesses > 0 ? totalDuration / totalProcesses : 0;
        }
        
        @Override
        public String toString() {
            return String.format("Stats[Processes: %d, Success: %.1f%%, Avg Duration: %dms]",
                               totalProcesses, getSuccessRate(), getAverageDuration());
        }
        
        public String getDetailedString() {
            StringBuilder sb = new StringBuilder();
            sb.append("=== APK Process Statistics ===\n");
            sb.append("Total Processes: ").append(totalProcesses).append("\n");
            sb.append("Successful: ").append(successfulProcesses).append("\n");
            sb.append("Failed: ").append(failedProcesses).append("\n");
            sb.append("Success Rate: ").append(String.format("%.1f%%", getSuccessRate())).append("\n");
            sb.append("Total Steps: ").append(totalSteps).append("\n");
            sb.append("Average Duration: ").append(getAverageDuration()).append("ms\n");
            
            if (!stepPerformance.isEmpty()) {
                sb.append("\nStep Performance:\n");
                for (Map.Entry<String, Long> entry : stepPerformance.entrySet()) {
                    sb.append("  ").append(entry.getKey()).append(": ").append(entry.getValue()).append("ms\n");
                }
            }
            
            return sb.toString();
        }
    }
}

/ModLoader/app/src/main/java/com/modloader/loader/debugger/MelonLoaderDebugger.java

// File: MelonLoaderDebugger.java - Advanced MelonLoader integration debugging
// Path: app/src/main/java/com/modloader/loader/debug/MelonLoaderDebugger.java

package com.modloader.loader.debug;

import android.content.Context;
import com.modloader.util.LogUtils;
import com.modloader.util.PathManager;
import com.modloader.loader.MelonLoaderManager;
import java.io.*;
import java.text.SimpleDateFormat;
import java.util.*;

/**
 * MelonLoaderDebugger - Advanced debugging for MelonLoader integration issues
 * Single responsibility: Diagnose and debug MelonLoader installation and patching problems
 */
public class MelonLoaderDebugger {
    private static final String TAG = "MLDebugger";
    private final Context context;
    private final String gamePackage;
    private final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS", Locale.getDefault());
    
    public MelonLoaderDebugger(Context context, String gamePackage) {
        this.context = context;
        this.gamePackage = gamePackage != null ? gamePackage : MelonLoaderManager.TERRARIA_PACKAGE;
    }
    
    /**
     * Comprehensive MelonLoader diagnosis
     */
    public DiagnosisReport runFullDiagnosis() {
        LogUtils.logUser("🔍 Starting comprehensive MelonLoader diagnosis...");
        
        DiagnosisReport report = new DiagnosisReport();
        report.gamePackage = gamePackage;
        report.timestamp = new Date();
        
        try {
            // Step 1: Directory structure analysis
            LogUtils.logApkProcessStep("Diagnosis", "Checking directory structure");
            report.directoryCheck = checkDirectoryStructure();
            
            // Step 2: MelonLoader files validation
            LogUtils.logApkProcessStep("Diagnosis", "Validating MelonLoader files");
            report.filesCheck = validateMelonLoaderFiles();
            
            // Step 3: Dependencies analysis
            LogUtils.logApkProcessStep("Diagnosis", "Analyzing dependencies");
            report.dependenciesCheck = checkDependencies();
            
            // Step 4: Architecture compatibility
            LogUtils.logApkProcessStep("Diagnosis", "Checking architecture compatibility");
            report.architectureCheck = checkArchitectureCompatibility();
            
            // Step 5: Permissions analysis
            LogUtils.logApkProcessStep("Diagnosis", "Checking permissions");
            report.permissionsCheck = checkPermissions();
            
            // Step 6: Previous installation analysis
            LogUtils.logApkProcessStep("Diagnosis", "Analyzing previous installations");
            report.previousInstallCheck = checkPreviousInstallations();
            
            // Overall health assessment
            report.overallHealth = calculateOverallHealth(report);
            
            LogUtils.logUser("✅ MelonLoader diagnosis completed");
            LogUtils.logUser("🎯 Overall health: " + report.overallHealth.getDisplayName());
            
        } catch (Exception e) {
            LogUtils.logError("MelonLoader diagnosis failed", e);
            report.criticalError = e.getMessage();
        }
        
        return report;
    }
    
    /**
     * Quick health check for MelonLoader integration
     */
    public HealthStatus quickHealthCheck() {
        try {
            // Check essential components quickly
            File baseDir = PathManager.getGameBaseDir(context, gamePackage);
            File melonDir = PathManager.getMelonLoaderDir(context, gamePackage);
            File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
            
            if (baseDir == null || !baseDir.exists()) {
                return HealthStatus.CRITICAL;
            }
            
            if (melonDir == null || !melonDir.exists()) {
                return HealthStatus.NOT_INSTALLED;
            }
            
            if (dllModsDir == null || !dllModsDir.exists()) {
                return HealthStatus.DEGRADED;
            }
            
            // Check for core files
            File net8Dir = new File(melonDir, "net8");
            File net35Dir = new File(melonDir, "net35");
            
            if (net8Dir.exists() || net35Dir.exists()) {
                return HealthStatus.HEALTHY;
            }
            
            return HealthStatus.DEGRADED;
            
        } catch (Exception e) {
            LogUtils.logError("Quick health check failed", e);
            return HealthStatus.CRITICAL;
        }
    }
    
    /**
     * Generate detailed diagnosis report text
     */
    public String generateDiagnosisReport(DiagnosisReport report) {
        StringBuilder sb = new StringBuilder();
        
        sb.append("=== MelonLoader Diagnosis Report ===\n");
        sb.append("Game Package: ").append(report.gamePackage).append("\n");
        sb.append("Diagnosis Time: ").append(dateFormat.format(report.timestamp)).append("\n");
        sb.append("Overall Health: ").append(report.overallHealth.getDisplayName()).append("\n\n");
        
        if (report.criticalError != null) {
            sb.append("CRITICAL ERROR: ").append(report.criticalError).append("\n\n");
        }
        
        // Directory structure
        sb.append("=== Directory Structure ===\n");
        appendCheckResult(sb, report.directoryCheck);
        
        // Files validation
        sb.append("=== MelonLoader Files ===\n");
        appendCheckResult(sb, report.filesCheck);
        
        // Dependencies
        sb.append("=== Dependencies ===\n");
        appendCheckResult(sb, report.dependenciesCheck);
        
        // Architecture
        sb.append("=== Architecture Compatibility ===\n");
        appendCheckResult(sb, report.architectureCheck);
        
        // Permissions
        sb.append("=== Permissions ===\n");
        appendCheckResult(sb, report.permissionsCheck);
        
        // Previous installations
        sb.append("=== Previous Installations ===\n");
        appendCheckResult(sb, report.previousInstallCheck);
        
        // Recommendations
        sb.append("=== Recommendations ===\n");
        appendRecommendations(sb, report);
        
        return sb.toString();
    }
    
    /**
     * Auto-repair common MelonLoader issues
     */
    public RepairResult attemptAutoRepair() {
        LogUtils.logUser("🔧 Attempting MelonLoader auto-repair...");
        
        RepairResult result = new RepairResult();
        
        try {
            // Repair directory structure
            LogUtils.logApkProcessStep("Auto-repair", "Fixing directory structure");
            if (repairDirectoryStructure()) {
                result.repairedIssues.add("Directory structure recreated");
                result.successCount++;
            }
            
            // Repair permissions
            LogUtils.logApkProcessStep("Auto-repair", "Fixing permissions");
            if (repairPermissions()) {
                result.repairedIssues.add("Permissions fixed");
                result.successCount++;
            }
            
            // Clean up corrupted files
            LogUtils.logApkProcessStep("Auto-repair", "Cleaning corrupted files");
            if (cleanupCorruptedFiles()) {
                result.repairedIssues.add("Corrupted files cleaned");
                result.successCount++;
            }
            
            // Verify repair success
            HealthStatus healthAfterRepair = quickHealthCheck();
            result.success = healthAfterRepair != HealthStatus.CRITICAL;
            result.finalHealth = healthAfterRepair;
            
            if (result.success) {
                LogUtils.logUser("✅ Auto-repair completed successfully");
                LogUtils.logUser("🎯 Final health: " + healthAfterRepair.getDisplayName());
            } else {
                LogUtils.logUser("⚠️ Auto-repair completed with issues");
            }
            
        } catch (Exception e) {
            LogUtils.logError("Auto-repair failed", e);
            result.error = e.getMessage();
        }
        
        return result;
    }
    
    /**
     * Get installation troubleshooting steps
     */
    public List<TroubleshootingStep> getTroubleshootingSteps() {
        List<TroubleshootingStep> steps = new ArrayList<>();
        HealthStatus health = quickHealthCheck();
        
        switch (health) {
            case NOT_INSTALLED:
                steps.add(new TroubleshootingStep("Install MelonLoader", 
                    "Use the 'Automated Installation' option to download and install MelonLoader files", 
                    TroubleshootingStep.Priority.HIGH));
                break;
                
            case CRITICAL:
                steps.add(new TroubleshootingStep("Check Storage Space", 
                    "Ensure you have at least 100MB free space", 
                    TroubleshootingStep.Priority.HIGH));
                steps.add(new TroubleshootingStep("Check Permissions", 
                    "Grant storage permissions to the app", 
                    TroubleshootingStep.Priority.HIGH));
                break;
                
            case DEGRADED:
                steps.add(new TroubleshootingStep("Reinstall MelonLoader", 
                    "Try reinstalling MelonLoader to fix missing components", 
                    TroubleshootingStep.Priority.MEDIUM));
                steps.add(new TroubleshootingStep("Clear App Data", 
                    "Clear app data and restart the installation process", 
                    TroubleshootingStep.Priority.LOW));
                break;
                
            case HEALTHY:
                steps.add(new TroubleshootingStep("Check APK Compatibility", 
                    "Ensure your Terraria APK is compatible with MelonLoader", 
                    TroubleshootingStep.Priority.LOW));
                break;
        }
        
        // Common troubleshooting steps
        steps.add(new TroubleshootingStep("Restart App", 
            "Close and restart UniversalLoader", 
            TroubleshootingStep.Priority.LOW));
        steps.add(new TroubleshootingStep("Check Logs", 
            "Review error logs for specific issues", 
            TroubleshootingStep.Priority.LOW));
        
        return steps;
    }
    
    // === PRIVATE DIAGNOSTIC METHODS ===
    
    private CheckResult checkDirectoryStructure() {
        CheckResult result = new CheckResult("Directory Structure");
        
        try {
            File baseDir = PathManager.getGameBaseDir(context, gamePackage);
            File melonDir = PathManager.getMelonLoaderDir(context, gamePackage);
            File dllModsDir = PathManager.getDllModsDir(context, gamePackage);
            File dexModsDir = PathManager.getDexModsDir(context, gamePackage);
            
            // Check base directory
            if (baseDir == null) {
                result.issues.add("Base directory path is null");
                result.status = CheckResult.Status.FAILED;
            } else if (!baseDir.exists()) {
                result.issues.add("Base directory does not exist: " + baseDir.getAbsolutePath());
                result.status = CheckResult.Status.FAILED;
            } else {
                result.details.add("Base directory: " + baseDir.getAbsolutePath());
                result.passedChecks++;
            }
            
            // Check MelonLoader directory
            if (melonDir == null) {
                result.issues.add("MelonLoader directory path is null");
            } else if (!melonDir.exists()) {
                result.issues.add("MelonLoader directory missing");
            } else {
                result.details.add("MelonLoader directory: " + melonDir.getAbsolutePath());
                result.passedChecks++;
            }
            
            // Check mods directories
            if (dllModsDir != null && dllModsDir.exists()) {
                result.details.add("DLL mods directory: " + dllModsDir.getAbsolutePath());
                result.passedChecks++;
            } else {
                result.issues.add("DLL mods directory missing");
            }
            
            if (dexModsDir != null && dexModsDir.exists()) {
                result.details.add("DEX mods directory: " + dexModsDir.getAbsolutePath());
                result.passedChecks++;
            } else {
                result.issues.add("DEX mods directory missing");
            }
            
            result.totalChecks = 4;
            
            if (result.passedChecks == result.totalChecks) {
                result.status = CheckResult.Status.PASSED;
            } else if (result.passedChecks > 0) {
                result.status = CheckResult.Status.WARNING;
            } else {
                result.status = CheckResult.Status.FAILED;
            }
            
        } catch (Exception e) {
            result.issues.add("Directory check error: " + e.getMessage());
            result.status = CheckResult.Status.FAILED;
        }
        
        return result;
    }
    
    private CheckResult validateMelonLoaderFiles() {
        CheckResult result = new CheckResult("MelonLoader Files");
        
        try {
            File melonDir = PathManager.getMelonLoaderDir(context, gamePackage);
            if (melonDir == null || !melonDir.exists()) {
                result.issues.add("MelonLoader directory not found");
                result.status = CheckResult.Status.FAILED;
                return result;
            }
            
            // Check runtime directories
            File net8Dir = new File(melonDir, "net8");
            File net35Dir = new File(melonDir, "net35");
            
            boolean hasNet8 = checkRuntimeFiles(net8Dir, "NET8", result);
            boolean hasNet35 = checkRuntimeFiles(net35Dir, "NET35", result);
            
            if (hasNet8 || hasNet35) {
                result.details.add("Runtime support: " + 
                    (hasNet8 ? "NET8 " : "") + (hasNet35 ? "NET35" : ""));
                result.passedChecks++;
            } else {
                result.issues.add("No valid runtime found (NET8 or NET35)");
            }
            
            // Check dependencies
            File depsDir = new File(melonDir, "Dependencies");
            if (depsDir.exists()) {
                result.details.add("Dependencies directory found");
                result.passedChecks++;
                
                File supportDir = new File(depsDir, "SupportModules");
                if (supportDir.exists()) {
                    result.details.add("Support modules found");
                    result.passedChecks++;
                }
            } else {
                result.issues.add("Dependencies directory missing");
            }
            
            result.totalChecks = 3;
            
            if (result.passedChecks == result.totalChecks) {
                result.status = CheckResult.Status.PASSED;
            } else if (result.passedChecks > 0) {
                result.status = CheckResult.Status.WARNING;
            } else {
                result.status = CheckResult.Status.FAILED;
            }
            
        } catch (Exception e) {
            result.issues.add("File validation error: " + e.getMessage());
            result.status = CheckResult.Status.FAILED;
        }
        
        return result;
    }
    
    private boolean checkRuntimeFiles(File runtimeDir, String runtimeName, CheckResult result) {
        if (!runtimeDir.exists()) {
            return false;
        }
        
        // Check for essential runtime files
        String[] essentialFiles = {
            "MelonLoader.dll",
            "0Harmony.dll"
        };
        
        int foundFiles = 0;
        for (String filename : essentialFiles) {
            File file = new File(runtimeDir, filename);
            if (file.exists() && file.length() > 0) {
                foundFiles++;
            }
        }
        
        if (foundFiles >= essentialFiles.length / 2) {
            result.details.add(runtimeName + " runtime files: " + foundFiles + "/" + essentialFiles.length);
            return true;
        }
        
        return false;
    }
    
    private CheckResult checkDependencies() {
        CheckResult result = new CheckResult("Dependencies");
        
        try {
            File melonDir = PathManager.getMelonLoaderDir(context, gamePackage);
            if (melonDir == null || !melonDir.exists()) {
                result.issues.add("MelonLoader directory not found");
                result.status = CheckResult.Status.FAILED;
                return result;
            }
            
            File depsDir = new File(melonDir, "Dependencies");
            if (!depsDir.exists()) {
                result.issues.add("Dependencies directory not found");
                result.status = CheckResult.Status.FAILED;
                return result;
            }
            
            // Check key dependency directories
            String[] depDirs = {
                "SupportModules",
                "Il2CppAssemblyGenerator",
                "CompatibilityLayers"
            };
            
            for (String dirName : depDirs) {
                File dir = new File(depsDir, dirName);
                if (dir.exists()) {
                    result.details.add(dirName + ": Found");
                    result.passedChecks++;
                } else {
                    result.issues.add(dirName + ": Missing");
                }
            }
            
            result.totalChecks = depDirs.length;
            
            if (result.passedChecks == result.totalChecks) {
                result.status = CheckResult.Status.PASSED;
            } else if (result.passedChecks > 0) {
                result.status = CheckResult.Status.WARNING;
            } else {
                result.status = CheckResult.Status.FAILED;
            }
            
        } catch (Exception e) {
            result.issues.add("Dependencies check error: " + e.getMessage());
            result.status = CheckResult.Status.FAILED;
        }
        
        return result;
    }
    
    private CheckResult checkArchitectureCompatibility() {
        CheckResult result = new CheckResult("Architecture Compatibility");
        
        try {
            String deviceArch = android.os.Build.SUPPORTED_ABIS[0];
            result.details.add("Device architecture: " + deviceArch);
            
            File melonDir = PathManager.getMelonLoaderDir(context, gamePackage);
            if (melonDir != null && melonDir.exists()) {
                File runtimesDir = new File(melonDir, "Dependencies/Il2CppAssemblyGenerator/runtimes");
                if (runtimesDir.exists()) {
                    // Check for architecture-specific libraries
                    boolean hasArm64 = new File(runtimesDir, "linux-arm64").exists();
                    boolean hasArm = new File(runtimesDir, "linux-arm").exists();
                    
                    result.details.add("ARM64 support: " + hasArm64);
                    result.details.add("ARM support: " + hasArm);
                    
                    if ((deviceArch.contains("arm64") && hasArm64) || 
                        (deviceArch.contains("arm") && hasArm)) {
                        result.status = CheckResult.Status.PASSED;
                        result.details.add("Architecture compatibility: Good");
                    } else {
                        result.status = CheckResult.Status.WARNING;
                        result.issues.add("Native libraries may not match device architecture");
                    }
                } else {
                    result.status = CheckResult.Status.WARNING;
                    result.issues.add("Native libraries directory not found");
                }
            } else {
                result.status = CheckResult.Status.FAILED;
                result.issues.add("MelonLoader not installed");
            }
            
        } catch (Exception e) {
            result.issues.add("Architecture check error: " + e.getMessage());
            result.status = CheckResult.Status.FAILED;
        }
        
        return result;
    }
    
    private CheckResult checkPermissions() {
        CheckResult result = new CheckResult("Permissions");
        
        try {
            File baseDir = PathManager.getGameBaseDir(context, gamePackage);
            
            // Test write permission
            File testFile = new File(baseDir != null ? baseDir : context.getExternalFilesDir(null), 
                                   "permission_test.tmp");
            
            try {
                if (testFile.createNewFile()) {
                    result.details.add("Write permission: Available");
                    result.passedChecks++;
                    testFile.delete();
                } else {
                    result.issues.add("Cannot create test file");
                }
            } catch (Exception e) {
                result.issues.add("Write permission test failed: " + e.getMessage());
            }
            
            // Check directory permissions
            if (baseDir != null) {
                result.details.add("Base directory readable: " + baseDir.canRead());
                result.details.add("Base directory writable: " + baseDir.canWrite());
                
                if (baseDir.canRead() && baseDir.canWrite()) {
                    result.passedChecks++;
                }
            }
            
            result.totalChecks = 2;
            result.status = result.passedChecks == result.totalChecks ? 
                           CheckResult.Status.PASSED : CheckResult.Status.WARNING;
            
        } catch (Exception e) {
            result.issues.add("Permissions check error: " + e.getMessage());
            result.status = CheckResult.Status.FAILED;
        }
        
        return result;
    }
    
    private CheckResult checkPreviousInstallations() {
        CheckResult result = new CheckResult("Previous Installations");
        
        try {
            // Check for backup APKs
            File backupDir = PathManager.getBackupsDir(context, gamePackage);
            if (backupDir != null && backupDir.exists()) {
                File[] backups = backupDir.listFiles((dir, name) -> name.endsWith(".apk"));
                if (backups != null && backups.length > 0) {
                    result.details.add("APK backups found: " + backups.length);
                    result.passedChecks++;
                } else {
                    result.details.add("No APK backups found");
                }
            }
            
            // Check for legacy installations
            File legacyDir = PathManager.getLegacyModsDir(context);
            if (legacyDir.exists()) {
                result.details.add("Legacy installation detected");
                result.issues.add("Legacy installation may cause conflicts");
            }
            
            result.totalChecks = 1;
            result.status = CheckResult.Status.PASSED; // This is informational
            
        } catch (Exception e) {
            result.issues.add("Previous installation check error: " + e.getMessage());
            result.status = CheckResult.Status.WARNING;
        }
        
        return result;
    }
    
    // === REPAIR METHODS ===
    
    private boolean repairDirectoryStructure() {
        try {
            return PathManager.initializeGameDirectories(context, gamePackage);
        } catch (Exception e) {
            LogUtils.logError("Directory structure repair failed", e);
            return false;
        }
    }
    
    private boolean repairPermissions() {
        try {
            File baseDir = PathManager.getGameBaseDir(context, gamePackage);
            if (baseDir != null && baseDir.exists()) {
                // Try to fix directory permissions (limited on Android)
                baseDir.setReadable(true, false);
                baseDir.setWritable(true, false);
                return true;
            }
        } catch (Exception e) {
            LogUtils.logError("Permissions repair failed", e);
        }
        return false;
    }
    
    private boolean cleanupCorruptedFiles() {
        try {
            // Remove zero-byte files that might be corrupted
            File melonDir = PathManager.getMelonLoaderDir(context, gamePackage);
            if (melonDir != null && melonDir.exists()) {
                return cleanupCorruptedFilesInDirectory(melonDir);
            }
        } catch (Exception e) {
            LogUtils.logError("Corrupted files cleanup failed", e);
        }
        return false;
    }
    
    private boolean cleanupCorruptedFilesInDirectory(File directory) {
        boolean cleaned = false;
        File[] files = directory.listFiles();
        
        if (files != null) {
            for (File file : files) {
                if (file.isDirectory()) {
                    cleaned |= cleanupCorruptedFilesInDirectory(file);
                } else if (file.length() == 0) {
                    // Remove zero-byte files
                    if (file.delete()) {
                        LogUtils.logDebug("Removed corrupted file: " + file.getName());
                        cleaned = true;
                    }
                }
            }
        }
        
        return cleaned;
    }
    
    // === HELPER METHODS ===
    
    private HealthStatus calculateOverallHealth(DiagnosisReport report) {
        int passedChecks = 0;
        int totalChecks = 0;
        int failedChecks = 0;
        
        CheckResult[] checks = {
            report.directoryCheck, report.filesCheck, report.dependenciesCheck,
            report.architectureCheck, report.permissionsCheck, report.previousInstallCheck
        };
        
        for (CheckResult check : checks) {
            if (check != null) {
                totalChecks++;
                if (check.status == CheckResult.Status.PASSED) {
                    passedChecks++;
                } else if (check.status == CheckResult.Status.FAILED) {
                    failedChecks++;
                }
            }
        }
        
        if (report.criticalError != null) {
            return HealthStatus.CRITICAL;
        }
        
        if (failedChecks > totalChecks / 2) {
            return HealthStatus.CRITICAL;
        } else if (passedChecks == totalChecks) {
            return HealthStatus.HEALTHY;
        } else if (passedChecks > totalChecks / 2) {
            return HealthStatus.DEGRADED;
        } else {
            return HealthStatus.NOT_INSTALLED;
        }
    }
    
    private void appendCheckResult(StringBuilder sb, CheckResult check) {
        if (check == null) {
            sb.append("Check not performed\n\n");
            return;
        }
        
        sb.append("Status: ").append(check.status.getDisplayName()).append("\n");
        sb.append("Passed: ").append(check.passedChecks).append("/").append(check.totalChecks).append("\n");
        
        if (!check.details.isEmpty()) {
            sb.append("Details:\n");
            for (String detail : check.details) {
                sb.append("  ✓ ").append(detail).append("\n");
            }
        }
        
        if (!check.issues.isEmpty()) {
            sb.append("Issues:\n");
            for (String issue : check.issues) {
                sb.append("  ❌ ").append(issue).append("\n");
            }
        }
        
        sb.append("\n");
    }
    
    private void appendRecommendations(StringBuilder sb, DiagnosisReport report) {
        List<String> recommendations = new ArrayList<>();
        
        if (report.overallHealth == HealthStatus.NOT_INSTALLED) {
            recommendations.add("Install MelonLoader using the automated installation option");
        }
        
        if (report.directoryCheck != null && report.directoryCheck.status == CheckResult.Status.FAILED) {
            recommendations.add("Create missing directories using the 'Initialize Structure' option");
        }
        
        if (report.filesCheck != null && report.filesCheck.status != CheckResult.Status.PASSED) {
            recommendations.add("Reinstall MelonLoader files");
        }
        
        if (report.permissionsCheck != null && report.permissionsCheck.status != CheckResult.Status.PASSED) {
            recommendations.add("Grant storage permissions to the app in Android settings");
        }
        
        if (recommendations.isEmpty()) {
            recommendations.add("No specific recommendations - system appears healthy");
        }
        
        for (String rec : recommendations) {
            sb.append("• ").append(rec).append("\n");
        }
    }
    
    // === ENUMS AND CLASSES ===
    
    public enum HealthStatus {
        HEALTHY("Healthy", "✅"),
        DEGRADED("Degraded", "⚠️"),
        NOT_INSTALLED("Not Installed", "❌"),
        CRITICAL("Critical", "🔥");
        
        private final String displayName;
        private final String icon;
        
        HealthStatus(String displayName, String icon) {
            this.displayName = displayName;
            this.icon = icon;
        }
        
        public String getDisplayName() {
            return icon + " " + displayName;
        }
    }
    
    public static class DiagnosisReport {
        public String gamePackage;
        public Date timestamp;
        public HealthStatus overallHealth;
        public String criticalError;
        
        public CheckResult directoryCheck;
        public CheckResult filesCheck;
        public CheckResult dependenciesCheck;
        public CheckResult architectureCheck;
        public CheckResult permissionsCheck;
        public CheckResult previousInstallCheck;
    }
    
    public static class CheckResult {
        public enum Status {
            PASSED("Passed", "✅"),
            WARNING("Warning", "⚠️"),
            FAILED("Failed", "❌");
            
            private final String displayName;
            private final String icon;
            
            Status(String displayName, String icon) {
                this.displayName = displayName;
                this.icon = icon;
            }
            
            public String getDisplayName() {
                return icon + " " + displayName;
            }
        }
        
        public String checkName;
        public Status status = Status.FAILED;
        public List<String> details = new ArrayList<>();
        public List<String> issues = new ArrayList<>();
        public int passedChecks = 0;
        public int totalChecks = 0;
        
        public CheckResult(String checkName) {
            this.checkName = checkName;
        }
    }
    
    public static class RepairResult {
        public boolean success = false;
        public int successCount = 0;
        public List<String> repairedIssues = new ArrayList<>();
        public String error = null;
        public HealthStatus finalHealth = HealthStatus.CRITICAL;
    }
    
    public static class TroubleshootingStep {
        public enum Priority {
            HIGH, MEDIUM, LOW
        }
        
        public String title;
        public String description;
        public Priority priority;
        
        public TroubleshootingStep(String title, String description, Priority priority) {
            this.title = title;
            this.description = description;
            this.priority = priority;
        }
    }
}

/ModLoader/app/src/main/java/com/modloader/logging/ApkProcessLogger.java

// File: ApkProcessLogger.java - Specialized APK process tracking
// Path: app/src/main/java/com/modloader/logging/ApkProcessLogger.java

package com.modloader.logging;

import android.content.Context;
import java.io.*;
import java.text.SimpleDateFormat;
import java.util.*;

/**
 * ApkProcessLogger - Tracks APK patching, validation, and installation processes
 * Single responsibility: Log detailed APK operation steps and results
 */
public class ApkProcessLogger {
    private static final String APK_LOG_FILE = "APKProcess.log";
    private static final int MAX_PROCESSES = 100;
    
    private final Context context;
    private final File logFile;
    private final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS", Locale.getDefault());
    private final List<ApkProcess> activeProcesses = new ArrayList<>();
    private final List<ApkProcess> completedProcesses = new ArrayList<>();
    
    public ApkProcessLogger(Context context) {
        this.context = context;
        File logDir = new File(context.getExternalFilesDir(null), "ModLoader/com.and.games505.TerrariaPaid/AppLogs");
        logDir.mkdirs();
        this.logFile = new File(logDir, APK_LOG_FILE);
        
        // Load existing processes if log file exists
        loadExistingProcesses();
    }
    
    /**
     * Start tracking a new APK process
     */
    public synchronized String startProcess(String operation, String apkName) {
        String processId = generateProcessId();
        ApkProcess process = new ApkProcess(processId, operation, apkName);
        activeProcesses.add(process);
        
        logToFile("=== PROCESS STARTED ===");
        logToFile("Process ID: " + processId);
        logToFile("Operation: " + operation);
        logToFile("APK: " + apkName);
        logToFile("Started: " + dateFormat.format(new Date()));
        logToFile("");
        
        return processId;
    }
    
    /**
     * Log a step in the current process
     */
    public synchronized void logStep(String step, String details) {
        ApkProcess currentProcess = getCurrentProcess();
        if (currentProcess == null) {
            // Create anonymous process if none exists
            currentProcess = new ApkProcess(generateProcessId(), "Unknown", "Unknown");
            activeProcesses.add(currentProcess);
        }
        
        ProcessStep processStep = new ProcessStep(step, details);
        currentProcess.addStep(processStep);
        
        logToFile(String.format("[%s] STEP: %s", 
                               dateFormat.format(processStep.timestamp), step));
        if (details != null && !details.isEmpty()) {
            logToFile("  Details: " + details);
        }
    }
    
    /**
     * Complete the current process
     */
    public synchronized void completeProcess(boolean success, String result) {
        ApkProcess currentProcess = getCurrentProcess();
        if (currentProcess == null) return;
        
        currentProcess.complete(success, result);
        activeProcesses.remove(currentProcess);
        completedProcesses.add(currentProcess);
        
        // Keep only recent completed processes
        while (completedProcesses.size() > MAX_PROCESSES) {
            completedProcesses.remove(0);
        }
        
        logToFile("=== PROCESS COMPLETED ===");
        logToFile("Process ID: " + currentProcess.processId);
        logToFile("Status: " + (success ? "SUCCESS" : "FAILED"));
        logToFile("Duration: " + currentProcess.getDurationString());
        if (result != null) {
            logToFile("Result: " + result);
        }
        logToFile("Total Steps: " + currentProcess.steps.size());
        logToFile("");
    }
    
    /**
     * Log APK validation results
     */
    public synchronized void logValidation(String apkName, boolean valid, String details) {
        logToFile("=== APK VALIDATION ===");
        logToFile("APK: " + apkName);
        logToFile("Valid: " + (valid ? "YES" : "NO"));
        logToFile("Timestamp: " + dateFormat.format(new Date()));
        if (details != null) {
            logToFile("Details:");
            String[] lines = details.split("\n");
            for (String line : lines) {
                logToFile("  " + line);
            }
        }
        logToFile("");
    }
    
    /**
     * Get detailed report of current/recent processes
     */
    public synchronized String getProcessReport() {
        StringBuilder report = new StringBuilder();
        
        report.append("=== APK PROCESS REPORT ===\n");
        report.append("Generated: ").append(dateFormat.format(new Date())).append("\n\n");
        
        // Active processes
        if (!activeProcesses.isEmpty()) {
            report.append("ACTIVE PROCESSES (" + activeProcesses.size() + "):\n");
            for (ApkProcess process : activeProcesses) {
                report.append(process.getDetailedReport()).append("\n");
            }
        }
        
        // Recent completed processes (last 10)
        if (!completedProcesses.isEmpty()) {
            report.append("RECENT COMPLETED PROCESSES:\n");
            int start = Math.max(0, completedProcesses.size() - 10);
            for (int i = start; i < completedProcesses.size(); i++) {
                ApkProcess process = completedProcesses.get(i);
                report.append(process.getSummaryReport()).append("\n");
            }
        }
        
        return report.toString();
    }
    
    /**
     * Export APK process logs to a file
     */
    public boolean exportProcessLogs(File outputFile) {
        try (PrintWriter writer = new PrintWriter(new FileWriter(outputFile))) {
            writer.println(getProcessReport());
            
            // Include raw log content
            writer.println("\n=== RAW LOG CONTENT ===\n");
            if (logFile.exists()) {
                try (BufferedReader reader = new BufferedReader(new FileReader(logFile))) {
                    String line;
                    while ((line = reader.readLine()) != null) {
                        writer.println(line);
                    }
                }
            }
            
            return true;
        } catch (Exception e) {
            android.util.Log.e("ApkProcessLogger", "Failed to export process logs", e);
            return false;
        }
    }
    
    /**
     * Clear all process logs
     */
    public synchronized void clearLogs() {
        activeProcesses.clear();
        completedProcesses.clear();
        if (logFile.exists()) {
            logFile.delete();
        }
    }
    
    /**
     * Flush any pending log writes
     */
    public void flush() {
        // File operations are synchronous, no need to flush
    }
    
    // === PRIVATE HELPER METHODS ===
    
    private ApkProcess getCurrentProcess() {
        return activeProcesses.isEmpty() ? null : activeProcesses.get(activeProcesses.size() - 1);
    }
    
    private String generateProcessId() {
        return "APK_" + System.currentTimeMillis() + "_" + (int)(Math.random() * 1000);
    }
    
    private synchronized void logToFile(String message) {
        try (PrintWriter writer = new PrintWriter(new FileWriter(logFile, true))) {
            writer.println(message);
        } catch (Exception e) {
            android.util.Log.e("ApkProcessLogger", "Failed to write to log file", e);
        }
    }
    
    private void loadExistingProcesses() {
        // Implementation could load previous processes from file
        // For now, start fresh each time
    }
    
    // === HELPER CLASSES ===
    
    public static class ApkProcess {
        public final String processId;
        public final String operation;
        public final String apkName;
        public final Date startTime;
        public Date endTime;
        public boolean success;
        public String result;
        public final List<ProcessStep> steps = new ArrayList<>();
        
        public ApkProcess(String processId, String operation, String apkName) {
            this.processId = processId;
            this.operation = operation;
            this.apkName = apkName;
            this.startTime = new Date();
        }
        
        public void addStep(ProcessStep step) {
            steps.add(step);
        }
        
        public void complete(boolean success, String result) {
            this.endTime = new Date();
            this.success = success;
            this.result = result;
        }
        
        public long getDuration() {
            Date end = endTime != null ? endTime : new Date();
            return end.getTime() - startTime.getTime();
        }
        
        public String getDurationString() {
            long duration = getDuration();
            long seconds = duration / 1000;
            long minutes = seconds / 60;
            seconds = seconds % 60;
            
            if (minutes > 0) {
                return minutes + "m " + seconds + "s";
            } else {
                return seconds + "s";
            }
        }
        
        public String getSummaryReport() {
            SimpleDateFormat format = new SimpleDateFormat("HH:mm:ss", Locale.getDefault());
            return String.format("[%s] %s: %s - %s (%s, %d steps)",
                               format.format(startTime),
                               processId,
                               operation,
                               apkName,
                               endTime != null ? (success ? "SUCCESS" : "FAILED") : "RUNNING",
                               steps.size());
        }
        
        public String getDetailedReport() {
            StringBuilder report = new StringBuilder();
            SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss", Locale.getDefault());
            
            report.append("Process: ").append(processId).append("\n");
            report.append("Operation: ").append(operation).append("\n");
            report.append("APK: ").append(apkName).append("\n");
            report.append("Started: ").append(format.format(startTime)).append("\n");
            
            if (endTime != null) {
                report.append("Completed: ").append(format.format(endTime)).append("\n");
                report.append("Duration: ").append(getDurationString()).append("\n");
                report.append("Success: ").append(success).append("\n");
                if (result != null) {
                    report.append("Result: ").append(result).append("\n");
                }
            } else {
                report.append("Status: RUNNING\n");
            }
            
            if (!steps.isEmpty()) {
                report.append("Steps:\n");
                SimpleDateFormat stepFormat = new SimpleDateFormat("HH:mm:ss.SSS", Locale.getDefault());
                for (ProcessStep step : steps) {
                    report.append("  [").append(stepFormat.format(step.timestamp)).append("] ");
                    report.append(step.step);
                    if (step.details != null && !step.details.isEmpty()) {
                        report.append(" - ").append(step.details);
                    }
                    report.append("\n");
                }
            }
            
            return report.toString();
        }
    }
    
    public static class ProcessStep {
        public final String step;
        public final String details;
        public final Date timestamp;
        
        public ProcessStep(String step, String details) {
            this.step = step;
            this.details = details;
            this.timestamp = new Date();
        }
    }
}

/ModLoader/app/src/main/java/com/modloader/logging/BasicLogger.java

package com.modloader.logging;

public class BasicLogger {
}


/ModLoader/app/src/main/java/com/modloader/logging/ErrorLogger.java

// File: ErrorLogger.java - Specialized error and exception tracking
// Path: app/src/main/java/com/modloader/logging/ErrorLogger.java

package com.modloader.logging;

import android.content.Context;
import java.io.*;
import java.text.SimpleDateFormat;
import java.util.*;

/**
 * ErrorLogger - Specialized component for tracking errors and exceptions
 * Single responsibility: Log detailed error information with stack traces
 */
public class ErrorLogger {
    private static final String ERROR_LOG_FILE = "Errors.log";
    private static final String CRASH_LOG_FILE = "Crashes.log";
    private static final int MAX_ERROR_ENTRIES = 500;
    
    private final Context context;
    private final File errorLogFile;
    private final File crashLogFile;
    private final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS", Locale.getDefault());
    private final List<ErrorEntry> recentErrors = new ArrayList<>();
    private final Object errorLock = new Object();
    
    public ErrorLogger(Context context) {
        this.context = context;
        File logDir = new File(context.getExternalFilesDir(null), 
                             "ModLoader/com.and.games505.TerrariaPaid/AppLogs");
        logDir.mkdirs();
        
        this.errorLogFile = new File(logDir, ERROR_LOG_FILE);
        this.crashLogFile = new File(logDir, CRASH_LOG_FILE);
        
        // Set up uncaught exception handler
        setupUncaughtExceptionHandler();
        
        logError("ErrorLogger initialized at " + logDir.getAbsolutePath(), null);
    }
    
    /**
     * Log an error with optional exception
     */
    public void logError(String message, Exception exception) {
        ErrorEntry entry = new ErrorEntry(message, exception);
        
        synchronized (errorLock) {
            // Add to recent errors list
            recentErrors.add(entry);
            if (recentErrors.size() > MAX_ERROR_ENTRIES) {
                recentErrors.remove(0);
            }
            
            // Write to error log file
            writeErrorToFile(entry, errorLogFile);
        }
    }
    
    /**
     * Log a critical error (system crash, major failure)
     */
    public void logCriticalError(String message, Throwable throwable) {
        ErrorEntry entry = new ErrorEntry(message, throwable, true);
        
        synchronized (errorLock) {
            recentErrors.add(entry);
            if (recentErrors.size() > MAX_ERROR_ENTRIES) {
                recentErrors.remove(0);
            }
            
            // Write to both error log and crash log
            writeErrorToFile(entry, errorLogFile);
            writeErrorToFile(entry, crashLogFile);
        }
        
        // Also log to Android system
        android.util.Log.e("ModLoader", "CRITICAL: " + message, throwable);
    }
    
    /**
     * Log APK-specific errors with context
     */
    public void logApkError(String operation, String apkName, String error, Exception exception) {
        String contextualMessage = String.format("APK Operation Failed - %s on %s: %s", 
                                                operation, apkName, error);
        logError(contextualMessage, exception);
    }
    
    /**
     * Log validation errors with details
     */
    public void logValidationError(String item, String issue, String details) {
        String message = String.format("Validation Failed - %s: %s", item, issue);
        if (details != null && !details.isEmpty()) {
            message += " (" + details + ")";
        }
        logError(message, null);
    }
    
    /**
     * Get recent errors (for UI display)
     */
    public List<ErrorEntry> getRecentErrors(int maxCount) {
        synchronized (errorLock) {
            int start = Math.max(0, recentErrors.size() - maxCount);
            return new ArrayList<>(recentErrors.subList(start, recentErrors.size()));
        }
    }
    
    /**
     * Get errors by type/category
     */
    public List<ErrorEntry> getErrorsByType(String keyword) {
        List<ErrorEntry> filtered = new ArrayList<>();
        
        synchronized (errorLock) {
            for (ErrorEntry error : recentErrors) {
                if (error.message != null && error.message.toLowerCase().contains(keyword.toLowerCase())) {
                    filtered.add(error);
                }
            }
        }
        
        return filtered;
    }
    
    /**
     * Get comprehensive error report for debugging
     */
    public String getErrorReport() {
        StringBuilder report = new StringBuilder();
        
        synchronized (errorLock) {
            report.append("=== ModLoader Error Report ===\n");
            report.append("Generated: ").append(dateFormat.format(new Date())).append("\n");
            report.append("Total Errors in Memory: ").append(recentErrors.size()).append("\n");
            
            ErrorStats stats = getErrorStats();
            report.append("Critical Errors: ").append(stats.criticalErrors).append("\n");
            report.append("APK-related Errors: ").append(getErrorsByType("APK").size()).append("\n");
            report.append("Validation Errors: ").append(getErrorsByType("Validation").size()).append("\n");
            report.append("\n");
            
            if (recentErrors.isEmpty()) {
                report.append("No errors recorded in memory.\n");
            } else {
                // Show last 10 errors with full details
                int start = Math.max(0, recentErrors.size() - 10);
                report.append("Recent Errors (last 10):\n");
                report.append("========================\n");
                
                for (int i = start; i < recentErrors.size(); i++) {
                    ErrorEntry error = recentErrors.get(i);
                    report.append(String.format("Error #%d:\n", i + 1));
                    report.append(error.getDetailedString()).append("\n");
                    report.append("---\n");
                }
            }
            
            // Add file statistics
            appendFileStats(report);
        }
        
        return report.toString();
    }
    
    /**
     * Get a quick error summary for status displays
     */
    public String getErrorSummary() {
        ErrorStats stats = getErrorStats();
        
        if (stats.totalMemoryErrors == 0) {
            return "No errors recorded";
        }
        
        StringBuilder summary = new StringBuilder();
        summary.append("Errors: ").append(stats.totalMemoryErrors);
        
        if (stats.criticalErrors > 0) {
            summary.append(" (").append(stats.criticalErrors).append(" critical)");
        }
        
        // Show most recent error time
        synchronized (errorLock) {
            if (!recentErrors.isEmpty()) {
                ErrorEntry lastError = recentErrors.get(recentErrors.size() - 1);
                SimpleDateFormat timeFormat = new SimpleDateFormat("HH:mm:ss", Locale.getDefault());
                summary.append(" - Last: ").append(timeFormat.format(lastError.timestamp));
            }
        }
        
        return summary.toString();
    }
    
    /**
     * Export all error logs to a file
     */
    public boolean exportErrorLogs(File outputFile) {
        synchronized (errorLock) {
            try (PrintWriter writer = new PrintWriter(new FileWriter(outputFile))) {
                writer.println("=== Complete ModLoader Error Log Export ===");
                writer.println("Export Date: " + dateFormat.format(new Date()));
                writer.println("Export Location: " + outputFile.getAbsolutePath());
                writer.println();
                
                // Export error statistics
                ErrorStats stats = getErrorStats();
                writer.println("=== Error Statistics ===");
                writer.println(stats.getDetailedString());
                writer.println();
                
                // Export memory errors
                writer.println("=== Recent Errors (In Memory) ===");
                if (recentErrors.isEmpty()) {
                    writer.println("No errors in memory");
                } else {
                    for (int i = 0; i < recentErrors.size(); i++) {
                        ErrorEntry error = recentErrors.get(i);
                        writer.println("Memory Error #" + (i + 1) + ":");
                        writer.println(error.getDetailedString());
                        writer.println();
                    }
                }
                
                // Export error log file
                exportLogFile(errorLogFile, "Error Log File", writer);
                
                // Export crash log file  
                exportLogFile(crashLogFile, "Crash Log File", writer);
                
                writer.println("=== End of Error Export ===");
                return true;
                
            } catch (Exception e) {
                android.util.Log.e("ErrorLogger", "Failed to export error logs", e);
                return false;
            }
        }
    }
    
    /**
     * Clear all error logs
     */
    public void clearLogs() {
        synchronized (errorLock) {
            int clearedMemoryErrors = recentErrors.size();
            recentErrors.clear();
            
            boolean errorFileDeleted = false;
            boolean crashFileDeleted = false;
            
            if (errorLogFile.exists()) {
                errorFileDeleted = errorLogFile.delete();
            }
            
            if (crashLogFile.exists()) {
                crashFileDeleted = crashLogFile.delete();
            }
            
            // Log the clearing action
            logError(String.format("Error logs cleared - Memory: %d, ErrorFile: %s, CrashFile: %s", 
                                 clearedMemoryErrors, errorFileDeleted, crashFileDeleted), null);
        }
    }
    
    /**
     * Get detailed error statistics
     */
    public ErrorStats getErrorStats() {
        ErrorStats stats = new ErrorStats();
        
        synchronized (errorLock) {
            stats.totalMemoryErrors = recentErrors.size();
            
            for (ErrorEntry error : recentErrors) {
                if (error.isCritical) {
                    stats.criticalErrors++;
                }
                if (error.exception != null) {
                    stats.exceptionsWithStackTrace++;
                }
                
                // Categorize errors
                if (error.message != null) {
                    String msg = error.message.toLowerCase();
                    if (msg.contains("apk")) {
                        stats.apkErrors++;
                    }
                    if (msg.contains("validation")) {
                        stats.validationErrors++;
                    }
                    if (msg.contains("file") || msg.contains("io")) {
                        stats.fileErrors++;
                    }
                }
            }
            
            // Count file-based errors
            stats.totalFileErrors = countErrorsInFile(errorLogFile);
            stats.totalCrashes = countErrorsInFile(crashLogFile);
        }
        
        return stats;
    }
    
    /**
     * Check if there are any recent critical errors
     */
    public boolean hasRecentCriticalErrors(long timeWindowMs) {
        long cutoff = System.currentTimeMillis() - timeWindowMs;
        
        synchronized (errorLock) {
            for (ErrorEntry error : recentErrors) {
                if (error.isCritical && error.timestamp.getTime() > cutoff) {
                    return true;
                }
            }
        }
        
        return false;
    }
    
    /**
     * Flush any pending writes
     */
    public void flush() {
        // Error logging is synchronous, no need to flush
        // But we can verify file integrity
        synchronized (errorLock) {
            if (!errorLogFile.getParentFile().exists()) {
                errorLogFile.getParentFile().mkdirs();
            }
            if (!crashLogFile.getParentFile().exists()) {
                crashLogFile.getParentFile().mkdirs();
            }
        }
    }
    
    // === PRIVATE HELPER METHODS ===
    
    private void setupUncaughtExceptionHandler() {
        Thread.UncaughtExceptionHandler defaultHandler = Thread.getDefaultUncaughtExceptionHandler();
        
        Thread.setDefaultUncaughtExceptionHandler((thread, throwable) -> {
            // Log the crash with full context
            String crashMessage = String.format("Uncaught exception in thread '%s': %s", 
                                               thread.getName(), throwable.getMessage());
            logCriticalError(crashMessage, throwable);
            
            // Call the original handler to maintain normal crash behavior
            if (defaultHandler != null) {
                defaultHandler.uncaughtException(thread, throwable);
            }
        });
    }
    
    private void writeErrorToFile(ErrorEntry entry, File logFile) {
        try (PrintWriter writer = new PrintWriter(new FileWriter(logFile, true))) {
            writer.println("=== ERROR ENTRY ===");
            writer.println(entry.getDetailedString());
            writer.println("===================");
            writer.println();
        } catch (Exception e) {
            android.util.Log.e("ErrorLogger", "Failed to write error to file: " + e.getMessage());
            // Don't throw here to avoid infinite recursion
        }
    }
    
    private void exportLogFile(File logFile, String sectionTitle, PrintWriter writer) {
        writer.println("=== " + sectionTitle + " ===");
        
        if (!logFile.exists()) {
            writer.println("File does not exist: " + logFile.getName());
            writer.println();
            return;
        }
        
        writer.println("File: " + logFile.getName());
        writer.println("Size: " + formatFileSize(logFile.length()));
        writer.println("Last Modified: " + new Date(logFile.lastModified()));
        writer.println("Entries: " + countErrorsInFile(logFile));
        writer.println();
        
        try (BufferedReader reader = new BufferedReader(new FileReader(logFile))) {
            String line;
            while ((line = reader.readLine()) != null) {
                writer.println(line);
            }
        } catch (Exception e) {
            writer.println("Error reading file: " + e.getMessage());
        }
        
        writer.println();
    }
    
    private void appendFileStats(StringBuilder report) {
        report.append("\nFile Statistics:\n");
        report.append("================\n");
        
        if (errorLogFile.exists()) {
            report.append("Error Log: ").append(formatFileSize(errorLogFile.length()))
                  .append(" (").append(countErrorsInFile(errorLogFile)).append(" entries)\n");
            report.append("  Location: ").append(errorLogFile.getAbsolutePath()).append("\n");
        } else {
            report.append("Error Log: Not found\n");
        }
        
        if (crashLogFile.exists()) {
            report.append("Crash Log: ").append(formatFileSize(crashLogFile.length()))
                  .append(" (").append(countErrorsInFile(crashLogFile)).append(" entries)\n");
            report.append("  Location: ").append(crashLogFile.getAbsolutePath()).append("\n");
        } else {
            report.append("Crash Log: Not found\n");
        }
    }
    
    private int countErrorsInFile(File file) {
        if (!file.exists()) return 0;
        
        int errorEntries = 0;
        try (BufferedReader reader = new BufferedReader(new FileReader(file))) {
            String line;
            while ((line = reader.readLine()) != null) {
                if (line.contains("=== ERROR ENTRY ===")) {
                    errorEntries++;
                }
            }
        } catch (Exception e) {
            return -1; // Error counting
        }
        return errorEntries;
    }
    
    private String formatFileSize(long bytes) {
        if (bytes < 1024) return bytes + " B";
        if (bytes < 1024 * 1024) return String.format("%.1f KB", bytes / 1024.0);
        return String.format("%.1f MB", bytes / (1024.0 * 1024.0));
    }
    
    // === HELPER CLASSES ===
    
    public static class ErrorEntry {
        public final String message;
        public final Throwable exception;
        public final Date timestamp;
        public final boolean isCritical;
        public final String threadName;
        public final String className;
        
        public ErrorEntry(String message, Throwable exception) {
            this(message, exception, false);
        }
        
        public ErrorEntry(String message, Throwable exception, boolean isCritical) {
            this.message = message;
            this.exception = exception;
            this.timestamp = new Date();
            this.isCritical = isCritical;
            this.threadName = Thread.currentThread().getName();
            
            // Get calling class name
            StackTraceElement[] stack = Thread.currentThread().getStackTrace();
            this.className = stack.length > 4 ? stack[4].getClassName() : "Unknown";
        }
        
        public String getDetailedString() {
            StringBuilder sb = new StringBuilder();
            SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS", Locale.getDefault());
            
            sb.append("Timestamp: ").append(format.format(timestamp)).append("\n");
            sb.append("Thread: ").append(threadName).append("\n");
            sb.append("Class: ").append(className).append("\n");
            sb.append("Critical: ").append(isCritical ? "YES" : "NO").append("\n");
            sb.append("Message: ").append(message != null ? message : "No message").append("\n");
            
            if (exception != null) {
                sb.append("Exception Type: ").append(exception.getClass().getName()).append("\n");
                sb.append("Exception Message: ").append(exception.getMessage()).append("\n");
                
                // Include cause if available
                Throwable cause = exception.getCause();
                if (cause != null) {
                    sb.append("Caused By: ").append(cause.getClass().getName())
                      .append(": ").append(cause.getMessage()).append("\n");
                }
                
                sb.append("Stack Trace:\n");
                StringWriter sw = new StringWriter();
                PrintWriter pw = new PrintWriter(sw);
                exception.printStackTrace(pw);
                sb.append(sw.toString());
            } else {
                sb.append("No exception details\n");
            }
            
            return sb.toString();
        }
        
        public String getSummaryString() {
            SimpleDateFormat format = new SimpleDateFormat("HH:mm:ss", Locale.getDefault());
            String exceptionName = exception != null ? exception.getClass().getSimpleName() : "NoException";
            String criticalMark = isCritical ? "[CRITICAL] " : "";
            
            return String.format("[%s] %s%s: %s", 
                               format.format(timestamp), 
                               criticalMark,
                               exceptionName, 
                               message != null ? truncateString(message, 100) : "No message");
        }
        
        private String truncateString(String str, int maxLength) {
            if (str.length() <= maxLength) {
                return str;
            }
            return str.substring(0, maxLength - 3) + "...";
        }
    }
    
    public static class ErrorStats {
        public int totalMemoryErrors = 0;
        public int criticalErrors = 0;
        public int exceptionsWithStackTrace = 0;
        public int totalFileErrors = 0;
        public int totalCrashes = 0;
        public int apkErrors = 0;
        public int validationErrors = 0;
        public int fileErrors = 0;
        
        @Override
        public String toString() {
            return String.format("ErrorStats[Memory: %d, Critical: %d, Exceptions: %d, File: %d, Crashes: %d]",
                               totalMemoryErrors, criticalErrors, exceptionsWithStackTrace, 
                               totalFileErrors, totalCrashes);
        }
        
        public String getDetailedString() {
            StringBuilder sb = new StringBuilder();
            sb.append("Memory Errors: ").append(totalMemoryErrors).append("\n");
            sb.append("Critical Errors: ").append(criticalErrors).append("\n");
            sb.append("Exceptions with Stack Trace: ").append(exceptionsWithStackTrace).append("\n");
            sb.append("APK-related Errors: ").append(apkErrors).append("\n");
            sb.append("Validation Errors: ").append(validationErrors).append("\n");
            sb.append("File I/O Errors: ").append(fileErrors).append("\n");
            sb.append("File-based Errors: ").append(totalFileErrors).append("\n");
            sb.append("Total Crashes: ").append(totalCrashes).append("\n");
            
            return sb.toString();
        }
        
        public boolean hasErrors() {
            return totalMemoryErrors > 0 || totalFileErrors > 0 || totalCrashes > 0;
        }
        
        public boolean hasCriticalErrors() {
            return criticalErrors > 0 || totalCrashes > 0;
        }
    }
}

/ModLoader/app/src/main/java/com/modloader/logging/FileLogger.java

// File: FileLogger.java (Complete Fixed Version) - Full logging system with all properties
// Path: /storage/emulated/0/AndroidIDEProjects/ModLoader/app/src/main/java/com/modloader/logging/FileLogger.java

package com.modloader.logging;

import android.content.Context;
import android.util.Log;
import java.io.*;
import java.text.SimpleDateFormat;
import java.util.*;
import java.util.concurrent.ConcurrentLinkedQueue;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class FileLogger {
    private static final String TAG = "FileLogger";
    private static final int MAX_LOG_FILES = 5;
    private static final long MAX_LOG_SIZE = 10 * 1024 * 1024; // 10MB per log file
    private static final SimpleDateFormat DATE_FORMAT = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS", Locale.US);
    
    private Context context;
    private File logDirectory;
    private File currentLogFile;
    private FileWriter logWriter;
    private ExecutorService executorService;
    private ConcurrentLinkedQueue<String> logQueue;
    private boolean isInitialized = false;
    
    // Singleton instance
    private static volatile FileLogger instance;
    
    private FileLogger(Context context) {
        this.context = context.getApplicationContext();
        this.executorService = Executors.newSingleThreadExecutor();
        this.logQueue = new ConcurrentLinkedQueue<>();
        initialize();
    }
    
    public static FileLogger getInstance(Context context) {
        if (instance == null) {
            synchronized (FileLogger.class) {
                if (instance == null) {
                    instance = new FileLogger(context);
                }
            }
        }
        return instance;
    }
    
    private void initialize() {
        try {
            // Create log directory in app-specific external files directory
            logDirectory = new File(context.getExternalFilesDir(null), "ModLoader/com.and.games505.TerrariaPaid/AppLogs");
            if (!logDirectory.exists()) {
                logDirectory.mkdirs();
            }
            
            // Create current log file
            currentLogFile = new File(logDirectory, "AppLog.txt");
            
            // Check if rotation is needed
            if (currentLogFile.exists() && currentLogFile.length() > MAX_LOG_SIZE) {
                rotateLogFiles();
            }
            
            // Initialize file writer
            logWriter = new FileWriter(currentLogFile, true);
            isInitialized = true;
            
            // Write initialization message
            writeLogEntry("INFO", "FileLogger", "FileLogger initialized - " + currentLogFile.getAbsolutePath());
            
        } catch (IOException e) {
            Log.e(TAG, "Failed to initialize FileLogger", e);
            isInitialized = false;
        }
    }
    
    public void logDebug(String tag, String message) {
        logMessage("DEBUG", tag, message);
    }
    
    public void logInfo(String tag, String message) {
        logMessage("INFO", tag, message);
    }
    
    public void logWarning(String tag, String message) {
        logMessage("WARN", tag, message);
    }
    
    public void logError(String tag, String message) {
        logMessage("ERROR", tag, message);
    }
    
    public void logError(String tag, String message, Throwable throwable) {
        StringBuilder sb = new StringBuilder(message);
        if (throwable != null) {
            sb.append("\n").append(Log.getStackTraceString(throwable));
        }
        logMessage("ERROR", tag, sb.toString());
    }
    
    public void logUser(String message) {
        logMessage("USER", "ModLoader", message);
    }
    
    private void logMessage(String level, String tag, String message) {
        if (!isInitialized) {
            // Fallback to Android log
            Log.println(Log.INFO, tag, message);
            return;
        }
        
        String timestamp = DATE_FORMAT.format(new Date());
        String logEntry = String.format("[%s] %s/%s: %s", timestamp, level, tag, message);
        
        // Add to queue for async processing
        logQueue.offer(logEntry);
        
        // Process queue asynchronously
        executorService.submit(this::processLogQueue);
        
        // Also log to Android logcat based on level
        switch (level) {
            case "DEBUG":
                Log.d(tag, message);
                break;
            case "INFO":
            case "USER":
                Log.i(tag, message);
                break;
            case "WARN":
                Log.w(tag, message);
                break;
            case "ERROR":
                Log.e(tag, message);
                break;
        }
    }
    
    private void processLogQueue() {
        String logEntry;
        while ((logEntry = logQueue.poll()) != null) {
            writeLogEntry(logEntry);
        }
    }
    
    private void writeLogEntry(String level, String tag, String message) {
        String timestamp = DATE_FORMAT.format(new Date());
        String logEntry = String.format("[%s] %s/%s: %s", timestamp, level, tag, message);
        writeLogEntry(logEntry);
    }
    
    private synchronized void writeLogEntry(String logEntry) {
        if (!isInitialized || logWriter == null) {
            return;
        }
        
        try {
            logWriter.write(logEntry + "\n");
            logWriter.flush();
            
            // Check if rotation is needed
            if (currentLogFile.length() > MAX_LOG_SIZE) {
                rotateLogFiles();
            }
            
        } catch (IOException e) {
            Log.e(TAG, "Failed to write log entry", e);
            // Try to reinitialize
            closeCurrentWriter();
            initialize();
        }
    }
    
    private void rotateLogFiles() {
        try {
            closeCurrentWriter();
            
            // Move existing log files
            for (int i = MAX_LOG_FILES - 1; i >= 1; i--) {
                File oldFile = new File(logDirectory, "AppLog" + i + ".txt");
                File newFile = new File(logDirectory, "AppLog" + (i + 1) + ".txt");
                
                if (i == MAX_LOG_FILES - 1) {
                    // Delete the oldest file
                    if (oldFile.exists()) {
                        oldFile.delete();
                    }
                } else {
                    // Move file to next number
                    if (oldFile.exists()) {
                        oldFile.renameTo(newFile);
                    }
                }
            }
            
            // Move current log to AppLog1.txt
            if (currentLogFile.exists()) {
                File archivedFile = new File(logDirectory, "AppLog1.txt");
                currentLogFile.renameTo(archivedFile);
            }
            
            // Create new current log file
            currentLogFile = new File(logDirectory, "AppLog.txt");
            logWriter = new FileWriter(currentLogFile, true);
            
            writeLogEntry("INFO", "FileLogger", "Log rotation completed");
            
        } catch (IOException e) {
            Log.e(TAG, "Failed to rotate log files", e);
        }
    }
    
    private void closeCurrentWriter() {
        if (logWriter != null) {
            try {
                logWriter.close();
            } catch (IOException e) {
                Log.e(TAG, "Failed to close log writer", e);
            }
            logWriter = null;
        }
    }
    
    public String readCurrentLog() {
        StringBuilder sb = new StringBuilder();
        if (currentLogFile != null && currentLogFile.exists()) {
            try (BufferedReader reader = new BufferedReader(new FileReader(currentLogFile))) {
                String line;
                while ((line = reader.readLine()) != null) {
                    sb.append(line).append("\n");
                }
            } catch (IOException e) {
                Log.e(TAG, "Failed to read current log", e);
                sb.append("Error reading log file: ").append(e.getMessage());
            }
        } else {
            sb.append("No log file found");
        }
        return sb.toString();
    }
    
    public String readAllLogs() {
        StringBuilder sb = new StringBuilder();
        
        // Read current log
        sb.append("=== CURRENT LOG ===\n");
        sb.append(readCurrentLog());
        
        // Read archived logs
        for (int i = 1; i <= MAX_LOG_FILES; i++) {
            File archivedLog = new File(logDirectory, "AppLog" + i + ".txt");
            if (archivedLog.exists()) {
                sb.append("\n=== ARCHIVED LOG ").append(i).append(" ===\n");
                try (BufferedReader reader = new BufferedReader(new FileReader(archivedLog))) {
                    String line;
                    while ((line = reader.readLine()) != null) {
                        sb.append(line).append("\n");
                    }
                } catch (IOException e) {
                    Log.e(TAG, "Failed to read archived log " + i, e);
                    sb.append("Error reading archived log ").append(i).append(": ").append(e.getMessage()).append("\n");
                }
            }
        }
        
        return sb.toString();
    }
    
    public boolean exportLogs(File exportFile) {
        try (FileWriter writer = new FileWriter(exportFile)) {
            writer.write("=== ModLoader Export ===\n");
            writer.write("Export Date: " + new Date().toString() + "\n");
            writer.write("Log Directory: " + logDirectory.getAbsolutePath() + "\n\n");
            writer.write(readAllLogs());
            return true;
        } catch (IOException e) {
            Log.e(TAG, "Failed to export logs", e);
            return false;
        }
    }
    
    public void clearLogs() {
        try {
            closeCurrentWriter();
            
            // Delete all log files
            if (currentLogFile != null && currentLogFile.exists()) {
                currentLogFile.delete();
            }
            
            for (int i = 1; i <= MAX_LOG_FILES; i++) {
                File archivedLog = new File(logDirectory, "AppLog" + i + ".txt");
                if (archivedLog.exists()) {
                    archivedLog.delete();
                }
            }
            
            // Reinitialize
            initialize();
            
        } catch (Exception e) {
            Log.e(TAG, "Failed to clear logs", e);
        }
    }
    
    // FIXED: Complete LogStats class with all required properties
    public static class LogStats {
        public int totalFiles = 0;
        public long totalSize = 0;        // FIXED: Added missing field
        public int totalLines = 0;        // FIXED: Added missing field
        public String oldestEntry = "";   // FIXED: Added missing field
        public String newestEntry = "";   // FIXED: Added missing field
        public String currentLogFile = "";
        public List<String> archivedFiles = new ArrayList<>();
        
        public LogStats() {
            // Initialize any default values if needed
        }
        
        @Override
        public String toString() {
            return String.format("LogStats{totalFiles=%d, totalSize=%d bytes, totalLines=%d, oldest='%s', newest='%s'}",
                    totalFiles, totalSize, totalLines, oldestEntry, newestEntry);
        }
        
        public String getDetailedReport() {
            StringBuilder sb = new StringBuilder();
            sb.append("=== Log Statistics ===\n");
            sb.append("Total Files: ").append(totalFiles).append("\n");
            sb.append("Total Size: ").append(formatFileSize(totalSize)).append("\n");
            sb.append("Total Lines: ").append(totalLines).append("\n");
            sb.append("Oldest Entry: ").append(oldestEntry).append("\n");
            sb.append("Newest Entry: ").append(newestEntry).append("\n");
            sb.append("Current Log: ").append(currentLogFile).append("\n");
            if (!archivedFiles.isEmpty()) {
                sb.append("Archived Files:\n");
                for (String archived : archivedFiles) {
                    sb.append("  - ").append(archived).append("\n");
                }
            }
            return sb.toString();
        }
        
        private String formatFileSize(long bytes) {
            if (bytes < 1024) return bytes + " B";
            if (bytes < 1024 * 1024) return String.format("%.1f KB", bytes / 1024.0);
            if (bytes < 1024 * 1024 * 1024) return String.format("%.1f MB", bytes / (1024.0 * 1024.0));
            return String.format("%.1f GB", bytes / (1024.0 * 1024.0 * 1024.0));
        }
    }
    
    // FIXED: Complete getLogStats method with all property calculations
    public LogStats getLogStats() {
        LogStats stats = new LogStats();
        
        if (logDirectory != null && logDirectory.exists()) {
            File[] logFiles = logDirectory.listFiles((dir, name) -> name.endsWith(".txt"));
            if (logFiles != null) {
                stats.totalFiles = logFiles.length;
                
                // Calculate total size and collect file info
                for (File logFile : logFiles) {
                    stats.totalSize += logFile.length();  // FIXED: Now totalSize exists
                    stats.archivedFiles.add(logFile.getName());
                }
                
                // Set current log file path
                if (currentLogFile != null) {
                    stats.currentLogFile = currentLogFile.getName();
                }
                
                // Calculate total lines and find oldest/newest entries
                String firstLine = null;
                String lastLine = null;
                
                // Read through all log files to count lines and find date range
                for (File logFile : logFiles) {
                    try (BufferedReader reader = new BufferedReader(new FileReader(logFile))) {
                        String line;
                        boolean isFirstLineOfAllFiles = (firstLine == null);
                        
                        while ((line = reader.readLine()) != null) {
                            if (!line.trim().isEmpty()) {
                                stats.totalLines++;  // FIXED: Now totalLines exists
                                
                                // Capture first line ever read
                                if (isFirstLineOfAllFiles && firstLine == null) {
                                    firstLine = line;
                                    isFirstLineOfAllFiles = false;
                                }
                                
                                // Always update last line
                                lastLine = line;
                            }
                        }
                    } catch (IOException e) {
                        Log.e(TAG, "Error reading log file for stats: " + logFile.getName(), e);
                    }
                }
                
                // FIXED: Extract timestamps and set oldest/newest entries
                stats.oldestEntry = firstLine != null ? extractTimeFromLogLine(firstLine) : "";  // FIXED: Now oldestEntry exists
                stats.newestEntry = lastLine != null ? extractTimeFromLogLine(lastLine) : "";    // FIXED: Now newestEntry exists
            }
        }
        
        return stats;
    }
    
    // Helper method to extract timestamp from log line
    private String extractTimeFromLogLine(String logLine) {
        if (logLine == null || logLine.isEmpty()) {
            return "";
        }
        
        try {
            // Look for timestamp pattern [YYYY-MM-DD HH:MM:SS.mmm]
            int start = logLine.indexOf('[');
            int end = logLine.indexOf(']');
            if (start >= 0 && end > start) {
                return logLine.substring(start + 1, end);
            }
        } catch (Exception e) {
            Log.e(TAG, "Error extracting timestamp from log line", e);
        }
        
        // Fallback: return first 23 characters if they look like a timestamp
        if (logLine.length() >= 23) {
            return logLine.substring(0, 23);
        }
        
        return logLine.length() > 50 ? logLine.substring(0, 50) + "..." : logLine;
    }
    
    public File getLogDirectory() {
        return logDirectory;
    }
    
    public File getCurrentLogFile() {
        return currentLogFile;
    }
    
    public boolean isInitialized() {
        return isInitialized;
    }
    
    public void shutdown() {
        if (executorService != null && !executorService.isShutdown()) {
            executorService.shutdown();
        }
        closeCurrentWriter();
    }
    
    // Cleanup method for proper resource management
    @Override
    protected void finalize() throws Throwable {
        try {
            shutdown();
        } finally {
            super.finalize();
        }
    }
}

/ModLoader/app/src/main/java/com/modloader/logging/LogExporter.java

package com.modloader.logging;

public class LogExporter {
}


/ModLoader/app/src/main/java/com/modloader/ui/BaseActivity.java

// File: BaseActivity.java (FIXED) - Compatible with PermissionManager
// Path: /app/src/main/java/com/modloader/ui/BaseActivity.java

package com.modloader.ui;

import android.content.Intent;
import android.os.Bundle;
import android.view.MenuItem;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;

import com.modloader.util.LogUtils;
import com.modloader.util.PermissionManager;
import com.modloader.util.ShizukuManager;
import com.modloader.util.RootManager;

/**
 * Base activity that provides common functionality for all activities
 * including permission management, error handling, and utility methods
 */
public abstract class BaseActivity extends AppCompatActivity implements PermissionManager.PermissionCallback {
    
    protected static final String TAG = "BaseActivity";
    
    // Common managers
    protected PermissionManager permissionManager;
    protected ShizukuManager shizukuManager;
    protected RootManager rootManager;
    
    // Activity state
    private boolean isResumed = false;
    private boolean hasInitialized = false;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        LogUtils.logDebug(getClass().getSimpleName() + " onCreate started");
        
        try {
            // Initialize managers
            initializeManagers();
            
            // Setup common UI elements
            setupCommonUI();
            
            // Auto-setup permissions if needed
            if (shouldAutoSetupPermissions()) {
                autoSetupPermissions();
            }
            
            hasInitialized = true;
            LogUtils.logDebug(getClass().getSimpleName() + " onCreate completed");
            
        } catch (Exception e) {
            LogUtils.logError("Error in BaseActivity onCreate: " + e.getMessage());
            handleStartupError(e);
        }
    }
    
    /**
     * Initialize common managers
     */
    private void initializeManagers() {
        try {
            permissionManager = new PermissionManager(this);
            permissionManager.setCallback(this);
            
            shizukuManager = new ShizukuManager(this);
            rootManager = new RootManager(this);
            
            LogUtils.logDebug("Managers initialized successfully");
        } catch (Exception e) {
            LogUtils.logError("Error initializing managers: " + e.getMessage());
        }
    }
    
    /**
     * Setup common UI elements
     */
    private void setupCommonUI() {
        // Enable up navigation if not main activity
        if (getSupportActionBar() != null && !isMainActivity()) {
            getSupportActionBar().setDisplayHomeAsUpEnabled(true);
        }
    }
    
    /**
     * Auto-setup permissions if the activity requires them
     */
    private void autoSetupPermissions() {
        if (permissionManager != null && requiresPermissions()) {
            permissionManager.autoSetupPermissions();
        }
    }
    
    /**
     * Check if this activity has all required permissions
     */
    protected boolean hasRequiredPermissions() {
        if (permissionManager != null) {
            return permissionManager.hasAllRequiredPermissions();
        }
        return true; // Assume true if manager not available
    }
    
    /**
     * Handle startup errors gracefully
     */
    private void handleStartupError(Exception e) {
        LogUtils.logError("Startup error in " + getClass().getSimpleName() + ": " + e.getMessage());
        
        new AlertDialog.Builder(this)
            .setTitle("❌ Startup Error")
            .setMessage("An error occurred while starting this activity:\n\n" + e.getMessage() + 
                "\n\nThe app may not function correctly.")
            .setPositiveButton("Continue", null)
            .setNegativeButton("Close App", (dialog, which) -> finishAffinity())
            .show();
    }
    
    // === Permission Management Methods ===
    
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        
        LogUtils.logDebug("Permission result in " + getClass().getSimpleName() + 
            ": requestCode=" + requestCode);
        
        if (permissionManager != null) {
            permissionManager.handlePermissionResult(requestCode, permissions, grantResults);
        }
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        
        LogUtils.logDebug("Activity result in " + getClass().getSimpleName() + 
            ": requestCode=" + requestCode + ", resultCode=" + resultCode);
        
        // Handle special permission results
        if (permissionManager != null) {
            switch (requestCode) {
                case PermissionManager.REQUEST_ALL_FILES_ACCESS:
                case PermissionManager.REQUEST_INSTALL_PERMISSION:
                    permissionManager.handlePermissionResult(requestCode, null, null);
                    break;
            }
        }
        
        // Let subclasses handle their specific results
        handleActivityResult(requestCode, resultCode, data);
    }
    
    /**
     * Subclasses can override this to handle activity results
     */
    protected void handleActivityResult(int requestCode, int resultCode, Intent data) {
        // Default implementation does nothing
    }
    
    // === PermissionManager.PermissionCallback Implementation ===
    
    @Override
    public void onPermissionGranted(String permission) {
        LogUtils.logUser("✅ Permission granted: " + permission);
        onPermissionStateChanged();
    }
    
    @Override
    public void onPermissionDenied(String permission) {
        LogUtils.logUser("❌ Permission denied: " + permission);
        handlePermissionDenied(permission);
        onPermissionStateChanged();
    }
    
    @Override
    public void onPermissionPermanentlyDenied(String permission) {
        LogUtils.logUser("⚠️ Permission permanently denied: " + permission);
        handlePermissionPermanentlyDenied(permission);
        onPermissionStateChanged();
    }
    
    @Override
    public void onAllPermissionsGranted() {
        LogUtils.logUser("✅ All permissions granted!");
        onPermissionStateChanged();
        onAllPermissionsReady();
    }
    
    @Override
    public void onPermissionRequestCompleted(boolean allGranted) {
        LogUtils.logDebug("Permission request completed: " + allGranted);
        onPermissionStateChanged();
    }
    
    /**
     * Called when permission state changes - subclasses can override
     */
    protected void onPermissionStateChanged() {
        // Update UI or refresh content based on new permission state
        refreshPermissionStatus();
    }
    
    /**
     * Called when all permissions are ready - subclasses can override
     */
    protected void onAllPermissionsReady() {
        // Subclasses can implement specific behavior when all permissions are granted
    }
    
    /**
     * Handle permission denial with user-friendly messaging
     */
    protected void handlePermissionDenied(String permission) {
        String message = getPermissionDenialMessage(permission);
        showToast(message);
    }
    
    /**
     * Handle permanently denied permissions
     */
    protected void handlePermissionPermanentlyDenied(String permission) {
        new AlertDialog.Builder(this)
            .setTitle("🔐 Permission Required")
            .setMessage("The permission for " + permission + " has been permanently denied.\n\n" +
                "To enable it, please:\n" +
                "1. Go to App Settings\n" +
                "2. Find Permissions\n" +
                "3. Enable the required permission")
            .setPositiveButton("Open Settings", (dialog, which) -> {
                if (permissionManager != null) {
                    permissionManager.openAppSettings();
                }
            })
            .setNegativeButton("Cancel", null)
            .show();
    }
    
    /**
     * Get user-friendly message for permission denial
     */
    private String getPermissionDenialMessage(String permission) {
        switch (permission) {
            case "MANAGE_EXTERNAL_STORAGE":
                return "Storage access is required to manage mod files";
            case "INSTALL_PACKAGES":
                return "Install permission is required to install modded APKs";
            default:
                return "This permission is required for the app to function properly";
        }
    }
    
    /**
     * Refresh permission status display
     */
    protected void refreshPermissionStatus() {
        // Subclasses can override to update permission-related UI
        if (permissionManager != null) {
            boolean hasAll = permissionManager.hasAllRequiredPermissions();
            LogUtils.logDebug("Permission status refreshed: " + (hasAll ? "All granted" : "Missing some"));
        }
    }
    
    // === Utility Methods ===
    
    /**
     * Show permission status dialog
     */
    protected void showPermissionStatus() {
        if (permissionManager != null) {
            String status = permissionManager.getPermissionStatusText();
            new AlertDialog.Builder(this)
                .setTitle("🔐 Permission Status")
                .setMessage(status)
                .setPositiveButton("OK", null)
                .setNeutralButton("Request Missing", (dialog, which) -> {
                    permissionManager.requestAllPermissions();
                })
                .show();
        }
    }
    
    /**
     * Show toast message
     */
    protected void showToast(String message) {
        Toast.makeText(this, message, Toast.LENGTH_SHORT).show();
    }
    
    /**
     * Show long toast message
     */
    protected void showLongToast(String message) {
        Toast.makeText(this, message, Toast.LENGTH_LONG).show();
    }
    
    /**
     * Show error dialog
     */
    protected void showError(String title, String message) {
        new AlertDialog.Builder(this)
            .setTitle("❌ " + title)
            .setMessage(message)
            .setPositiveButton("OK", null)
            .show();
    }
    
    /**
     * Show confirmation dialog
     */
    protected void showConfirmation(String title, String message, Runnable onConfirm) {
        new AlertDialog.Builder(this)
            .setTitle(title)
            .setMessage(message)
            .setPositiveButton("Yes", (dialog, which) -> {
                if (onConfirm != null) {
                    onConfirm.run();
                }
            })
            .setNegativeButton("No", null)
            .show();
    }
    
    // === Navigation Methods ===
    
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        if (item.getItemId() == android.R.id.home) {
            onBackPressed();
            return true;
        }
        return super.onOptionsItemSelected(item);
    }
    
    /**
     * Safe finish that handles edge cases
     */
    protected void safeFinish() {
        try {
            if (!isFinishing()) {
                finish();
            }
        } catch (Exception e) {
            LogUtils.logDebug("Error during safeFinish: " + e.getMessage());
        }
    }
    
    // === Lifecycle Methods ===
    
    @Override
    protected void onResume() {
        super.onResume();
        isResumed = true;
        
        // Refresh permission states when returning from other apps
        if (hasInitialized && permissionManager != null) {
            permissionManager.refreshPermissionStates();
            refreshPermissionStatus();
        }
        
        LogUtils.logDebug(getClass().getSimpleName() + " onResume");
    }
    
    @Override
    protected void onPause() {
        super.onPause();
        isResumed = false;
        LogUtils.logDebug(getClass().getSimpleName() + " onPause");
    }
    
    @Override
    protected void onDestroy() {
        super.onDestroy();
        
        // Clean up managers
        if (permissionManager != null) {
            permissionManager.cleanup();
        }
        if (shizukuManager != null) {
            shizukuManager.cleanup();
        }
        if (rootManager != null) {
            rootManager.cleanup();
        }
        
        LogUtils.logDebug(getClass().getSimpleName() + " onDestroy");
    }
    
    // === Abstract/Virtual Methods for Subclasses ===
    
    /**
     * Whether this activity should auto-setup permissions on create
     */
    protected boolean shouldAutoSetupPermissions() {
        return requiresPermissions();
    }
    
    /**
     * Whether this activity requires permissions to function
     */
    protected boolean requiresPermissions() {
        return true; // Most activities require permissions
    }
    
    /**
     * Whether this is the main activity (affects navigation setup)
     */
    protected boolean isMainActivity() {
        return false; // Subclasses should override if they are main activities
    }
    
    // === State Query Methods ===
    
    protected boolean isActivityResumed() {
        return isResumed;
    }
    
    protected boolean isActivityInitialized() {
        return hasInitialized;
    }
    
    /**
     * Get the permission manager instance
     */
    protected PermissionManager getPermissionManager() {
        return permissionManager;
    }
    
    /**
     * Get the Shizuku manager instance
     */
    protected ShizukuManager getShizukuManager() {
        return shizukuManager;
    }
    
    /**
     * Get the root manager instance
     */
    protected RootManager getRootManager() {
        return rootManager;
    }
}

/ModLoader/app/src/main/java/com/modloader/ui/DllModActivity.java

// File: DllModActivity.java (Updated Activity) - Uses Controller Pattern
// Path: /storage/emulated/0/AndroidIDEProjects/main/java/com/modloader/ui/DllModActivity.java

package com.modloader.ui;

import android.app.Activity;
import android.app.AlertDialog;
import android.content.Intent;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.provider.OpenableColumns;
import android.view.View;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.TextView;
import android.widget.Toast;

import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import com.modloader.R;
import com.modloader.util.LogUtils;

import java.io.File;
import java.util.List;

/**
 * DllModActivity now delegates business logic to DllModController
 * This keeps the activity focused on UI management while the controller handles the logic
 */
public class DllModActivity extends Activity implements DllModController.DllModCallback {

    private static final int REQUEST_SELECT_APK = 1001;
    private static final int REQUEST_SELECT_DLL = 1002;
    
    // UI Components
    private TextView statusText;
    private TextView loaderStatusText;
    private RecyclerView dllModRecyclerView;
    private Button installLoaderBtn;
    private Button selectApkBtn;
    private Button installDllBtn;
    private Button viewLogsBtn;
    private Button refreshBtn;
    private LinearLayout loaderInfoSection;
    
    // Controller and Adapter
    private DllModController controller;
    private DllModAdapter adapter;
    private AlertDialog progressDialog;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_dll_mod);
        
        setTitle("DLL Mod Manager");
        
        // Initialize controller
        controller = new DllModController(this);
        controller.setCallback(this);
        
        initializeViews();
        setupUI();
        
        // Load initial data
        controller.loadDllMods();
        controller.updateLoaderStatus();
    }

    private void initializeViews() {
        statusText = findViewById(R.id.statusText);
        loaderStatusText = findViewById(R.id.loaderStatusText);
        dllModRecyclerView = findViewById(R.id.dllModRecyclerView);
        installLoaderBtn = findViewById(R.id.installLoaderBtn);
        selectApkBtn = findViewById(R.id.selectApkBtn);
        installDllBtn = findViewById(R.id.installDllBtn);
        viewLogsBtn = findViewById(R.id.viewLogsBtn);
        refreshBtn = findViewById(R.id.refreshBtn);
        loaderInfoSection = findViewById(R.id.loaderInfoSection);
    }

    private void setupUI() {
        // Setup RecyclerView
        dllModRecyclerView.setLayoutManager(new LinearLayoutManager(this));
        adapter = new DllModAdapter(this, controller);
        dllModRecyclerView.setAdapter(adapter);

        // Button listeners - delegate to controller
        installLoaderBtn.setOnClickListener(v -> controller.showLoaderInstallDialog());
        
        selectApkBtn.setOnClickListener(v -> {
            Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
            intent.setType("application/vnd.android.package-archive");
            intent.addCategory(Intent.CATEGORY_OPENABLE);
            startActivityForResult(intent, REQUEST_SELECT_APK);
        });
        
        installDllBtn.setOnClickListener(v -> {
            Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
            intent.setType("*/*"); // Allow all files, we'll filter by extension
            intent.addCategory(Intent.CATEGORY_OPENABLE);
            startActivityForResult(intent, REQUEST_SELECT_DLL);
        });
        
        viewLogsBtn.setOnClickListener(v -> {
            startActivity(new Intent(this, LogViewerActivity.class));
        });
        
        refreshBtn.setOnClickListener(v -> {
            controller.refresh();
            Toast.makeText(this, "Refreshed DLL mod list", Toast.LENGTH_SHORT).show();
        });
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        
        if (resultCode != RESULT_OK || data == null || data.getData() == null) {
            return;
        }
        
        Uri uri = data.getData();
        
        switch (requestCode) {
            case REQUEST_SELECT_APK:
                controller.handleApkSelection(uri);
                break;
                
            case REQUEST_SELECT_DLL:
                String filename = getFilenameFromUri(uri);
                controller.handleDllSelection(uri, filename);
                break;
        }
    }

    private String getFilenameFromUri(Uri uri) {
        String filename = null;
        
        try (Cursor cursor = getContentResolver().query(uri, null, null, null, null)) {
            if (cursor != null && cursor.moveToFirst()) {
                int nameIndex = cursor.getColumnIndex(OpenableColumns.DISPLAY_NAME);
                if (nameIndex >= 0) {
                    filename = cursor.getString(nameIndex);
                }
            }
        } catch (Exception e) {
            LogUtils.logDebug("Could not get filename from URI: " + e.getMessage());
        }
        
        return filename;
    }

    @Override
    protected void onResume() {
        super.onResume();
        controller.refresh();
    }

    // === DllModController.DllModCallback Implementation ===
    
    @Override
    public void onModsLoaded(List<File> mods) {
        runOnUiThread(() -> {
            adapter.updateMods(mods);
            statusText.setText(controller.getStatusText());
        });
    }

    @Override
    public void onLoaderStatusChanged(boolean installed, String statusText, int textColor) {
        runOnUiThread(() -> {
            loaderStatusText.setText(statusText);
            loaderStatusText.setTextColor(textColor);
            
            if (installed) {
                installLoaderBtn.setText("Reinstall Loader");
                loaderInfoSection.setVisibility(View.VISIBLE);
                installDllBtn.setEnabled(true);
                installDllBtn.setText("Install DLL Mod");
            } else {
                installLoaderBtn.setText("Install Loader");
                loaderInfoSection.setVisibility(View.GONE);
                installDllBtn.setEnabled(false);
                installDllBtn.setText("Install Loader First");
            }
        });
    }

    @Override
    public void onInstallationProgress(String message) {
        runOnUiThread(() -> {
            if (progressDialog != null) {
                progressDialog.dismiss();
            }
            
            if (message.contains("Please select")) {
                // This is a request for user action, not a progress message
                return;
            }
            
            progressDialog = new AlertDialog.Builder(this)
                .setTitle("Installing Loader")
                .setMessage(message)
                .setCancelable(false)
                .show();
        });
    }

    @Override
    public void onInstallationComplete(boolean success, String message) {
        runOnUiThread(() -> {
            if (progressDialog != null) {
                progressDialog.dismiss();
                progressDialog = null;
            }
            
            if (success) {
                Toast.makeText(this, message, Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        });
    }

    @Override
    public void onError(String error) {
        runOnUiThread(() -> {
            Toast.makeText(this, error, Toast.LENGTH_SHORT).show();
        });
    }

    // === Simple adapter class for DLL mods ===
    private static class DllModAdapter extends RecyclerView.Adapter<DllModAdapter.ViewHolder> {
        private final Activity activity;
        private final DllModController controller;
        private List<File> mods;

        public DllModAdapter(Activity activity, DllModController controller) {
            this.activity = activity;
            this.controller = controller;
            this.mods = controller.getDllMods();
        }

        public void updateMods(List<File> newMods) {
            this.mods = newMods;
            notifyDataSetChanged();
        }

        @Override
        public ViewHolder onCreateViewHolder(android.view.ViewGroup parent, int viewType) {
            View view = android.view.LayoutInflater.from(parent.getContext())
                .inflate(R.layout.item_mod, parent, false);
            return new ViewHolder(view);
        }

        @Override
        public void onBindViewHolder(ViewHolder holder, int position) {
            File mod = mods.get(position);
            String name = mod.getName();
            boolean isEnabled = !name.endsWith(".disabled");
            
            holder.modDescription.setText(name);
            holder.modSwitch.setChecked(isEnabled);
            
            holder.modSwitch.setOnCheckedChangeListener((buttonView, isChecked) -> {
                controller.toggleMod(mod, isChecked);
            });
            
            holder.modDeleteButton.setOnClickListener(v -> {
                controller.deleteMod(mod, () -> {
                    mods.remove(position);
                    notifyItemRemoved(position);
                    notifyItemRangeChanged(position, mods.size());
                });
            });
        }

        @Override
        public int getItemCount() {
            return mods.size();
        }

        static class ViewHolder extends RecyclerView.ViewHolder {
            TextView modDescription;
            android.widget.Switch modSwitch;
            android.widget.ImageButton modDeleteButton;

            ViewHolder(View itemView) {
                super(itemView);
                modDescription = itemView.findViewById(R.id.modDescription);
                modSwitch = itemView.findViewById(R.id.modSwitch);
                modDeleteButton = itemView.findViewById(R.id.modDeleteButton);
            }
        }
    }
}

/ModLoader/app/src/main/java/com/modloader/ui/DllModController.java

// File: DllModController.java (Fixed) - Corrected method calls with Context parameter
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/modloader/ui/DllModController.java

package com.modloader.ui;

import android.app.Activity;
import android.app.AlertDialog;
import android.content.Intent;
import android.net.Uri;
import com.modloader.loader.ModManager;
import com.modloader.loader.ModBase;
import com.modloader.loader.MelonLoaderManager;
import com.modloader.util.LogUtils;
import com.modloader.util.FileUtils;
import com.modloader.installer.ModInstaller;
import java.io.File;
import java.util.ArrayList;
import java.util.List;

public class DllModController {
    private final Activity activity;
    private List<File> dllMods = new ArrayList<>();
    
    // Callback interface for UI updates
    public interface DllModCallback {
        void onModsLoaded(List<File> mods);
        void onLoaderStatusChanged(boolean installed, String statusText, int textColor);
        void onInstallationProgress(String message);
        void onInstallationComplete(boolean success, String message);
        void onError(String error);
    }
    
    private DllModCallback callback;

    public DllModController(Activity activity) {
        this.activity = activity;
    }
    
    public void setCallback(DllModCallback callback) {
        this.callback = callback;
    }

    public void loadDllMods() {
        dllMods.clear();
        
        // Get DLL mods from ModManager
        List<File> allMods = ModManager.getAvailableMods();
        for (File mod : allMods) {
            ModBase.ModType type = ModBase.ModType.fromFileName(mod.getName());
            if (type == ModBase.ModType.DLL || type == ModBase.ModType.HYBRID) {
                dllMods.add(mod);
            }
        }
        
        LogUtils.logDebug("Loaded " + dllMods.size() + " DLL mods");
        
        if (callback != null) {
            callback.onModsLoaded(dllMods);
        }
    }

    public void updateLoaderStatus() {
        // FIXED: Pass activity context to MelonLoaderManager methods
        boolean melonInstalled = MelonLoaderManager.isMelonLoaderInstalled(activity);
        boolean lemonInstalled = MelonLoaderManager.isLemonLoaderInstalled(activity);
        
        if (melonInstalled) {
            String statusText = "✅ MelonLoader " + MelonLoaderManager.getInstalledLoaderVersion() + " installed";
            if (callback != null) {
                callback.onLoaderStatusChanged(true, statusText, 0xFF4CAF50); // Green
            }
        } else if (lemonInstalled) {
            String statusText = "✅ LemonLoader " + MelonLoaderManager.getInstalledLoaderVersion() + " installed";
            if (callback != null) {
                callback.onLoaderStatusChanged(true, statusText, 0xFF4CAF50); // Green
            }
        } else {
            String statusText = "❌ No loader installed - DLL mods will not work";
            if (callback != null) {
                callback.onLoaderStatusChanged(false, statusText, 0xFFF44336); // Red
            }
        }
    }

    public void showLoaderInstallDialog() {
        AlertDialog.Builder builder = new AlertDialog.Builder(activity);
        builder.setTitle("Choose Loader");
        builder.setMessage("Select which loader to install:\n\n" +
                          "• MelonLoader: Full-featured, works with most Unity games\n" +
                          "• LemonLoader: Lightweight, better for older devices\n\n" +
                          "You will need to select a Terraria APK file after choosing.");
        
        builder.setPositiveButton("MelonLoader", (dialog, which) -> {
            LogUtils.logUser("User selected MelonLoader installation");
            showApkInstructions("MelonLoader");
        });
        
        builder.setNegativeButton("LemonLoader", (dialog, which) -> {
            LogUtils.logUser("User selected LemonLoader installation");
            showApkInstructions("LemonLoader");
        });
        
        builder.setNeutralButton("Cancel", null);
        builder.show();
    }

    public void showApkInstructions(String loaderType) {
        AlertDialog.Builder builder = new AlertDialog.Builder(activity);
        builder.setTitle("APK Installation Instructions");
        builder.setMessage("To install " + loaderType + ":\n\n" +
                          "1. Select your Terraria APK file\n" +
                          "2. The loader will be injected into the APK\n" +
                          "3. Install the modified APK\n" +
                          "4. Your DLL mods will work in Terraria\n\n" +
                          "Ready to select APK?");
        
        builder.setPositiveButton("Select APK", (dialog, which) -> {
            // Store the selected loader type for later use
            activity.getSharedPreferences("dll_mod_prefs", Activity.MODE_PRIVATE)
                .edit()
                .putString("selected_loader", loaderType)
                .apply();
            
            // Trigger APK selection in the UI
            if (callback != null) {
                callback.onInstallationProgress("Please select Terraria APK file");
            }
        });
        
        builder.setNegativeButton("Cancel", null);
        builder.show();
    }

    public void handleApkSelection(Uri apkUri) {
        LogUtils.logUser("APK selected for loader installation");
        
        // Get the selected loader type
        String selectedLoader = activity.getSharedPreferences("dll_mod_prefs", Activity.MODE_PRIVATE)
            .getString("selected_loader", "MelonLoader");
        
        // Show confirmation dialog
        AlertDialog.Builder builder = new AlertDialog.Builder(activity);
        builder.setTitle("Confirm Installation");
        builder.setMessage("Install " + selectedLoader + " into the selected APK?\n\n" +
                          "This will create a new patched APK file that you can install.");
        
        builder.setPositiveButton("Install", (dialog, which) -> {
            if ("MelonLoader".equals(selectedLoader)) {
                installLoaderIntoApk(apkUri, MelonLoaderManager.LoaderType.MELONLOADER_NET8);
            } else {
                installLoaderIntoApk(apkUri, MelonLoaderManager.LoaderType.MELONLOADER_NET35);
            }
        });
        
        builder.setNegativeButton("Cancel", null);
        builder.show();
    }

    public void handleDllSelection(Uri dllUri, String filename) {
        LogUtils.logUser("DLL file selected for installation");
        
        if (filename == null) {
            filename = "mod_" + System.currentTimeMillis() + ".dll";
        }
        
        if (!filename.toLowerCase().endsWith(".dll")) {
            if (callback != null) {
                callback.onError("Please select a .dll file");
            }
            return;
        }
        
        // FIXED: Pass activity context to MelonLoaderManager methods
        if (!MelonLoaderManager.isMelonLoaderInstalled(activity) && !MelonLoaderManager.isLemonLoaderInstalled(activity)) {
            showLoaderRequiredDialog();
            return;
        }
        
        // Install DLL mod
        boolean success = ModInstaller.installMod(activity, dllUri, filename);
        
        if (success) {
            if (callback != null) {
                callback.onInstallationComplete(true, "DLL mod installed successfully");
            }
            loadDllMods(); // Refresh list
        } else {
            if (callback != null) {
                callback.onInstallationComplete(false, "Failed to install DLL mod");
            }
        }
    }

    public void showLoaderRequiredDialog() {
        AlertDialog.Builder builder = new AlertDialog.Builder(activity);
        builder.setTitle("Loader Required");
        builder.setMessage("DLL mods require MelonLoader or LemonLoader to be installed first.\n\n" +
                          "Would you like to install a loader now?");
        
        builder.setPositiveButton("Install Loader", (dialog, which) -> {
            showLoaderInstallDialog();
        });
        
        builder.setNegativeButton("Cancel", null);
        builder.show();
    }

    public void installLoaderIntoApk(Uri apkUri, MelonLoaderManager.LoaderType loaderType) {
        LogUtils.logUser("Installing " + loaderType.getDisplayName() + " into APK");
        
        if (callback != null) {
            callback.onInstallationProgress("Installing " + loaderType.getDisplayName() + " into APK...\nThis may take a few minutes.");
        }
        
        // Run installation in background thread
        new Thread(() -> {
            boolean success = false;
            File outputApk = null;
            
            try {
                // Create temporary files
                File tempApk = File.createTempFile("input_", ".apk", activity.getCacheDir());
                outputApk = new File(activity.getExternalFilesDir(null), "patched_terraria_" + 
                                        loaderType.name().toLowerCase() + "_" +
                                        System.currentTimeMillis() + ".apk");
                
                // Copy input APK to temp file
                FileUtils.copyUriToFile(activity, apkUri, tempApk);
                
                // Install loader
                if (loaderType == MelonLoaderManager.LoaderType.MELONLOADER_NET8) {
                    success = MelonLoaderManager.installMelonLoader(activity, tempApk, outputApk);
                } else if (loaderType == MelonLoaderManager.LoaderType.MELONLOADER_NET35) {
                    success = MelonLoaderManager.installLemonLoader(activity, tempApk, outputApk);
                }
                
                // Cleanup temp file
                tempApk.delete();
                
            } catch (Exception e) {
                LogUtils.logDebug("Loader installation error: " + e.getMessage());
                success = false;
            }
            
            // Update UI on main thread
            final boolean finalSuccess = success;
            final File finalOutputApk = outputApk;
            
            activity.runOnUiThread(() -> {
                if (finalSuccess) {
                    showInstallationSuccessDialog(finalOutputApk, loaderType);
                } else {
                    if (callback != null) {
                        callback.onInstallationComplete(false, "Loader installation failed");
                    }
                    LogUtils.logUser("❌ " + loaderType.getDisplayName() + " installation failed");
                }
                
                updateLoaderStatus();
            });
        }).start();
    }

    public void showInstallationSuccessDialog(File outputApk, MelonLoaderManager.LoaderType loaderType) {
        AlertDialog.Builder builder = new AlertDialog.Builder(activity);
        builder.setTitle("Installation Complete");
        builder.setMessage("✅ " + loaderType.getDisplayName() + " has been installed into the APK!\n\n" +
                          "Patched APK saved to:\n" + outputApk.getName() + "\n\n" +
                          "Next steps:\n" +
                          "1. Install the patched APK\n" +
                          "2. Install DLL mods\n" +
                          "3. Launch Terraria");
        
        builder.setPositiveButton("Install APK", (dialog, which) -> {
            installApk(outputApk);
        });
        
        builder.setNegativeButton("Later", null);
        builder.show();
        
        LogUtils.logUser("✅ " + loaderType.getDisplayName() + " installation completed successfully");
        
        if (callback != null) {
            callback.onInstallationComplete(true, loaderType.getDisplayName() + " installation completed successfully");
        }
    }

    public void installApk(File apkFile) {
        try {
            Intent intent = new Intent(Intent.ACTION_VIEW);
            intent.setDataAndType(
                androidx.core.content.FileProvider.getUriForFile(
                    activity, 
                    activity.getPackageName() + ".provider", 
                    apkFile
                ),
                "application/vnd.android.package-archive"
            );
            intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
            activity.startActivity(intent);
        } catch (Exception e) {
            if (callback != null) {
                callback.onError("Cannot install APK: " + e.getMessage());
            }
            LogUtils.logDebug("APK installation error: " + e.getMessage());
        }
    }

    public void toggleMod(File mod, boolean isChecked) {
        if (isChecked) {
            ModManager.enableMod(activity, mod);
        } else {
            ModManager.disableMod(activity, mod);
        }
    }

    public void deleteMod(File mod, Runnable onSuccess) {
        AlertDialog.Builder builder = new AlertDialog.Builder(activity);
        builder.setTitle("Delete Mod");
        builder.setMessage("Delete " + mod.getName() + "?");
        builder.setPositiveButton("Delete", (dialog, which) -> {
            if (ModManager.deleteMod(activity, mod)) {
                if (onSuccess != null) {
                    onSuccess.run();
                }
                loadDllMods(); // Refresh the list
            }
        });
        builder.setNegativeButton("Cancel", null);
        builder.show();
    }

    public List<File> getDllMods() {
        return new ArrayList<>(dllMods);
    }

    public String getStatusText() {
        int enabledCount = 0;
        for (File mod : dllMods) {
            if (!mod.getName().endsWith(".disabled")) {
                enabledCount++;
            }
        }
        
        return "DLL Mods: " + enabledCount + " enabled, " + 
               (dllMods.size() - enabledCount) + " disabled, " + 
               dllMods.size() + " total";
    }

    public boolean isLoaderInstalled() {
        // FIXED: Pass activity context to MelonLoaderManager methods
        return MelonLoaderManager.isMelonLoaderInstalled(activity) || MelonLoaderManager.isLemonLoaderInstalled(activity);
    }

    public void refresh() {
        loadDllMods();
        updateLoaderStatus();
    }
}
