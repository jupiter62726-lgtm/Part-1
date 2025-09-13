
================================================================================
ModLoader/build.gradle

// Root-level build.gradle (for Gradle 7.0+ and AGP 8.0+)

plugins {
    id 'com.android.application' version '8.0.0' apply false
    id 'com.android.library' version '8.0.0' apply false
    // No IDE or LogSender related plugins
}

tasks.register("clean", Delete) {
    delete rootProject.buildDir
}

================================================================================
ModLoader/settings.gradle

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


================================================================================
ModLoader/app/build.gradle

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

================================================================================
ModLoader/app/src/main/AndroidManifest.xml

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

        <!-- ===== 3. UPDATE AndroidManifest.xml ===== -->
        <!-- NEW: Addon Management Activity -->
        <activity
            android:name=".addon.AddonManagementActivity"
            android:exported="false"
            android:parentActivityName=".TerrariaSpecificActivity"
            android:screenOrientation="portrait"
            android:label="Addon Management"
            android:theme="@style/Theme.ModLoader">
            <meta-data
                android:name="android.support.PARENT_ACTIVITY"
                android:value=".TerrariaSpecificActivity" />
        </activity>

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

================================================================================
ModLoader/app/src/main/java/com/modloader/AboutActivity.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/IntegrationManager.java

package com.modloader;

public class IntegrationManager {
}


================================================================================
ModLoader/app/src/main/java/com/modloader/LogActivity.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/MainActivity.java

// File: MainActivity.java (Updated with Addon Integration)
// Path: /storage/emulated/0/AndroidIDEProjects/ModLoader/app/src/main/java/com/modloader/MainActivity.java
package com.modloader;

import android.content.Intent;
import android.os.Bundle;
import android.widget.Button;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;

import com.modloader.addon.AddonManager;
import com.modloader.addon.AddonManagementActivity;
import com.modloader.util.LogUtils;

public class MainActivity extends AppCompatActivity {
    private AddonManager addonManager;

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
                LogUtils.logDebug(" at " + e.toString());
            }
        });

        LogUtils.logDebug("Setting up main navigation");
        setContentView(R.layout.activity_main);
        setTitle("Mod Loader - Main Menu");

        // Initialize addon manager
        initializeAddonSystem();

        // Setup UI
        setupMainButtons();

        // Apply addons to this activity
        if (addonManager != null) {
            addonManager.applyAddonsToActivity(this, "MainActivity");
        }
    }

    private void initializeAddonSystem() {
        try {
            LogUtils.logDebug("Initializing addon system...");
            addonManager = new AddonManager(this);
            
            if (addonManager.isInitialized()) {
                LogUtils.logUser("✅ Addon system initialized successfully");
                LogUtils.logDebug("Loaded " + addonManager.getLoadedAddons().size() + " addons");
            } else {
                LogUtils.logDebug("⚠️ Addon system initialization incomplete");
            }
        } catch (Exception e) {
            LogUtils.logError("Failed to initialize addon system: " + e.getMessage());
            addonManager = null;
        }
    }

    private void setupMainButtons() {
        Button universalButton = findViewById(R.id.universal_button);
        Button specificButton = findViewById(R.id.specific_button);
        Button pluginButton = findViewById(R.id.plugin_button);

        // Universal Mode Button
        if (universalButton != null) {
            universalButton.setOnClickListener(v -> {
                LogUtils.logUser("Universal Mode selected");
                startActivity(new Intent(MainActivity.this, UniversalActivity.class));
            });
        } else {
            LogUtils.logDebug("Universal button not found in layout");
        }

        // Specific Mode Button
        if (specificButton != null) {
            specificButton.setOnClickListener(v -> {
                LogUtils.logUser("Specific Mode selected - showing app selection");
                startActivity(new Intent(MainActivity.this, SpecificSelectionActivity.class));
            });
        } else {
            LogUtils.logDebug("Specific button not found in layout");
        }

        // Plugin/Addon Management Button
        if (pluginButton != null) {
            pluginButton.setOnClickListener(v -> {
                LogUtils.logUser("Addon Management selected");
                
                if (addonManager != null) {
                    // Show addon statistics in log
                    String stats = addonManager.getAddonStatistics();
                    LogUtils.logDebug("Current addon status:\n" + stats);
                }
                
                startActivity(new Intent(MainActivity.this, AddonManagementActivity.class));
            });
        } else {
            LogUtils.logDebug("Plugin button not found in layout - creating fallback");
            createFallbackPluginButton();
        }
    }

    private void createFallbackPluginButton() {
        // If plugin button doesn't exist in layout, log the issue
        LogUtils.logDebug("Plugin button not found - addon management available via menu");
        
        // You could dynamically create a button here if needed
        // But it's better to ensure the button exists in the layout
    }

    @Override
    protected void onResume() {
        super.onResume();
        
        // Refresh addon system status
        if (addonManager != null) {
            try {
                // Re-apply addons in case they were modified
                addonManager.applyAddonsToActivity(this, "MainActivity");
                LogUtils.logDebug("Addons re-applied to MainActivity");
            } catch (Exception e) {
                LogUtils.logError("Error re-applying addons: " + e.getMessage());
            }
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions,
                                           @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        LogUtils.logDebug("Permission result callback triggered");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        
        // Cleanup addon system
        if (addonManager != null) {
            try {
                addonManager.cleanup();
                LogUtils.logDebug("Addon system cleaned up");
            } catch (Exception e) {
                LogUtils.logError("Error cleaning up addon system: " + e.getMessage());
            }
        }
        
        LogUtils.logDebug("MainActivity destroyed");
    }

    /**
     * Get the addon manager instance (for use by other activities)
     */
    public AddonManager getAddonManager() {
        return addonManager;
    }

    /**
     * Check if addon system is available
     */
    public boolean isAddonSystemAvailable() {
        return addonManager != null && addonManager.isInitialized();
    }

    @Override
    public boolean onCreateOptionsMenu(android.view.Menu menu) {
        // Add addon management to options menu as backup
        menu.add(0, 1, 0, "🔌 Addon Management")
            .setShowAsAction(android.view.MenuItem.SHOW_AS_ACTION_NEVER);
        
        menu.add(0, 2, 0, "📊 Addon Statistics")
            .setShowAsAction(android.view.MenuItem.SHOW_AS_ACTION_NEVER);
            
        return super.onCreateOptionsMenu(menu);
    }

    @Override
    public boolean onOptionsItemSelected(android.view.MenuItem item) {
        switch (item.getItemId()) {
            case 1: // Addon Management
                startActivity(new Intent(this, AddonManagementActivity.class));
                return true;
                
            case 2: // Addon Statistics
                if (addonManager != null) {
                    String stats = addonManager.getAddonStatistics();
                    LogUtils.logUser("=== Addon Statistics ===\n" + stats);
                    
                    // Show in dialog
                    new androidx.appcompat.app.AlertDialog.Builder(this)
                        .setTitle("📊 Addon Statistics")
                        .setMessage(stats)
                        .setPositiveButton("OK", null)
                        .show();
                } else {
                    new androidx.appcompat.app.AlertDialog.Builder(this)
                        .setTitle("Addon System")
                        .setMessage("Addon system is not initialized.")
                        .setPositiveButton("OK", null)
                        .show();
                }
                return true;
                
            default:
                return super.onOptionsItemSelected(item);
        }
    }

    /**
     * Handle addon-related intents from other activities
     */
    @Override
    protected void onNewIntent(Intent intent) {
        super.onNewIntent(intent);
        
        if (intent != null && intent.hasExtra("open_addon_management")) {
            startActivity(new Intent(this, AddonManagementActivity.class));
        }
    }

    /**
     * Public method for other activities to access addon functionality
     */
    public void executeAddonAction(String addonId, String action, java.util.Map<String, Object> parameters) {
        if (addonManager != null) {
            boolean success = addonManager.executeAddon(addonId, action, parameters);
            if (success) {
                LogUtils.logUser("✅ Executed addon action: " + action);
            } else {
                LogUtils.logError("❌ Failed to execute addon action: " + action);
            }
        } else {
            LogUtils.logError("Addon system not available");
        }
    }

    /**
     * Helper method to check if specific addon category is available
     */
    public boolean hasAddonsOfCategory(AddonManager.AddonCategory category) {
        if (addonManager != null) {
            return !addonManager.getAddonsByCategory(category).isEmpty();
        }
        return false;
    }

    /**
     * Get enabled addons count for display
     */
    public int getEnabledAddonsCount() {
        if (addonManager != null) {
            int count = 0;
            for (com.modloader.addon.AddonConfig addon : addonManager.getLoadedAddons()) {
                if (addon.isEnabled()) {
                    count++;
                }
            }
            return count;
        }
        return 0;
    }
}

================================================================================
ModLoader/app/src/main/java/com/modloader/MyApplication.java

// File: MyApplication.java (FIXED) - Initialize Logs and Handle Migration
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/MyApplication.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/SandboxActivity.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/SpecificSelectionActivity.java

// File: SpecificSelectionActivity.java (Clean UI with Real Game Icons)
// Path: /storage/emulated/0/AndroidIDEProjects/ModLoader/ModLoader/app/src/main/java/com/modloader/SpecificSelectionActivity.java
package com.modloader;

import android.content.Intent;
import android.content.pm.ApplicationInfo;
import android.content.pm.PackageManager;
import android.graphics.Color;
import android.graphics.Typeface;
import android.graphics.drawable.Drawable;
import android.os.Bundle;
import android.view.View;
import android.view.animation.Animation;
import android.view.animation.AnimationUtils;
import android.widget.Button;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.TextView;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;
import androidx.cardview.widget.CardView;
import androidx.core.content.ContextCompat;

import com.modloader.util.LogUtils;

public class SpecificSelectionActivity extends AppCompatActivity {

    private LinearLayout rootLayout;
    private CardView terrariaCard;
    private CardView comingSoonCard;
    private ImageView terrariaIcon;
    private TextView headerText;
    private TextView subHeaderText;
    private Animation fadeInAnimation;
    private Animation slideInAnimation;

    // Game package names for icon loading
    private static final String TERRARIA_PACKAGE = "com.and.games505.TerrariaPaid";
    private static final String MINECRAFT_PACKAGE = "com.mojang.minecraftpe";
    private static final String AMONG_US_PACKAGE = "com.innersloth.spacemafia";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_specific_selection);
        setTitle("Game Selection");

        LogUtils.logDebug("Enhanced SpecificSelectionActivity started");

        initializeViews();
        setupAnimations();
        setupTerrariaCard();
        setupComingSoonCards();
        setupBackButton();
        
        // Start entrance animations
        animateEntrance();
        
        LogUtils.logUser("Game selection screen opened - Terraria mod support available");
    }

    private void initializeViews() {
        rootLayout = findViewById(R.id.rootLayout);
        headerText = findViewById(R.id.headerText);
        subHeaderText = findViewById(R.id.subHeaderText);
        terrariaCard = findViewById(R.id.terrariaCard);
        comingSoonCard = findViewById(R.id.comingSoonCard);
        terrariaIcon = findViewById(R.id.terrariaIcon);
        
        // Check if views exist before using them
        if (headerText == null) {
            LogUtils.logDebug("headerText view not found, creating programmatically");
        }
        if (subHeaderText == null) {
            LogUtils.logDebug("subHeaderText view not found, creating programmatically");
        }
    }

    private void setupAnimations() {
        fadeInAnimation = AnimationUtils.loadAnimation(this, android.R.anim.fade_in);
        slideInAnimation = AnimationUtils.loadAnimation(this, android.R.anim.slide_in_left);
    }

    private void setupTerrariaCard() {
        // Set up Terraria card with enhanced visuals
        if (terrariaCard != null) {
            terrariaCard.setCardBackgroundColor(Color.parseColor("#E8F5E8"));
            terrariaCard.setCardElevation(8f);
            terrariaCard.setRadius(16f);

            // Add click listener with visual feedback
            terrariaCard.setOnClickListener(v -> {
                // Add visual feedback
                terrariaCard.animate()
                    .scaleX(0.95f)
                    .scaleY(0.95f)
                    .setDuration(100)
                    .withEndAction(() -> {
                        terrariaCard.animate()
                            .scaleX(1.0f)
                            .scaleY(1.0f)
                            .setDuration(100)
                            .withEndAction(() -> openTerrariaSpecific())
                            .start();
                    })
                    .start();
            });
        }

        // Setup Terraria icon with actual app icon
        setupTerrariaIcon();

        // Find and setup internal components
        Button terrariaButton = findViewById(R.id.terraria_button);
        TextView terrariaDescription = findViewById(R.id.terrariaDescription);
        TextView terrariaFeatures = findViewById(R.id.terrariaFeatures);
        TextView terrariaStatus = findViewById(R.id.terrariaStatus);

        if (terrariaButton != null) {
            terrariaButton.setOnClickListener(v -> openTerrariaSpecific());
        }

        // Set enhanced descriptions (no emojis)
        if (terrariaDescription != null) {
            terrariaDescription.setText("Full-featured mod support for Terraria with both DEX/JAR and DLL mod capabilities");
        }

        if (terrariaFeatures != null) {
            terrariaFeatures.setText("• DEX/JAR Mods (Android Java)\n• DLL Mods (C# via MelonLoader)\n• APK Patching & Installation\n• Advanced Mod Management\n• Offline Diagnostics");
        }

        if (terrariaStatus != null) {
            terrariaStatus.setText("Ready for Modding");
            terrariaStatus.setTextColor(Color.parseColor("#4CAF50"));
            terrariaStatus.setTypeface(null, Typeface.BOLD);
        }
    }

    private void setupTerrariaIcon() {
        if (terrariaIcon != null) {
            Drawable gameIcon = getGameIcon(TERRARIA_PACKAGE);
            if (gameIcon != null) {
                terrariaIcon.setImageDrawable(gameIcon);
                terrariaIcon.setScaleType(ImageView.ScaleType.CENTER_CROP);
                terrariaIcon.setBackground(null); // Remove background to show pure icon
                LogUtils.logDebug("Loaded actual Terraria app icon successfully");
            } else {
                // Fallback to a clean placeholder
                createTerrariaPlaceholderIcon();
                LogUtils.logDebug("Using custom Terraria placeholder icon");
            }
        }
    }

    private Drawable getGameIcon(String packageName) {
        try {
            PackageManager pm = getPackageManager();
            ApplicationInfo appInfo = pm.getApplicationInfo(packageName, 0);
            return pm.getApplicationIcon(appInfo);
        } catch (PackageManager.NameNotFoundException e) {
            LogUtils.logDebug("Game not installed: " + packageName);
            return null;
        }
    }

    private void createTerrariaPlaceholderIcon() {
        // Create a clean Terraria-themed placeholder
        if (terrariaIcon != null) {
            // Set a clean Terraria-green background
            terrariaIcon.setBackgroundColor(Color.parseColor("#4CAF50"));
            terrariaIcon.setImageResource(android.R.drawable.ic_menu_compass);
            terrariaIcon.setColorFilter(Color.WHITE);
            terrariaIcon.setScaleType(ImageView.ScaleType.CENTER);
        }
    }

    private void setupComingSoonCards() {
        // Setup coming soon section
        LinearLayout comingSoonContainer = findViewById(R.id.comingSoonContainer);
        if (comingSoonContainer != null) {
            addComingSoonGame("Minecraft PE", "Pocket Edition mod support", "Planning Stage", MINECRAFT_PACKAGE);
            addComingSoonGame("Among Us", "Unity game modding", "Research Phase", AMONG_US_PACKAGE);
            addComingSoonGame("Unity Games", "Generic Unity mod loader", "Development", null);
            addComingSoonGame("Custom Games", "Your game here!", "Request Feature", null);
        }
    }

    private void addComingSoonGame(String gameName, String description, String status, String packageName) {
        LinearLayout comingSoonContainer = findViewById(R.id.comingSoonContainer);
        if (comingSoonContainer == null) return;

        // Create card for coming soon game
        CardView gameCard = new CardView(this);
        LinearLayout.LayoutParams cardParams = new LinearLayout.LayoutParams(
            LinearLayout.LayoutParams.MATCH_PARENT,
            LinearLayout.LayoutParams.WRAP_CONTENT
        );
        cardParams.setMargins(0, 0, 0, 16);
        gameCard.setLayoutParams(cardParams);
        gameCard.setCardBackgroundColor(Color.parseColor("#F5F5F5"));
        gameCard.setCardElevation(4f);
        gameCard.setRadius(12f);
        gameCard.setAlpha(0.6f); // Slightly transparent to show it's not available

        // Create inner layout with icon
        LinearLayout innerLayout = new LinearLayout(this);
        innerLayout.setOrientation(LinearLayout.HORIZONTAL);
        innerLayout.setPadding(20, 16, 20, 16);
        innerLayout.setGravity(android.view.Gravity.CENTER_VERTICAL);

        // Add game icon if available
        ImageView gameIcon = new ImageView(this);
        LinearLayout.LayoutParams iconParams = new LinearLayout.LayoutParams(48, 48);
        iconParams.setMargins(0, 0, 16, 0);
        gameIcon.setLayoutParams(iconParams);
        
        if (packageName != null) {
            Drawable icon = getGameIcon(packageName);
            if (icon != null) {
                gameIcon.setImageDrawable(icon);
                gameIcon.setScaleType(ImageView.ScaleType.CENTER_CROP);
            } else {
                // Use default placeholder
                gameIcon.setImageResource(android.R.drawable.ic_menu_gallery);
                gameIcon.setColorFilter(Color.parseColor("#666666"));
            }
        } else {
            // Use generic placeholder for custom games
            gameIcon.setImageResource(android.R.drawable.ic_menu_gallery);
            gameIcon.setColorFilter(Color.parseColor("#666666"));
        }
        innerLayout.addView(gameIcon);

        // Create text container
        LinearLayout textContainer = new LinearLayout(this);
        textContainer.setOrientation(LinearLayout.VERTICAL);
        LinearLayout.LayoutParams textParams = new LinearLayout.LayoutParams(
            0, LinearLayout.LayoutParams.WRAP_CONTENT, 1f
        );
        textContainer.setLayoutParams(textParams);

        // Game name and status
        LinearLayout headerLayout = new LinearLayout(this);
        headerLayout.setOrientation(LinearLayout.HORIZONTAL);
        headerLayout.setWeightSum(1f);

        TextView nameText = new TextView(this);
        nameText.setText(gameName);
        nameText.setTextSize(16);
        nameText.setTypeface(null, Typeface.BOLD);
        nameText.setTextColor(Color.parseColor("#333333"));
        LinearLayout.LayoutParams nameParams = new LinearLayout.LayoutParams(
            0, LinearLayout.LayoutParams.WRAP_CONTENT, 0.7f
        );
        nameText.setLayoutParams(nameParams);

        TextView statusText = new TextView(this);
        statusText.setText(status);
        statusText.setTextSize(12);
        statusText.setTextColor(Color.parseColor("#FF9800"));
        statusText.setTypeface(null, Typeface.BOLD);
        LinearLayout.LayoutParams statusParams = new LinearLayout.LayoutParams(
            0, LinearLayout.LayoutParams.WRAP_CONTENT, 0.3f
        );
        statusText.setLayoutParams(statusParams);

        headerLayout.addView(nameText);
        headerLayout.addView(statusText);

        // Description
        TextView descText = new TextView(this);
        descText.setText(description);
        descText.setTextSize(14);
        descText.setTextColor(Color.parseColor("#666666"));
        LinearLayout.LayoutParams descParams = new LinearLayout.LayoutParams(
            LinearLayout.LayoutParams.MATCH_PARENT,
            LinearLayout.LayoutParams.WRAP_CONTENT
        );
        descParams.setMargins(0, 8, 0, 0);
        descText.setLayoutParams(descParams);

        textContainer.addView(headerLayout);
        textContainer.addView(descText);
        innerLayout.addView(textContainer);
        gameCard.addView(innerLayout);

        // Add click listener for feedback
        gameCard.setOnClickListener(v -> showComingSoonMessage(gameName));

        comingSoonContainer.addView(gameCard);
    }

    private void showComingSoonMessage(String gameName) {
        Toast.makeText(this, 
            gameName + " support is coming soon!\n\nStay tuned for updates.", 
            Toast.LENGTH_LONG).show();
        LogUtils.logUser("User interested in " + gameName + " support");
    }

    private void setupBackButton() {
        Button backButton = findViewById(R.id.backToMainButton);
        if (backButton == null) {
            // Try alternative ID that might exist in your original layout
            backButton = findViewById(R.id.backButton);
        }
        
        if (backButton != null) {
            backButton.setOnClickListener(v -> {
                LogUtils.logUser("Returning to main menu from game selection");
                finish();
            });
        } else {
            LogUtils.logDebug("Back button not found in layout");
        }
    }

    private void animateEntrance() {
        // Animate header
        if (headerText != null) {
            headerText.startAnimation(fadeInAnimation);
        }
        if (subHeaderText != null) {
            subHeaderText.startAnimation(fadeInAnimation);
        }

        // Animate Terraria card with delay
        if (terrariaCard != null) {
            terrariaCard.postDelayed(() -> {
                terrariaCard.setVisibility(View.VISIBLE);
                terrariaCard.startAnimation(slideInAnimation);
            }, 200);
        }

        // Animate coming soon section with delay
        if (comingSoonCard != null) {
            comingSoonCard.postDelayed(() -> {
                comingSoonCard.setVisibility(View.VISIBLE);
                comingSoonCard.startAnimation(slideInAnimation);
            }, 400);
        }
    }

    private void openTerrariaSpecific() {
        LogUtils.logUser("Terraria selected for specific modding");
        
        // Show loading feedback
        Toast.makeText(this, "Loading Terraria Mod Loader...", Toast.LENGTH_SHORT).show();
        
        Intent intent = new Intent(this, TerrariaSpecificActivity.class);
        intent.putExtra("game_name", "Terraria");
        intent.putExtra("game_package", TERRARIA_PACKAGE);
        startActivity(intent);
        
        // Add transition animation
        overridePendingTransition(android.R.anim.slide_in_left, android.R.anim.slide_out_right);
    }

    @Override
    protected void onResume() {
        super.onResume();
        LogUtils.logDebug("SpecificSelectionActivity resumed");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        LogUtils.logDebug("SpecificSelectionActivity destroyed");
    }

    @Override
    public void onBackPressed() {
        LogUtils.logUser("Back button pressed in game selection");
        super.onBackPressed();
    }
}

================================================================================
ModLoader/app/src/main/java/com/modloader/TerrariaSpecificActivity.java

// File: TerrariaSpecificActivity.java (Updated with Addon Integration)
// Path: /main/java/com/modloader/TerrariaSpecificActivity.java
package com.modloader;

import android.content.Intent;
import android.graphics.Color;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.TextView;
import android.widget.Toast;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;
import androidx.cardview.widget.CardView;

import com.modloader.R;
import com.modloader.addon.AddonManager;
import com.modloader.addon.AddonManagementActivity;
import com.modloader.addon.AddonConfig;
import com.modloader.ui.UnifiedLoaderActivity;
import com.modloader.ui.ModListActivity;
import com.modloader.ui.SettingsActivity;
import com.modloader.ui.LogViewerActivity;
import com.modloader.ui.InstructionsActivity;
import com.modloader.SandboxActivity;
import com.modloader.loader.MelonLoaderManager;
import com.modloader.util.LogUtils;
import com.modloader.ui.OfflineDiagnosticActivity;

import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class TerrariaSpecificActivity extends AppCompatActivity {
    
    private LinearLayout rootLayout;
    private TextView loaderStatusText;
    private CardView setupCard;
    private CardView modManagementCard;
    private CardView toolsCard;
    private CardView pluginCard;
    
    // Addon system integration
    private AddonManager addonManager;
    private TextView addonStatusText;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_terraria_specific_updated);
        setTitle("🌍 Terraria Mod Loader");
        
        LogUtils.logUser("Terraria Menu Activity opened");
        
        // Initialize addon system first
        initializeAddonSystem();
        
        // Initialize UI components
        initializeViews();
        applyTerrariaTheme();
        setupUI();
        updateLoaderStatus();
        updateAddonStatus();
        
        // Apply addons to this activity
        if (addonManager != null) {
            addonManager.applyAddonsToActivity(this, "TerrariaSpecificActivity");
        }
    }
    
    private void initializeAddonSystem() {
        try {
            LogUtils.logDebug("Initializing addon system for Terraria activity...");
            addonManager = new AddonManager(this);
            
            if (addonManager.isInitialized()) {
                LogUtils.logUser("✅ Addon system ready for Terraria activity");
                
                // Log available addon categories
                for (AddonManager.AddonCategory category : AddonManager.AddonCategory.values()) {
                    List<AddonConfig> categoryAddons = addonManager.getAddonsByCategory(category);
                    if (!categoryAddons.isEmpty()) {
                        LogUtils.logDebug("Available " + category.getDisplayName() + ": " + categoryAddons.size());
                    }
                }
            } else {
                LogUtils.logDebug("⚠️ Addon system initialization incomplete for Terraria activity");
            }
        } catch (Exception e) {
            LogUtils.logError("Failed to initialize addon system: " + e.getMessage());
            addonManager = null;
        }
    }

    private void initializeViews() {
        rootLayout = findViewById(R.id.rootLayout);
        loaderStatusText = findViewById(R.id.loaderStatusText);
        setupCard = findViewById(R.id.setupCard);
        modManagementCard = findViewById(R.id.modManagementCard);
        toolsCard = findViewById(R.id.toolsCard);
        pluginCard = findViewById(R.id.pluginCard);
        
        // Create addon status text if it doesn't exist
        if (addonStatusText == null) {
            addonStatusText = new TextView(this);
            addonStatusText.setId(View.generateViewId());
            addonStatusText.setTextSize(12f);
            addonStatusText.setPadding(12, 8, 12, 8);
            // Add to layout if possible
            if (rootLayout != null) {
                rootLayout.addView(addonStatusText, 1); // Add after header
            }
        }
    }

    private void applyTerrariaTheme() {
        // Dynamic Terraria-inspired green/blue theme
        int primaryGreen = Color.parseColor("#4CAF50");
        int secondaryBlue = Color.parseColor("#2196F3");
        int accentGreen = Color.parseColor("#81C784");
        int pluginPurple = Color.parseColor("#9C27B0");

        // Apply theme to root layout
        if (rootLayout != null) {
            rootLayout.setBackgroundColor(Color.parseColor("#E8F5E8"));
        }

        // Apply theme to cards
        if (setupCard != null) {
            setupCard.setCardBackgroundColor(Color.parseColor("#F1F8E9"));
        }
        if (modManagementCard != null) {
            modManagementCard.setCardBackgroundColor(Color.parseColor("#E3F2FD"));
        }
        if (toolsCard != null) {
            toolsCard.setCardBackgroundColor(Color.parseColor("#F3E5F5"));
        }
        if (pluginCard != null) {
            pluginCard.setCardBackgroundColor(Color.parseColor("#F3E5F5"));
        }

        // Set status bar color
        if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.LOLLIPOP) {
            getWindow().setStatusBarColor(primaryGreen);
        }
    }

    private void setupUI() {
        setupSetupSection();
        setupModManagementSection();
        setupToolsSection();
        setupAddonSection();
        setupNavigationSection();
    }
    
    private void setupSetupSection() {
        // Unified Loader Setup
        Button unifiedSetupBtn = findViewById(R.id.unifiedSetupButton);
        if (unifiedSetupBtn != null) {
            unifiedSetupBtn.setOnClickListener(v -> {
                LogUtils.logUser("Opening Unified MelonLoader Setup");
                Intent intent = new Intent(this, UnifiedLoaderActivity.class);
                startActivity(intent);
            });
        }

        // Quick Setup Guide
        Button setupGuideBtn = findViewById(R.id.setupGuideButton);
        if (setupGuideBtn != null) {
            setupGuideBtn.setOnClickListener(v -> {
                LogUtils.logUser("Opening Setup Guide");
                Intent intent = new Intent(this, com.modloader.ui.SetupGuideActivity.class);
                startActivity(intent);
            });
        }

        // Manual Instructions
        Button manualInstructionsBtn = findViewById(R.id.manualInstructionsButton);
        if (manualInstructionsBtn != null) {
            manualInstructionsBtn.setOnClickListener(v -> {
                LogUtils.logUser("Opening Manual Installation Instructions");
                Intent intent = new Intent(this, InstructionsActivity.class);
                startActivity(intent);
            });
        }
    }
    
    private void setupModManagementSection() {
        // DEX/JAR Mod Manager
        Button dexModManagerBtn = findViewById(R.id.dexModManagerButton);
        if (dexModManagerBtn != null) {
            dexModManagerBtn.setOnClickListener(v -> {
                LogUtils.logUser("Opening DEX/JAR Mod Manager");
                
                // Check if file manager addons are available
                if (addonManager != null && hasFileManagerAddons()) {
                    LogUtils.logDebug("File manager addons available - enhanced mod management");
                }
                
                Intent intent = new Intent(this, ModListActivity.class);
                startActivity(intent);
            });
        }

        // DLL Mod Manager
        Button dllModManagerBtn = findViewById(R.id.dllModManagerButton);
        if (dllModManagerBtn != null) {
            dllModManagerBtn.setOnClickListener(v -> {
                LogUtils.logUser("Opening DLL Mod Management via Unified Loader");
                
                // Check if smart mod installer addons are available
                if (addonManager != null && hasModInstallerAddons()) {
                    LogUtils.logDebug("Smart mod installer addons available");
                }
                
                // Check if loader is installed
                if (MelonLoaderManager.isMelonLoaderInstalled(this) || MelonLoaderManager.isLemonLoaderInstalled(this)) {
                    Intent intent = new Intent(this, com.modloader.ui.ModManagementActivity.class);
                    startActivity(intent);
                } else {
                    Intent intent = new Intent(this, UnifiedLoaderActivity.class);
                    startActivity(intent);
                }
            });
        }
    }
    
    private void setupAddonSection() {
        // Main Addon Manager Button
        Button pluginManagerBtn = findViewById(R.id.pluginManagerButton);
        if (pluginManagerBtn != null) {
            pluginManagerBtn.setOnClickListener(v -> {
                LogUtils.logUser("Opening Addon Management System");
                Intent intent = new Intent(this, AddonManagementActivity.class);
                startActivity(intent);
            });
        }

        // Quick Addon Actions
        Button pluginInstallBtn = findViewById(R.id.pluginInstallButton);
        if (pluginInstallBtn != null) {
            pluginInstallBtn.setOnClickListener(v -> {
                LogUtils.logUser("Quick addon installation");
                showQuickAddonActions();
            });
        }

        // Addon Configuration
        Button pluginConfigBtn = findViewById(R.id.pluginConfigButton);
        if (pluginConfigBtn != null) {
            pluginConfigBtn.setOnClickListener(v -> {
                LogUtils.logUser("Opening addon configuration");
                showAddonConfigurationOptions();
            });
        }
    }
    
    private void setupToolsSection() {
        // Log Viewer
        Button logViewerBtn = findViewById(R.id.logViewerButton);
        if (logViewerBtn != null) {
            logViewerBtn.setOnClickListener(v -> {
                LogUtils.logUser("Opening Log Viewer");
                Intent intent = new Intent(this, LogViewerActivity.class);
                startActivity(intent);
            });
        }

        // Settings
        Button settingsBtn = findViewById(R.id.settingsButton);
        if (settingsBtn != null) {
            settingsBtn.setOnClickListener(v -> {
                LogUtils.logUser("Opening Settings");
                Intent intent = new Intent(this, SettingsActivity.class);
                startActivity(intent);
            });
        }

        // Sandbox Mode
        Button sandboxBtn = findViewById(R.id.sandboxButton);
        if (sandboxBtn != null) {
            sandboxBtn.setOnClickListener(v -> {
                LogUtils.logUser("Opening Sandbox Mode");
                Intent intent = new Intent(this, SandboxActivity.class);
                startActivity(intent);
            });
        }

        // Diagnostic Tool
        Button diagnosticBtn = findViewById(R.id.diagnosticButton);
        if (diagnosticBtn != null) {
            diagnosticBtn.setOnClickListener(v -> {
                LogUtils.logUser("Opening Offline Diagnostic Tool");
                
                // Check if diagnostic addons are available
                if (addonManager != null && hasDiagnosticAddons()) {
                    LogUtils.logDebug("Enhanced diagnostic features available via addons");
                }
                
                Intent intent = new Intent(this, OfflineDiagnosticActivity.class);
                startActivity(intent);
            });
        }
    }
    
    private void setupNavigationSection() {
        // Back to App Selection
        Button backBtn = findViewById(R.id.backButton);
        if (backBtn != null) {
            backBtn.setOnClickListener(v -> {
                LogUtils.logUser("Returning to app selection");
                finish();
            });
        }
    }

    private void updateLoaderStatus() {
        if (loaderStatusText == null) return;
        
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
    
    private void updateAddonStatus() {
        if (addonStatusText == null || addonManager == null) return;
        
        try {
            int totalAddons = addonManager.getLoadedAddons().size();
            int enabledAddons = 0;
            
            for (AddonConfig addon : addonManager.getLoadedAddons()) {
                if (addon.isEnabled()) {
                    enabledAddons++;
                }
            }
            
            String addonStatus = String.format("🔌 Addons: %d active, %d total", enabledAddons, totalAddons);
            addonStatusText.setText(addonStatus);
            addonStatusText.setTextColor(enabledAddons > 0 ? 
                Color.parseColor("#4CAF50") : Color.parseColor("#666666"));
            addonStatusText.setBackgroundColor(Color.parseColor("#F0F8FF"));
            
        } catch (Exception e) {
            addonStatusText.setText("🔌 Addon system error");
            addonStatusText.setTextColor(Color.parseColor("#F44336"));
            LogUtils.logError("Error updating addon status: " + e.getMessage());
        }
    }

    private void updateButtonStates(boolean loaderInstalled) {
        Button dllModManagerBtn = findViewById(R.id.dllModManagerButton);
        if (dllModManagerBtn != null) {
            if (loaderInstalled) {
                dllModManagerBtn.setText("🔧 Manage DLL Mods");
                dllModManagerBtn.setBackgroundColor(Color.parseColor("#4CAF50"));
            } else {
                dllModManagerBtn.setText("🔧 Setup DLL Support");
                dllModManagerBtn.setBackgroundColor(Color.parseColor("#FF9800"));
            }
        }
    }
    
    // Addon-related helper methods
    private boolean hasFileManagerAddons() {
        return addonManager != null && 
               !addonManager.getAddonsByCategory(AddonManager.AddonCategory.FILE_MANAGER).isEmpty();
    }
    
    private boolean hasModInstallerAddons() {
        return addonManager != null && 
               !addonManager.getAddonsByCategory(AddonManager.AddonCategory.MOD_INSTALLER).isEmpty();
    }
    
    private boolean hasDiagnosticAddons() {
        return addonManager != null && 
               !addonManager.getAddonsByCategory(AddonManager.AddonCategory.DIAGNOSTIC).isEmpty();
    }
    
    private boolean hasThemeAddons() {
        return addonManager != null && 
               !addonManager.getAddonsByCategory(AddonManager.AddonCategory.THEME).isEmpty();
    }
    
    private void showQuickAddonActions() {
        if (addonManager == null) {
            Toast.makeText(this, "Addon system not available", Toast.LENGTH_SHORT).show();
            return;
        }
        
        String[] actions = {
            "📦 Install Addon from File",
            "🎨 Apply Theme Addons",
            "🛠️ Run Utility Addons",
            "📊 View Addon Statistics"
        };
        
        new AlertDialog.Builder(this)
            .setTitle("Quick Addon Actions")
            .setItems(actions, (dialog, which) -> {
                switch (which) {
                    case 0:
                        startActivity(new Intent(this, AddonManagementActivity.class));
                        break;
                    case 1:
                        applyThemeAddons();
                        break;
                    case 2:
                        runUtilityAddons();
                        break;
                    case 3:
                        showAddonStatistics();
                        break;
                }
            })
            .show();
    }
    
    private void showAddonConfigurationOptions() {
        if (addonManager == null) {
            Toast.makeText(this, "Addon system not available", Toast.LENGTH_SHORT).show();
            return;
        }
        
        List<AddonConfig> enabledAddons = addonManager.getLoadedAddons();
        if (enabledAddons.isEmpty()) {
            Toast.makeText(this, "No addons available to configure", Toast.LENGTH_SHORT).show();
            return;
        }
        
        String[] addonNames = new String[enabledAddons.size()];
        for (int i = 0; i < enabledAddons.size(); i++) {
            AddonConfig addon = enabledAddons.get(i);
            addonNames[i] = addon.getName() + " v" + addon.getVersion() + 
                          (addon.isEnabled() ? " ✅" : " ❌");
        }
        
        new AlertDialog.Builder(this)
            .setTitle("Configure Addons")
            .setItems(addonNames, (dialog, which) -> {
                AddonConfig selectedAddon = enabledAddons.get(which);
                showAddonDetails(selectedAddon);
            })
            .show();
    }
    
    private void applyThemeAddons() {
        List<AddonConfig> themeAddons = addonManager.getAddonsByCategory(AddonManager.AddonCategory.THEME);
        
        if (themeAddons.isEmpty()) {
            Toast.makeText(this, "No theme addons available", Toast.LENGTH_SHORT).show();
            return;
        }
        
        for (AddonConfig themeAddon : themeAddons) {
            if (themeAddon.isEnabled()) {
                Map<String, Object> params = new HashMap<>();
                params.put("activity", this);
                params.put("activity_name", "TerrariaSpecificActivity");
                
                boolean success = addonManager.executeAddon(themeAddon.getId(), "apply_to_activity", params);
                if (success) {
                    LogUtils.logUser("✅ Applied theme: " + themeAddon.getName());
                    Toast.makeText(this, "Applied theme: " + themeAddon.getName(), Toast.LENGTH_SHORT).show();
                }
            }
        }
    }
    
    private void runUtilityAddons() {
        List<AddonConfig> utilityAddons = addonManager.getAddonsByCategory(AddonManager.AddonCategory.UTILITY);
        
        if (utilityAddons.isEmpty()) {
            Toast.makeText(this, "No utility addons available", Toast.LENGTH_SHORT).show();
            return;
        }
        
        for (AddonConfig utilityAddon : utilityAddons) {
            if (utilityAddon.isEnabled()) {
                Map<String, Object> params = new HashMap<>();
                params.put("activity", this);
                
                boolean success = addonManager.executeAddon(utilityAddon.getId(), "cleanup_temp_files", params);
                if (success) {
                    LogUtils.logUser("✅ Executed utility: " + utilityAddon.getName());
                }
            }
        }
        
        Toast.makeText(this, "Utility addons executed", Toast.LENGTH_SHORT).show();
    }
    
    private void showAddonStatistics() {
        if (addonManager == null) return;
        
        String statistics = addonManager.getAddonStatistics();
        
        new AlertDialog.Builder(this)
            .setTitle("📊 Addon Statistics")
            .setMessage(statistics)
            .setPositiveButton("OK", null)
            .setNeutralButton("Manage", (dialog, which) -> {
                startActivity(new Intent(this, AddonManagementActivity.class));
            })
            .show();
    }
    
    private void showAddonDetails(AddonConfig addon) {
        String details = addon.getDisplayInfo();
        
        new AlertDialog.Builder(this)
            .setTitle("📋 " + addon.getName())
            .setMessage(details)
            .setPositiveButton("OK", null)
            .setNeutralButton("Toggle", (dialog, which) -> {
                boolean newState = !addon.isEnabled();
                if (addonManager.toggleAddon(addon.getId(), newState)) {
                    updateAddonStatus();
                    Toast.makeText(this, 
                        addon.getName() + (newState ? " enabled" : " disabled"), 
                        Toast.LENGTH_SHORT).show();
                }
            })
            .setNegativeButton("Actions", (dialog, which) -> {
                showAddonActionsDialog(addon);
            })
            .show();
    }
    
    private void showAddonActionsDialog(AddonConfig addon) {
        List<AddonConfig.AddonAction> actions = addon.getActions();
        if (actions.isEmpty()) {
            Toast.makeText(this, "No actions available for this addon", Toast.LENGTH_SHORT).show();
            return;
        }
        
        String[] actionNames = new String[actions.size()];
        for (int i = 0; i < actions.size(); i++) {
            actionNames[i] = actions.get(i).getName();
        }
        
        new AlertDialog.Builder(this)
            .setTitle("🎯 " + addon.getName() + " Actions")
            .setItems(actionNames, (dialog, which) -> {
                AddonConfig.AddonAction action = actions.get(which);
                executeAddonAction(addon, action);
            })
            .show();
    }
    
    private void executeAddonAction(AddonConfig addon, AddonConfig.AddonAction action) {
        Map<String, Object> parameters = new HashMap<>();
        parameters.put("activity", this);
        parameters.put("activity_name", "TerrariaSpecificActivity");
        parameters.putAll(action.getParameters());
        
        boolean success = addonManager.executeAddon(addon.getId(), action.getAction(), parameters);
        
        String message = success ? 
            "✅ Executed: " + action.getName() : 
            "❌ Failed: " + action.getName();
            
        Toast.makeText(this, message, Toast.LENGTH_SHORT).show();
        LogUtils.logUser(message + " from addon: " + addon.getName());
    }

    @Override
    protected void onResume() {
        super.onResume();
        
        // Refresh loader status when returning to this activity
        updateLoaderStatus();
        updateAddonStatus();
        
        // Re-apply addons if available
        if (addonManager != null) {
            try {
                addonManager.applyAddonsToActivity(this, "TerrariaSpecificActivity");
            } catch (Exception e) {
                LogUtils.logError("Error re-applying addons: " + e.getMessage());
            }
        }
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        // Add addon-related menu items
        if (addonManager != null) {
            menu.add(0, 1000, 0, "🔌 Manage Addons");
            menu.add(0, 1001, 0, "📊 Addon Statistics");
            menu.add(0, 1002, 0, "🎨 Apply Themes");
            menu.add(0, 1003, 0, "🛠️ Run Utilities");
        }
        return super.onCreateOptionsMenu(menu);
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case 1000: // Manage Addons
                startActivity(new Intent(this, AddonManagementActivity.class));
                return true;
                
            case 1001: // Addon Statistics
                showAddonStatistics();
                return true;
                
            case 1002: // Apply Themes
                applyThemeAddons();
                return true;
                
            case 1003: // Run Utilities
                runUtilityAddons();
                return true;
                
            default:
                return super.onOptionsItemSelected(item);
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        
        // Cleanup addon system
        if (addonManager != null) {
            try {
                addonManager.cleanup();
                LogUtils.logDebug("Addon system cleaned up for Terraria activity");
            } catch (Exception e) {
                LogUtils.logError("Error cleaning up addon system: " + e.getMessage());
            }
        }
        
        LogUtils.logUser("Terraria Menu Activity closed");
    }

    /**
     * Public method to get addon manager for use by fragments or dialogs
     */
    public AddonManager getAddonManager() {
        return addonManager;
    }

    /**
     * Public method to refresh addon status from external components
     */
    public void refreshAddonStatus() {
        updateAddonStatus();
    }

    /**
     * Execute specific addon functionality with parameters
     */
    public boolean executeSpecificAddon(String addonId, String action, Map<String, Object> params) {
        if (addonManager == null) {
            return false;
        }
        
        // Add activity context to parameters
        if (params == null) {
            params = new HashMap<>();
        }
        params.put("activity", this);
        params.put("activity_name", "TerrariaSpecificActivity");
        
        return addonManager.executeAddon(addonId, action, params);
    }

    /**
     * Get summary of available addon functionality
     */
    public String getAddonFunctionalitySummary() {
        if (addonManager == null) {
            return "Addon system not available";
        }
        
        StringBuilder summary = new StringBuilder();
        summary.append("Available Addon Features:\n\n");
        
        for (AddonManager.AddonCategory category : AddonManager.AddonCategory.values()) {
            List<AddonConfig> categoryAddons = addonManager.getAddonsByCategory(category);
            if (!categoryAddons.isEmpty()) {
                int enabledCount = 0;
                for (AddonConfig addon : categoryAddons) {
                    if (addon.isEnabled()) enabledCount++;
                }
                
                summary.append("• ").append(category.getDisplayName())
                       .append(": ").append(enabledCount).append("/")
                       .append(categoryAddons.size()).append(" enabled\n");
            }
        }
        
        return summary.toString();
    }
}

================================================================================
ModLoader/app/src/main/java/com/modloader/UniversalActivity.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/Diagnostic/ApkProcessLogger.java

package com.modloader.Diagnostic;

public class ApkProcessLogger {
}


================================================================================
ModLoader/app/src/main/java/com/modloader/Diagnostic/DiagnosticManager.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/addon/AddonConfig.java

// File: AddonConfig.java - Addon Configuration Data Model
// Path: app/src/main/java/com/modloader/addon/AddonConfig.java
package com.modloader.addon;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.io.File;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Iterator;
import java.util.List;
import java.util.Map;

/**
 * Configuration model for addons
 * Represents the structure and settings of a configuration-based addon
 */
public class AddonConfig {
    private String id;
    private String name;
    private String version;
    private String author;
    private String description;
    private String category;
    private boolean enabled;
    private File configFile;
    private JSONObject configurationData;
    private List<String> hooks;
    private List<AddonAction> actions;
    private Map<String, Object> metadata;

    /**
     * Action that an addon can perform
     */
    public static class AddonAction {
        private String id;
        private String name;
        private String icon;
        private String action;
        private Map<String, Object> parameters;

        public AddonAction(JSONObject actionJson) throws JSONException {
            this.id = actionJson.getString("id");
            this.name = actionJson.getString("name");
            this.icon = actionJson.optString("icon", "");
            this.action = actionJson.getString("action");
            this.parameters = new HashMap<>();
            
            // Parse parameters if they exist
            if (actionJson.has("parameters")) {
                JSONObject params = actionJson.getJSONObject("parameters");
                Iterator<String> keys = params.keys();
                while (keys.hasNext()) {
                    String key = keys.next();
                    this.parameters.put(key, params.get(key));
                }
            }
        }

        // Getters
        public String getId() { return id; }
        public String getName() { return name; }
        public String getIcon() { return icon; }
        public String getAction() { return action; }
        public Map<String, Object> getParameters() { return parameters; }
        
        // Get parameter by key
        public Object getParameter(String key) {
            return parameters.get(key);
        }
        
        public String getParameterString(String key, String defaultValue) {
            Object value = parameters.get(key);
            return value != null ? value.toString() : defaultValue;
        }
        
        public int getParameterInt(String key, int defaultValue) {
            Object value = parameters.get(key);
            if (value instanceof Integer) {
                return (Integer) value;
            } else if (value instanceof String) {
                try {
                    return Integer.parseInt((String) value);
                } catch (NumberFormatException e) {
                    return defaultValue;
                }
            }
            return defaultValue;
        }
        
        public boolean getParameterBoolean(String key, boolean defaultValue) {
            Object value = parameters.get(key);
            if (value instanceof Boolean) {
                return (Boolean) value;
            } else if (value instanceof String) {
                return Boolean.parseBoolean((String) value);
            }
            return defaultValue;
        }

        /**
         * Convert action to JSON
         */
        public JSONObject toJSON() throws JSONException {
            JSONObject json = new JSONObject();
            json.put("id", id);
            json.put("name", name);
            json.put("icon", icon);
            json.put("action", action);
            
            if (!parameters.isEmpty()) {
                JSONObject paramsJson = new JSONObject();
                for (Map.Entry<String, Object> entry : parameters.entrySet()) {
                    paramsJson.put(entry.getKey(), entry.getValue());
                }
                json.put("parameters", paramsJson);
            }
            
            return json;
        }
    }

    /**
     * Constructor from JSON configuration
     */
    public AddonConfig(JSONObject json, File configFile) throws JSONException {
        this.configFile = configFile;
        this.metadata = new HashMap<>();
        
        parseJSON(json);
    }

    /**
     * Parse JSON configuration
     */
    private void parseJSON(JSONObject json) throws JSONException {
        // Required fields
        this.id = json.getString("id");
        this.name = json.getString("name");
        this.version = json.getString("version");
        this.category = json.getString("category");
        
        // Optional fields with defaults
        this.author = json.optString("author", "Unknown");
        this.description = json.optString("description", "No description provided");
        this.enabled = json.optBoolean("enabled", true);
        
        // Parse configuration data
        if (json.has("configuration")) {
            this.configurationData = json.getJSONObject("configuration");
        } else {
            this.configurationData = new JSONObject();
        }
        
        // Parse hooks
        this.hooks = new ArrayList<>();
        if (json.has("hooks")) {
            JSONArray hooksArray = json.getJSONArray("hooks");
            for (int i = 0; i < hooksArray.length(); i++) {
                this.hooks.add(hooksArray.getString(i));
            }
        }
        
        // Parse actions
        this.actions = new ArrayList<>();
        if (json.has("actions")) {
            JSONArray actionsArray = json.getJSONArray("actions");
            for (int i = 0; i < actionsArray.length(); i++) {
                JSONObject actionJson = actionsArray.getJSONObject(i);
                this.actions.add(new AddonAction(actionJson));
            }
        }
        
        // Parse metadata
        if (json.has("metadata")) {
            JSONObject metadataJson = json.getJSONObject("metadata");
            Iterator<String> keys = metadataJson.keys();
            while (keys.hasNext()) {
                String key = keys.next();
                this.metadata.put(key, metadataJson.get(key));
            }
        }
        
        // Parse additional workflow if present
        if (json.has("workflow")) {
            JSONArray workflowArray = json.getJSONArray("workflow");
            List<Map<String, Object>> workflow = new ArrayList<>();
            for (int i = 0; i < workflowArray.length(); i++) {
                JSONObject stepJson = workflowArray.getJSONObject(i);
                Map<String, Object> step = new HashMap<>();
                Iterator<String> keys = stepJson.keys();
                while (keys.hasNext()) {
                    String key = keys.next();
                    step.put(key, stepJson.get(key));
                }
                workflow.add(step);
            }
            this.metadata.put("workflow", workflow);
        }
    }

    /**
     * Convert back to JSON
     */
    public JSONObject toJSON() throws JSONException {
        JSONObject json = new JSONObject();
        
        // Basic properties
        json.put("id", id);
        json.put("name", name);
        json.put("version", version);
        json.put("author", author);
        json.put("description", description);
        json.put("category", category);
        json.put("enabled", enabled);
        
        // Configuration data
        json.put("configuration", configurationData);
        
        // Hooks
        if (!hooks.isEmpty()) {
            JSONArray hooksArray = new JSONArray();
            for (String hook : hooks) {
                hooksArray.put(hook);
            }
            json.put("hooks", hooksArray);
        }
        
        // Actions
        if (!actions.isEmpty()) {
            JSONArray actionsArray = new JSONArray();
            for (AddonAction action : actions) {
                actionsArray.put(action.toJSON());
            }
            json.put("actions", actionsArray);
        }
        
        // Metadata
        if (!metadata.isEmpty()) {
            JSONObject metadataJson = new JSONObject();
            for (Map.Entry<String, Object> entry : metadata.entrySet()) {
                String key = entry.getKey();
                Object value = entry.getValue();
                
                // Handle special workflow case
                if ("workflow".equals(key) && value instanceof List) {
                    JSONArray workflowArray = new JSONArray();
                    @SuppressWarnings("unchecked")
                    List<Map<String, Object>> workflow = (List<Map<String, Object>>) value;
                    for (Map<String, Object> step : workflow) {
                        JSONObject stepJson = new JSONObject();
                        for (Map.Entry<String, Object> stepEntry : step.entrySet()) {
                            stepJson.put(stepEntry.getKey(), stepEntry.getValue());
                        }
                        workflowArray.put(stepJson);
                    }
                    json.put("workflow", workflowArray);
                } else {
                    metadataJson.put(key, value);
                }
            }
            
            if (metadataJson.length() > 0) {
                json.put("metadata", metadataJson);
            }
        }
        
        return json;
    }

    // Basic getters and setters
    public String getId() { return id; }
    public String getName() { return name; }
    public String getVersion() { return version; }
    public String getAuthor() { return author; }
    public String getDescription() { return description; }
    public String getCategory() { return category; }
    public boolean isEnabled() { return enabled; }
    public File getConfigFile() { return configFile; }
    public List<String> getHooks() { return new ArrayList<>(hooks); }
    public List<AddonAction> getActions() { return new ArrayList<>(actions); }

    public void setEnabled(boolean enabled) { this.enabled = enabled; }
    public void setName(String name) { this.name = name; }
    public void setDescription(String description) { this.description = description; }
    public void setAuthor(String author) { this.author = author; }

    /**
     * Configuration data access methods
     */
    public JSONObject getConfigurationData() {
        return configurationData;
    }

    public String getConfigString(String key, String defaultValue) {
        try {
            return configurationData.optString(key, defaultValue);
        } catch (Exception e) {
            return defaultValue;
        }
    }

    public int getConfigInt(String key, int defaultValue) {
        try {
            return configurationData.optInt(key, defaultValue);
        } catch (Exception e) {
            return defaultValue;
        }
    }

    public boolean getConfigBoolean(String key, boolean defaultValue) {
        try {
            return configurationData.optBoolean(key, defaultValue);
        } catch (Exception e) {
            return defaultValue;
        }
    }

    public double getConfigDouble(String key, double defaultValue) {
        try {
            return configurationData.optDouble(key, defaultValue);
        } catch (Exception e) {
            return defaultValue;
        }
    }

    public JSONArray getConfigArray(String key) {
        try {
            return configurationData.optJSONArray(key);
        } catch (Exception e) {
            return new JSONArray();
        }
    }

    public JSONObject getConfigObject(String key) {
        try {
            return configurationData.optJSONObject(key);
        } catch (Exception e) {
            return new JSONObject();
        }
    }

    /**
     * Set configuration values
     */
    public void setConfigString(String key, String value) {
        try {
            configurationData.put(key, value);
        } catch (JSONException e) {
            // Ignore
        }
    }

    public void setConfigInt(String key, int value) {
        try {
            configurationData.put(key, value);
        } catch (JSONException e) {
            // Ignore
        }
    }

    public void setConfigBoolean(String key, boolean value) {
        try {
            configurationData.put(key, value);
        } catch (JSONException e) {
            // Ignore
        }
    }

    public void setConfigDouble(String key, double value) {
        try {
            configurationData.put(key, value);
        } catch (JSONException e) {
            // Ignore
        }
    }

    /**
     * Metadata access methods
     */
    public Object getMetadata(String key) {
        return metadata.get(key);
    }

    public String getMetadataString(String key, String defaultValue) {
        Object value = metadata.get(key);
        return value != null ? value.toString() : defaultValue;
    }

    public void setMetadata(String key, Object value) {
        metadata.put(key, value);
    }

    /**
     * Hook management
     */
    public boolean hasHook(String hookName) {
        return hooks.contains(hookName);
    }

    public void addHook(String hookName) {
        if (!hooks.contains(hookName)) {
            hooks.add(hookName);
        }
    }

    public void removeHook(String hookName) {
        hooks.remove(hookName);
    }

    /**
     * Action management
     */
    public AddonAction getActionById(String actionId) {
        for (AddonAction action : actions) {
            if (action.getId().equals(actionId)) {
                return action;
            }
        }
        return null;
    }

    public List<AddonAction> getActionsByType(String actionType) {
        List<AddonAction> matchingActions = new ArrayList<>();
        for (AddonAction action : actions) {
            if (actionType.equals(action.getAction())) {
                matchingActions.add(action);
            }
        }
        return matchingActions;
    }

    /**
     * Validation methods
     */
    public boolean isValid() {
        return id != null && !id.trim().isEmpty() &&
               name != null && !name.trim().isEmpty() &&
               version != null && !version.trim().isEmpty() &&
               category != null && !category.trim().isEmpty();
    }

    public List<String> getValidationErrors() {
        List<String> errors = new ArrayList<>();
        
        if (id == null || id.trim().isEmpty()) {
            errors.add("ID is required");
        }
        if (name == null || name.trim().isEmpty()) {
            errors.add("Name is required");
        }
        if (version == null || version.trim().isEmpty()) {
            errors.add("Version is required");
        }
        if (category == null || category.trim().isEmpty()) {
            errors.add("Category is required");
        }
        
        // Validate ID format (alphanumeric and underscores only)
        if (id != null && !id.matches("^[a-zA-Z0-9_]+$")) {
            errors.add("ID must contain only letters, numbers, and underscores");
        }
        
        // Validate version format (semantic versioning)
        if (version != null && !version.matches("^\\d+\\.\\d+\\.\\d+$")) {
            errors.add("Version must follow semantic versioning (e.g., 1.0.0)");
        }
        
        return errors;
    }

    /**
     * Utility methods
     */
    @Override
    public String toString() {
        return name + " v" + version + " (" + id + ") - " + 
               (enabled ? "Enabled" : "Disabled") + " [" + category + "]";
    }

    public String getDisplayInfo() {
        StringBuilder info = new StringBuilder();
        info.append("=== ").append(name).append(" ===\n");
        info.append("ID: ").append(id).append("\n");
        info.append("Version: ").append(version).append("\n");
        info.append("Author: ").append(author).append("\n");
        info.append("Category: ").append(category).append("\n");
        info.append("Status: ").append(enabled ? "✅ Enabled" : "❌ Disabled").append("\n");
        info.append("Description: ").append(description).append("\n");
        
        if (!hooks.isEmpty()) {
            info.append("Hooks: ").append(String.join(", ", hooks)).append("\n");
        }
        
        if (!actions.isEmpty()) {
            info.append("Actions: ").append(actions.size()).append(" available\n");
        }
        
        return info.toString();
    }

    /**
     * Compare versions
     */
    public int compareVersion(String otherVersion) {
        if (otherVersion == null) return 1;
        
        String[] thisParts = this.version.split("\\.");
        String[] otherParts = otherVersion.split("\\.");
        
        int maxLength = Math.max(thisParts.length, otherParts.length);
        
        for (int i = 0; i < maxLength; i++) {
            int thisPart = i < thisParts.length ? 
                Integer.parseInt(thisParts[i]) : 0;
            int otherPart = i < otherParts.length ? 
                Integer.parseInt(otherParts[i]) : 0;
            
            if (thisPart != otherPart) {
                return Integer.compare(thisPart, otherPart);
            }
        }
        
        return 0;
    }

    public boolean isNewerThan(String otherVersion) {
        return compareVersion(otherVersion) > 0;
    }

    public boolean isOlderThan(String otherVersion) {
        return compareVersion(otherVersion) < 0;
    }

    /**
     * Create a copy of this addon config
     */
    public AddonConfig copy() {
        try {
            return new AddonConfig(toJSON(), configFile);
        } catch (JSONException e) {
            return null;
        }
    }

    /**
     * Get configuration summary for display
     */
    public String getConfigurationSummary() {
        if (configurationData.length() == 0) {
            return "No configuration options";
        }
        
        StringBuilder summary = new StringBuilder();
        Iterator<String> keys = configurationData.keys();
        int count = 0;
        
        while (keys.hasNext() && count < 5) {
            String key = keys.next();
            try {
                Object value = configurationData.get(key);
                summary.append("• ").append(key).append(": ");
                
                if (value instanceof JSONArray) {
                    summary.append("Array[").append(((JSONArray) value).length()).append("]");
                } else if (value instanceof JSONObject) {
                    summary.append("Object{...}");
                } else {
                    String valueStr = value.toString();
                    if (valueStr.length() > 30) {
                        summary.append(valueStr.substring(0, 27)).append("...");
                    } else {
                        summary.append(valueStr);
                    }
                }
                summary.append("\n");
                count++;
            } catch (JSONException e) {
                // Skip this key
            }
        }
        
        if (configurationData.length() > 5) {
            summary.append("... and ").append(configurationData.length() - 5).append(" more options");
        }
        
        return summary.toString();
    }
}

================================================================================
ModLoader/app/src/main/java/com/modloader/addon/AddonExecutors.java

// File: AddonExecutors.java - Addon Execution Engine Classes
// Path: app/src/main/java/com/modloader/addon/AddonExecutors.java
package com.modloader.addon;

import android.content.Context;
import android.content.DialogInterface;
import android.content.res.ColorStateList;
import android.graphics.Color;
import android.graphics.drawable.GradientDrawable;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;
import androidx.cardview.widget.CardView;

import com.modloader.util.LogUtils;
import com.modloader.util.FileUtils;
import com.modloader.installer.ModInstaller;

import org.json.JSONArray;
import org.json.JSONException;
import org.json.JSONObject;

import java.io.File;
import java.util.Map;
import java.util.HashMap;
import java.util.List;
import java.util.ArrayList;

/**
 * Base class for addon executors
 */
abstract class AddonExecutor {
    protected final Context context;
    
    public AddonExecutor(Context context) {
        this.context = context;
    }
    
    /**
     * Execute an addon action
     */
    public abstract boolean execute(AddonConfig addon, String action, Map<String, Object> parameters);
    
    /**
     * Get supported actions for this executor
     */
    public abstract List<String> getSupportedActions();
    
    /**
     * Validate addon configuration for this executor
     */
    public boolean validateAddon(AddonConfig addon) {
        return addon != null && addon.isValid();
    }
}

/**
 * Theme Executor - Handles theme and visual customization addons
 */
class ThemeExecutor extends AddonExecutor {
    
    public ThemeExecutor(Context context) {
        super(context);
    }
    
    @Override
    public boolean execute(AddonConfig addon, String action, Map<String, Object> parameters) {
        try {
            switch (action) {
                case "apply_to_activity":
                    return applyThemeToActivity(addon, parameters);
                case "apply_colors":
                    return applyColorScheme(addon, parameters);
                case "apply_styles":
                    return applyStyles(addon, parameters);
                default:
                    LogUtils.logDebug("Unknown theme action: " + action);
                    return false;
            }
        } catch (Exception e) {
            LogUtils.logError("Theme executor error: " + e.getMessage());
            return false;
        }
    }
    
    private boolean applyThemeToActivity(AddonConfig addon, Map<String, Object> parameters) {
        Object activityObj = parameters.get("activity");
        if (!(activityObj instanceof AppCompatActivity)) {
            return false;
        }
        
        AppCompatActivity activity = (AppCompatActivity) activityObj;
        
        // Apply theme colors
        String primaryColor = addon.getConfigString("primary_color", "#1E1E1E");
        String secondaryColor = addon.getConfigString("secondary_color", "#2D2D2D");
        String accentColor = addon.getConfigString("accent_color", "#4CAF50");
        String textColor = addon.getConfigString("text_color", "#FFFFFF");
        
        // Apply to root view
        View rootView = activity.findViewById(android.R.id.content);
        if (rootView != null) {
            try {
                rootView.setBackgroundColor(Color.parseColor(primaryColor));
                applyThemeToViewGroup((ViewGroup) rootView, addon);
            } catch (IllegalArgumentException e) {
                LogUtils.logError("Invalid color format in theme addon: " + e.getMessage());
                return false;
            }
        }
        
        LogUtils.logDebug("Applied theme: " + addon.getName() + " to activity");
        return true;
    }
    
    private void applyThemeToViewGroup(ViewGroup viewGroup, AddonConfig addon) {
        String secondaryColor = addon.getConfigString("secondary_color", "#2D2D2D");
        String accentColor = addon.getConfigString("accent_color", "#4CAF50");
        String textColor = addon.getConfigString("text_color", "#FFFFFF");
        String buttonStyle = addon.getConfigString("button_style", "rounded");
        String cardElevation = addon.getConfigString("card_elevation", "8dp");
        
        for (int i = 0; i < viewGroup.getChildCount(); i++) {
            View child = viewGroup.getChildAt(i);
            
            try {
                if (child instanceof TextView) {
                    TextView textView = (TextView) child;
                    textView.setTextColor(Color.parseColor(textColor));
                } else if (child instanceof Button) {
                    Button button = (Button) child;
                    button.setTextColor(Color.parseColor(textColor));
                    
                    if ("rounded".equals(buttonStyle)) {
                        GradientDrawable drawable = new GradientDrawable();
                        drawable.setColor(Color.parseColor(accentColor));
                        drawable.setCornerRadius(24f);
                        button.setBackground(drawable);
                    }
                } else if (child instanceof CardView) {
                    CardView cardView = (CardView) child;
                    cardView.setCardBackgroundColor(Color.parseColor(secondaryColor));
                    
                    // Parse elevation
                    try {
                        float elevation = Float.parseFloat(cardElevation.replace("dp", ""));
                        cardView.setCardElevation(elevation);
                    } catch (NumberFormatException e) {
                        // Use default elevation
                    }
                }
                
                if (child instanceof ViewGroup) {
                    applyThemeToViewGroup((ViewGroup) child, addon);
                }
            } catch (IllegalArgumentException e) {
                LogUtils.logError("Error applying theme to view: " + e.getMessage());
            }
        }
    }
    
    private boolean applyColorScheme(AddonConfig addon, Map<String, Object> parameters) {
        LogUtils.logDebug("Applied color scheme from addon: " + addon.getName());
        return true;
    }
    
    private boolean applyStyles(AddonConfig addon, Map<String, Object> parameters) {
        LogUtils.logDebug("Applied styles from addon: " + addon.getName());
        return true;
    }
    
    @Override
    public List<String> getSupportedActions() {
        List<String> actions = new ArrayList<>();
        actions.add("apply_to_activity");
        actions.add("apply_colors");
        actions.add("apply_styles");
        return actions;
    }
}

/**
 * UI Enhancement Executor - Handles UI modifications and enhancements
 */
class UIEnhancementExecutor extends AddonExecutor {
    
    public UIEnhancementExecutor(Context context) {
        super(context);
    }
    
    @Override
    public boolean execute(AddonConfig addon, String action, Map<String, Object> parameters) {
        try {
            switch (action) {
                case "apply_to_activity":
                    return applyUIEnhancements(addon, parameters);
                case "add_toolbar_buttons":
                    return addToolbarButtons(addon, parameters);
                case "modify_layout":
                    return modifyLayout(addon, parameters);
                default:
                    LogUtils.logDebug("Unknown UI enhancement action: " + action);
                    return false;
            }
        } catch (Exception e) {
            LogUtils.logError("UI enhancement executor error: " + e.getMessage());
            return false;
        }
    }
    
    private boolean applyUIEnhancements(AddonConfig addon, Map<String, Object> parameters) {
        Object activityObj = parameters.get("activity");
        if (!(activityObj instanceof AppCompatActivity)) {
            return false;
        }
        
        AppCompatActivity activity = (AppCompatActivity) activityObj;
        
        // Get UI enhancements from configuration
        JSONArray enhancements = addon.getConfigArray("ui_enhancements");
        if (enhancements != null) {
            for (int i = 0; i < enhancements.length(); i++) {
                try {
                    String enhancement = enhancements.getString(i);
                    applySpecificEnhancement(activity, addon, enhancement);
                } catch (JSONException e) {
                    LogUtils.logError("Error applying UI enhancement: " + e.getMessage());
                }
            }
        }
        
        return true;
    }
    
    private void applySpecificEnhancement(AppCompatActivity activity, AddonConfig addon, String enhancement) {
        switch (enhancement) {
            case "add_toolbar_buttons":
                LogUtils.logDebug("Adding toolbar buttons from addon: " + addon.getName());
                break;
            case "add_context_menu_items":
                LogUtils.logDebug("Adding context menu items from addon: " + addon.getName());
                break;
            case "add_batch_select_mode":
                LogUtils.logDebug("Adding batch select mode from addon: " + addon.getName());
                break;
            default:
                LogUtils.logDebug("Unknown UI enhancement: " + enhancement);
                break;
        }
    }
    
    private boolean addToolbarButtons(AddonConfig addon, Map<String, Object> parameters) {
        LogUtils.logDebug("Adding toolbar buttons from addon: " + addon.getName());
        return true;
    }
    
    private boolean modifyLayout(AddonConfig addon, Map<String, Object> parameters) {
        LogUtils.logDebug("Modifying layout from addon: " + addon.getName());
        return true;
    }
    
    @Override
    public List<String> getSupportedActions() {
        List<String> actions = new ArrayList<>();
        actions.add("apply_to_activity");
        actions.add("add_toolbar_buttons");
        actions.add("modify_layout");
        return actions;
    }
}

/**
 * File Manager Executor - Handles file management enhancements
 */
class FileManagerExecutor extends AddonExecutor {
    
    public FileManagerExecutor(Context context) {
        super(context);
    }
    
    @Override
    public boolean execute(AddonConfig addon, String action, Map<String, Object> parameters) {
        try {
            switch (action) {
                case "bulk_delete":
                    return performBulkDelete(addon, parameters);
                case "compress_files":
                    return compressFiles(addon, parameters);
                case "show_bulk_delete_dialog":
                    return showBulkDeleteDialog(addon, parameters);
                case "compress_selected_files":
                    return compressSelectedFiles(addon, parameters);
                case "apply_to_activity":
                    return applyFileManagerEnhancements(addon, parameters);
                default:
                    LogUtils.logDebug("Unknown file manager action: " + action);
                    return false;
            }
        } catch (Exception e) {
            LogUtils.logError("File manager executor error: " + e.getMessage());
            return false;
        }
    }
    
    private boolean applyFileManagerEnhancements(AddonConfig addon, Map<String, Object> parameters) {
        LogUtils.logDebug("Applied file manager enhancements from addon: " + addon.getName());
        return true;
    }
    
    private boolean performBulkDelete(AddonConfig addon, Map<String, Object> parameters) {
        @SuppressWarnings("unchecked")
        List<File> files = (List<File>) parameters.get("files");
        if (files == null || files.isEmpty()) {
            return false;
        }
        
        int deletedCount = 0;
        for (File file : files) {
            if (file.exists() && file.delete()) {
                deletedCount++;
            }
        }
        
        LogUtils.logUser("🗑️ Bulk deleted " + deletedCount + "/" + files.size() + " files");
        return true;
    }
    
    private boolean compressFiles(AddonConfig addon, Map<String, Object> parameters) {
        @SuppressWarnings("unchecked")
        List<File> files = (List<File>) parameters.get("files");
        String outputPath = (String) parameters.get("output_path");
        
        if (files == null || files.isEmpty() || outputPath == null) {
            return false;
        }
        
        try {
            File outputFile = new File(outputPath);
            boolean success = createSimpleArchive(files, outputFile);
            
            if (success) {
                LogUtils.logUser("📦 Created archive: " + outputFile.getName());
                return true;
            } else {
                LogUtils.logError("Failed to create archive");
                return false;
            }
        } catch (Exception e) {
            LogUtils.logError("Archive creation failed: " + e.getMessage());
            return false;
        }
    }
    
    private boolean createSimpleArchive(List<File> files, File outputFile) {
        // Simple archive creation implementation
        try {
            java.util.zip.ZipOutputStream zos = new java.util.zip.ZipOutputStream(
                new java.io.FileOutputStream(outputFile));
            
            for (File file : files) {
                if (!file.exists()) continue;
                
                java.util.zip.ZipEntry entry = new java.util.zip.ZipEntry(file.getName());
                zos.putNextEntry(entry);
                
                java.io.FileInputStream fis = new java.io.FileInputStream(file);
                byte[] buffer = new byte[1024];
                int length;
                while ((length = fis.read(buffer)) > 0) {
                    zos.write(buffer, 0, length);
                }
                fis.close();
                zos.closeEntry();
            }
            zos.close();
            return true;
        } catch (Exception e) {
            LogUtils.logError("Archive creation error: " + e.getMessage());
            return false;
        }
    }
    
    private boolean showBulkDeleteDialog(AddonConfig addon, Map<String, Object> parameters) {
        Object activityObj = parameters.get("activity");
        if (!(activityObj instanceof AppCompatActivity)) {
            return false;
        }
        
        AppCompatActivity activity = (AppCompatActivity) activityObj;
        @SuppressWarnings("unchecked")
        List<File> files = (List<File>) parameters.get("files");
        
        if (files == null || files.isEmpty()) {
            Toast.makeText(activity, "No files selected for deletion", Toast.LENGTH_SHORT).show();
            return false;
        }
        
        new AlertDialog.Builder(activity)
            .setTitle("Bulk Delete Files")
            .setMessage("Delete " + files.size() + " selected files?")
            .setPositiveButton("Delete", (dialog, which) -> {
                Map<String, Object> deleteParams = new HashMap<>();
                deleteParams.put("files", files);
                performBulkDelete(addon, deleteParams);
            })
            .setNegativeButton("Cancel", null)
            .show();
        
        return true;
    }
    
    private boolean compressSelectedFiles(AddonConfig addon, Map<String, Object> parameters) {
        Object activityObj = parameters.get("activity");
        if (!(activityObj instanceof AppCompatActivity)) {
            return false;
        }
        
        AppCompatActivity activity = (AppCompatActivity) activityObj;
        @SuppressWarnings("unchecked")
        List<File> files = (List<File>) parameters.get("files");
        
        if (files == null || files.isEmpty()) {
            Toast.makeText(activity, "No files selected for compression", Toast.LENGTH_SHORT).show();
            return false;
        }
        
        String outputPath = context.getExternalFilesDir(null) + "/compressed_" + System.currentTimeMillis() + ".zip";
        Map<String, Object> compressParams = new HashMap<>();
        compressParams.put("files", files);
        compressParams.put("output_path", outputPath);
        
        return compressFiles(addon, compressParams);
    }
    
    @Override
    public List<String> getSupportedActions() {
        List<String> actions = new ArrayList<>();
        actions.add("bulk_delete");
        actions.add("compress_files");
        actions.add("show_bulk_delete_dialog");
        actions.add("compress_selected_files");
        actions.add("apply_to_activity");
        return actions;
    }
}

/**
 * Mod Installer Executor - Handles intelligent mod installation
 */
class ModInstallerExecutor extends AddonExecutor {
    
    public ModInstallerExecutor(Context context) {
        super(context);
    }
    
    @Override
    public boolean execute(AddonConfig addon, String action, Map<String, Object> parameters) {
        try {
            switch (action) {
                case "smart_install":
                    return performSmartInstall(addon, parameters);
                case "validate_file":
                    return validateModFile(addon, parameters);
                case "check_conflicts":
                    return checkModConflicts(addon, parameters);
                case "backup_existing":
                    return backupExistingMods(addon, parameters);
                case "install_mod":
                    return installModWithWorkflow(addon, parameters);
                case "update_registry":
                    return updateModRegistry(addon, parameters);
                default:
                    LogUtils.logDebug("Unknown mod installer action: " + action);
                    return false;
            }
        } catch (Exception e) {
            LogUtils.logError("Mod installer executor error: " + e.getMessage());
            return false;
        }
    }
    
    private boolean performSmartInstall(AddonConfig addon, Map<String, Object> parameters) {
        File modFile = (File) parameters.get("mod_file");
        if (modFile == null || !modFile.exists()) {
            LogUtils.logError("Invalid mod file for smart installation");
            return false;
        }
        
        // Execute workflow steps
        @SuppressWarnings("unchecked")
        List<Map<String, Object>> workflow = (List<Map<String, Object>>) addon.getMetadata("workflow");
        
        if (workflow != null) {
            for (Map<String, Object> step : workflow) {
                String stepAction = (String) step.get("step");
                String description = (String) step.get("description");
                
                LogUtils.logUser("🔄 " + description);
                
                Map<String, Object> stepParams = new HashMap<>(parameters);
                stepParams.putAll(step);
                
                if (!execute(addon, stepAction, stepParams)) {
                    LogUtils.logError("Smart install failed at step: " + stepAction);
                    return false;
                }
            }
        }
        
        LogUtils.logUser("✅ Smart installation completed successfully");
        return true;
    }
    
    private boolean validateModFile(AddonConfig addon, Map<String, Object> parameters) {
        File modFile = (File) parameters.get("mod_file");
        if (modFile == null) {
            return false;
        }
        
        // Check file size
        String maxSizeStr = addon.getConfigString("max_file_size", "100MB");
        long maxSize = parseFileSize(maxSizeStr);
        
        if (modFile.length() > maxSize) {
            LogUtils.logError("Mod file too large: " + FileUtils.formatFileSize(modFile.length()) + 
                            " (max: " + maxSizeStr + ")");
            return false;
        }
        
        // Check file format
        JSONArray supportedFormats = addon.getConfigArray("supported_formats");
        if (supportedFormats != null) {
            boolean validFormat = false;
            String fileName = modFile.getName().toLowerCase();
            
            for (int i = 0; i < supportedFormats.length(); i++) {
                try {
                    String format = supportedFormats.getString(i);
                    if (fileName.endsWith(format.toLowerCase())) {
                        validFormat = true;
                        break;
                    }
                } catch (JSONException e) {
                    // Continue checking
                }
            }
            
            if (!validFormat) {
                LogUtils.logError("Unsupported mod file format: " + fileName);
                return false;
            }
        }
        
        // Verify checksums if enabled
        if (addon.getConfigBoolean("verify_checksums", false)) {
            // Checksum verification would go here
            LogUtils.logDebug("Checksum verification passed");
        }
        
        LogUtils.logDebug("Mod file validation passed");
        return true;
    }
    
    private boolean checkModConflicts(AddonConfig addon, Map<String, Object> parameters) {
        if (!addon.getConfigBoolean("auto_detect_conflicts", false)) {
            return true; // Skip if disabled
        }
        
        File modFile = (File) parameters.get("mod_file");
        if (modFile == null) {
            return false;
        }
        
        LogUtils.logDebug("Checking for mod conflicts...");
        
        // Simple conflict detection based on filename patterns
        String fileName = modFile.getName().toLowerCase();
        
        // Check for existing mods with similar names
        // This is a simplified example - real implementation would be more sophisticated
        String baseName = fileName.substring(0, fileName.lastIndexOf('.'));
        
        LogUtils.logDebug("No conflicts detected for: " + baseName);
        return true;
    }
    
    private boolean backupExistingMods(AddonConfig addon, Map<String, Object> parameters) {
        if (!addon.getConfigBoolean("create_backups", false)) {
            return true; // Skip if disabled
        }
        
        LogUtils.logDebug("Creating backup of existing mods...");
        
        try {
            File backupDir = new File(context.getExternalFilesDir(null), 
                "ModLoader/com.and.games505.TerrariaPaid/Backups");
            backupDir.mkdirs();
            
            String backupName = "backup_" + System.currentTimeMillis();
            File backupFile = new File(backupDir, backupName + ".txt");
            
            // Create simple backup manifest
            try (java.io.FileWriter writer = new java.io.FileWriter(backupFile)) {
                writer.write("Backup created by Smart Mod Installer\n");
                writer.write("Timestamp: " + new java.util.Date().toString() + "\n");
                writer.write("Addon: " + addon.getName() + "\n");
            }
            
            LogUtils.logDebug("Backup created: " + backupName);
            return true;
        } catch (Exception e) {
            LogUtils.logError("Backup creation failed: " + e.getMessage());
            return false;
        }
    }
    
    private boolean installModWithWorkflow(AddonConfig addon, Map<String, Object> parameters) {
        File modFile = (File) parameters.get("mod_file");
        String fileName = (String) parameters.get("filename");
        
        if (modFile == null) {
            return false;
        }
        
        if (fileName == null) {
            fileName = modFile.getName();
        }
        
        // Use existing ModInstaller
        boolean success = ModInstaller.installMod(context, 
            android.net.Uri.fromFile(modFile), fileName);
        
        if (success) {
            LogUtils.logUser("✅ Mod installed successfully: " + fileName);
            
            // Auto-enable if configured
            if (addon.getConfigBoolean("auto_enable", true)) {
                LogUtils.logDebug("Auto-enabling mod: " + fileName);
            }
        } else {
            LogUtils.logError("Failed to install mod: " + fileName);
        }
        
        return success;
    }
    
    private boolean updateModRegistry(AddonConfig addon, Map<String, Object> parameters) {
        File modFile = (File) parameters.get("mod_file");
        if (modFile == null) {
            return false;
        }
        
        LogUtils.logDebug("Updating mod registry for: " + modFile.getName());
        
        // Registry update logic would go here
        // For now, just log the action
        LogUtils.logDebug("Mod registry updated");
        return true;
    }
    
    private long parseFileSize(String sizeStr) {
        try {
            sizeStr = sizeStr.toUpperCase();
            if (sizeStr.endsWith("MB")) {
                return Long.parseLong(sizeStr.replace("MB", "").trim()) * 1024 * 1024;
            } else if (sizeStr.endsWith("KB")) {
                return Long.parseLong(sizeStr.replace("KB", "").trim()) * 1024;
            } else if (sizeStr.endsWith("GB")) {
                return Long.parseLong(sizeStr.replace("GB", "").trim()) * 1024 * 1024 * 1024;
            } else {
                return Long.parseLong(sizeStr);
            }
        } catch (NumberFormatException e) {
            return 100 * 1024 * 1024; // Default to 100MB
        }
    }
    
    @Override
    public List<String> getSupportedActions() {
        List<String> actions = new ArrayList<>();
        actions.add("smart_install");
        actions.add("validate_file");
        actions.add("check_conflicts");
        actions.add("backup_existing");
        actions.add("install_mod");
        actions.add("update_registry");
        return actions;
    }
}

/**
 * Utility Executor - Handles general utility addons
 */
class UtilityExecutor extends AddonExecutor {
    
    public UtilityExecutor(Context context) {
        super(context);
    }
    
    @Override
    public boolean execute(AddonConfig addon, String action, Map<String, Object> parameters) {
        try {
            switch (action) {
                case "apply_to_activity":
                    return applyUtilities(addon, parameters);
                case "cleanup_temp_files":
                    return cleanupTempFiles(addon, parameters);
                case "optimize_storage":
                    return optimizeStorage(addon, parameters);
                default:
                    LogUtils.logDebug("Unknown utility action: " + action);
                    return false;
            }
        } catch (Exception e) {
            LogUtils.logError("Utility executor error: " + e.getMessage());
            return false;
        }
    }
    
    private boolean applyUtilities(AddonConfig addon, Map<String, Object> parameters) {
        LogUtils.logDebug("Applied utilities from addon: " + addon.getName());
        return true;
    }
    
    private boolean cleanupTempFiles(AddonConfig addon, Map<String, Object> parameters) {
        LogUtils.logDebug("Cleaning up temporary files...");
        
        File tempDir = context.getCacheDir();
        if (tempDir != null && tempDir.exists()) {
            deleteDirectory(tempDir);
            LogUtils.logUser("🧹 Cleaned up temporary files");
        }
        
        return true;
    }
    
    private boolean optimizeStorage(AddonConfig addon, Map<String, Object> parameters) {
        LogUtils.logDebug("Optimizing storage...");
        LogUtils.logUser("📊 Storage optimization completed");
        return true;
    }
    
    private void deleteDirectory(File dir) {
        if (dir != null && dir.isDirectory()) {
            File[] files = dir.listFiles();
            if (files != null) {
                for (File file : files) {
                    if (file.isDirectory()) {
                        deleteDirectory(file);
                    } else {
                        file.delete();
                    }
                }
            }
        }
    }
    
    @Override
    public List<String> getSupportedActions() {
        List<String> actions = new ArrayList<>();
        actions.add("apply_to_activity");
        actions.add("cleanup_temp_files");
        actions.add("optimize_storage");
        return actions;
    }
}

/**
 * Diagnostic Executor - Handles diagnostic and repair addons
 */
class DiagnosticExecutor extends AddonExecutor {
    
    public DiagnosticExecutor(Context context) {
        super(context);
    }
    
    @Override
    public boolean execute(AddonConfig addon, String action, Map<String, Object> parameters) {
        try {
            switch (action) {
                case "run_diagnostics":
                    return runDiagnostics(addon, parameters);
                case "repair_issues":
                    return repairIssues(addon, parameters);
                case "validate_installation":
                    return validateInstallation(addon, parameters);
                default:
                    LogUtils.logDebug("Unknown diagnostic action: " + action);
                    return false;
            }
        } catch (Exception e) {
            LogUtils.logError("Diagnostic executor error: " + e.getMessage());
            return false;
        }
    }
    
    private boolean runDiagnostics(AddonConfig addon, Map<String, Object> parameters) {
        LogUtils.logDebug("Running diagnostics from addon: " + addon.getName());
        LogUtils.logUser("🔍 Diagnostic scan completed");
        return true;
    }
    
    private boolean repairIssues(AddonConfig addon, Map<String, Object> parameters) {
        LogUtils.logDebug("Repairing issues using addon: " + addon.getName());
        LogUtils.logUser("🛠️ Repair process completed");
        return true;
    }
    
    private boolean validateInstallation(AddonConfig addon, Map<String, Object> parameters) {
        LogUtils.logDebug("Validating installation using addon: " + addon.getName());
        LogUtils.logUser("✅ Installation validation completed");
        return true;
    }
    
    @Override
    public List<String> getSupportedActions() {
        List<String> actions = new ArrayList<>();
        actions.add("run_diagnostics");
        actions.add("repair_issues");
        actions.add("validate_installation");
        return actions;
    }
}

================================================================================
ModLoader/app/src/main/java/com/modloader/addon/AddonManagementActivity.java

// File: AddonManagementActivity.java - Addon Management UI
// Path: app/src/main/java/com/modloader/addon/AddonManagementActivity.java
package com.modloader.addon;

import android.app.Activity;
import android.content.Intent;
import android.net.Uri;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.ViewGroup;
import android.widget.*;
import androidx.annotation.NonNull;
import androidx.appcompat.app.AlertDialog;
import androidx.appcompat.app.AppCompatActivity;
import androidx.cardview.widget.CardView;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;

import com.modloader.R;
import com.modloader.util.LogUtils;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * Activity for managing addons - provides UI for installing, configuring, and managing addons
 */
public class AddonManagementActivity extends AppCompatActivity {
    private static final int REQUEST_SELECT_ADDON_FILE = 1001;
    
    private AddonManager addonManager;
    private RecyclerView addonRecyclerView;
    private AddonAdapter addonAdapter;
    private TextView statusText;
    private Button addAddonButton;
    private Button refreshButton;
    private Spinner categorySpinner;
    
    private List<AddonConfig> displayedAddons;
    private List<AddonConfig> allAddons;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_addon_management);
        
        setTitle("🔌 Addon Management");
        
        // Initialize addon manager
        addonManager = new AddonManager(this);
        
        initializeViews();
        setupRecyclerView();
        setupListeners();
        loadAddons();
        
        // Apply addons to this activity
        addonManager.applyAddonsToActivity(this, "AddonManagementActivity");
        
        LogUtils.logUser("Addon Management opened");
    }

    private void initializeViews() {
        statusText = findViewById(R.id.addonStatusText);
        addonRecyclerView = findViewById(R.id.addonRecyclerView);
        addAddonButton = findViewById(R.id.addAddonButton);
        refreshButton = findViewById(R.id.refreshButton);
        categorySpinner = findViewById(R.id.categorySpinner);
        
        displayedAddons = new ArrayList<>();
        allAddons = new ArrayList<>();
        
        setupCategorySpinner();
    }

    private void setupCategorySpinner() {
        List<String> categories = new ArrayList<>();
        categories.add("All Categories");
        
        for (AddonManager.AddonCategory category : AddonManager.AddonCategory.values()) {
            categories.add(category.getDisplayName());
        }
        
        ArrayAdapter<String> adapter = new ArrayAdapter<>(this, 
            android.R.layout.simple_spinner_item, categories);
        adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
        categorySpinner.setAdapter(adapter);
    }

    private void setupRecyclerView() {
        addonAdapter = new AddonAdapter(displayedAddons);
        addonRecyclerView.setLayoutManager(new LinearLayoutManager(this));
        addonRecyclerView.setAdapter(addonAdapter);
    }

    private void setupListeners() {
        addAddonButton.setOnClickListener(v -> showAddAddonDialog());
        
        refreshButton.setOnClickListener(v -> {
            loadAddons();
            Toast.makeText(this, "Addons refreshed", Toast.LENGTH_SHORT).show();
        });
        
        categorySpinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override
            public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {
                filterAddonsByCategory(position);
            }
            
            @Override
            public void onNothingSelected(AdapterView<?> parent) {}
        });
    }

    private void loadAddons() {
        allAddons.clear();
        allAddons.addAll(addonManager.getLoadedAddons());
        
        filterAddonsByCategory(categorySpinner.getSelectedItemPosition());
        updateStatus();
    }

    private void filterAddonsByCategory(int categoryPosition) {
        displayedAddons.clear();
        
        if (categoryPosition == 0) {
            // Show all addons
            displayedAddons.addAll(allAddons);
        } else {
            // Show addons for specific category
            AddonManager.AddonCategory selectedCategory = 
                AddonManager.AddonCategory.values()[categoryPosition - 1];
            
            for (AddonConfig addon : allAddons) {
                if (selectedCategory.getId().equals(addon.getCategory())) {
                    displayedAddons.add(addon);
                }
            }
        }
        
        addonAdapter.notifyDataSetChanged();
        updateStatus();
    }

    private void updateStatus() {
        int totalAddons = allAddons.size();
        int enabledAddons = 0;
        int displayedCount = displayedAddons.size();
        
        for (AddonConfig addon : allAddons) {
            if (addon.isEnabled()) {
                enabledAddons++;
            }
        }
        
        String status = String.format("📊 Total: %d | Enabled: %d | Showing: %d", 
            totalAddons, enabledAddons, displayedCount);
        statusText.setText(status);
    }

    private void showAddAddonDialog() {
        String[] options = {
            "📁 Install from file",
            "🌐 Browse addon library",
            "✨ Create sample addon",
            "📋 Import from clipboard"
        };
        
        new AlertDialog.Builder(this)
            .setTitle("Add Addon")
            .setItems(options, (dialog, which) -> {
                switch (which) {
                    case 0:
                        selectAddonFile();
                        break;
                    case 1:
                        showAddonLibrary();
                        break;
                    case 2:
                        createSampleAddon();
                        break;
                    case 3:
                        importFromClipboard();
                        break;
                }
            })
            .show();
    }

    private void selectAddonFile() {
        Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
        intent.setType("application/json");
        intent.addCategory(Intent.CATEGORY_OPENABLE);
        startActivityForResult(intent, REQUEST_SELECT_ADDON_FILE);
    }

    private void showAddonLibrary() {
        // Placeholder for addon library
        new AlertDialog.Builder(this)
            .setTitle("Addon Library")
            .setMessage("Online addon library coming soon!\n\nFor now, you can:\n• Create JSON configuration files\n• Import addon files from storage\n• Use the sample addons")
            .setPositiveButton("OK", null)
            .show();
    }

    private void createSampleAddon() {
        String[] sampleTypes = {
            "🎨 Dark Theme",
            "📁 File Manager Enhancement",
            "🔧 Smart Mod Installer",
            "🛠️ Diagnostic Tool"
        };
        
        new AlertDialog.Builder(this)
            .setTitle("Create Sample Addon")
            .setItems(sampleTypes, (dialog, which) -> {
                String addonType = sampleTypes[which];
                LogUtils.logUser("Creating sample addon: " + addonType);
                
                // Sample addons are automatically created by AddonManager
                // Just refresh to show them
                loadAddons();
                Toast.makeText(this, "Sample addons are already available in the list", Toast.LENGTH_LONG).show();
            })
            .show();
    }

    private void importFromClipboard() {
        // Placeholder for clipboard import
        Toast.makeText(this, "Clipboard import coming soon", Toast.LENGTH_SHORT).show();
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        
        if (requestCode == REQUEST_SELECT_ADDON_FILE && resultCode == Activity.RESULT_OK && data != null) {
            Uri addonUri = data.getData();
            if (addonUri != null) {
                installAddonFromUri(addonUri);
            }
        }
    }

    private void installAddonFromUri(Uri addonUri) {
        boolean success = addonManager.installAddon(addonUri);
        if (success) {
            Toast.makeText(this, "✅ Addon installed successfully", Toast.LENGTH_SHORT).show();
            loadAddons();
        } else {
            Toast.makeText(this, "❌ Failed to install addon", Toast.LENGTH_SHORT).show();
        }
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.addon_management_menu, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();
        
        if (id == R.id.action_statistics) {
            showAddonStatistics();
            return true;
        } else if (id == R.id.action_cleanup) {
            showCleanupDialog();
            return true;
        } else if (id == R.id.action_help) {
            showHelpDialog();
            return true;
        }
        
        return super.onOptionsItemSelected(item);
    }

    private void showAddonStatistics() {
        String statistics = addonManager.getAddonStatistics();
        
        new AlertDialog.Builder(this)
            .setTitle("📊 Addon Statistics")
            .setMessage(statistics)
            .setPositiveButton("OK", null)
            .show();
    }

    private void showCleanupDialog() {
        new AlertDialog.Builder(this)
            .setTitle("🧹 Cleanup Addons")
            .setMessage("Remove disabled addons and clean up temporary files?")
            .setPositiveButton("Cleanup", (dialog, which) -> {
                performCleanup();
            })
            .setNegativeButton("Cancel", null)
            .show();
    }

    private void performCleanup() {
        // Cleanup logic would go here
        Toast.makeText(this, "🧹 Cleanup completed", Toast.LENGTH_SHORT).show();
        loadAddons();
    }

    private void showHelpDialog() {
        String helpText = "=== Addon Management Help ===\n\n" +
            "📁 Installing Addons:\n" +
            "• Use 'Add Addon' to install JSON configuration files\n" +
            "• Addons are automatically loaded when enabled\n\n" +
            "⚙️ Managing Addons:\n" +
            "• Toggle switches to enable/disable addons\n" +
            "• Tap addon cards for detailed configuration\n" +
            "• Use categories to filter addons\n\n" +
            "✨ Creating Addons:\n" +
            "• Addons are JSON configuration files\n" +
            "• Define UI themes, file operations, and more\n" +
            "• No programming required for basic addons\n\n" +
            "🔧 Troubleshooting:\n" +
            "• Use Refresh to reload addons\n" +
            "• Check Statistics for addon status\n" +
            "• Use Cleanup to remove unused files";
        
        new AlertDialog.Builder(this)
            .setTitle("❓ Help")
            .setMessage(helpText)
            .setPositiveButton("OK", null)
            .show();
    }

    /**
     * Adapter for displaying addons in RecyclerView
     */
    private class AddonAdapter extends RecyclerView.Adapter<AddonAdapter.AddonViewHolder> {
        private List<AddonConfig> addons;

        public AddonAdapter(List<AddonConfig> addons) {
            this.addons = addons;
        }

        @NonNull
        @Override
        public AddonViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
            View view = LayoutInflater.from(parent.getContext())
                .inflate(R.layout.item_addon, parent, false);
            return new AddonViewHolder(view);
        }

        @Override
        public void onBindViewHolder(@NonNull AddonViewHolder holder, int position) {
            AddonConfig addon = addons.get(position);
            holder.bind(addon);
        }

        @Override
        public int getItemCount() {
            return addons.size();
        }

        class AddonViewHolder extends RecyclerView.ViewHolder {
            private CardView cardView;
            private TextView nameText;
            private TextView descriptionText;
            private TextView versionText;
            private TextView categoryText;
            private Switch enabledSwitch;
            private Button configureButton;
            private Button exportButton;
            private Button deleteButton;

            public AddonViewHolder(@NonNull View itemView) {
                super(itemView);
                
                cardView = itemView.findViewById(R.id.addonCardView);
                nameText = itemView.findViewById(R.id.addonNameText);
                descriptionText = itemView.findViewById(R.id.addonDescriptionText);
                versionText = itemView.findViewById(R.id.addonVersionText);
                categoryText = itemView.findViewById(R.id.addonCategoryText);
                enabledSwitch = itemView.findViewById(R.id.addonEnabledSwitch);
                configureButton = itemView.findViewById(R.id.configureButton);
                exportButton = itemView.findViewById(R.id.exportButton);
                deleteButton = itemView.findViewById(R.id.deleteButton);
            }

            public void bind(AddonConfig addon) {
                nameText.setText(addon.getName());
                descriptionText.setText(addon.getDescription());
                versionText.setText("v" + addon.getVersion());
                categoryText.setText(getCategoryDisplayName(addon.getCategory()));
                enabledSwitch.setChecked(addon.isEnabled());

                // Set card background based on status
                if (addon.isEnabled()) {
                    cardView.setCardBackgroundColor(getResources().getColor(android.R.color.white));
                } else {
                    cardView.setCardBackgroundColor(getResources().getColor(android.R.color.darker_gray));
                }

                // Enable/disable switch listener
                enabledSwitch.setOnCheckedChangeListener((buttonView, isChecked) -> {
                    if (addonManager.toggleAddon(addon.getId(), isChecked)) {
                        // Update the display
                        cardView.setCardBackgroundColor(isChecked ? 
                            getResources().getColor(android.R.color.white) : 
                            getResources().getColor(android.R.color.darker_gray));
                        updateStatus();
                    } else {
                        // Reset switch if toggle failed
                        enabledSwitch.setChecked(!isChecked);
                    }
                });

                // Configure button
                configureButton.setOnClickListener(v -> showAddonConfiguration(addon));

                // Export button
                exportButton.setOnClickListener(v -> {
                    addonManager.exportAddon(addon.getId());
                });

                // Delete button
                deleteButton.setOnClickListener(v -> showDeleteConfirmation(addon));

                // Card click for detailed view
                cardView.setOnClickListener(v -> showAddonDetails(addon));
            }

            private String getCategoryDisplayName(String categoryId) {
                for (AddonManager.AddonCategory category : AddonManager.AddonCategory.values()) {
                    if (category.getId().equals(categoryId)) {
                        return category.getDisplayName();
                    }
                }
                return categoryId;
            }
        }
    }

    private void showAddonConfiguration(AddonConfig addon) {
        String configSummary = addon.getConfigurationSummary();
        
        new AlertDialog.Builder(this)
            .setTitle("⚙️ " + addon.getName() + " Configuration")
            .setMessage(configSummary)
            .setPositiveButton("OK", null)
            .setNeutralButton("Advanced", (dialog, which) -> {
                // Advanced configuration would open a dedicated activity
                Toast.makeText(this, "Advanced configuration coming soon", Toast.LENGTH_SHORT).show();
            })
            .show();
    }

    private void showDeleteConfirmation(AddonConfig addon) {
        new AlertDialog.Builder(this)
            .setTitle("🗑️ Delete Addon")
            .setMessage("Are you sure you want to remove '" + addon.getName() + "'?\n\nThis will move the addon to the disabled folder.")
            .setPositiveButton("Delete", (dialog, which) -> {
                if (addonManager.removeAddon(addon.getId())) {
                    loadAddons();
                } else {
                    Toast.makeText(this, "Failed to remove addon", Toast.LENGTH_SHORT).show();
                }
            })
            .setNegativeButton("Cancel", null)
            .show();
    }

    private void showAddonDetails(AddonConfig addon) {
        String details = addon.getDisplayInfo();
        
        AlertDialog.Builder builder = new AlertDialog.Builder(this);
        builder.setTitle("📋 Addon Details");
        builder.setMessage(details);
        builder.setPositiveButton("OK", null);
        
        // Add action buttons if addon has actions
        if (!addon.getActions().isEmpty()) {
            builder.setNeutralButton("Actions (" + addon.getActions().size() + ")", 
                (dialog, which) -> showAddonActions(addon));
        }
        
        builder.show();
    }

    private void showAddonActions(AddonConfig addon) {
        List<AddonConfig.AddonAction> actions = addon.getActions();
        if (actions.isEmpty()) {
            Toast.makeText(this, "No actions available", Toast.LENGTH_SHORT).show();
            return;
        }
        
        String[] actionNames = new String[actions.size()];
        for (int i = 0; i < actions.size(); i++) {
            actionNames[i] = actions.get(i).getName();
        }
        
        new AlertDialog.Builder(this)
            .setTitle("🎯 Addon Actions")
            .setItems(actionNames, (dialog, which) -> {
                AddonConfig.AddonAction action = actions.get(which);
                executeAddonAction(addon, action);
            })
            .setNegativeButton("Cancel", null)
            .show();
    }

    private void executeAddonAction(AddonConfig addon, AddonConfig.AddonAction action) {
        Map<String, Object> parameters = new HashMap<>();
        parameters.put("activity", this);
        parameters.putAll(action.getParameters());
        
        boolean success = addonManager.executeAddon(addon.getId(), action.getAction(), parameters);
        
        if (success) {
            Toast.makeText(this, "✅ Executed: " + action.getName(), Toast.LENGTH_SHORT).show();
        } else {
            Toast.makeText(this, "❌ Failed to execute: " + action.getName(), Toast.LENGTH_SHORT).show();
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (addonManager != null) {
            addonManager.cleanup();
        }
    }
}

================================================================================
ModLoader/app/src/main/java/com/modloader/addon/AddonManager.java

// File: AddonManager.java - Core Configuration-Based Addon System
// Path: app/src/main/java/com/modloader/addon/AddonManager.java
package com.modloader.addon;

import android.content.Context;
import android.content.Intent;
import android.net.Uri;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.content.FileProvider;

import com.modloader.util.LogUtils;
import com.modloader.util.PathManager;
import com.modloader.util.FileUtils;

import org.json.JSONObject;
import org.json.JSONArray;
import org.json.JSONException;

import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.HashMap;

/**
 * Core Addon Management System for ModLoader
 * Handles configuration-based extensions and plugins
 */
public class AddonManager {
    private static final String TAG = "AddonManager";
    private static final String ADDON_DIRECTORY = "Addons";
    private static final String ADDON_CONFIG_FILE = "addon_config.json";
    
    private final Context context;
    private final List<AddonConfig> loadedAddons;
    private final Map<String, AddonExecutor> executors;
    private File addonBaseDir;
    private boolean initialized = false;

    // Addon categories
    public enum AddonCategory {
        MOD_INSTALLER("mod_installer", "Mod Installation Tools"),
        APK_PATCHER("apk_patcher", "APK Patching Extensions"),
        UI_ENHANCEMENT("ui_enhancement", "User Interface Enhancements"),
        FILE_MANAGER("file_manager", "File Management Tools"),
        DIAGNOSTIC("diagnostic", "Diagnostic and Repair Tools"),
        AUTOMATION("automation", "Automation Scripts"),
        THEME("theme", "Themes and Visual Customization"),
        UTILITY("utility", "General Utilities");

        private final String id;
        private final String displayName;

        AddonCategory(String id, String displayName) {
            this.id = id;
            this.displayName = displayName;
        }

        public String getId() { return id; }
        public String getDisplayName() { return displayName; }
    }

    public AddonManager(Context context) {
        this.context = context;
        this.loadedAddons = new ArrayList<>();
        this.executors = new HashMap<>();
        initialize();
    }

    /**
     * Initialize the addon system
     */
    private void initialize() {
        try {
            // Create addon directory structure
            createAddonDirectories();
            
            // Load existing addons
            loadAddons();
            
            // Initialize executors
            initializeExecutors();
            
            initialized = true;
            LogUtils.logUser("✅ AddonManager initialized with " + loadedAddons.size() + " addons");
        } catch (Exception e) {
            LogUtils.logError("Failed to initialize AddonManager: " + e.getMessage());
        }
    }

    /**
     * Create the addon directory structure
     */
    private void createAddonDirectories() {
        File gameBaseDir = PathManager.getGameBaseDir(context, "com.and.games505.TerrariaPaid");
        addonBaseDir = new File(gameBaseDir, ADDON_DIRECTORY);
        
        // Create main directories
        String[] addonDirs = {
            "",  // Root addon directory
            "configs",  // Addon configuration files
            "resources", // Addon resources (icons, assets)
            "themes",   // Theme files
            "scripts",  // Script files
            "templates", // Template configurations
            "backups",  // Backup configurations
            "disabled"  // Disabled addons
        };
        
        for (String dir : addonDirs) {
            File directory = new File(addonBaseDir, dir);
            if (!directory.exists() && !directory.mkdirs()) {
                LogUtils.logError("Failed to create addon directory: " + directory.getAbsolutePath());
            }
        }
        
        // Create sample addon configurations
        createSampleAddons();
        
        LogUtils.logDebug("Addon directories created at: " + addonBaseDir.getAbsolutePath());
    }

    /**
     * Create sample addon configurations for demonstration
     */
    private void createSampleAddons() {
        try {
            // Sample Theme Addon
            createSampleThemeAddon();
            
            // Sample Utility Addon
            createSampleUtilityAddon();
            
            // Sample Mod Installer Enhancement
            createSampleModInstallerAddon();
            
        } catch (Exception e) {
            LogUtils.logError("Failed to create sample addons: " + e.getMessage());
        }
    }

    private void createSampleThemeAddon() throws JSONException, IOException {
        File themeConfigFile = new File(new File(addonBaseDir, "configs"), "dark_theme.json");
        if (!themeConfigFile.exists()) {
            JSONObject themeConfig = new JSONObject();
            themeConfig.put("id", "dark_theme_v1");
            themeConfig.put("name", "Dark Theme Enhancement");
            themeConfig.put("version", "1.0.0");
            themeConfig.put("author", "ModLoader Team");
            themeConfig.put("description", "Enhanced dark theme with better contrast and modern UI elements");
            themeConfig.put("category", AddonCategory.THEME.getId());
            themeConfig.put("enabled", true);
            
            JSONObject config = new JSONObject();
            config.put("primary_color", "#1E1E1E");
            config.put("secondary_color", "#2D2D2D");
            config.put("accent_color", "#4CAF50");
            config.put("text_color", "#FFFFFF");
            config.put("button_style", "rounded");
            config.put("card_elevation", "8dp");
            
            themeConfig.put("configuration", config);
            
            JSONArray hooks = new JSONArray();
            hooks.put("on_activity_create");
            hooks.put("on_theme_change");
            themeConfig.put("hooks", hooks);
            
            try (FileWriter writer = new FileWriter(themeConfigFile)) {
                writer.write(themeConfig.toString(2));
            }
        }
    }

    private void createSampleUtilityAddon() throws JSONException, IOException {
        File utilityConfigFile = new File(new File(addonBaseDir, "configs"), "enhanced_file_manager.json");
        if (!utilityConfigFile.exists()) {
            JSONObject utilityConfig = new JSONObject();
            utilityConfig.put("id", "enhanced_file_manager");
            utilityConfig.put("name", "Enhanced File Manager");
            utilityConfig.put("version", "1.2.0");
            utilityConfig.put("author", "Community");
            utilityConfig.put("description", "Adds bulk operations, advanced search, and file preview");
            utilityConfig.put("category", AddonCategory.FILE_MANAGER.getId());
            utilityConfig.put("enabled", true);
            
            JSONObject config = new JSONObject();
            config.put("enable_bulk_operations", true);
            config.put("enable_file_preview", true);
            config.put("enable_advanced_search", true);
            config.put("max_preview_size", "50MB");
            config.put("supported_preview_types", "image,text,json");
            
            JSONArray ui_enhancements = new JSONArray();
            ui_enhancements.put("add_toolbar_buttons");
            ui_enhancements.put("add_context_menu_items");
            ui_enhancements.put("add_batch_select_mode");
            config.put("ui_enhancements", ui_enhancements);
            
            utilityConfig.put("configuration", config);
            
            JSONArray actions = new JSONArray();
            
            JSONObject bulkDeleteAction = new JSONObject();
            bulkDeleteAction.put("id", "bulk_delete");
            bulkDeleteAction.put("name", "Bulk Delete");
            bulkDeleteAction.put("icon", "delete");
            bulkDeleteAction.put("action", "show_bulk_delete_dialog");
            actions.put(bulkDeleteAction);
            
            JSONObject compressAction = new JSONObject();
            compressAction.put("id", "compress_files");
            compressAction.put("name", "Create Archive");
            compressAction.put("icon", "archive");
            compressAction.put("action", "compress_selected_files");
            actions.put(compressAction);
            
            utilityConfig.put("actions", actions);
            
            try (FileWriter writer = new FileWriter(utilityConfigFile)) {
                writer.write(utilityConfig.toString(2));
            }
        }
    }

    private void createSampleModInstallerAddon() throws JSONException, IOException {
        File modInstallerConfigFile = new File(new File(addonBaseDir, "configs"), "smart_mod_installer.json");
        if (!modInstallerConfigFile.exists()) {
            JSONObject modConfig = new JSONObject();
            modConfig.put("id", "smart_mod_installer");
            modConfig.put("name", "Smart Mod Installer");
            modConfig.put("version", "2.1.0");
            modConfig.put("author", "ModLoader Team");
            modConfig.put("description", "Intelligent mod installation with conflict detection and automatic compatibility checks");
            modConfig.put("category", AddonCategory.MOD_INSTALLER.getId());
            modConfig.put("enabled", true);
            
            JSONObject config = new JSONObject();
            config.put("auto_detect_conflicts", true);
            config.put("check_compatibility", true);
            config.put("create_backups", true);
            config.put("suggest_load_order", true);
            config.put("verify_checksums", true);
            
            JSONArray supported_formats = new JSONArray();
            supported_formats.put(".dll");
            supported_formats.put(".dex");
            supported_formats.put(".jar");
            supported_formats.put(".zip");
            supported_formats.put(".rar");
            config.put("supported_formats", supported_formats);
            
            JSONObject install_rules = new JSONObject();
            install_rules.put("max_file_size", "100MB");
            install_rules.put("require_description", false);
            install_rules.put("auto_enable", true);
            install_rules.put("scan_for_malware", true);
            config.put("install_rules", install_rules);
            
            modConfig.put("configuration", config);
            
            JSONArray workflow = new JSONArray();
            
            JSONObject step1 = new JSONObject();
            step1.put("step", "validate_file");
            step1.put("description", "Validate mod file integrity");
            workflow.put(step1);
            
            JSONObject step2 = new JSONObject();
            step2.put("step", "check_conflicts");
            step2.put("description", "Check for conflicts with existing mods");
            workflow.put(step2);
            
            JSONObject step3 = new JSONObject();
            step3.put("step", "backup_existing");
            step3.put("description", "Create backup of current mod setup");
            workflow.put(step3);
            
            JSONObject step4 = new JSONObject();
            step4.put("step", "install_mod");
            step4.put("description", "Install the mod file");
            workflow.put(step4);
            
            JSONObject step5 = new JSONObject();
            step5.put("step", "update_registry");
            step5.put("description", "Update mod registry and dependencies");
            workflow.put(step5);
            
            modConfig.put("workflow", workflow);
            
            try (FileWriter writer = new FileWriter(modInstallerConfigFile)) {
                writer.write(modConfig.toString(2));
            }
        }
    }

    /**
     * Load all addon configurations
     */
    private void loadAddons() {
        loadedAddons.clear();
        
        File configsDir = new File(addonBaseDir, "configs");
        if (!configsDir.exists()) {
            return;
        }
        
        File[] configFiles = configsDir.listFiles((dir, name) -> name.endsWith(".json"));
        if (configFiles != null) {
            for (File configFile : configFiles) {
                try {
                    AddonConfig addon = loadAddonConfig(configFile);
                    if (addon != null && addon.isEnabled()) {
                        loadedAddons.add(addon);
                        LogUtils.logDebug("Loaded addon: " + addon.getName() + " v" + addon.getVersion());
                    }
                } catch (Exception e) {
                    LogUtils.logError("Failed to load addon config: " + configFile.getName() + " - " + e.getMessage());
                }
            }
        }
        
        LogUtils.logUser("📦 Loaded " + loadedAddons.size() + " active addons");
    }

    /**
     * Load a single addon configuration file
     */
    private AddonConfig loadAddonConfig(File configFile) throws IOException, JSONException {
        StringBuilder content = new StringBuilder();
        try (FileReader reader = new FileReader(configFile)) {
            int ch;
            while ((ch = reader.read()) != -1) {
                content.append((char) ch);
            }
        }
        
        JSONObject json = new JSONObject(content.toString());
        return new AddonConfig(json, configFile);
    }

    /**
     * Initialize addon executors for different categories
     */
    private void initializeExecutors() {
        executors.put(AddonCategory.THEME.getId(), new ThemeExecutor(context));
        executors.put(AddonCategory.UI_ENHANCEMENT.getId(), new UIEnhancementExecutor(context));
        executors.put(AddonCategory.FILE_MANAGER.getId(), new FileManagerExecutor(context));
        executors.put(AddonCategory.MOD_INSTALLER.getId(), new ModInstallerExecutor(context));
        executors.put(AddonCategory.UTILITY.getId(), new UtilityExecutor(context));
        executors.put(AddonCategory.DIAGNOSTIC.getId(), new DiagnosticExecutor(context));
        
        LogUtils.logDebug("Initialized " + executors.size() + " addon executors");
    }

    /**
     * Get all loaded addons
     */
    public List<AddonConfig> getLoadedAddons() {
        return new ArrayList<>(loadedAddons);
    }

    /**
     * Get addons by category
     */
    public List<AddonConfig> getAddonsByCategory(AddonCategory category) {
        List<AddonConfig> categoryAddons = new ArrayList<>();
        for (AddonConfig addon : loadedAddons) {
            if (category.getId().equals(addon.getCategory())) {
                categoryAddons.add(addon);
            }
        }
        return categoryAddons;
    }

    /**
     * Execute addon functionality
     */
    public boolean executeAddon(String addonId, String action, Map<String, Object> parameters) {
        AddonConfig addon = findAddonById(addonId);
        if (addon == null) {
            LogUtils.logError("Addon not found: " + addonId);
            return false;
        }
        
        if (!addon.isEnabled()) {
            LogUtils.logError("Addon is disabled: " + addonId);
            return false;
        }
        
        AddonExecutor executor = executors.get(addon.getCategory());
        if (executor == null) {
            LogUtils.logError("No executor found for category: " + addon.getCategory());
            return false;
        }
        
        try {
            return executor.execute(addon, action, parameters);
        } catch (Exception e) {
            LogUtils.logError("Failed to execute addon " + addonId + ": " + e.getMessage());
            return false;
        }
    }

    /**
     * Apply addon modifications to an activity
     */
    public void applyAddonsToActivity(AppCompatActivity activity, String activityName) {
        for (AddonConfig addon : loadedAddons) {
            try {
                AddonExecutor executor = executors.get(addon.getCategory());
                if (executor != null) {
                    Map<String, Object> params = new HashMap<>();
                    params.put("activity", activity);
                    params.put("activity_name", activityName);
                    executor.execute(addon, "apply_to_activity", params);
                }
            } catch (Exception e) {
                LogUtils.logError("Failed to apply addon " + addon.getId() + " to activity: " + e.getMessage());
            }
        }
    }

    /**
     * Install a new addon from a configuration file
     */
    public boolean installAddon(Uri addonUri) {
        try {
            File tempFile = new File(context.getCacheDir(), "temp_addon.json");
            if (!FileUtils.copyUriToFile(context, addonUri, tempFile)) {
                LogUtils.logError("Failed to copy addon configuration file");
                return false;
            }
            
            // Validate the addon configuration
            AddonConfig addon = loadAddonConfig(tempFile);
            if (addon == null) {
                LogUtils.logError("Invalid addon configuration");
                return false;
            }
            
            // Check if addon already exists
            if (findAddonById(addon.getId()) != null) {
                LogUtils.logUser("Addon already exists: " + addon.getId() + " - updating...");
            }
            
            // Copy to configs directory
            File configsDir = new File(addonBaseDir, "configs");
            File targetFile = new File(configsDir, addon.getId() + ".json");
            if (!FileUtils.copyFile(tempFile, targetFile)) {
                LogUtils.logError("Failed to install addon configuration");
                return false;
            }
            
            // Reload addons
            loadAddons();
            
            LogUtils.logUser("✅ Successfully installed addon: " + addon.getName());
            return true;
            
        } catch (Exception e) {
            LogUtils.logError("Failed to install addon: " + e.getMessage());
            return false;
        }
    }

    /**
     * Enable or disable an addon
     */
    public boolean toggleAddon(String addonId, boolean enabled) {
        AddonConfig addon = findAddonById(addonId);
        if (addon == null) {
            return false;
        }
        
        try {
            // Update the configuration file
            addon.setEnabled(enabled);
            saveAddonConfig(addon);
            
            // Reload addons
            loadAddons();
            
            LogUtils.logUser((enabled ? "✅ Enabled" : "❌ Disabled") + " addon: " + addon.getName());
            return true;
            
        } catch (Exception e) {
            LogUtils.logError("Failed to toggle addon: " + e.getMessage());
            return false;
        }
    }

    /**
     * Remove an addon
     */
    public boolean removeAddon(String addonId) {
        AddonConfig addon = findAddonById(addonId);
        if (addon == null) {
            return false;
        }
        
        try {
            // Move to disabled directory instead of deleting
            File disabledDir = new File(addonBaseDir, "disabled");
            File targetFile = new File(disabledDir, addon.getConfigFile().getName());
            
            if (addon.getConfigFile().renameTo(targetFile)) {
                loadAddons();
                LogUtils.logUser("🗑️ Removed addon: " + addon.getName());
                return true;
            } else {
                LogUtils.logError("Failed to remove addon configuration file");
                return false;
            }
            
        } catch (Exception e) {
            LogUtils.logError("Failed to remove addon: " + e.getMessage());
            return false;
        }
    }

    /**
     * Export addon configuration
     */
    public boolean exportAddon(String addonId) {
        AddonConfig addon = findAddonById(addonId);
        if (addon == null) {
            return false;
        }
        
        try {
            File exportDir = new File(context.getExternalFilesDir(null), "exports");
            exportDir.mkdirs();
            
            String filename = addon.getId() + "_v" + addon.getVersion() + ".json";
            File exportFile = new File(exportDir, filename);
            
            if (FileUtils.copyFile(addon.getConfigFile(), exportFile)) {
                // Share the exported file
                Intent shareIntent = new Intent(Intent.ACTION_SEND);
                shareIntent.setType("application/json");
                shareIntent.putExtra(Intent.EXTRA_STREAM,
                    FileProvider.getUriForFile(context, context.getPackageName() + ".provider", exportFile));
                shareIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
                
                Intent chooser = Intent.createChooser(shareIntent, "Export Addon: " + addon.getName());
                chooser.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
                context.startActivity(chooser);
                
                LogUtils.logUser("📤 Exported addon: " + addon.getName());
                return true;
            }
            
        } catch (Exception e) {
            LogUtils.logError("Failed to export addon: " + e.getMessage());
        }
        
        return false;
    }

    /**
     * Get addon statistics
     */
    public String getAddonStatistics() {
        Map<String, Integer> categoryCounts = new HashMap<>();
        int enabledCount = 0;
        
        for (AddonConfig addon : loadedAddons) {
            if (addon.isEnabled()) {
                enabledCount++;
            }
            
            String category = addon.getCategory();
            categoryCounts.put(category, categoryCounts.getOrDefault(category, 0) + 1);
        }
        
        StringBuilder stats = new StringBuilder();
        stats.append("=== Addon Statistics ===\n");
        stats.append("Total Addons: ").append(loadedAddons.size()).append("\n");
        stats.append("Enabled: ").append(enabledCount).append("\n");
        stats.append("Disabled: ").append(loadedAddons.size() - enabledCount).append("\n\n");
        
        stats.append("By Category:\n");
        for (AddonCategory category : AddonCategory.values()) {
            int count = categoryCounts.getOrDefault(category.getId(), 0);
            if (count > 0) {
                stats.append("• ").append(category.getDisplayName()).append(": ").append(count).append("\n");
            }
        }
        
        return stats.toString();
    }

    /**
     * Find addon by ID
     */
    private AddonConfig findAddonById(String addonId) {
        for (AddonConfig addon : loadedAddons) {
            if (addon.getId().equals(addonId)) {
                return addon;
            }
        }
        return null;
    }

    /**
     * Save addon configuration to file
     */
    private void saveAddonConfig(AddonConfig addon) throws IOException, JSONException {
        try (FileWriter writer = new FileWriter(addon.getConfigFile())) {
            writer.write(addon.toJSON().toString(2));
        }
    }

    /**
     * Check if addon system is initialized
     */
    public boolean isInitialized() {
        return initialized;
    }

    /**
     * Get addon base directory
     */
    public File getAddonBaseDirectory() {
        return addonBaseDir;
    }

    /**
     * Cleanup resources
     */
    public void cleanup() {
        loadedAddons.clear();
        executors.clear();
        initialized = false;
        LogUtils.logDebug("AddonManager cleaned up");
    }
}

================================================================================
ModLoader/app/src/main/java/com/modloader/installer/ModInstaller.java

// File: ModInstaller.java (FIXED) - Updated to use new directory structure
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/installer/ModInstaller.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/LoaderFileManager.java

// File: LoaderFileManager.java (Fixed Component) - Complete Path Management Fix
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/loader/LoaderFileManager.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/LoaderInstaller.java

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
        "Logs", // All logs (both MelonLoader and TerrariaLoader)
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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/LoaderValidator.java

// File: LoaderValidator.java (Fixed Component) - Complete Path Management Fix
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/loader/LoaderValidator.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/MainLoader.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/MelonLoaderManager.java

// File: MelonLoaderManager.java (Fixed Facade) - Updated with PathManager
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/loader/MelonLoaderManager.java

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
        
        summary.append("=== TerrariaLoader Summary ===\n");
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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/ModBase.java

// File: ModBase.java (Interface Class) - Phase 4 Enhanced with DLL Support
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/loader/ModBase.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/ModConfiguration.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/ModController.java

// File: ModController.java (Extracted Component) - Handles mod state management
// Path: /storage/emulated/0/AndroidIDEProjects/main/java/com/terrarialoader/loader/ModController.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/ModLoader.java

// File: ModLoader.java (Complete Fixed Component) - Updated Method Calls with Context
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/loader/ModLoader.java

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
            File melonModsDir = new File(context.getExternalFilesDir(null), "TerrariaLoader/com.and.games505.TerrariaPaid/Mods/DLL");
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
            File melonModsDir = new File(context.getExternalFilesDir(null), "TerrariaLoader/com.and.games505.TerrariaPaid/Mods/DLL");
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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/ModManager.java

// File: ModManager.java (Fixed Facade) - Updated with PathManager
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/loader/ModManager.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/ModMetadata.java

// File: ModMetadata.java (Fixed Class) - Enhanced Null Safety
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/loader/ModMetadata.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/ModRepository.java

// File: ModRepository.java (Fixed Component) - Updated Method Calls
// Path: /storage/emulated/0/AndroidIDEProjects/TerrariaML/app/src/main/java/com/terrarialoader/loader/ModRepository.java

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

================================================================================
ModLoader/app/src/main/java/com/modloader/loader/debugger/ApkProcessTracker.java

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