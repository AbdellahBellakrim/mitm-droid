# MITM Proxy Setup with Certificate Pinning Bypass for Android

This guide walks through setting up a Man-in-the-Middle proxy with HTTPS support and certificate pinning bypass for Android applications.

## Prerequisites

- Rooted Android device
- Computer running Linux (Ubuntu recommended)
- Both devices connected to the same network

## Step 1: Install and Run mitmproxy

1. Install mitmproxy on your computer
2. Start mitmweb:
   ```bash
   mitmweb --mode regular -p 3000
   ```

## Step 2: Connect Android to the Proxy

1. Find your computer's IP address:
   ```bash
   hostname -I
   ```
2. On your Android device:
   - Go to Settings > Wi-Fi
   - Long-press your connected network
   - Modify network > Advanced options
   - Set proxy to Manual
   - Enter your computer's IP address and port 3000
   - Save the settings

## Step 3: Install the MITM Certificate

1. On your Android device, open a browser
2. Visit http://mitm.it/
3. Download and install the certificate for Android
4. For Android 7+, additional steps may be required:
   - Go to Settings > Security > Install from storage
   - Select the downloaded certificate
   - For newer Android versions, go to Settings > Security > Encryption & Credentials > Install certificate

## Step 4: Set Up Python Environment for Certificate Pinning Bypass

1. Create and activate a virtual environment:
   ```bash
   python3 -m venv venv
   source venv/bin/activate
   ```
2. Install necessary tools:
   ```bash
   pip install frida-tools
   pip install objection
   ```
3. Install ADB:
   ```bash
   sudo apt install adb
   ```

## Step 5: Set Up Frida on Android

1. Download the appropriate Frida server for your Android version from:
   https://github.com/frida/frida/releases
2. Unzip the downloaded file and rename it to 'frida'
3. Push Frida to your Android device:
   ```bash
   adb push frida /data/local/tmp
   ```
4. Access Android shell:
   ```bash
   adb shell
   ```
5. Navigate to the directory and set executable permissions:
   ```bash
   cd /data/local/tmp
   chmod 755 frida
   ```
6. Run Frida in the background:
   ```bash
   ./frida &
   ```
7. Exit the shell or keep it open if Frida is running in the foreground

## Step 6: Identify Target Application

1. List all installed applications:
   ```bash
   frida-ps -Uai | grep [app-keyword]
   ```
   Example output:
   ```
   8991 Amaze com.amaze.filemanager
   9309 رابطة Z com.zsystems.retailers
   ```
2. Note the package name of the app you want to bypass (e.g., com.zsystems.retailers)

## Step 7: Bypass Certificate Pinning

Choose one of these methods:

### Method 1: Using Frida script
1. Create a file named `frida_universal_pinning_bypasser.js` with universal bypassing code
2. Run Frida with the script:
   ```bash
   frida -U -f com.zsystems.retailers -l frida_universal_pinning_bypasser.js
   ```

### Method 2: Using Objection
```bash
objection --gadget com.zsystems.retailers explore -s "android sslpinning disable"
```

## Step 8: Testing

1. With the bypass running, open the target app
2. The app should now communicate through your MITM proxy
3. Check mitmweb interface in your browser (http://127.0.0.1:3000) to see the intercepted traffic

## Troubleshooting

- If Frida server doesn't start, check if you have the correct version for your Android device
- For some apps, you may need more specific certificate pinning bypass scripts
- Make sure your Android device and computer are on the same network
- Verify that the app is actually using certificate pinning

## Resources
- https://www.youtube.com/watch?v=N5CkIjtwCiY
- https://github.com/frida/frida/releases

## Security Notice

This setup should only be used for legitimate security research, debugging, or educational purposes. Always respect applicable laws and terms of service.
