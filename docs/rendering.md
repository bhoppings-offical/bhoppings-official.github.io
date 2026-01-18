# Rendering Guide

Create custom HUDs and visual elements using Distance's 2D rendering API.

## Table of Contents

- [Screen Dimensions](#screen-dimensions)
- [Text Rendering](#text-rendering)
- [Shapes](#shapes)
- [Colors](#colors)
- [Complete Examples](#complete-examples)

---

## Screen Dimensions

### `Distance.getScreenWidth()`
Returns screen width in pixels.

### `Distance.getScreenHeight()`
Returns screen height in pixels.

```javascript
Distance.on("render2d", function() {
    var width = Distance.getScreenWidth();
    var height = Distance.getScreenHeight();
    
    Distance.drawText("Screen: " + width + "x" + height, 10, 10, 0xFFFFFF);
});
```

---

## Text Rendering

### `Distance.drawText(text, x, y, color)`
Draws text without shadow.

**Parameters:**
- `text` - String to display
- `x` - X position (pixels from left)
- `y` - Y position (pixels from top)
- `color` - Color in 0xAARRGGBB format

```javascript
Distance.on("render2d", function() {
    Distance.drawText("Hello World", 100, 100, 0xFFFFFFFF);
});
```

### `Distance.drawTextShadow(text, x, y, color)`
Draws text with shadow (recommended for better readability).

```javascript
Distance.on("render2d", function() {
    Distance.drawTextShadow("Hello World", 100, 120, 0xFFFFFFFF);
});
```

### `Distance.getStringWidth(text)`
Returns the width of text in pixels (useful for alignment).

**Example - Centered Text:**
```javascript
Distance.on("render2d", function() {
    var text = "Centered Text";
    var textWidth = Distance.getStringWidth(text);
    var screenWidth = Distance.getScreenWidth();
    var centerX = (screenWidth - textWidth) / 2;
    
    Distance.drawTextShadow(text, centerX, 100, 0xFFFFFF00);
});
```

**Example - Right-Aligned Text:**
```javascript
Distance.on("render2d", function() {
    var text = "Right Aligned";
    var textWidth = Distance.getStringWidth(text);
    var screenWidth = Distance.getScreenWidth();
    var rightX = screenWidth - textWidth - 10; // 10px padding
    
    Distance.drawTextShadow(text, rightX, 10, 0xFFFFFFFF);
});
```

---

## Shapes

### Rectangles

#### `Distance.drawRect(x, y, width, height, color)`
Draws a filled rectangle.

```javascript
Distance.on("render2d", function() {
    // Draw a semi-transparent black background
    Distance.drawRect(50, 50, 200, 100, 0x80000000);
});
```

#### `Distance.drawRoundedRect(x, y, width, height, radius, color)`
Draws a filled rectangle with rounded corners.

**Parameters:**
- `x, y` - Top-left corner position
- `width, height` - Rectangle dimensions
- `radius` - Corner radius (higher = rounder)
- `color` - Color in 0xAARRGGBB format

```javascript
Distance.on("render2d", function() {
    // Modern rounded panel
    Distance.drawRoundedRect(200, 50, 150, 80, 5, 0xFF1E90FF);
});
```

### Circles

#### `Distance.drawCircle(x, y, radius, color)`
Draws a filled circle.

**Parameters:**
- `x, y` - Center position
- `radius` - Circle radius
- `color` - Color in 0xAARRGGBB format

```javascript
Distance.on("render2d", function() {
    // Draw a magenta circle
    Distance.drawCircle(300, 100, 25, 0xFFFF00FF);
});
```

**Example - Dot Indicator:**
```javascript
Distance.on("render2d", function() {
    if (Distance.isPlayerNull()) return;
    
    var health = Distance.getHealth();
    var maxHealth = Distance.getMaxHealth();
    var healthPercent = health / maxHealth;
    
    // Color based on health
    var color = healthPercent > 0.5 ? 0xFF55FF55 : 0xFFFF5555;
    
    Distance.drawCircle(30, 30, 8, color);
});
```

---

## Colors

Colors use hexadecimal format: `0xAARRGGBB`

**Format Breakdown:**
- `AA` - Alpha (transparency): `00` = fully transparent, `FF` = opaque
- `RR` - Red component
- `GG` - Green component
- `BB` - Blue component

### Common Colors

```javascript
// Solid colors (opaque)
0xFFFFFFFF  // White
0xFF000000  // Black
0xFFFF0000  // Red
0xFF00FF00  // Green
0xFF0000FF  // Blue
0xFFFFFF00  // Yellow
0xFFFF00FF  // Magenta
0xFF00FFFF  // Cyan
0xFFFF8800  // Orange

// Semi-transparent colors
0x80FFFFFF  // Semi-transparent white
0x80000000  // Semi-transparent black
0x90000000  // Dark overlay (common for HUD backgrounds)
```

### Custom Colors

```javascript
// Calculate custom color
function makeColor(r, g, b, a) {
    return (a << 24) | (r << 16) | (g << 8) | b;
}

var customColor = makeColor(255, 128, 64, 255); // Orange
Distance.drawRect(10, 10, 100, 50, customColor);
```

### Dynamic Colors

```javascript
Distance.on("render2d", function() {
    if (Distance.isPlayerNull()) return;
    
    var health = Distance.getHealth();
    var maxHealth = Distance.getMaxHealth();
    var healthPercent = health / maxHealth;
    
    // Gradient from red to green based on health
    var color;
    if (healthPercent > 0.75) color = 0xFF55FF55;      // Green
    else if (healthPercent > 0.5) color = 0xFFFFFF55;  // Yellow
    else if (healthPercent > 0.25) color = 0xFFFFAA00; // Orange
    else color = 0xFFFF5555;                           // Red
    
    Distance.drawTextShadow("HP: " + health, 10, 10, color);
});
```

---

## Complete Examples

### Example 1: Player Info Panel

```javascript
Distance.on("render2d", function() {
    if (Distance.isPlayerNull()) return;
    
    // Panel background
    Distance.drawRoundedRect(10, 10, 200, 80, 3, 0x90000000);
    
    // Player info
    var name = Distance.getName();
    var health = Distance.getHealth().toFixed(1);
    var maxHealth = Distance.getMaxHealth().toFixed(1);
    
    Distance.drawTextShadow("Player: " + name, 15, 15, 0xFFFFFFFF);
    Distance.drawTextShadow("Health: " + health + "/" + maxHealth, 15, 30, 0xFFFF5555);
    
    // Position
    var x = Distance.getPlayerX().toFixed(1);
    var y = Distance.getPlayerY().toFixed(1);
    var z = Distance.getPlayerZ().toFixed(1);
    
    Distance.drawTextShadow("X: " + x, 15, 45, 0xFF55FF55);
    Distance.drawTextShadow("Y: " + y, 15, 60, 0xFF55FF55);
    Distance.drawTextShadow("Z: " + z, 15, 75, 0xFF55FF55);
});
```

### Example 2: Speed Meter

```javascript
var speed = 0;

Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    var mx = Distance.getMotionX();
    var mz = Distance.getMotionZ();
    speed = Math.sqrt(mx * mx + mz * mz) * 20; // blocks/second
});

Distance.on("render2d", function() {
    var screenWidth = Distance.getScreenWidth();
    
    // Background
    Distance.drawRoundedRect(screenWidth - 130, 10, 120, 40, 3, 0x90000000);
    
    // Speed text
    var speedText = "Speed: " + speed.toFixed(2) + " b/s";
    Distance.drawTextShadow(speedText, screenWidth - 125, 15, 0xFF55FF55);
    
    // Color based on speed
    var color = 0xFFFFFFFF;
    if (speed > 10) color = 0xFFFF5555;      // Red - very fast
    else if (speed > 5) color = 0xFFFFFF55;  // Yellow - fast
    else color = 0xFF55FF55;                  // Green - normal
    
    Distance.drawTextShadow(speed.toFixed(2) + " b/s", screenWidth - 125, 30, color);
});
```

### Example 3: FPS Monitor with Bar

```javascript
Distance.on("render2d", function() {
    var fps = Distance.getFPS();
    var maxFps = 144;
    var fpsPercent = Math.min(fps / maxFps, 1.0);
    
    // Background
    Distance.drawRoundedRect(10, 10, 110, 30, 3, 0x90000000);
    
    // FPS bar
    var barWidth = 100 * fpsPercent;
    var barColor = fps >= 60 ? 0xFF55FF55 : 0xFFFF5555;
    Distance.drawRoundedRect(15, 25, barWidth, 10, 2, barColor);
    
    // FPS text
    Distance.drawTextShadow("FPS: " + fps, 15, 12, 0xFFFFFFFF);
});
```

### Example 4: Compass/Direction Indicator

```javascript
Distance.on("render2d", function() {
    if (Distance.isPlayerNull()) return;
    
    var yaw = Distance.getYaw();
    var normalizedYaw = ((yaw % 360) + 360) % 360;
    
    // Determine direction
    var direction = "";
    if (normalizedYaw >= 337.5 || normalizedYaw < 22.5) direction = "South";
    else if (normalizedYaw >= 22.5 && normalizedYaw < 67.5) direction = "SW";
    else if (normalizedYaw >= 67.5 && normalizedYaw < 112.5) direction = "West";
    else if (normalizedYaw >= 112.5 && normalizedYaw < 157.5) direction = "NW";
    else if (normalizedYaw >= 157.5 && normalizedYaw < 202.5) direction = "North";
    else if (normalizedYaw >= 202.5 && normalizedYaw < 247.5) direction = "NE";
    else if (normalizedYaw >= 247.5 && normalizedYaw < 292.5) direction = "East";
    else direction = "SE";
    
    var screenWidth = Distance.getScreenWidth();
    var screenHeight = Distance.getScreenHeight();
    
    var text = direction + " (" + Math.floor(normalizedYaw) + "°)";
    var textWidth = Distance.getStringWidth(text);
    
    // Centered at bottom
    Distance.drawRoundedRect(
        (screenWidth - textWidth) / 2 - 5,
        screenHeight - 40,
        textWidth + 10,
        25,
        3,
        0x90000000
    );
    
    Distance.drawTextShadow(text, (screenWidth - textWidth) / 2, screenHeight - 35, 0xFF55FFFF);
});
```

### Example 5: Multi-Panel Dashboard

```javascript
var speed = 0;
var health = 0;
var fps = 0;

// Update values in tick
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    var mx = Distance.getMotionX();
    var mz = Distance.getMotionZ();
    speed = Math.sqrt(mx * mx + mz * mz) * 20;
    health = Distance.getHealth();
});

// Render dashboard
Distance.on("render2d", function() {
    fps = Distance.getFPS();
    var screenWidth = Distance.getScreenWidth();
    
    // Panel 1: Speed
    Distance.drawRoundedRect(screenWidth - 130, 10, 120, 35, 3, 0x90000000);
    Distance.drawTextShadow("Speed", screenWidth - 125, 15, 0xFFFFAA00);
    Distance.drawTextShadow(speed.toFixed(2) + " b/s", screenWidth - 125, 28, 0xFF55FF55);
    
    // Panel 2: FPS
    Distance.drawRoundedRect(screenWidth - 130, 50, 120, 35, 3, 0x90000000);
    Distance.drawTextShadow("FPS", screenWidth - 125, 55, 0xFFFFAA00);
    var fpsColor = fps >= 60 ? 0xFF55FF55 : 0xFFFF5555;
    Distance.drawTextShadow(fps.toString(), screenWidth - 125, 68, fpsColor);
    
    // Panel 3: Health
    Distance.drawRoundedRect(screenWidth - 130, 90, 120, 35, 3, 0x90000000);
    Distance.drawTextShadow("Health", screenWidth - 125, 95, 0xFFFFAA00);
    var healthColor = health > 10 ? 0xFF55FF55 : 0xFFFF5555;
    Distance.drawTextShadow(health.toFixed(1) + " HP", screenWidth - 125, 108, healthColor);
});
```

---

## Best Practices

### 1. Use Rounded Rectangles for Modern Look
```javascript
// Modern
Distance.drawRoundedRect(10, 10, 200, 50, 3, 0x90000000);

// Old-school
Distance.drawRect(10, 10, 200, 50, 0x90000000);
```

### 2. Semi-Transparent Backgrounds
```javascript
// Good - doesn't completely block game view
Distance.drawRoundedRect(10, 10, 200, 80, 3, 0x90000000);

// Bad - completely opaque
Distance.drawRoundedRect(10, 10, 200, 80, 3, 0xFF000000);
```

### 3. Use Shadows for Text
```javascript
// Good - readable on any background
Distance.drawTextShadow("Text", 10, 10, 0xFFFFFFFF);

// Less readable
Distance.drawText("Text", 10, 10, 0xFFFFFFFF);
```

### 4. Cache Calculations
```javascript
// Compute in tick, render in render2d
var cachedValue = 0;

Distance.on("tick", function() {
    cachedValue = expensiveCalculation();
});

Distance.on("render2d", function() {
    Distance.drawText("Value: " + cachedValue, 10, 10, 0xFFFFFFFF);
});
```

---

## Next Steps

- Create [GUI Settings](gui-settings.md) for user customization
- View complete [Examples](examples.md)
- Learn [Best Practices](best-practices.md)
