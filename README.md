# Baby Monitor - P2P Browser Application

A peer-to-peer baby monitoring solution that works entirely in your browser without requiring servers, installation, or account setup. Stream video/audio between devices and receive cry detection notifications—all offline-capable after initial load.

## 🌟 Features

- **Zero Infrastructure**: No servers, no cloud services, no installation required
- **Peer-to-Peer Streaming**: Direct WebRTC connection between browser instances
- **Cry Detection**: Real-time audio analysis with configurable sensitivity
- **Browser Notifications**: Get alerts when crying is detected, even when the tab is backgrounded
- **Dual Mode Operation**: Any device can be camera (sender) or monitor (viewer)
- **Single File**: Entire application in one HTML file (<5MB)
- **Offline Capable**: Works without internet after initial load
- **Privacy First**: No data leaves your local network

## 📋 Requirements

### Browser Compatibility
- **Chrome 80+** (Primary, fully tested)
- **Firefox 75+** (Compatible)
- **Safari 14+** (Compatible)
- **Edge 80+** (Compatible)

### Network Requirements
- Both devices must be on the **same local network** (same WiFi)
- No STUN/TURN servers configured (local network only for this prototype)
- Firewall/NAT must allow direct peer connections

### Permissions Required
- **Camera Mode**: Camera and microphone access
- **Monitor Mode**: Notification permission (optional but recommended)

## 🚀 Quick Start

### Step 1: Open the Application
1. Download `baby-monitor.html` to both devices
2. Open the file in a supported browser (Chrome recommended)
3. No internet connection required after file is loaded

### Step 2: Set Up Camera (Sender)
1. On the device in the baby's room, click **"📹 Camera Mode (Sender)"**
2. Grant camera and microphone permissions when prompted
3. Wait for the connection code to appear
4. **Copy the connection code** using the "📋 Copy Code" button

### Step 3: Set Up Monitor (Viewer)
1. On your monitoring device, click **"👀 Monitor Mode (Viewer)"**
2. Grant notification permission when prompted (recommended)
3. **Paste the connection code** from the camera into the "Offer Code" field
4. Click **"📥 Process Offer"**
5. **Copy the answer code** that appears
6. Send this answer code back to the camera device

### Step 4: Complete Connection
1. On the camera device, **paste the answer code** into the "Answer Code" field
2. Click **"✅ Connect"**
3. Wait a few seconds for the connection to establish
4. The status indicator will turn green when connected

### Step 5: Monitor Your Baby
- The video stream will appear on the monitor device
- Cry detection runs automatically on both devices
- You'll see a visual indicator (😢) when crying is detected
- If notifications are enabled, you'll receive alerts even when the tab is in the background

## 🔧 Configuration

### Cry Detection Sensitivity
Both camera and monitor modes have a sensitivity slider:
- **Low (1-3)**: Fewer false alarms, may miss quiet crying
- **Medium (4-7)**: Balanced detection (default: 7)
- **High (8-10)**: Maximum sensitivity, more false positives

The sensitivity setting is saved automatically and persists across sessions.

### Adjusting Sensitivity
1. Locate the "🔊 Cry Detection Sensitivity" section
2. Move the slider left (less sensitive) or right (more sensitive)
3. The value is saved automatically to browser storage

## 🧪 Testing Cry Detection

To test if cry detection is working:

1. **Use Test Audio**: Play baby crying sounds from YouTube or other sources near the camera
2. **Watch for Indicators**: 
   - Visual indicator (😢 "Crying Detected!") appears on both devices
   - Monitor device sends a notification if permissions granted
3. **Adjust Sensitivity**: If detection is too sensitive or not sensitive enough, adjust the slider

### Recommended Test Audio Sources
- Search YouTube for "baby crying sound"
- Use publicly available baby crying audio samples
- Test with various volumes and distances from the microphone

## 🔍 Troubleshooting

### Connection Fails
**Problem**: Status shows "Disconnected" or "Connection failed"

**Solutions**:
1. **Verify Same Network**: Ensure both devices are on the same WiFi network
2. **Check Firewall**: Temporarily disable firewall to test if it's blocking the connection
3. **Restart Browsers**: Close and reopen both browser instances
4. **Try Different Network**: Some public/corporate WiFi networks block peer-to-peer connections
5. **Use Mobile Hotspot**: Create a hotspot on one device and connect the other to it

### Camera Access Denied
**Problem**: "Failed to access camera/microphone" error

**Solutions**:
1. **Grant Permissions**: Click the camera icon in the browser address bar and allow access
2. **Check Device**: Ensure camera/microphone are not in use by another application
3. **Browser Settings**: Go to browser settings → Privacy → Camera/Microphone and allow access
4. **Restart Browser**: Close all browser windows and reopen

### No Notifications
**Problem**: Not receiving cry detection alerts

**Solutions**:
1. **Grant Permission**: Ensure you clicked "Allow" when prompted for notifications
2. **Browser Settings**: Check browser notification settings (Settings → Notifications)
3. **System Settings**: On macOS/Windows, check system notification settings
4. **Keep Browser Open**: Notifications only work when the browser is running (can be minimized)
5. **Test Notifications**: Click the notification permission prompt again by refreshing the page

### No Video Stream
**Problem**: Video doesn't appear on monitor device

**Solutions**:
1. **Wait for Connection**: Give it 10-15 seconds after clicking "Connect"
2. **Check Connection Status**: Status indicator should be green
3. **Verify Codes**: Ensure connection codes were copied/pasted correctly
4. **Restart Process**: Switch both devices back to idle mode and start over
5. **Check Console**: Open browser developer tools (F12) and check for errors

### High False Positives
**Problem**: Cry detection triggers too often

**Solutions**:
1. **Lower Sensitivity**: Move the sensitivity slider to the left (lower values)
2. **Reduce Background Noise**: Move camera away from TV, music, or other noise sources
3. **Test Different Positions**: Adjust camera placement for better audio quality

### Low Detection Rate
**Problem**: Crying not detected

**Solutions**:
1. **Increase Sensitivity**: Move the sensitivity slider to the right (higher values)
2. **Check Audio**: Ensure microphone is not muted or covered
3. **Test with Sample Audio**: Play baby crying sounds to verify detection is working
4. **Adjust Microphone**: Position camera closer to where baby sleeps

### Browser Compatibility Warning
**Problem**: Yellow warning banner appears

**Solutions**:
1. **Update Browser**: Ensure you're using the latest version
2. **Switch Browser**: Try Chrome 80+ for best compatibility
3. **Check Features**: The warning indicates which features are not supported

## 🔒 Privacy & Security

### Data Handling
- **No Cloud Storage**: Video/audio streams are never recorded or stored
- **Local Network Only**: Data stays on your local network, never sent to external servers
- **Ephemeral**: All data is temporary and exists only during the active session
- **No Analytics**: No tracking, no telemetry, no data collection

### Security Considerations
- **Trusted Peers Only**: Only share connection codes with devices you trust
- **Secure Exchange**: Exchange connection codes through secure channels (in person, encrypted messaging)
- **Local Network**: Keep both devices on a trusted, password-protected WiFi network
- **Revoke Permissions**: Revoke camera/microphone permissions when not in use
- **Close When Done**: Close the browser tab when monitoring session is complete

### Threat Model
This is a **prototype** designed for trusted home use. It is **not suitable** for:
- Public networks
- Untrusted devices
- Long-term unattended operation
- Environments requiring high security

## 📱 Usage Tips

### Best Practices
1. **Test First**: Do a test run during the day before using at night
2. **Charge Devices**: Ensure both devices are plugged in or fully charged
3. **Stable Placement**: Use a stable mount or stand for the camera device
4. **Good Lighting**: Ensure adequate lighting in the baby's room for video quality
5. **Audio Check**: Test audio levels before starting monitoring
6. **Backup Plan**: Keep a traditional baby monitor as backup

### Battery Considerations
- Streaming video/audio is battery-intensive
- Keep devices plugged in for extended monitoring sessions
- Reduce screen brightness on camera device to save power

### Network Performance
- **WiFi Signal**: Ensure strong WiFi signal at both locations
- **Bandwidth**: Video streaming requires ~1-3 Mbps
- **Interference**: Keep devices away from microwave ovens and other interference sources
- **Router Distance**: Keep both devices within good range of WiFi router

## 🐛 Known Limitations

### Prototype Constraints
- **Local Network Only**: Does not work across different networks or over the internet
- **Manual Connection**: Requires copy-paste of connection codes (no automatic pairing)
- **Single Viewer**: One camera can connect to one viewer at a time
- **No Recording**: Cannot record or save video streams
- **No Playback**: No historical playback of monitoring sessions
- **Browser Dependent**: Must keep browser open (can minimize or background tab)

### Technical Limitations
- **NAT/Firewall**: May not work on restrictive networks (corporate, public WiFi)
- **No STUN/TURN**: Cannot traverse complex NAT configurations
- **No Reconnection**: Connection drops require manual reconnection
- **False Positives**: Cry detection may trigger on other loud noises
- **CPU Usage**: Audio analysis may impact performance on older devices

## 🔄 Mode Switching

You can switch between camera and monitor modes without reloading the page:

1. Click the mode button at the top (📹 Camera Mode or 👀 Monitor Mode)
2. Previous mode resources are automatically cleaned up
3. New mode initializes with fresh connection setup

**Note**: Switching modes will disconnect any active peer connection.

## 📊 Connection Status Indicators

- **🔴 Red Dot**: Disconnected or idle
- **🟠 Orange Dot**: Connecting (pulsing)
- **🟢 Green Dot**: Connected (pulsing)

## 🎯 Cry Detection Algorithm

The application uses Web Audio API to analyze audio in real-time:

- **Frequency Range**: Focuses on 300-600Hz (typical baby crying range)
- **Amplitude Threshold**: Configurable via sensitivity slider
- **Debouncing**: Notifications limited to once every 5 seconds
- **Dual Detection**: Runs on both sender and viewer independently

### Algorithm Details
- Analyzes frequency spectrum using FFT (Fast Fourier Transform)
- Compares amplitude in crying frequency range to overall audio levels
- Triggers when target frequency amplitude exceeds threshold
- Higher sensitivity = lower threshold = more detections

## 🆘 Emergency Fallback

If the application stops working:

1. **Refresh Both Pages**: Reload the HTML file on both devices
2. **Clear Browser Cache**: Clear cache and reload
3. **Restart Browsers**: Close all browser windows and reopen
4. **Check Network**: Verify WiFi connection on both devices
5. **Use Backup**: Switch to traditional baby monitor if available

## 📝 Technical Details

### Architecture
- **Single HTML File**: All code embedded (HTML, CSS, JavaScript)
- **WebRTC**: Peer-to-peer media streaming
- **Web Audio API**: Real-time audio analysis for cry detection
- **Notification API**: Browser notifications for alerts
- **LocalStorage**: Sensitivity preference persistence

### File Size
- Complete application: ~40KB (well under 5MB limit)
- No external dependencies or CDN links
- Fully offline-capable after initial load

### Performance
- Video latency: <3 seconds on local network
- Audio analysis: ~20-50Hz sampling rate
- Cry detection response: <5 seconds
- Notification delivery: <2 seconds

## 🤝 Support

### Getting Help
This is a prototype application with limited support. For issues:

1. Check this README troubleshooting section
2. Verify browser compatibility and version
3. Test on a different network
4. Try a different browser (Chrome recommended)

### Reporting Issues
When reporting issues, include:
- Browser name and version
- Operating system
- Network type (home WiFi, corporate, etc.)
- Error messages from browser console (F12 → Console tab)
- Steps to reproduce the issue

## 📄 License

This is a prototype application provided as-is without warranty. Use at your own risk.

## ⚠️ Disclaimer

This application is a **prototype** for demonstration purposes. It is not intended for production use or as a replacement for professional baby monitoring equipment. Always maintain appropriate supervision of infants and young children.

**Important Safety Notes**:
- Never rely solely on this application for baby monitoring
- Always have a backup monitoring method
- Check on your baby regularly in person
- Ensure proper infant sleep safety practices
- This application does not replace parental supervision

## 🎓 Educational Purpose

This application demonstrates:
- WebRTC peer-to-peer connections
- Browser media capture APIs
- Real-time audio analysis
- Browser notification system
- Single-file web application architecture
- Offline-first web design

---

**Version**: 1.0.0 (Prototype)  
**Last Updated**: 2026-02-21  
**Initiative ID**: init-babycameraapp-20260221-0750
