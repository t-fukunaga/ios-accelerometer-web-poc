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
- Lines 1-721: HTML structure and CSS styles
- Lines 873-1267: JavaScript implementation
- No external dependencies in production

### Core Components

1. **Accelerometer Integration**
   - Uses `DeviceMotionEvent` API with iOS 13+ permission handling
   - Updates at 100ms intervals for sensor data
   - Requires HTTPS connection on iOS

2. **Two Interactive Modes**
   - **Tilt Sensor**: Real-time 3D device visualization with axis indicators
   - **Dowsing (Pendulum)**: Advanced physics-based pendulum simulation with chart segments

3. **Physics Engine (Pendulum Mode)**
   - **Damped Oscillation Model**: F = -kx - cv (spring-damper system)
   - **Gravity Compensation**: Low-pass filter (LPF_ALPHA = 0.8) separates gravity from motion
   - **3-Axis Motion**: X/Y for main swing, Z for twist rotation
   - **60fps Physics Updates**: Independent from sensor updates for smooth animation
   - **Stillness Detection**: Converges to center when device is stationary

4. **Key Functions**
   - `startSensor()`: Handles iOS permission request and initializes accelerometer
   - `updatePendulumPhysics()`: 60fps physics simulation loop
   - `updatePendulum()`: Applies external forces from accelerometer
   - `updateChartHighlight()`: Detects which chart segment the pendulum points to
   - `calculateAccelerationChange()`: Tracks motion history for dowsing detection

### Physics System

#### Constants
```javascript
const SPRING_CONSTANT = 0.02;      // Restoring force coefficient
const BASE_DAMPING = 0.98;         // Base damping coefficient
const BASE_MAX_ANGLE = 60;         // Base maximum swing angle
const LPF_ALPHA = 0.8;             // Low-pass filter coefficient
const CENTER_PULL_FORCE = 0.005;   // Force pulling to center when still
const MOVEMENT_THRESHOLD = 0.05;   // Angular velocity threshold for stillness
const STILLNESS_THRESHOLD = 15;    // Frames before considered still
```

#### Gravity Separation
The system uses a low-pass filter to separate gravity from linear acceleration:
```javascript
gravity = LPF_ALPHA * gravity + (1 - LPF_ALPHA) * acceleration;
linearAcceleration = acceleration - gravity;
```

### Sensitivity System
- Global `sensitivity` variable controls reaction strength (0.5x to 25x)
- Dynamically adjusts:
  - Damping coefficient (higher sensitivity = less damping)
  - Maximum angle (higher sensitivity = larger swings)
  - Spring constant (higher sensitivity = weaker restoring force)
  - Force scale (non-linear adjustment for natural feel)

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
- Dynamic shadow that changes scale and opacity based on distance
- Responsive design capped at 500px width for mobile optimization

## Testing on iOS Devices

1. Ensure HTTPS connection (GitHub Pages or ngrok)
2. Open in Safari (not Chrome or other browsers)
3. Grant motion sensor permission when prompted
4. Device must be physically moved to generate sensor data
5. Place device flat on table to see gravity compensation and center convergence

## Common Modifications

### Adjusting Physics Parameters
- `SPRING_CONSTANT`: Controls how strongly pendulum returns to center
- `BASE_DAMPING`: Controls how quickly motion stops (air resistance)
- `BASE_MAX_ANGLE`: Maximum swing angle before clamping
- `CENTER_PULL_FORCE`: How strongly pendulum pulls to center when still

### Modifying Visual Design
- All styles are in the `<style>` section (lines 8-721)
- 3D effects use `transform-style: preserve-3d`
- Animations use CSS transitions for performance
- Shadow calculations in `updatePendulumDisplay()`

### Changing Sensitivity Behavior
- Look for `sensitivity` usage in physics calculations
- Adjust non-linear scaling in `updatePendulum()` force calculation
- Modify dynamic parameter adjustments in `updatePendulumPhysics()`