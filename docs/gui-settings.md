# GUI Settings

Create custom configuration panels for your scripts in the Distance GUI.

## Overview

The GUI system allows you to add:
- **Custom tabs** with your script name and icon
- **Toggle switches** for on/off settings
- **Sliders** for numeric values
- **Real-time callbacks** when settings change

---

## Creating a Tab

### `Distance.createTab(name, icon)`

Creates a custom tab in the Distance GUI.

**Parameters:**
- `name` - Tab display name (string)
- `icon` - Icon identifier (string)

```javascript
Distance.createTab("My Script", "star");
```

**Available Icons:**
- `star` - Star icon (recommended for features)
- `gear` - Settings icon
- `sword` - Combat icon
- `heart` - Health/life icon
- More icons available (experiment!)

**Best Practice:** Create your tab at the top of your script, before adding settings.

---

## Toggle Settings

### `Distance.addToggle(label, defaultValue, callback)`

Adds a toggle switch (ON/OFF) to your tab.

**Parameters:**
- `label` - Display text (string)
- `defaultValue` - Initial state (boolean: `true` or `false`)
- `callback` - Function called when toggle changes

**Returns:** Nothing (use a variable to track state)

```javascript
var myFeatureEnabled = false;

Distance.addToggle("Enable Feature", false, function(value) {
    myFeatureEnabled = value;
    Distance.chat(value ? "&aFeature ON" : "&cFeature OFF");
});

Distance.on("tick", function() {
    if (myFeatureEnabled) {
        // Feature logic here
    }
});
```

### Example - Multiple Toggles

```javascript
Distance.createTab("Movement", "star");

var speedBoost = false;
var jumpBoost = false;
var particles = true;

Distance.addToggle("Speed Boost", false, function(value) {
    speedBoost = value;
});

Distance.addToggle("Jump Boost", false, function(value) {
    jumpBoost = value;
});

Distance.addToggle("Show Particles", true, function(value) {
    particles = value;
});
```

---

## Slider Settings

### `Distance.addSlider(label, defaultValue, minValue, maxValue, callback)`

Adds a slider for numeric values.

**Parameters:**
- `label` - Display text (string)
- `defaultValue` - Initial value (number)
- `minValue` - Minimum value (number)
- `maxValue` - Maximum value (number)
- `callback` - Function called when slider changes

```javascript
var speedMultiplier = 1.5;

Distance.addSlider("Speed Multiplier", 1.5, 1.0, 3.0, function(value) {
    speedMultiplier = value;
    Distance.chat("Speed set to: " + value.toFixed(2));
});

Distance.on("tick", function() {
    if (Distance.isKeyDown(17)) {
        var mx = Distance.getMotionX() * speedMultiplier;
        var mz = Distance.getMotionZ() * speedMultiplier;
        Distance.setMotion(mx, Distance.getMotionY(), mz);
    }
});
```

### Example - Multiple Sliders

```javascript
Distance.createTab("Boost Config", "star");

var speedMult = 1.5;
var jumpHeight = 0.5;
var particleDensity = 5;

Distance.addSlider("Speed", 1.5, 1.0, 3.0, function(value) {
    speedMult = value;
});

Distance.addSlider("Jump Power", 0.5, 0.42, 1.0, function(value) {
    jumpHeight = value;
});

Distance.addSlider("Particles", 5, 1, 10, function(value) {
    particleDensity = Math.floor(value);
});
```

---

## Complete Example

```javascript
// ===================================
// Enhanced Movement with GUI
// ===================================

Distance.log("Loading Enhanced Movement...");

// Create custom tab
Distance.createTab("Movement Boost", "star");

// Settings variables
var boostEnabled = false;
var boostMultiplier = 1.5;
var boostParticles = true;
var soundEnabled = true;

// Toggle: Enable/Disable
Distance.addToggle("Enable Boost", false, function(value) {
    boostEnabled = value;
    
    if (value) {
        Distance.chat("&aMovement Boost enabled!");
        if (soundEnabled) {
            Distance.playSound("random.levelup", 1.0, 1.0);
        }
    } else {
        Distance.chat("&cMovement Boost disabled!");
    }
});

// Slider: Boost Power
Distance.addSlider("Boost Power", 1.5, 1.0, 3.0, function(value) {
    boostMultiplier = value;
    Distance.chat("&eBoost set to: &fx" + value.toFixed(2));
});

// Toggle: Particles
Distance.addToggle("Show Particles", true, function(value) {
    boostParticles = value;
});

// Toggle: Sounds
Distance.addToggle("Enable Sounds", true, function(value) {
    soundEnabled = value;
});

// Logic: Use settings in tick event
Distance.on("tick", function() {
    if (!boostEnabled || Distance.isPlayerNull()) return;
    
    if (Distance.isKeyDown(17)) { // W key
        var mx = Distance.getMotionX() * boostMultiplier;
        var mz = Distance.getMotionZ() * boostMultiplier;
        Distance.setMotion(mx, Distance.getMotionY(), mz);
        
        if (boostParticles) {
            Distance.spawnParticle("FLAME",
                Distance.getPlayerX(),
                Distance.getPlayerY(),
                Distance.getPlayerZ(),
                0, 0, 0
            );
        }
    }
});

Distance.log("Enhanced Movement loaded!");
```

---

## User Feedback Best Practices

### 1. Provide Clear Feedback

```javascript
Distance.addToggle("Enable Feature", false, function(value) {
    // GOOD - tells user what happened
    if (value) {
        Distance.chat("&aFeature enabled!");
        Distance.playSound("random.levelup", 1.0, 1.0);
    } else {
        Distance.chat("&cFeature disabled!");
    }
});

Distance.addToggle("Enable Feature", false, function(value) {
    // BAD - no feedback
    featureEnabled = value;
});
```

### 2. Show Slider Values

```javascript
Distance.addSlider("Speed", 1.5, 1.0, 3.0, function(value) {
    // GOOD - shows current value
    speedMultiplier = value;
    Distance.chat("Speed set to: " + value.toFixed(2) + "x");
});
```

### 3. Use Colors in Messages

```javascript
Distance.addToggle("Auto Jump", false, function(value) {
    if (value) {
        Distance.chat("&a&lAuto-Jump ENABLED");
    } else {
        Distance.chat("&c&lAuto-Jump DISABLED");
    }
});
```

### 4. Add Sound Effects

```javascript
Distance.addToggle("Feature", false, function(value) {
    if (value) {
        Distance.playSound("random.levelup", 1.0, 1.0);
    } else {
        Distance.playSound("random.pop", 0.5, 0.8);
    }
});
```

---

## Advanced Example: Feature with Safety Limits

```javascript
Distance.createTab("Speed Control", "star");

var speedEnabled = false;
var maxSpeed = 10.0;
var safetyEnabled = true;

Distance.addToggle("Enable Speed Boost", false, function(value) {
    speedEnabled = value;
    Distance.chat(value ? "&aSpeed boost ON" : "&cSpeed boost OFF");
});

Distance.addSlider("Max Speed", 10.0, 5.0, 20.0, function(value) {
    maxSpeed = value;
    
    if (safetyEnabled && value > 15) {
        Distance.chat("&e⚠ Warning: High speeds may cause lag!");
    }
});

Distance.addToggle("Safety Warnings", true, function(value) {
    safetyEnabled = value;
});

var currentSpeed = 0;

Distance.on("tick", function() {
    if (!speedEnabled || Distance.isPlayerNull()) return;
    
    if (Distance.isKeyDown(17)) {
        var mx = Distance.getMotionX();
        var mz = Distance.getMotionZ();
        currentSpeed = Math.sqrt(mx * mx + mz * mz) * 20;
        
        // Apply boost but cap at max speed
        if (currentSpeed < maxSpeed) {
            var boost = 1.1;
            Distance.setMotion(mx * boost, Distance.getMotionY(), mz * boost);
        }
    }
});

Distance.on("render2d", function() {
    if (!speedEnabled) return;
    
    Distance.drawRoundedRect(10, 10, 150, 40, 3, 0x90000000);
    Distance.drawTextShadow("Speed: " + currentSpeed.toFixed(2), 15, 15, 0xFF55FF55);
    Distance.drawTextShadow("Max: " + maxSpeed.toFixed(1), 15, 30, 0xFFFFAA00);
});
```

---

## Organization Tips

### 1. Group Related Settings

```javascript
Distance.createTab("Combat Settings", "sword");

// Toggles first
Distance.addToggle("Enable Combat Mode", false, callback1);
Distance.addToggle("Show Hit Counter", true, callback2);
Distance.addToggle("Play Hit Sounds", true, callback3);

// Then sliders
Distance.addSlider("Hit Volume", 1.0, 0.0, 1.0, callback4);
Distance.addSlider("Particle Density", 5, 1, 10, callback5);
```

### 2. Use Descriptive Labels

```javascript
// GOOD
Distance.addToggle("Enable Auto-Jump", false, callback);
Distance.addSlider("Jump Height Multiplier", 1.0, 0.5, 2.0, callback);

// BAD
Distance.addToggle("Jump", false, callback);
Distance.addSlider("Height", 1.0, 0.5, 2.0, callback);
```

### 3. Set Reasonable Defaults

```javascript
// GOOD - safe defaults
Distance.addToggle("Enable Feature", false, callback); // Off by default
Distance.addSlider("Speed", 1.5, 1.0, 3.0, callback);  // Middle-range default

// BAD - extreme defaults
Distance.addToggle("Enable Feature", true, callback);  // Might surprise users
Distance.addSlider("Speed", 10.0, 1.0, 10.0, callback); // Max by default
```

---

## Common Patterns

### Pattern 1: Master Toggle with Sub-Settings

```javascript
var enabled = false;
var setting1 = 1.5;
var setting2 = true;

Distance.addToggle("Enable Feature", false, function(value) {
    enabled = value;
});

Distance.addSlider("Power", 1.5, 1.0, 3.0, function(value) {
    setting1 = value;
    if (enabled) {
        Distance.chat("Power updated while feature is active");
    }
});

Distance.addToggle("Sub-Feature", true, function(value) {
    setting2 = value;
});
```

### Pattern 2: Preset Modes

```javascript
var mode = "normal";

Distance.addToggle("Normal Mode", true, function(value) {
    if (value) {
        mode = "normal";
        speedMult = 1.5;
        Distance.chat("&eNormal mode");
    }
});

Distance.addToggle("Extreme Mode", false, function(value) {
    if (value) {
        mode = "extreme";
        speedMult = 3.0;
        Distance.chat("&cExtreme mode!");
    }
});
```

---

## Next Steps

- View complete [Examples](examples.md)
- Learn [Best Practices](best-practices.md)
- Explore [Rendering](rendering.md) for visual feedback
