---
{"dg-publish":true,"permalink":"/vault/windows/tauri-setup-guide-for-windows/"}
---

# Tauri Setup Guide for Windows (Fresh Install)

## Using Bun and TypeScript

---

## Prerequisites Overview

You need to install (in order):

1. Microsoft C++ Build Tools
2. WebView2 _(Windows 11: skip, already installed)_
3. Rust
4. Bun

---

## Step 1: Install Microsoft C++ Build Tools

1. Download the installer from: https://visualstudio.microsoft.com/visual-cpp-build-tools/
2. Run the installer
3. Select **"Desktop development with C++"**
4. Ensure **"Windows 10 SDK"** is checked (or Windows 11 SDK)
5. Click Install and wait for completion

> **Note:** This is ~6-8 GB and may take a while.

---

## Step 2: WebView2

**Windows 11:** WebView2 is pre-installed. **Skip this step.**

**Windows 10 (v1803+):** Usually pre-installed. If you encounter WebView2 errors later, install via PowerShell:

```powershell
winget install Microsoft.EdgeWebView2Runtime
```

---

## Step 3: Install Rust

Open **PowerShell** and run:

```powershell
winget install --id Rustlang.Rustup
```

Or download from: https://www.rust-lang.org/tools/install

**Important:** During installation, ensure the default host triple is set to `x86_64-pc-windows-msvc` (or `aarch64-pc-windows-msvc` for ARM).

After installation, **restart your terminal**, then verify:

```powershell
rustc --version
cargo --version
```

Ensure MSVC toolchain is default:

```powershell
rustup default stable-msvc
```

---

## Step 4: Install Bun

Open **PowerShell** and run:

```powershell
powershell -c "irm bun.sh/install.ps1|iex"
```

**Restart your terminal**, then verify:

```powershell
bun --version
```

### If `bun` command not found:

Add Bun to PATH manually:

```powershell
[System.Environment]::SetEnvironmentVariable(
  "Path",
  [System.Environment]::GetEnvironmentVariable("Path", "User") + ";$env:USERPROFILE\.bun\bin",
  [System.EnvironmentVariableTarget]::User
)
```

Then restart your terminal.

---

## Step 5: Create Your Tauri Project

Open **PowerShell** in the folder where you want your project:

```powershell
bun create tauri-app
```

When prompted:

- **Project name:** `my-app` (or your choice)
- **Identifier:** `com.my-app.app` (or your choice)
- **Frontend language:** `TypeScript / JavaScript`
- **Package manager:** `bun`
- **UI template:** Choose one (Vanilla, React, Vue, Svelte, etc.)
- **UI flavor:** `TypeScript`

---

## Step 6: Install Dependencies and Run

```powershell
cd my-app
bun install
bun tauri dev
```

The first run will:

1. Compile Rust code (takes 2-5 minutes initially)
2. Start your dev server
3. Open a native window with your app

---

## Quick Reference Commands

|Action|Command|
|---|---|
|Start dev server|`bun tauri dev`|
|Build for production|`bun tauri build`|
|Check environment|`bun tauri info`|
|Update Tauri CLI|`bun update @tauri-apps/cli`|
|Update Rust|`rustup update`|
|Update Bun|`bun upgrade`|

---

## Project Structure

```
my-app/
├── src/              # Frontend code (TypeScript)
├── src-tauri/        # Rust backend
│   ├── src/
│   │   └── main.rs   # Rust entry point
│   ├── Cargo.toml    # Rust dependencies
│   └── tauri.conf.json  # Tauri config
├── package.json
└── bun.lockb
```

---

## Optional: Enable MSI Installer Building

If you want to build `.msi` installers, ensure VBSCRIPT is enabled:

1. Settings → Apps → Optional Features → More Windows Features
2. Find and check **VBSCRIPT**
3. Restart if prompted

---

## Troubleshooting

### "MSVC toolchain not found"

```powershell
rustup default stable-msvc
```

### Build errors about missing icons

Ensure `src-tauri/icons/` contains the required icon files. Run:

```powershell
bun tauri icon path/to/your/icon.png
```

### Rust compilation slow

First build is slow (~2-5 min). Subsequent builds are faster due to caching.

### "WebView2 not found"

Windows 11 has it pre-installed. For Windows 10, run:

```powershell
winget install Microsoft.EdgeWebView2Runtime
```

### Path issues after installation

Restart your terminal or PC. Windows often requires this for PATH changes.

---

## Useful Links

- Tauri Docs: https://v2.tauri.app
- Bun Docs: https://bun.sh/docs
- Tauri Discord: https://discord.com/invite/tauri
- Bun Discord: https://bun.sh/discord

---

## Android Development Setup (Optional)

### Step A: Install Android Studio

1. Download from: https://developer.android.com/studio
2. Run installer with default settings
3. Launch Android Studio after installation and let it complete initial setup

---

### Step B: Install SDK Components

1. Open Android Studio
2. Go to **Settings** → **Languages & Frameworks** → **Android SDK**
3. Click **SDK Tools** tab
4. Check "Show Package Details" (bottom right)
5. Install these components:
    - ✅ Android SDK Platform (latest, e.g., Android 14.0 "UpsideDownCake")
    - ✅ Android SDK Platform-Tools
    - ✅ Android SDK Build-Tools (latest version)
    - ✅ Android SDK Command-line Tools (latest)
    - ✅ NDK (Side by side) — **note the version number** (e.g., `27.0.12077973`)
6. Click **Apply** and accept licenses

---

### Step C: Set Environment Variables

Open **PowerShell** and run these commands (adjust NDK version to match yours):

```powershell
# Set JAVA_HOME (Android Studio's bundled JDK)
[System.Environment]::SetEnvironmentVariable("JAVA_HOME", "C:\Program Files\Android\Android Studio\jbr", "User")

# Set ANDROID_HOME
[System.Environment]::SetEnvironmentVariable("ANDROID_HOME", "$env:LocalAppData\Android\Sdk", "User")

# Set NDK_HOME (replace version with yours from Step B)
$NDK_VERSION = Get-ChildItem -Name "$env:LocalAppData\Android\Sdk\ndk" | Select-Object -Last 1
[System.Environment]::SetEnvironmentVariable("NDK_HOME", "$env:LocalAppData\Android\Sdk\ndk\$NDK_VERSION", "User")
```

**Refresh current PowerShell session:**

```powershell
[System.Environment]::GetEnvironmentVariables("User").GetEnumerator() | % { Set-Item -Path "Env:\$($_.key)" -Value $_.value }
```

Or simply **restart your PC** (recommended).

---

### Step D: Add Android Rust Targets

```powershell
rustup target add aarch64-linux-android armv7-linux-androideabi i686-linux-android x86_64-linux-android
```

---

### Step E: Enable Windows Developer Mode

**Required for symlink creation:**

1. Open **Settings**
2. Go to **Privacy & Security** → **For developers**
3. Toggle **Developer Mode** to **ON**
4. Restart your terminal

---

### Step F: Initialize Android in Your Project

```powershell
cd your-tauri-project
bun tauri android init
```

---

### Step G: Run on Android

**Using emulator:**

1. Open Android Studio → Device Manager → Create Virtual Device
2. Start the emulator
3. Run:

```powershell
bun tauri android dev
```

**Using physical device:**

1. Enable Developer Mode: Settings → About Phone → Tap "Build Number" 7 times
2. Enable USB Debugging: Settings → Developer Options → USB Debugging ON
3. Connect via USB
4. Run:

```powershell
bun tauri android dev
```

---

### Step H: Build APK for Release

```powershell
bun tauri android build
```

Output location: `src-tauri/gen/android/app/build/outputs/apk/`

---

### Android Troubleshooting

**"Failed to create symbolic link" / symlink error**

- Enable Windows Developer Mode: Settings → Privacy & Security → For developers → ON
- Restart terminal and retry

**"NDK_HOME environment variable isn't set"**

```powershell
# Check if NDK is installed
ls $env:LocalAppData\Android\Sdk\ndk

# Set it manually with exact version
[System.Environment]::SetEnvironmentVariable("NDK_HOME", "$env:LocalAppData\Android\Sdk\ndk\YOUR_VERSION", "User")
```

**"No available Android Emulator detected"**

- Create an emulator in Android Studio → Device Manager
- Or connect a physical device with USB debugging enabled
- Run `adb devices` to verify connection

**Emulator stuck on "Waiting for emulator to start"**

```powershell
adb kill-server
adb start-server
adb devices
```

**"INSTALL_FAILED_ALREADY_EXISTS" on physical device**

- Uninstall the app from device manually, then retry

**Dev server connection issues**

- Ensure your dev server listens on `0.0.0.0` or uses `TAURI_DEV_HOST`
- Check firewall isn't blocking connections

**Verify your setup:**

```powershell
echo $env:JAVA_HOME
echo $env:ANDROID_HOME  
echo $env:NDK_HOME
adb devices
```