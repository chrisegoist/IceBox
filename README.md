# ❄️ IceBox

**Freeze apps you don't use so they stop running in the background. One tap to freeze, one tap to bring them back.**

IceBox is a lightweight Android app freezer. It disables apps for your user profile (`pm disable-user`) and re-enables them whenever you need them. Set it up once with ADB or root, and after that it works entirely on the device, with no PC needed.

<!-- Add screenshots here -->
<!-- <p align="center"><img src="screenshots/home.png" width="240"> <img src="screenshots/settings.png" width="240"></p> -->

## Features

- **One-tap freeze / unfreeze** with a confirmation dialog
- **Search** to find apps fast, plus a **Frozen** tab showing everything you've frozen
- **Unfreeze all** in a single action
- **Two ways to start the service:** run a one-line ADB command, or tap **Use root**
- **Protected apps:** your launcher, System UI, Settings, Phone and IceBox itself can't be frozen, so you can't lock yourself out
- **System apps are hidden by default**, with an opt-in toggle (freezing system apps can break your phone)
- **Customization:** light, dark and pure-black (AMOLED) themes, match-system-theme, accent colors, compact rows, package-name display, animations toggle and haptic feedback
- **6 languages:** English, Español, Français, Deutsch, Português, Русский
- **No ads, no analytics, no backup**

## How it works

Freezing other apps needs shell privileges, which a normal app doesn't have. IceBox gets around this with a tiny helper process:

1. IceBox generates a start script. You run it once from an ADB shell (or tap **Use root**).
2. The script launches `Server` with `app_process` as the **shell user**.
3. `Server` listens on **`127.0.0.1:38258`**, loopback only, so it is never reachable from the network.
4. The app talks to it over that local socket. Every request must include a **random per-install token** or it is rejected.

The server accepts only a handful of commands (`ping`, `freeze <pkg>`, `unfreeze <pkg>` and `stop`), validates package names against a strict pattern, and runs only:

```
pm disable-user --user 0 <package>
pm enable --user 0 <package>
```

> The `INTERNET` permission is declared only because Android needs it to open the local socket. IceBox doesn't talk to any remote server.

## Setup

1. Install the APK and open IceBox.
2. Choose how to start the service:
   - **ADB:** tap **Copy**, then run the command in an ADB shell (from a PC, prefix it with `adb shell`).
   - **Root:** tap **Use root** and grant access.
3. Tap **Check**. When the status pill shows **Service running**, you're ready.
4. Tap an app to freeze it. Tap it again to unfreeze.

The service stops when you reboot your phone, so just repeat step 2 after a restart.

## Requirements

- Android 5.0+ (API 21)
- ADB access (USB or wireless debugging) **or** root

## Build

IceBox is a plain Gradle Android project with no third-party dependencies, and it was built in AIDE.

```
compileSdkVersion 28
minSdkVersion     21
targetSdkVersion  28
applicationId     com.superchrisss.appfreezer
```

Open the project in AIDE or Android Studio and build as usual.

```
IceBox/
└── app/src/main/java/com/superchrisss/appfreezer/
    ├── MainActivity.java   # UI, themes, settings, setup flow
    ├── Server.java         # shell-user helper (freeze / unfreeze)
    ├── Shell.java          # loopback client for Server
    └── Lang.java           # translations
```

## ⚠️ Disclaimer

Freezing the wrong app, especially a system app, can cause instability or stop your phone from working properly. IceBox protects the most critical packages, but use common sense and don't freeze things you don't understand. Use at your own risk.

## Author

Made by **[@chrisegoist](https://github.com/chrisegoist)**
