---
{"dg-publish":true,"permalink":"/vault/windows/tauri-setup-guide-for-windows/"}
---

## Using Bun and TypeScript

---

## Prerequisites Overview

You need to install (in order):

1. Microsoft C++ Build Tools
2. WebView2 (usually pre-installed on Windows 10/11)
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

## Step 2: Verify WebView2

WebView2 is pre-installed on Windows 10 (v1803+) and Windows 11. To verify:

1. Open Settings → Apps → Installed Apps
2. Search for "WebView2"
3. If missing, download from: https://developer.microsoft.com/en-us/microsoft-edge/webview2/
    - Get the **"Evergreen Bootstrapper"** and run it

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

Download and install from Microsoft's WebView2 page.

### Path issues after installation

Restart your terminal or PC. Windows often requires this for PATH changes.

---

## Useful Links

- Tauri Docs: https://v2.tauri.app
- Bun Docs: https://bun.sh/docs
- Tauri Discord: https://discord.com/invite/tauri
- Bun Discord: https://bun.sh/discord