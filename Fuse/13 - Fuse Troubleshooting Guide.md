# $(Fuse Troubleshooting Guide)

## Cannot connect to daemon

### Symptom

- You get a message `fuse: A network error occurred: Could not resolve host '<your hostname>' Please check your network setup and try again.`

### Solution

- You have an issue with your network. Try doing `ping $(hostname)` in a terminal. If you get an error message, you have to fix your network setup before you continue.

### Symptom

- You get a stack trace with `FailedToConnectToDaemon` somewhere in it
 
### Solution

- First, try to stop the Fuse daemon.
 - Windows: Right click on the Fuse icon in the tray, click "Exit"
 - OS X: Control-click on the Fuse icon in the menu bar, click "Quit"
- A fresh daemon will then start automatically the next time you interact with Fuse.
- If that did not help, please try logging out of and back in to Windows / OS X.
- If that did not help, please try to reboot your computer.

### Symptom
- While previewing on an iOS or Android device, you get the message "Failed to connect"

### Solution
- Make sure your device has WiFi enabled
- Make sure your device is connected to the same WiFi as the computer running Fuse 
- If your computer is running a firewall (such as Windows Firewall), make sure Fuse is allowed to accept incoming connections
- If you still have the problem, quit Fuse from the tray / menu bar icon and re-start preview

## Cannot preview or export to Android

### Symptom
- The Android build gets stuck at "Trying to uninstall existing version of APK"
- The build finishes with "ERROR: No android devices found."

### Solution
- Check that the device is connected with a USB cable
- Check that usb debugging is enabled on your device (Search the web for "enable usb debugging &lt;your device name&gt;")
- Check that your device shows up in `adb devices`. `adb` can usually be found in
 - `C:\ProgramData\Uno\SDKs\AndroidSDK\platform-tools` (Windows)
 - `/usr/share/uno/SDKs/AndroidSDK/platform-tools` (OS X)
- (Windows) Check that you have the latest drivers for your device
- If your device still doesn't show up in `adb devices`, please search the web for solutions for your specific device. Fuse (like other Android development tools) depends on a working connection to your device in order to work.

## Sublime plugin does not work

### Symptom

- You get the message "Error loading: Packages/Fuse/UX.tmLanguage"
- You don't get syntax highlighting in Sublime

### Solution

- Open the dashboard, click "Sublime Text Setup" and follow the wizard.
- If that didn't work, delete the files staring with `Fuse` in `%APPDATA%\Sublime Text 3\Installed Packages` (Windows) or `~/Library/Application Support/Sublime Text 3/Installed Packages` (OS X), and try the wizard again.

## My Uno code is not run/updated in preview

### Symptom

- Changes to your Uno code is not reflected in preview
- Your Uno code is not executed in preview

### Explanation

- Unlike ux/js, Uno-code is not refreshed when you save and auto-refresh preview, you'll have to restart Fuse. A better solution for this is coming soon.
 - Windows: Right click on the Fuse icon in the tray, click "Exit"
 - OS X: Control-click on the Fuse icon in the menu bar, click "Quit"
- In addition, any Uno-code you have in `ux.uno` files is not run at all in preview.
- All in all, it can be easier to work with Uno code through `uno build` than through preview. A better solution for this is coming soon.

## Preview says "Oops! Something went wrong here"

### Symptom

- When previewing your app, the "Oops! Something went wrong here" screen appears.

### Solution

- Open the Fuse Monitor from the Tray or Dashboard to see details about the problem.
