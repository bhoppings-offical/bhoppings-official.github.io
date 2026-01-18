# Example Scripts

Complete, ready-to-use Distance scripts showcasing different features.

## Table of Contents

- [Speed Meter](#speed-meter)
- [Auto Jump](#auto-jump)
- [Particle Trail](#particle-trail)
- [Coordinate Logger](#coordinate-logger)
- [Combat Counter](#combat-counter)
- [FPS Monitor](#fps-monitor)
- [Direction Indicator](#direction-indicator)
- [Enhanced Movement (Complete)](#enhanced-movement-complete)

---

## Speed Meter

Displays your current movement speed on screen.

```javascript
// speed-meter.dscript

Distance.log("Speed Meter loaded!");

Distance.on("render2d", function() {
    if (Distance.isPlayerNull()) return;
    
    var mx = Distance.getMotionX();
    var mz = Distance.getMotionZ();
    var speed = Math.sqrt(mx * mx + mz * mz);
    var bps = speed * 20; // Blocks per second
    
    var screenWidth = Distance.getScreenWidth();
    
    // Background
    Distance.drawRoundedRect(screenWidth - 130, 10, 120, 40, 3, 0x90000000);
    
    // Speed text
    var speedText = "Speed: " + bps.toFixed(2) + " b/s";
    Distance.drawTextShadow(speedText, screenWidth - 125, 15, 0x55FF55);
    
    // Color based on speed
    var color = 0xFFFFFF;
    if (bps > 10) color = 0xFF5555;      // Red
    else if (bps > 5) color = 0xFFFF55;  // Yellow
    else color = 0x55FF55;                // Green
    
    var tickSpeed = (speed * 20).toFixed(2);
    Distance.drawTextShadow(tickSpeed + " ticks", screenWidth - 125, 30, color);
});
```

---

## Auto Jump

Automatically jumps when moving forward.

```javascript
// auto-jump.dscript

Distance.createTab("Auto Jump", "star");

var enabled = false;

Distance.addToggle("Enable Auto-Jump", false, function(value) {
    enabled = value;
    Distance.chat(value ? "&aAuto-Jump enabled!" : "&cAuto-Jump disabled!");
    if (value) {
        Distance.playSound("random.levelup", 1.0, 1.0);
    }
});

Distance.on("tick", function() {
    if (!enabled || Distance.isPlayerNull()) return;
    
    // Jump when on ground and moving forward
    if (Distance.isOnGround() && Distance.isKeyDown(17)) { // W key
        Distance.setMotion(
            Distance.getMotionX(),
            0.42, // Jump velocity
            Distance.getMotionZ()
        );
    }
});
```

---

## Particle Trail

Leaves a trail of particles behind you.

```javascript
// particle-trail.dscript

Distance.createTab("Particle Trail", "star");

var enabled = false;
var particleType = "FLAME";
var density = 3;

Distance.addToggle("Enable Trail", false, function(value) {
    enabled = value;
    Distance.chat(value ? "&aParticle trail ON" : "&cParticle trail OFF");
});

Distance.addSlider("Density", 3, 1, 10, function(value) {
    density = Math.floor(value);
});

var tickCounter = 0;

Distance.on("tick", function() {
    if (!enabled || Distance.isPlayerNull()) return;
    
    tickCounter++;
    if (tickCounter % (11 - density) !== 0) return; // Control spawn rate
    
    var x = Distance.getPlayerX();
    var y = Distance.getPlayerY();
    var z = Distance.getPlayerZ();
    
    // Random offset
    var offsetX = (Math.random() - 0.5) * 0.5;
    var offsetZ = (Math.random() - 0.5) * 0.5;
    
    Distance.spawnParticle(particleType, x + offsetX, y, z + offsetZ, 0, 0, 0);
});
```

---

## Coordinate Logger

Saves waypoints when you press a key.

```javascript
// coord-logger.dscript

Distance.createTab("Coord Logger", "star");

var waypoints = [];
var lastKeyState = false;

Distance.addToggle("Press L to Log Position", false, function(value) {
    // Informational toggle
});

Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    var keyDown = Distance.isKeyDown(38); // L key
    
    if (keyDown && !lastKeyState) { // Key just pressed
        var x = Math.floor(Distance.getPlayerX());
        var y = Math.floor(Distance.getPlayerY());
        var z = Math.floor(Distance.getPlayerZ());
        
        waypoints.push({x: x, y: y, z: z});
        
        Distance.chat("&aWaypoint saved: " + x + ", " + y + ", " + z);
        Distance.playSound("random.orb", 1.0, 1.0);
        
        Distance.log("Total waypoints: " + waypoints.length);
    }
    
    lastKeyState = keyDown;
});

Distance.on("render2d", function() {
    if (waypoints.length === 0) return;
    
    var y = 100;
    Distance.drawTextShadow("Waypoints:", 10, y, 0xFFAA00);
    y += 15;
    
    // Show last 5 waypoints
    for (var i = 0; i < Math.min(waypoints.length, 5); i++) {
        var wp = waypoints[waypoints.length - 1 - i];
        var text = (i + 1) + ": " + wp.x + ", " + wp.y + ", " + wp.z;
        Distance.drawTextShadow(text, 10, y, 0xFFFFFF);
        y += 12;
    }
});
```

---

## Combat Counter

Tracks your attacks with statistics.

```javascript
// combat-counter.dscript

Distance.createTab("Combat Stats", "sword");

var hitCount = 0;
var sessionStart = Date.now();

Distance.on("attack", function(target) {
    hitCount++;
    
    Distance.chat("&e⚔ Hit #" + hitCount + " on " + target.getName());
    Distance.playSound("random.successful_hit", 0.5, 1.5);
    
    // Spawn particles at target
    if (target.posX !== undefined) {
        Distance.spawnParticle("CRIT", 
            target.posX, 
            target.posY + 1, 
            target.posZ, 
            0, 0, 0
        );
    }
});

Distance.on("render2d", function() {
    var elapsed = (Date.now() - sessionStart) / 1000; // seconds
    var hitsPerMinute = (hitCount / elapsed) * 60;
    
    Distance.drawRoundedRect(10, 200, 150, 50, 3, 0x90000000);
    Distance.drawTextShadow("Combat Stats", 15, 205, 0xFF5555);
    Distance.drawTextShadow("Hits: " + hitCount, 15, 220, 0xFFFFFF);
    Distance.drawTextShadow("APM: " + hitsPerMinute.toFixed(1), 15, 235, 0xFFFF55);
});
```

---

## FPS Monitor

Monitors FPS with low-FPS warnings.

```javascript
// fps-monitor.dscript

Distance.createTab("FPS Monitor", "gear");

var warnEnabled = true;
var warnThreshold = 30;
var lastWarnTime = 0;

Distance.addToggle("Low FPS Warning", true, function(value) {
    warnEnabled = value;
});

Distance.addSlider("Warning Threshold", 30, 10, 60, function(value) {
    warnThreshold = Math.floor(value);
});

Distance.on("render2d", function() {
    var fps = Distance.getFPS();
    
    // Color based on FPS
    var color = 0x55FF55; // Green
    if (fps < 30) color = 0xFF5555;      // Red
    else if (fps < 60) color = 0xFFFF55; // Yellow
    
    var screenWidth = Distance.getScreenWidth();
    Distance.drawRoundedRect(screenWidth - 80, 10, 70, 25, 3, 0x90000000);
    Distance.drawTextShadow("FPS: " + fps, screenWidth - 75, 15, color);
    
    // Warning system
    if (warnEnabled && fps < warnThreshold) {
        var now = Date.now();
        if (now - lastWarnTime > 5000) { // Warn every 5 seconds
            Distance.chat("&c[!] Low FPS detected: " + fps);
            Distance.playSound("note.bass", 1.0, 0.5);
            lastWarnTime = now;
        }
    }
});
```

---

## Direction Indicator

Shows cardinal direction you're facing.

```javascript
// direction-indicator.dscript

Distance.on("render2d", function() {
    if (Distance.isPlayerNull()) return;
    
    var yaw = Distance.getYaw();
    var normalizedYaw = ((yaw % 360) + 360) % 360;
    
    var direction = "";
    
    if (normalizedYaw >= 337.5 || normalizedYaw < 22.5) direction = "South";
    else if (normalizedYaw >= 22.5 && normalizedYaw < 67.5) direction = "Southwest";
    else if (normalizedYaw >= 67.5 && normalizedYaw < 112.5) direction = "West";
    else if (normalizedYaw >= 112.5 && normalizedYaw < 157.5) direction = "Northwest";
    else if (normalizedYaw >= 157.5 && normalizedYaw < 202.5) direction = "North";
    else if (normalizedYaw >= 202.5 && normalizedYaw < 247.5) direction = "Northeast";
    else if (normalizedYaw >= 247.5 && normalizedYaw < 292.5) direction = "East";
    else direction = "Southeast";
    
    var screenWidth = Distance.getScreenWidth();
    var screenHeight = Distance.getScreenHeight();
    
    var text = direction + " (" + Math.floor(normalizedYaw) + "°)";
    var textWidth = Distance.getStringWidth(text);
    
    Distance.drawRoundedRect(
        (screenWidth - textWidth) / 2 - 5,
        screenHeight - 40,
        textWidth + 10,
        25,
        3,
        0x90000000
    );
    
    Distance.drawTextShadow(text, (screenWidth - textWidth) / 2, screenHeight - 35, 0x55FFFF);
});
```

---

## Enhanced Movement (Complete)

A full-featured movement enhancement script with GUI.

```javascript
// enhanced-movement.dscript

Distance.log("Enhanced Movement script loading...");

// Create GUI tab
Distance.createTab("Enhanced Movement", "star");

// Settings
var speedBoostEnabled = false;
var speedMultiplier = 1.5;
var jumpBoostEnabled = false;
var jumpHeight = 0.5;
var particlesEnabled = true;
var soundEnabled = true;

// GUI Controls
Distance.addToggle("Speed Boost", false, function(value) {
    speedBoostEnabled = value;
    Distance.chat(value ? "&aSpeed boost enabled!" : "&cSpeed boost disabled!");
    if (value && soundEnabled) {
        Distance.playSound("random.levelup", 1.0, 1.0);
    }
});

Distance.addSlider("Speed Multiplier", 1.5, 1.0, 3.0, function(value) {
    speedMultiplier = value;
});

Distance.addToggle("Jump Boost", false, function(value) {
    jumpBoostEnabled = value;
    Distance.chat(value ? "&aJump boost enabled!" : "&cJump boost disabled!");
});

Distance.addSlider("Jump Height", 0.5, 0.42, 1.0, function(value) {
    jumpHeight = value;
});

Distance.addToggle("Particles", true, function(value) {
    particlesEnabled = value;
});

Distance.addToggle("Sounds", true, function(value) {
    soundEnabled = value;
});

// Cached values
var currentSpeed = 0;
var tickCounter = 0;

// Tick handler
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    tickCounter++;
    
    // Speed boost
    if (speedBoostEnabled && Distance.isKeyDown(17)) { // W key
        var mx = Distance.getMotionX() * speedMultiplier;
        var mz = Distance.getMotionZ() * speedMultiplier;
        Distance.setMotion(mx, Distance.getMotionY(), mz);
        
        // Particles (every 3 ticks)
        if (particlesEnabled && tickCounter % 3 === 0) {
            Distance.spawnParticle("FLAME",
                Distance.getPlayerX(),
                Distance.getPlayerY(),
                Distance.getPlayerZ(),
                0, 0, 0
            );
        }
    }
    
    // Jump boost
    if (jumpBoostEnabled && Distance.isOnGround() && Distance.isKeyDown(57)) { // Space
        Distance.setMotion(
            Distance.getMotionX(),
            jumpHeight,
            Distance.getMotionZ()
        );
        
        if (soundEnabled) {
            Distance.playSound("random.orb", 0.5, 1.5);
        }
    }
    
    // Update speed cache
    var mx = Distance.getMotionX();
    var mz = Distance.getMotionZ();
    currentSpeed = Math.sqrt(mx * mx + mz * mz) * 20;
});

// Render handler
Distance.on("render2d", function() {
    if (Distance.isPlayerNull()) return;
    
    var screenWidth = Distance.getScreenWidth();
    
    // Draw status panel
    Distance.drawRoundedRect(screenWidth - 150, 10, 140, 70, 3, 0x90000000);
    
    Distance.drawTextShadow("Enhanced Movement", screenWidth - 145, 15, 0xFFAA00);
    
    var statusColor = (speedBoostEnabled || jumpBoostEnabled) ? 0x55FF55 : 0xFF5555;
    var statusText = (speedBoostEnabled || jumpBoostEnabled) ? "ACTIVE" : "INACTIVE";
    Distance.drawTextShadow("Status: " + statusText, screenWidth - 145, 30, statusColor);
    
    Distance.drawTextShadow("Speed: " + currentSpeed.toFixed(2) + " b/s", screenWidth - 145, 45, 0xFFFFFF);
    
    if (speedBoostEnabled) {
        Distance.drawTextShadow("Boost: x" + speedMultiplier.toFixed(1), screenWidth - 145, 60, 0x55FFFF);
    }
});

Distance.log("Enhanced Movement script loaded successfully!");
Distance.chat("&aEnhanced Movement loaded! Open GUI to configure.");
```

---

## Using These Examples

1. Copy the code into a `.dscript` file
2. Place in `config/DistanceMod/scripts/`
3. Enable in the Distance GUI
4. Customize settings as needed

Each example demonstrates different features:
- **Speed Meter**: Rendering and calculations
- **Auto Jump**: Input handling and motion
- **Particle Trail**: Particles and GUI settings
- **Coord Logger**: Key detection and data storage
- **Combat Counter**: Event handling and statistics
- **FPS Monitor**: Monitoring and warnings
- **Direction Indicator**: Math and compass logic
- **Enhanced Movement**: Complete feature with all systems

---

## Next Steps

- Modify these examples for your needs
- Combine features from multiple examples
- Read [Best Practices](best-practices.md) for optimization
- Check the [API Reference](api-reference.md) for more functions
