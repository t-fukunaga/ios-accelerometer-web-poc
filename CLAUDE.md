# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a web-based accelerometer demo specifically designed for iOS devices. The entire application is contained in a single `index.html` file with embedded CSS and JavaScript, making it simple to deploy and maintain.

## Key Commands

### Development
```bash
# Start development server (live-reload on port 8080)
npm run dev

# For iOS testing, use ngrok to get HTTPS URL
ngrok http 8080
```

### Deployment
The app is deployed to GitHub Pages at https://t-fukunaga.github.io/ios-accelerometer-web-poc/
No build process is required - just push changes to the main branch.

## Architecture

### Single-File Structure
All functionality is contained in `index.html`:
- Lines 1-706: HTML structure and CSS styles
- Lines 708-1180: JavaScript implementation
- No external dependencies in production

### Core Components

1. **Accelerometer Integration**
   - Uses `DeviceMotionEvent` API with iOS 13+ permission handling
   - Updates at 100ms intervals for smooth animation
   - Requires HTTPS connection on iOS

2. **Two Interactive Modes**
   - **Tilt Sensor**: Real-time 3D device visualization with axis indicators
   - **Dowsing (Pendulum)**: Physics-based pendulum simulation with chart segments

3. **Key Functions**
   - `startSensor()`: Handles iOS permission request and initializes accelerometer
   - `updatePendulum()`: Physics simulation for pendulum movement
   - `updateChartHighlight()`: Detects which chart segment the pendulum points to
   - `calculateAccelerationChange()`: Tracks motion history for dowsing detection

### Sensitivity System
- Global `sensitivity` variable controls reaction strength (0.5x to 25x)
- Separate `tiltSensitivity` for tilt mode (half of main sensitivity)
- Affects both visual feedback and detection thresholds

## iOS-Specific Considerations

1. **Permission Handling**
   ```javascript
   if (window.DeviceMotionEvent && typeof window.DeviceMotionEvent.requestPermission === 'function') {
       const permission = await window.DeviceMotionEvent.requestPermission();
   }
   ```

2. **HTTPS Requirement**
   - iOS 13+ requires HTTPS for accelerometer access
   - Use ngrok for local development testing

3. **Safari Compatibility**
   - No modern JavaScript features that require transpilation
   - CSS uses vendor prefixes where needed
   - Touch-friendly interface design

## Visual Design System

The app uses a purple gradient theme (#667eea to #764ba2) with:
- 3D perspective effects using CSS transforms
- Pendulum viewed at 60-degree angle for natural appearance
- Amethyst-colored pendulum weight with realistic shadows
- Responsive design capped at 500px width for mobile optimization

## Testing on iOS Devices

1. Ensure HTTPS connection (GitHub Pages or ngrok)
2. Open in Safari (not Chrome or other browsers)
3. Grant motion sensor permission when prompted
4. Device must be physically moved to generate sensor data

## Common Modifications

When modifying the pendulum physics:
- Adjust `maxSwing` for maximum pendulum angle
- Modify `chainLength` for shadow calculations
- Change `amplification` formula for different sensitivity curves

When updating visual design:
- All styles are in the `<style>` section (lines 7-706)
- 3D effects use `transform-style: preserve-3d`
- Animations use CSS transitions for performance