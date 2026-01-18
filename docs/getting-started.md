# Getting Started

Welcome to Distance scripting! This guide will help you create your first script.

## Prerequisites

- Minecraft with Distance Mod installed
- Basic JavaScript knowledge (helpful but not required)

## File Setup

### Location
All scripts must be placed in:
```
config/DistanceMod/scripts/
```

### File Extension
Scripts must end with `.dscript`:
```
MyScript.dscript ✓
MyScript.js ✗
```

## Your First Script

Create a file called `MyFirstScript.dscript`:

```javascript
// MyFirstScript.dscript

// The Distance API is automatically available
Distance.log("Script loaded!");

// Send a colored chat message
Distance.chat("&aHello from my script!");

// Register a tick event (runs 20 times per second)
Distance.on("tick", function(event) {
    // Your code here
});

// Register a render event
Distance.on("render2d", function(resolution) {
    // Draw on screen here
});
```

## Loading Your Script

1. Place your `.dscript` file in `config/DistanceMod/scripts/`
2. Open the Distance GUI in-game
3. Navigate to the **Scripts** tab
4. Find your script in the list
5. Toggle it **ON** to enable it
6. Scripts automatically reload when toggled

## Core Concepts

### The Distance Object

Every script has access to a global `Distance` object that provides all functionality:

```javascript
Distance.log("Logging to console");
Distance.chat("Message to player");
Distance.error("Error message");
```

### Script Name

Access your script's filename:

```javascript
Distance.log("My script name is: " + Distance.scriptName);
```

### Minecraft Client Access

The `mc` variable is automatically available:

```javascript
var player = mc.thePlayer;
var world = mc.theWorld;

if (player != null) {
    Distance.log("Player: " + player.getName());
}
```

## Simple Examples

### Example 1: Position Logger

```javascript
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    var x = Distance.getPlayerX();
    var y = Distance.getPlayerY();
    var z = Distance.getPlayerZ();
    
    Distance.log("Position: " + x + ", " + y + ", " + z);
});
```

### Example 2: Health Display

```javascript
Distance.on("render2d", function() {
    if (Distance.isPlayerNull()) return;
    
    var health = Distance.getHealth();
    var maxHealth = Distance.getMaxHealth();
    
    Distance.drawTextShadow(
        "Health: " + health + "/" + maxHealth,
        10, 10, 0xFF5555
    );
});
```

### Example 3: Speed Boost

```javascript
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    if (Distance.isKeyDown(17)) { // W key
        var mx = Distance.getMotionX() * 1.5;
        var mz = Distance.getMotionZ() * 1.5;
        Distance.setMotion(mx, Distance.getMotionY(), mz);
    }
});
```

## Color Codes

Use `&` followed by a code in chat messages:

```javascript
Distance.chat("&aGreen text");
Distance.chat("&cRed &eYellow &bBlue");
Distance.chat("&l&nBold and underlined");
```

**Available codes:**
- `&0-9` - Colors (0=black, 9=blue)
- `&a-f` - More colors (a=green, c=red, e=yellow)
- `&l` - Bold
- `&n` - Underline
- `&r` - Reset

## Safety Checks

Always check if player/world exists before accessing data:

```javascript
Distance.on("tick", function() {
    // Check if player exists
    if (Distance.isPlayerNull()) {
        return; // Exit early
    }
    
    // Safe to use player data now
    var health = Distance.getHealth();
});
```

## Debugging

### Console Logging

```javascript
Distance.log("Debug info");
Distance.log("Variable value: " + myVariable);
```

View logs in:
- F3 debug overlay
- Game console/terminal

### On-Screen Debug

```javascript
var debugEnabled = true;

Distance.on("render2d", function() {
    if (!debugEnabled) return;
    
    Distance.drawTextShadow("Debug Mode", 10, 10, 0xFFFF00);
    Distance.drawTextShadow("Player Null: " + Distance.isPlayerNull(), 10, 25, 0xFFFFFF);
});
```

## Next Steps

- Read the [API Reference](api-reference.md) for complete function documentation
- Check out [Examples](examples.md) for more complex scripts
- Learn about the [Event System](events.md)
- Explore [Rendering](rendering.md) for HUD creation
- Review [Best Practices](best-practices.md) for optimization tips

## Common Issues

**Script doesn't load:**
- Check console for syntax errors
- Verify `.dscript` extension
- Make sure file is in correct folder

**Events don't fire:**
- Verify event name spelling
- Check script is enabled in GUI
- Ensure callbacks are registered at load time

**Player data returns null:**
- Always use `Distance.isPlayerNull()` check
- Player is null in menus and loading screens
