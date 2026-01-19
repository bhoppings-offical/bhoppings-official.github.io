# Distance Scripting Framework

A powerful JavaScript scripting framework for Minecraft that allows you to create custom HUDs, movement enhancers, combat utilities, and much more.

## Features

- **Custom HUDs & Overlays** - Build personalized on-screen displays
- **Movement Enhancement** - Create custom movement mechanics
- **Combat Utilities** - Build attack counters, hit indicators, and combat stats
- **Visual Effects** - Spawn particles and create visual trails
- **Event System** - Hook into game events (tick, render, attack)
- **GUI Settings** - Easy-to-use settings interface with toggles and sliders
- **Real-time Rendering** - 2D and 3D rendering capabilities

## Quick Start

### Installation

1. Place your scripts in: `config/DistanceMod/scripts/`
2. Scripts must have the `.dscript` extension
3. Enable scripts through the Distance GUI in-game

### Hello World

```javascript
// HelloWorld.dscript
Distance.log("Script loaded!");
Distance.chat("&aHello from Distance!");

Distance.on("tick", function() {
    // Runs 20 times per second
});

Distance.on("render2d", function() {
    Distance.drawTextShadow("Hello World!", 10, 10, 0xFFFFFF);
});
```

## Documentation

- [Getting Started](docs/getting-started.md) - Installation and basic concepts
- [API Reference](docs/api-reference.md) - Complete API documentation
- [Event System](docs/events.md) - Event handlers and usage
- [Rendering Guide](docs/rendering.md) - 2D/3D rendering documentation
- [GUI Settings](docs/gui-settings.md) - Creating custom settings panels
- [Examples](docs/examples.md) - Complete example scripts
- [Best Practices](docs/best-practices.md) - Tips and optimization

## Example Scripts

Browse the [examples](examples/) folder for complete, ready-to-use scripts:

- [Speed Meter](examples/speed-meter.dscript) - Display your movement speed
- [Auto Jump](examples/auto-jump.dscript) - Automatic jumping while moving
- [Particle Trail](examples/particle-trail.dscript) - Leave particle effects behind you
- [Combat Counter](examples/combat-counter.dscript) - Track your attacks
- [Enhanced Movement](examples/enhanced-movement.dscript) - Complete movement enhancement suite

## Quick Reference

### Core Functions
```javascript
Distance.log(message)          // Console logging
Distance.chat(message)         // Chat messages (supports color codes)
Distance.error(message)        // Error messages
```

### Player Data
```javascript
Distance.getPlayerX/Y/Z()      // Position
Distance.getMotionX/Y/Z()      // Velocity
Distance.getYaw/Pitch()        // Rotation
Distance.getHealth()           // Health
Distance.isOnGround()          // Ground check
```

### Events
```javascript
Distance.on("tick", function() { })
Distance.on("render2d", function() { })
Distance.on("render3d", function() { })
Distance.on("attack", function(target) { })
```

## Contributing

Feel free to submit issues and enhancement requests!

## License

[Add your license here]

---

**Powered by Nashorn JavaScript Engine**
