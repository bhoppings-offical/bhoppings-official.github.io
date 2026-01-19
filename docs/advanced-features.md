# Advanced Features

Advanced capabilities for Distance scripts including web requests, image rendering, and 3D graphics.

## Table of Contents

- [Web Requests](#web-requests)
- [Image Rendering](#image-rendering)
- [3D Rendering](#3d-rendering)
- [Inventory Management](#inventory-management)
- [Notifications](#notifications)

---

## Web Requests

Distance scripts can make asynchronous HTTP requests to external APIs.

### GET Requests

```javascript
Distance.httpGet("https://api.example.com/data", function(response) {
    Distance.log("Received: " + response);
    
    var data = JSON.parse(response);
    Distance.chat("Value: " + data.value);
});
```

**Use cases:**
- Fetch server status
- Get player statistics
- Load external data
- Check for updates

### POST Requests

```javascript
var payload = JSON.stringify({
    username: Distance.getName(),
    score: 1000
});

Distance.httpPost("https://api.example.com/submit", payload, function(response) {
    Distance.chat("&aSubmitted successfully!");
});
```

**Note:** Callbacks execute on the main thread, safe for all Distance API calls.

### Example - Weather Display

```javascript
Distance.createTab("Weather", "world");

var weatherData = "Loading...";
var lastUpdate = 0;

Distance.on("tick", function() {
    var now = Date.now();
    if (now - lastUpdate > 60000) {
        Distance.httpGet("https://api.weather.com/current", function(response) {
            var data = JSON.parse(response);
            weatherData = data.temp + "°F - " + data.conditions;
            lastUpdate = now;
        });
    }
});

Distance.on("render2d", function() {
    Distance.drawTextShadow(weatherData, 10, 10, 0xFFFFFF);
});
```

---

## Image Rendering

Download and display images from the internet.

### Downloading Images

```javascript
Distance.downloadImage(
    "https://example.com/icon.png",
    "player_icon",
    function() {
        Distance.log("Image ready!");
    }
);
```

**Parameters:**
1. URL (string)
2. Unique ID (string) - used to reference the image later
3. Callback (function) - called when image is ready

### Drawing Images

```javascript
Distance.on("render2d", function() {
    Distance.drawImage("player_icon", 10, 10, 64, 64);
});
```

**Parameters:**
- `imageId` - The unique ID from `downloadImage`
- `x, y` - Position on screen
- `width, height` - Size to render

### Example - Avatar Display

```javascript
Distance.createTab("Avatar", "star");

var avatarLoaded = false;
var playerUUID = "069a79f4-44e9-4726-a5be-fca90e38aaf5";

Distance.downloadImage(
    "https://crafatar.com/avatars/" + playerUUID,
    "avatar",
    function() {
        avatarLoaded = true;
        Distance.chat("&aAvatar loaded!");
    }
);

Distance.on("render2d", function() {
    if (avatarLoaded) {
        var screenWidth = Distance.getScreenWidth();
        Distance.drawImage("avatar", screenWidth - 74, 10, 64, 64);
    }
});
```

---

## 3D Rendering

Render shapes and lines in world space.

### 3D Boxes

```javascript
Distance.on("render3d", function() {
    if (Distance.isPlayerNull()) return;
    
    var px = Distance.getPlayerX();
    var py = Distance.getPlayerY();
    var pz = Distance.getPlayerZ();
    
    Distance.drawBox3D(
        px + 2,     // x
        py,         // y
        pz,         // z
        1,          // width
        2,          // height
        1,          // depth
        0x8000FF00  // color (semi-transparent green)
    );
});
```

### 3D Lines

```javascript
Distance.on("render3d", function() {
    if (Distance.isPlayerNull()) return;
    
    var px = Distance.getPlayerX();
    var py = Distance.getPlayerY();
    var pz = Distance.getPlayerZ();
    
    Distance.drawLine3D(
        px, py, pz,
        px + 5, py + 5, pz + 5,
        0xFFFF0000
    );
});
```

### Example - Waypoint Markers

```javascript
var waypoints = [];

Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    if (Distance.isKeyDown(38)) {
        waypoints.push({
            x: Distance.getPlayerX(),
            y: Distance.getPlayerY(),
            z: Distance.getPlayerZ()
        });
        Distance.chat("&aWaypoint added!");
    }
});

Distance.on("render3d", function() {
    for (var i = 0; i < waypoints.length; i++) {
        var wp = waypoints[i];
        
        Distance.drawBox3D(
            wp.x - 0.5, wp.y, wp.z - 0.5,
            1, 2, 1,
            0x80FF00FF
        );
        
        if (Distance.isPlayerNull()) continue;
        
        Distance.drawLine3D(
            Distance.getPlayerX(), Distance.getPlayerY(), Distance.getPlayerZ(),
            wp.x, wp.y + 1, wp.z,
            0x8000FFFF
        );
    }
});
```

---

## Inventory Management

Interact with player inventory and containers.

### Reading Inventory

```javascript
Distance.on("inventoryOpen", function() {
    var inventory = Distance.getInventory();
    
    for (var i = 0; i < inventory.length; i++) {
        if (inventory[i] != null) {
            var item = inventory[i];
            Distance.log("Slot " + i + ": " + item.name + " x" + item.count);
        }
    }
});
```

### Getting Held Item

```javascript
Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    var held = Distance.getHeldItem();
    if (held != null) {
        Distance.log("Holding: " + held.name);
        Distance.log("Durability: " + (held.maxDamage - held.damage) + "/" + held.maxDamage);
    }
});
```

### Moving Items

```javascript
Distance.on("chestOpen", function() {
    Distance.moveItem(0, 27);
});
```

### Dropping Items

```javascript
Distance.on("tick", function() {
    if (Distance.isKeyDown(34)) {
        Distance.dropItem(0);
    }
});
```

### Example - Auto Sort

```javascript
var sortEnabled = false;

Distance.createTab("Auto Sort", "gear");

Distance.addToggle("Enable Auto Sort", false, function(value) {
    sortEnabled = value;
});

Distance.on("chestOpen", function() {
    if (!sortEnabled) return;
    
    var inventory = Distance.getInventory();
    
    for (var i = 0; i < 27; i++) {
        if (inventory[i] != null) {
            var item = inventory[i];
            
            if (item.name.includes("Diamond")) {
                Distance.moveItem(i, 0);
            }
        }
    }
    
    Distance.chat("&aSorted diamonds to top!");
});
```

---

## Notifications

Display popup notifications.

### Basic Notification

```javascript
Distance.notification("Title", "Message text", 5);
```

**Parameters:**
- `title` - Notification title (string)
- `message` - Notification text (string)
- `duration` - Display time in seconds (number)

### Example - Health Alert

```javascript
var lastHealth = 20;

Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    var health = Distance.getHealth();
    
    if (health < 5 && health < lastHealth) {
        Distance.notification(
            "Low Health!",
            "Health: " + health.toFixed(1),
            3
        );
    }
    
    lastHealth = health;
});
```

### Example - Item Pickup Notification

```javascript
Distance.on("itemPickup", function(item) {
    if (item.name.includes("Diamond")) {
        Distance.notification(
            "Rare Item!",
            "Picked up: " + item.name,
            5
        );
        Distance.playSound("random.levelup", 1.0, 1.0);
    }
});
```

---

## Complete Advanced Example

```javascript
Distance.log("Advanced script loading...");

Distance.createTab("Advanced Features", "star");

var apiEnabled = false;
var avatarId = "069a79f4-44e9-4726-a5be-fca90e38aaf5";
var showWaypoints = false;
var waypoints = [];

Distance.addToggle("Enable API", false, function(value) {
    apiEnabled = value;
});

Distance.addTextInput("Avatar UUID", avatarId, function(value) {
    avatarId = value;
    loadAvatar();
});

Distance.addToggle("Show Waypoints", false, function(value) {
    showWaypoints = value;
});

function loadAvatar() {
    Distance.downloadImage(
        "https://crafatar.com/avatars/" + avatarId,
        "player_avatar",
        function() {
            Distance.notification("Avatar", "Loaded successfully!", 3);
        }
    );
}

loadAvatar();

Distance.on("tick", function() {
    if (Distance.isPlayerNull()) return;
    
    if (Distance.isKeyDown(38) && showWaypoints) {
        waypoints.push({
            x: Distance.getPlayerX(),
            y: Distance.getPlayerY(),
            z: Distance.getPlayerZ()
        });
        Distance.notification("Waypoint", "Added at current location", 2);
    }
});

Distance.on("render2d", function() {
    var screenWidth = Distance.getScreenWidth();
    
    Distance.drawImage("player_avatar", screenWidth - 74, 10, 64, 64);
    
    Distance.drawTextShadow(
        "Waypoints: " + waypoints.length,
        screenWidth - 74,
        80,
        0xFFFFFF
    );
});

Distance.on("render3d", function() {
    if (!showWaypoints) return;
    
    for (var i = 0; i < waypoints.length; i++) {
        var wp = waypoints[i];
        Distance.drawBox3D(wp.x - 0.5, wp.y, wp.z - 0.5, 1, 2, 1, 0x80FF00FF);
    }
});

Distance.on("itemPickup", function(item) {
    if (item.name.includes("Diamond")) {
        Distance.notification("Rare Item!", item.name + " x" + item.count, 5);
    }
});

Distance.log("Advanced script loaded!");
```

---

## Next Steps

- Review the [API Reference](api-reference.md) for function details
- Check [Events](events.md) for all available events
- See [Examples](examples.md) for more complete scripts
