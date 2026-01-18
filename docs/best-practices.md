# Best Practices

Guidelines for writing efficient, maintainable Distance scripts.

## Table of Contents

- [Safety & Null Checks](#safety--null-checks)
- [Performance Optimization](#performance-optimization)
- [Code Organization](#code-organization)
- [User Experience](#user-experience)
- [Debugging](#debugging)
- [Common Mistakes](#common-mistakes)

---

## Safety & Null Checks

### Always Check for Null

**Rule:** Check if player/world exists before accessing data.

```javascript
// ✅ GOOD
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    var health = Distance.getHealth();
    // Safe to use player data
});

// ❌ BAD
Distance.on("tick", function() {
    var health = Distance.getHealth(); // Will crash if player is null
});
```

### Check World State

```javascript
// ✅ GOOD
Distance.on("tick", function() {
    if (Distance.isWorldNull()) return;
    if (Distance.isPlayerNull()) return;
    
    // Both world and player exist
});
```

---

## Performance Optimization

### 1. Cache Calculations

Compute values in `tick`, display in `render2d`:

```javascript
// ✅ GOOD - compute once per tick
var cachedSpeed = 0;

Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    var mx = Distance.getMotionX();
    var mz = Distance.getMotionZ();
    cachedSpeed = Math.sqrt(mx * mx + mz * mz);
});

Distance.on("render2d", function() {
    Distance.drawText("Speed: " + cachedSpeed.toFixed(2), 10, 10, 0xFFFFFF);
});

// ❌ BAD - computes every frame
Distance.on("render2d", function() {
    if (Distance.isPlayerNull()) return;
    var mx = Distance.getMotionX();
    var mz = Distance.getMotionZ();
    var speed = Math.sqrt(mx * mx + mz * mz);
    Distance.drawText("Speed: " + speed.toFixed(2), 10, 10, 0xFFFFFF);
});
```

### 2. Limit Particle Spawning

Spawn particles sparingly to avoid lag:

```javascript
// ✅ GOOD - spawns every 3 ticks
var tickCounter = 0;

Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    tickCounter++;
    if (tickCounter % 3 !== 0) return;
    
    Distance.spawnParticle("FLAME", x, y, z, 0, 0, 0);
});

// ❌ BAD - spawns 20 times per second
Distance.on("tick", function() {
    Distance.spawnParticle("FLAME", x, y, z, 0, 0, 0);
});
```

### 3. Minimize String Operations

```javascript
// ✅ GOOD - formats only when needed
var speed = 0;

Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    var mx = Distance.getMotionX();
    var mz = Distance.getMotionZ();
    speed = Math.sqrt(mx * mx + mz * mz);
});

Distance.on("render2d", function() {
    Distance.drawText("Speed: " + speed.toFixed(2), 10, 10, 0xFFFFFF);
});

// ❌ BAD - creates strings every tick
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    var mx = Distance.getMotionX();
    var mz = Distance.getMotionZ();
    var speed = Math.sqrt(mx * mx + mz * mz);
    var text = "Speed: " + speed.toFixed(2); // Unnecessary
});
```

### 4. Use Conditional Rendering

Only draw what's visible or enabled:

```javascript
// ✅ GOOD
var showDebug = false;

Distance.on("render2d", function() {
    if (!showDebug) return; // Skip rendering entirely
    
    // Debug rendering here
});

// ❌ BAD
var showDebug = false;

Distance.on("render2d", function() {
    if (showDebug) {
        // Debug rendering
    }
    // Function still executes every frame
});
```

---

## Code Organization

### 1. Use Descriptive Variable Names

```javascript
// ✅ GOOD
var speedBoostEnabled = false;
var speedMultiplier = 1.5;
var particleTrailDensity = 5;

// ❌ BAD
var e = false;
var m = 1.5;
var d = 5;
```

### 2. Define Constants

```javascript
// ✅ GOOD - define at top of script
var KEY_W = 17;
var KEY_SPACE = 57;
var KEY_SHIFT = 42;
var KEY_L = 38;

Distance.on("tick", function() {
    if (Distance.isKeyDown(KEY_W)) {
        // Much more readable
    }
});

// ❌ BAD - magic numbers
Distance.on("tick", function() {
    if (Distance.isKeyDown(17)) {
        // What key is 17?
    }
});
```

### 3. Group Related Code

```javascript
// ✅ GOOD - organized sections
Distance.log("Script loading...");

// ===== Configuration =====
Distance.createTab("My Script", "star");

var enabled = false;
var multiplier = 1.5;

// ===== GUI Settings =====
Distance.addToggle("Enable", false, function(value) {
    enabled = value;
});

Distance.addSlider("Multiplier", 1.5, 1.0, 3.0, function(value) {
    multiplier = value;
});

// ===== Event Handlers =====
Distance.on("tick", function() {
    // Logic here
});

Distance.on("render2d", function() {
    // Rendering here
});
```

### 4. Add Comments

```javascript
// ✅ GOOD - explains intent
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    // Calculate horizontal speed (ignore Y velocity)
    var mx = Distance.getMotionX();
    var mz = Distance.getMotionZ();
    var speed = Math.sqrt(mx * mx + mz * mz);
    
    // Convert from units/tick to blocks/second
    var blocksPerSecond = speed * 20;
});
```

---

## User Experience

### 1. Provide Clear Feedback

```javascript
// ✅ GOOD - clear feedback
Distance.addToggle("Enable Feature", false, function(value) {
    if (value) {
        Distance.chat("&a&lFeature ENABLED");
        Distance.playSound("random.levelup", 1.0, 1.0);
    } else {
        Distance.chat("&c&lFeature DISABLED");
    }
});

// ❌ BAD - no feedback
Distance.addToggle("Enable Feature", false, function(value) {
    enabled = value;
});
```

### 2. Limit Chat Spam

```javascript
// ✅ GOOD - cooldown on messages
var lastChatTime = 0;
var chatCooldown = 1000; // 1 second

Distance.on("tick", function() {
    var now = Date.now();
    if (condition && now - lastChatTime > chatCooldown) {
        Distance.chat("Message");
        lastChatTime = now;
    }
});

// ❌ BAD - spams chat
Distance.on("tick", function() {
    if (condition) {
        Distance.chat("Message"); // Sent 20 times per second!
    }
});
```

### 3. Use Appropriate Colors

```javascript
// ✅ GOOD - meaningful colors
Distance.chat("&aSuccess message");
Distance.chat("&cError message");
Distance.chat("&eWarning message");
Distance.chat("&bInfo message");

// ❌ BAD - random colors
Distance.chat("&5This doesn't mean anything");
```

### 4. Set Reasonable Defaults

```javascript
// ✅ GOOD - safe defaults
Distance.addToggle("Enable Feature", false, callback);
Distance.addSlider("Speed", 1.5, 1.0, 3.0, callback);

// ❌ BAD - extreme defaults
Distance.addToggle("Enable Feature", true, callback); // On by default might surprise users
Distance.addSlider("Speed", 10.0, 1.0, 10.0, callback); // Max value by default
```

---

## Debugging

### 1. Use Console Logging

```javascript
Distance.log("Script started");

Distance.on("tick", function() {
    Distance.log("Tick event fired");
    Distance.log("Player null: " + Distance.isPlayerNull());
});
```

### 2. Display Debug Info

```javascript
var debugEnabled = true;

Distance.on("render2d", function() {
    if (!debugEnabled) return;
    
    var y = 10;
    Distance.drawTextShadow("=== DEBUG ===", 10, y, 0xFFFF00);
    y += 15;
    
    Distance.drawTextShadow("Player: " + !Distance.isPlayerNull(), 10, y, 0xFFFFFF);
    y += 12;
    
    Distance.drawTextShadow("World: " + !Distance.isWorldNull(), 10, y, 0xFFFFFF);
    y += 12;
    
    if (!Distance.isPlayerNull()) {
        Distance.drawTextShadow("Health: " + Distance.getHealth(), 10, y, 0xFFFFFF);
    }
});
```

### 3. Error Handling

```javascript
Distance.on("tick", function() {
    try {
        if (Distance.isPlayerNull()) return;
        
        // Your code here
        
    } catch (e) {
        Distance.error("Script error: " + e.message);
        Distance.log("Full error: " + e);
    }
});
```

### 4. Test Edge Cases

```javascript
// Test with:
// - Player in menu
// - Player in loading screen
// - World not loaded
// - Different game modes
// - Various speeds/positions
```

---

## Common Mistakes

### Mistake 1: Not Checking for Null

```javascript
// ❌ WRONG - will crash
Distance.on("tick", function() {
    var health = Distance.getHealth();
});

// ✅ CORRECT
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    var health = Distance.getHealth();
});
```

### Mistake 2: Creating Strings in Tick

```javascript
// ❌ WRONG - unnecessary string operations
Distance.on("tick", function() {
    var text = "Speed: " + speed; // Why create this if not displaying?
});

// ✅ CORRECT - create only when displaying
Distance.on("render2d", function() {
    Distance.drawText("Speed: " + speed, 10, 10, 0xFFFFFF);
});
```

### Mistake 3: Not Using Variables for Settings

```javascript
// ❌ WRONG - can't be changed
Distance.on("tick", function() {
    if (Distance.isKeyDown(17)) {
        Distance.setMotion(mx * 1.5, my, mz); // Hardcoded 1.5
    }
});

// ✅ CORRECT - configurable
var speedMultiplier = 1.5;

Distance.addSlider("Speed", 1.5, 1.0, 3.0, function(value) {
    speedMultiplier = value;
});

Distance.on("tick", function() {
    if (Distance.isKeyDown(17)) {
        Distance.setMotion(mx * speedMultiplier, my, mz);
    }
});
```

### Mistake 4: Spawning Too Many Particles

```javascript
// ❌ WRONG - lags the game
Distance.on("tick", function() {
    for (var i = 0; i < 10; i++) {
        Distance.spawnParticle("FLAME", x, y, z, 0, 0, 0);
    }
    // 10 particles x 20 ticks = 200 particles/second!
});

// ✅ CORRECT - controlled spawning
var tickCounter = 0;

Distance.on("tick", function() {
    tickCounter++;
    if (tickCounter % 5 !== 0) return; // Every 5 ticks
    
    Distance.spawnParticle("FLAME", x, y, z, 0, 0, 0);
    // 4 particles per second
});
```

### Mistake 5: Not Registering Events at Load

```javascript
// ❌ WRONG - event inside another event
Distance.on("tick", function() {
    Distance.on("render2d", function() {
        // This registers a NEW handler every tick!
    });
});

// ✅ CORRECT - register once at load
Distance.on("tick", function() {
    // Tick logic
});

Distance.on("render2d", function() {
    // Render logic
});
```

---

## Performance Checklist

Before releasing your script, check:

- [ ] All player/world accesses have null checks
- [ ] Heavy calculations are cached
- [ ] Particle spawning is limited
- [ ] String formatting only happens when displaying
- [ ] Event handlers are registered once
- [ ] GUI settings use variables
- [ ] No unnecessary logging in production
- [ ] Tested in various scenarios (menu, loading, gameplay)

---

## Script Template

Use this template for new scripts:

```javascript
// script-name.dscript
// Description: What this script does
// Author: Your name

Distance.log("Loading [Script Name]...");

// ===== Configuration =====
var FEATURE_NAME = "My Feature";
var KEY_TOGGLE = 38; // L key

// ===== Settings =====
Distance.createTab(FEATURE_NAME, "star");

var enabled = false;
var setting1 = 1.0;

Distance.addToggle("Enable " + FEATURE_NAME, false, function(value) {
    enabled = value;
    Distance.chat(value ? "&aEnabled" : "&cDisabled");
});

Distance.addSlider("Setting", 1.0, 0.5, 2.0, function(value) {
    setting1 = value;
});

// ===== State Variables =====
var cachedValue = 0;

// ===== Event Handlers =====
Distance.on("tick", function() {
    if (!enabled || Distance.isPlayerNull()) return;
    
    try {
        // Your logic here
        
    } catch (e) {
        Distance.error("Error: " + e.message);
    }
});

Distance.on("render2d", function() {
    if (!enabled || Distance.isPlayerNull()) return;
    
    // Your rendering here
});

Distance.log("[Script Name] loaded successfully!");
Distance.chat("&a" + FEATURE_NAME + " loaded! Toggle with GUI.");
```

---

## Next Steps

- Review the [API Reference](api-reference.md)
- Study complete [Examples](examples.md)
- Join the community for script sharing
