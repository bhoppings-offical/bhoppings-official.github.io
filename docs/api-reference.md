# API Reference

Complete reference for all Distance API functions.

## Table of Contents

- [Core Functions](#core-functions)
- [Player API](#player-api)
- [World & Game API](#world--game-api)
- [Input API](#input-api)
- [Particle & Sound Effects](#particle--sound-effects)

---

## Core Functions

### `Distance.log(message)`

Prints a message to the console (F3 debug or terminal).

```javascript
Distance.log("Debug information");
Distance.log("Player X: " + Distance.getPlayerX());
```

### `Distance.chat(message)`

Sends a message to the player's chat. Supports Minecraft color codes (`&`).

```javascript
Distance.chat("&aGreen text");
Distance.chat("&cRed &eYellow &bBlue");
Distance.chat("&l&nBold and underlined");
```

**Color Codes:**
| Code | Color | Code | Color |
|------|-------|------|-------|
| `&0` | Black | `&8` | Dark Gray |
| `&1` | Dark Blue | `&9` | Blue |
| `&2` | Dark Green | `&a` | Green |
| `&3` | Dark Aqua | `&b` | Aqua |
| `&4` | Dark Red | `&c` | Red |
| `&5` | Dark Purple | `&d` | Light Purple |
| `&6` | Gold | `&e` | Yellow |
| `&7` | Gray | `&f` | White |

**Format Codes:**
- `&l` - Bold
- `&n` - Underline
- `&r` - Reset

### `Distance.error(message)`

Sends a red error message to chat.

```javascript
Distance.error("Something went wrong!");
```

---

## Player API

### Position

#### `Distance.getPlayerX()`
Returns the player's X coordinate.

```javascript
var x = Distance.getPlayerX();
Distance.chat("X: " + x.toFixed(2));
```

#### `Distance.getPlayerY()`
Returns the player's Y coordinate.

#### `Distance.getPlayerZ()`
Returns the player's Z coordinate.

**Example:**
```javascript
var x = Distance.getPlayerX();
var y = Distance.getPlayerY();
var z = Distance.getPlayerZ();

Distance.chat("Position: " + x.toFixed(2) + ", " + y.toFixed(2) + ", " + z.toFixed(2));
```

### Motion (Velocity)

#### `Distance.getMotionX()`
Returns the player's X velocity.

#### `Distance.getMotionY()`
Returns the player's Y velocity.

#### `Distance.getMotionZ()`
Returns the player's Z velocity.

**Example - Calculate Speed:**
```javascript
var mx = Distance.getMotionX();
var mz = Distance.getMotionZ();
var speed = Math.sqrt(mx * mx + mz * mz);
var blocksPerSecond = speed * 20;

Distance.chat("Speed: " + blocksPerSecond.toFixed(2) + " blocks/second");
```

#### `Distance.setMotion(x, y, z)`
Sets the player's velocity.

```javascript
// Launch player upward
Distance.setMotion(
    Distance.getMotionX(),
    0.5,
    Distance.getMotionZ()
);

// Speed boost forward
Distance.on("tick", function() {
    if (Distance.isKeyDown(57)) { // Space
        var mx = Distance.getMotionX() * 1.5;
        var mz = Distance.getMotionZ() * 1.5;
        Distance.setMotion(mx, Distance.getMotionY(), mz);
    }
});
```

### Rotation

#### `Distance.getYaw()`
Returns horizontal rotation (-180 to 180 degrees).

#### `Distance.getPitch()`
Returns vertical rotation (-90 to 90 degrees).

```javascript
var yaw = Distance.getYaw();
var pitch = Distance.getPitch();

Distance.chat("Looking at: Yaw=" + yaw.toFixed(1) + "° Pitch=" + pitch.toFixed(1) + "°");
```

### Health & Stats

#### `Distance.getHealth()`
Returns current health.

#### `Distance.getMaxHealth()`
Returns maximum health.

#### `Distance.getName()`
Returns player name.

#### `Distance.isOnGround()`
Returns `true` if player is on the ground.

```javascript
var health = Distance.getHealth();
var maxHealth = Distance.getMaxHealth();
var name = Distance.getName();
var onGround = Distance.isOnGround();

Distance.chat(name + " Health: " + health + "/" + maxHealth);

if (onGround) {
    Distance.chat("Standing on ground");
}
```

### Null Checks

#### `Distance.isPlayerNull()`
Returns `true` if player doesn't exist (in menu, loading screen, etc.).

**Always check before accessing player data:**
```javascript
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) {
        return; // Exit early
    }
    
    // Safe to use player data here
    var health = Distance.getHealth();
});
```

---

## World & Game API

### `Distance.isWorldNull()`
Returns `true` if world doesn't exist.

```javascript
if (Distance.isWorldNull()) {
    Distance.log("Not in a world");
    return;
}
```

### `Distance.getFPS()`
Returns current frames per second.

```javascript
Distance.on("render2d", function() {
    var fps = Distance.getFPS();
    Distance.drawTextShadow("FPS: " + fps, 10, 10, 0xFFFFFF);
});
```

### `Distance.getWorldTime()`
Returns world time (0-23999).

```javascript
var time = Distance.getWorldTime();
var dayTime = time % 24000;

if (dayTime < 12000) {
    Distance.chat("It's daytime!");
} else {
    Distance.chat("It's nighttime!");
}
```

---

## Input API

### Keyboard

#### `Distance.isKeyDown(keyCode)`
Returns `true` if key is currently pressed.

```javascript
Distance.on("tick", function() {
    if (Distance.isKeyDown(17)) { // W key
        Distance.chat("Moving forward!");
    }
    
    if (Distance.isKeyDown(57)) { // Space
        Distance.chat("Jumping!");
    }
});
```

**Common Key Codes:**
| Key | Code | Key | Code |
|-----|------|-----|------|
| W | 17 | Space | 57 |
| A | 30 | Left Shift | 42 |
| S | 31 | Left Ctrl | 29 |
| D | 32 | Left Alt | 56 |
| 1-9 | 2-10 | Up Arrow | 200 |
| L | 38 | Down Arrow | 208 |
| P | 25 | Left Arrow | 203 |
| | | Right Arrow | 205 |

[Full LWJGL Keyboard Constants](https://legacy.lwjgl.org/javadoc/org/lwjgl/input/Keyboard.html)

### Mouse

#### `Distance.isMouseDown(button)`
Returns `true` if mouse button is currently pressed.

```javascript
Distance.on("tick", function() {
    if (Distance.isMouseDown(0)) {
        Distance.chat("Left click held!");
    }
    
    if (Distance.isMouseDown(1)) {
        Distance.chat("Right click held!");
    }
    
    if (Distance.isMouseDown(2)) {
        Distance.chat("Middle click held!");
    }
});
```

**Mouse Buttons:**
- `0` - Left button
- `1` - Right button
- `2` - Middle button

---

## Particle & Sound Effects

### Particles

#### `Distance.spawnParticle(type, x, y, z, vx, vy, vz)`
Spawns a particle effect.

**Parameters:**
- `type` - Particle type (string, uppercase)
- `x, y, z` - Position coordinates
- `vx, vy, vz` - Velocity/offset

```javascript
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    var x = Distance.getPlayerX();
    var y = Distance.getPlayerY() + 1;
    var z = Distance.getPlayerZ();
    
    Distance.spawnParticle("FLAME", x, y, z, 0, 0.1, 0);
});
```

**Common Particle Types:**
- `FLAME`, `LAVA`
- `SMOKE_NORMAL`, `SMOKE_LARGE`
- `EXPLOSION_NORMAL`, `EXPLOSION_LARGE`
- `FIREWORKS_SPARK`
- `WATER_BUBBLE`, `WATER_SPLASH`
- `CRIT`, `CRIT_MAGIC`
- `SPELL`, `SPELL_MOB`, `SPELL_WITCH`
- `PORTAL`, `ENCHANTMENT_TABLE`
- `HEART`, `VILLAGER_HAPPY`, `VILLAGER_ANGRY`
- `NOTE`, `REDSTONE`
- `CLOUD`, `SNOW_SHOVEL`
- `DRIP_WATER`, `DRIP_LAVA`

### Sounds

#### `Distance.playSound(sound, volume, pitch)`
Plays a sound at the player's location.

**Parameters:**
- `sound` - Sound name (string)
- `volume` - Volume (0.0 to 1.0)
- `pitch` - Pitch (0.5 to 2.0)

```javascript
// Standard sound
Distance.playSound("random.orb", 1.0, 1.0);

// Higher pitch
Distance.playSound("random.orb", 1.0, 2.0);

// Lower volume
Distance.playSound("random.orb", 0.5, 1.0);
```

**Common Sounds:**
- `random.orb`
- `random.levelup`
- `random.explode`
- `random.successful_hit`
- `mob.endermen.portal`
- `note.bass`, `note.pling`
- `fire.ignite`
- `random.pop`

---

## Next Steps

- Learn about the [Event System](events.md)
- Explore [Rendering Functions](rendering.md)
- Create [GUI Settings](gui-settings.md)
- View complete [Examples](examples.md)
