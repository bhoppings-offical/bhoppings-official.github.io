# API Reference

Complete reference for all Distance API functions.

## Table of Contents

- [Projection & Coordinates](#projection--coordinates)
- [Core Functions](#core-functions)
- [Player API](#player-api)
- [Rotation & Combat](#rotation--combat)
- [World & Game API](#world--game-api)
- [Input API](#input-api)
- [Particle & Sound Effects](#particle--sound-effects)
- [Web Requests](#web-requests)
- [Inventory & Items](#inventory--items)
- [Advanced Rendering](#advanced-rendering)
- [Gameplay Actions](#gameplay-actions)

---

## Projection & Coordinates

### `Distance.worldToScreen(x, y, z)`

Converts 3D world coordinates to 2D screen coordinates.

**Returns:** Object with `x`, `y`, `z` (depth), and `visible` (boolean), or `null` if projection fails.

```javascript
Distance.on("render2d", function() {
    if (Distance.isPlayerNull()) return;
    
    var entities = Distance.getEntities();
    for (var i = 0; i < entities.length; i++) {
        var e = entities[i];
        var screenPos = Distance.worldToScreen(e.x, e.y + 2, e.z);
        
        if (screenPos != null && screenPos.visible) {
            Distance.drawTextShadow(
                e.name,
                screenPos.x,
                screenPos.y,
                0xFFFFFF
            );
        }
    }
});
```

**Use cases:**
- ESP/Nametags
- Health bars above entities
- Distance markers
- World position labels

**Example - Distance Labels:**
```javascript
Distance.on("render2d", function() {
    if (Distance.isPlayerNull()) return;
    
    var entities = Distance.getEntities();
    var px = Distance.getPlayerX();
    var py = Distance.getPlayerY();
    var pz = Distance.getPlayerZ();
    
    for (var i = 0; i < entities.length; i++) {
        var e = entities[i];
        if (e.type !== "Player") continue;
        
        var dist = Distance.getDistance(px, py, pz, e.x, e.y, e.z);
        var screenPos = Distance.worldToScreen(e.x, e.y + 2.5, e.z);
        
        if (screenPos != null && screenPos.visible) {
            Distance.drawTextShadow(
                e.name + " [" + dist.toFixed(1) + "m]",
                screenPos.x - Distance.textWidth(e.name) / 2,
                screenPos.y,
                0xFFFFFF
            );
        }
    }
});
```

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

### `Distance.disable()`

Disables the current script.

```javascript
if (someErrorCondition) {
    Distance.error("Critical error, disabling script");
    Distance.disable();
}
```

### `Distance.reset()`

Reloads the current script (re-reads and re-evaluates the file).

```javascript
Distance.reset();
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

#### `Distance.onGround()`
Alias for `isOnGround()`.

#### `Distance.getFallDistance()`
Returns current fall distance.

```javascript
var fallDist = Distance.getFallDistance();
if (fallDist > 3) {
    Distance.chat("&cHigh fall detected!");
}
```

#### `Distance.jump()`
Makes the player jump.

```javascript
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    if (Distance.isOnGround()) {
        Distance.jump();
    }
});
```

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

## Rotation & Combat

### `Distance.setRotation(float yaw, float pitch)`

Sets player's rotation.

```javascript
Distance.setRotation(90, 0);
```

### `Distance.setSilentRotation(yaw, pitch)`

Sets silent rotation (rotations not sent to server).

```javascript
Distance.setSilentRotation(90, 0);
```

### `Distance.lookAt(x, y, z)`

Makes player look at specific coordinates.

```javascript
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    Distance.lookAt(100, 64, 100);
});
```

### `Distance.lookAtEntity(entity)`

Makes player look at an entity.

```javascript
Distance.on("tick", function() {
    var target = Distance.getTargetEntity();
    if (target != null) {
        Distance.lookAtEntity(target);
    }
});
```

### `Distance.attack(entity)`

Attacks an entity.

```javascript
Distance.on("tick", function() {
    var target = Distance.getTargetEntity();
    if (target != null && Distance.isMouseDown(0)) {
        Distance.attack(target);
    }
});
```

### `Distance.swing()`

Swings player's hand.

```javascript
Distance.swing();
```

### `Distance.canSeeEntity(entity)`

Returns `true` if player has line of sight to entity.

```javascript
var target = Distance.getTargetEntity();
if (target != null && Distance.canSeeEntity(target)) {
    Distance.chat("Can see target!");
}
```

### `Distance.getReachDistance()`

Returns player's reach distance.

```javascript
var reach = Distance.getReachDistance();
Distance.chat("Reach: " + reach);
```

### Utilities

#### `Distance.setTimeout(task, delayMs)`

Executes a function after a delay.

```javascript
Distance.chat("Starting timer...");

Distance.setTimeout(function() {
    Distance.chat("3 seconds passed!");
}, 3000);
```

**Use for:**
- Delayed actions
- Timers
- Scheduled tasks

```javascript
var counter = 0;

function countdown() {
    counter++;
    Distance.chat("Count: " + counter);
    
    if (counter < 10) {
        Distance.setTimeout(countdown, 1000);
    }
}

countdown();
```

---

## World & Game API

### World Checks
Returns `true` if world doesn't exist.

```javascript
if (Distance.isWorldNull()) {
    Distance.log("Not in a world");
    return;
}
```

### FPS & Time

#### `Distance.getFPS()`
Returns current frames per second.

```javascript
Distance.on("render2d", function() {
    var fps = Distance.getFPS();
    Distance.drawTextShadow("FPS: " + fps, 10, 10, 0xFFFFFF);
});
```

#### `Distance.getWorldTime()`
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

#### `Distance.isKeyDown(keyName)`
Returns `true` if key is pressed (using key name).

```javascript
Distance.on("tick", function() {
    if (Distance.isKeyDown("W")) {
        Distance.chat("Moving forward!");
    }
    
    if (Distance.isKeyDown("SPACE")) {
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

## Web Requests

### `Distance.httpGet(url, callback)`

Performs an asynchronous GET request.

```javascript
Distance.httpGet("https://api.example.com/data", function(response) {
    Distance.log("Response: " + response);
    Distance.chat("&aData loaded!");
});
```

### `Distance.httpPost(url, data, callback)`

Performs an asynchronous POST request.

```javascript
var data = JSON.stringify({ username: "player", score: 100 });

Distance.httpPost("https://api.example.com/score", data, function(response) {
    Distance.log("Server response: " + response);
});
```

### `Distance.downloadImage(url, imageId, callback)`

Downloads an image for later rendering.

```javascript
Distance.downloadImage(
    "https://example.com/image.png",
    "my_icon",
    function() {
        Distance.log("Image ready!");
    }
);

Distance.on("render2d", function() {
    Distance.drawImage("my_icon", 10, 10, 32, 32);
});
```

---

## Inventory & Items

### `Distance.getHeldItem()`

Returns information about the currently held item.

```javascript
var item = Distance.getHeldItem();
if (item != null) {
    Distance.chat("Holding: " + item.name);
    Distance.chat("Count: " + item.count);
}
```

**Returns object with:**
- `name` - Display name
- `count` - Stack size
- `damage` - Current damage/durability
- `maxDamage` - Maximum damage
- `id` - Item registry ID

### `Distance.getInventory(type)`

Returns array of inventory items. Type can be "all", "hotbar", or "inventory".

```javascript
var hotbar = Distance.getInventory("hotbar");
var mainInventory = Distance.getInventory("inventory");
var all = Distance.getInventory("all");  // or just Distance.getInventory()

for (var i = 0; i < hotbar.length; i++) {
    if (hotbar[i] != null) {
        Distance.log("Hotbar slot " + i + ": " + hotbar[i].name);
    }
}
```

### `Distance.getContainerItems()`

Returns all items in the currently open container.

```javascript
Distance.on("chestOpen", function() {
    var items = Distance.getContainerItems();
    Distance.chat("Container has " + items.length + " slots");
    
    for (var i = 0; i < items.length; i++) {
        if (items[i] != null) {
            Distance.log("Slot " + i + ": " + items[i].name);
        }
    }
});
```

### `Distance.getChestItems()`

Returns only the chest items (excludes player inventory from container).

```javascript
Distance.on("chestOpen", function() {
    var chestItems = Distance.getChestItems();
    Distance.chat("Chest has " + chestItems.length + " items");
    
    for (var i = 0; i < chestItems.length; i++) {
        if (chestItems[i] != null) {
            Distance.chat("Chest slot " + i + ": " + chestItems[i].name);
        }
    }
});
```

### `Distance.getSlot(index)`

Returns a specific slot's item from the open container.

```javascript
var firstSlot = Distance.getSlot(0);
if (firstSlot != null) {
    Distance.chat("First slot: " + firstSlot.name);
}
```

### `Distance.setSlot(slot)`

Sets the active hotbar slot (0-8).

```javascript
Distance.setSlot(0);
```

### `Distance.moveItem(fromSlot, toSlot)`

Moves item between slots in open container.

```javascript
Distance.moveItem(0, 9);
```

### `Distance.dropItem(slot)`

Drops item from specified slot.

```javascript
Distance.dropItem(0);
```

---

## Advanced Rendering

### 2D Image Rendering

#### `Distance.drawImage(imageId, x, y, width, height)`

Draws a previously downloaded image.

```javascript
Distance.downloadImage("https://example.com/logo.png", "logo", function() {
    Distance.log("Logo loaded");
});

Distance.on("render2d", function() {
    Distance.drawImage("logo", 10, 10, 64, 64);
});
```

#### `Distance.drawResource(path, x, y, width, height)`

Draws a Minecraft resource texture.

```javascript
Distance.on("render2d", function() {
    Distance.drawResource("textures/blocks/dirt.png", 10, 10, 32, 32);
    Distance.drawResource("textures/items/diamond_sword.png", 50, 10, 32, 32);
    
    Distance.drawResource("minecraft:textures/entity/creeper/creeper.png", 100, 10, 32, 32);
});
```

### 3D Rendering

#### `Distance.drawBox3D(x, y, z, width, height, depth, color)`

Draws a 3D box in world space.

```javascript
Distance.on("render3d", function() {
    if (Distance.isPlayerNull()) return;
    
    var px = Distance.getPlayerX();
    var py = Distance.getPlayerY();
    var pz = Distance.getPlayerZ();
    
    Distance.drawBox3D(px + 2, py, pz, 1, 2, 1, 0x8000FF00);
});
```

#### `Distance.drawLine3D(x1, y1, z1, x2, y2, z2, color)`

Draws a 3D line between two points.

```javascript
Distance.on("render3d", function() {
    if (Distance.isPlayerNull()) return;
    
    var px = Distance.getPlayerX();
    var py = Distance.getPlayerY();
    var pz = Distance.getPlayerZ();
    
    Distance.drawLine3D(px, py, pz, px + 5, py + 5, pz + 5, 0xFFFF0000);
});
```

#### `Distance.drawSphere(x, y, z, radius, slices, stacks, color)`

Draws a 3D sphere.

```javascript
Distance.on("render3d", function() {
    if (Distance.isPlayerNull()) return;
    
    var px = Distance.getPlayerX();
    var py = Distance.getPlayerY();
    var pz = Distance.getPlayerZ();
    
    Distance.drawSphere(px + 3, py, pz, 1.0, 16, 16, 0x80FF00FF);
});
```

#### `Distance.drawPyramid(x, y, z, width, height, color)`

Draws a 3D pyramid.

```javascript
Distance.on("render3d", function() {
    if (Distance.isPlayerNull()) return;
    
    var px = Distance.getPlayerX();
    var py = Distance.getPlayerY();
    var pz = Distance.getPlayerZ();
    
    Distance.drawPyramid(px + 3, py, pz, 2, 3, 0x80FFAA00);
});
```

#### `Distance.drawOutline(entity, color, lineWidth)`

Draws an outline around an entity.

```javascript
Distance.on("render3d", function() {
    var target = Distance.getTargetEntity();
    if (target != null) {
        Distance.drawOutline(target, 0xFFFF0000, 2.0);
    }
});
```

#### `Distance.drawOutline(x, y, z, color, lineWidth)`

Draws an outline box at coordinates.

```javascript
Distance.on("render3d", function() {
    Distance.drawOutline(100, 64, 100, 0xFF00FF00, 2.0);
});
```

---

## Gameplay Actions

### `Distance.sendMessage(text)`

Sends a chat message as the player.

```javascript
Distance.sendMessage("Hello world!");
```

### `Distance.executeCommand(cmd)`

Executes a command.

```javascript
Distance.executeCommand("/help");
Distance.executeCommand("gamemode creative");
```

### `Distance.notification(title, message, durationSeconds)`

Shows a notification.

```javascript
Distance.notification("Alert", "Important message!", 5);
```

### `Distance.getDistance(x1, y1, z1, x2, y2, z2)`

Calculates distance between two 3D points.

```javascript
var dist = Distance.getDistance(
    Distance.getPlayerX(), Distance.getPlayerY(), Distance.getPlayerZ(),
    100, 64, 100
);
Distance.chat("Distance to target: " + dist.toFixed(2));
```

### Player State Checks

#### `Distance.isSneaking()`
Returns `true` if player is sneaking.

#### `Distance.isSprinting()`
Returns `true` if player is sprinting.

#### `Distance.getTargetEntity()`
Returns the entity the player is looking at, or `null`.

```javascript
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    if (Distance.isSneaking()) {
        Distance.chat("Sneaking!");
    }
    
    var target = Distance.getTargetEntity();
    if (target != null) {
        Distance.chat("Looking at: " + target.getName());
    }
});
```

---

## Next Steps

- Learn about the [Event System](events.md)
- Explore [Rendering Functions](rendering.md)
- Create [GUI Settings](gui-settings.md)
- View complete [Examples](examples.md)
