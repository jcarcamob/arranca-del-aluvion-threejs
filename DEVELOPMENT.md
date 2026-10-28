# Development Guide - Arranca del Aluvión

Comprehensive guide for developers working on the game engine, physics, and rendering systems.

## Architecture Overview

### High-Level Flow

```
Browser Load
    ↓
Initialize Three.js Scene
    ↓
Build World (Streets, Buildings, Trees)
    ↓
Create Car & Hazards
    ↓
Game Loop (60 FPS)
    ├─ Update Car Physics
    ├─ Update Hazard Positions
    ├─ Render Frame
    └─ Update HUD
    ↓
Game End (Win / Caught / Timeout)
```

### Module Responsibilities

| Module | Purpose | Key Functions |
|--------|---------|---|
| `game.js` | Main game loop, scene setup, HUD | `main()`, `update()`, `render()` |
| `gamecore.js` | Car physics, hazard systems, levels | `createCar()`, `stepCar()`, `makeHazards()` |
| `world.js` | Map geometry, streets, procedural generation | `STREETS`, `buildCity()` |
| `app.js` | Application entry, initialization | `init()`, `bootstrap()` |
| `sim.js` | Utilities, mathematics, helpers | Various utility functions |

## Core Systems

### 1. Physics System (gamecore.js)

#### Car Physics Model

The car uses a simplified arcade physics model:

```javascript
const car = {
  x, z,           // Position
  v,              // Velocity (speed)
  h,              // Heading (angle)
  depth,          // Damage accumulation
  crashes,        // Collision count
  puddles,        // Puddle hits
  holes           // Pothole hits
}
```

#### Movement Update

```javascript
function stepCar(car, input, dt, env) {
  // 1. Apply acceleration/deceleration
  if (input.up) car.v += ACCEL * dt
  if (input.down) car.v = Math.max(0, car.v - BRAKE * dt)
  car.v = Math.min(car.v, MAX_SPEED)

  // 2. Update heading based on input
  if (input.left) car.h -= TURN_RATE * dt * speedFactor
  if (input.right) car.h += TURN_RATE * dt * speedFactor

  // 3. Update position
  car.x += Math.cos(car.h) * car.v * dt
  car.z += Math.sin(car.h) * car.v * dt

  // 4. Check collisions & hazards
  checkHazards(car, env)
}
```

**Physics Constants:**

```javascript
const ACCEL = 45        // m/s²
const BRAKE = 60        // m/s² deceleration
const MAX_SPEED = 30    // m/s (~108 km/h)
const TURN_RATE = 3.5   // radians/sec
const FRICTION = 0.94   // Speed multiplier per frame
```

#### Collision Detection

```javascript
function checkHazards(car, env) {
  for (const hazard of env.hazards.list) {
    const dist = Math.hypot(hazard.x - car.x, hazard.z - car.z)
    
    if (dist < COLLISION_RADIUS) {
      switch (hazard.kind) {
        case 'p': // Puddle
          car.puddles++
          car.v *= 0.7  // Slow down
          break
        case 'h': // Pothole
          car.holes++
          car.depth += 0.15  // Damage
          break
      }
    }
  }
}
```

### 2. Hazard System (gamecore.js)

#### Hazard Types

```javascript
const HAZARD_TYPES = {
  'p': { name: 'puddle', color: 0x4a5f8f, radius: 8 },
  'h': { name: 'pothole', color: 0x2d2d2d, radius: 6 },
  'tree': { model: '3d', radius: 4 }
}
```

#### Hazard Generation

```javascript
function makeHazards() {
  return {
    list: [],        // All hazards
    falls: [],       // Falling trees
    spawned: 0,      // Total spawned
    caught: false
  }
}

function setHazardLevel(hazards, level) {
  // LEVEL_1: 40 hazards
  // LEVEL_2: 60 hazards
  // LEVEL_3: 100 hazards
  hazards.density = LEVEL_CONFIG[level].density
}
```

### 3. Rendering System (game.js)

#### Scene Structure

```
Scene
├── Lights
│   ├── Ambient Light (0.4 intensity)
│   ├── Directional Light (sun)
│   └── Fog (dark color)
├── Environment
│   ├── Sky (procedural)
│   └── Terrain
├── World
│   ├── Streets
│   ├── Buildings
│   ├── Trees
│   └── Furniture
├── Hazards (instanced)
├── Car (with lights)
└── Camera
```

#### Camera System

```javascript
const camera = new THREE.PerspectiveCamera(
  75,                  // FOV
  w / h,              // Aspect ratio
  0.1,                // Near plane
  2000                // Far plane
)

// Follow car with offset
camera.position.x = car.x + Math.cos(car.h + Math.PI) * FOLLOW_DIST
camera.position.z = car.z + Math.sin(car.h + Math.PI) * FOLLOW_DIST
camera.position.y = 20
camera.lookAt(car.x, 10, car.z)
```

#### Level of Detail (LOD)

```javascript
function streamSet(scene, camera, world) {
  const cameraDist = Math.hypot(camera.x - car.x, camera.z - car.z)
  
  // Dynamic culling
  for (const building of world.buildings) {
    const dist = distanceTo(camera, building)
    if (dist > LOD_DISTANCE) {
      building.visible = false
    }
  }
  
  // Update instanced geometry counts
  updateInstanceCounts(world, cameraDist)
}
```

### 4. HUD System (game.js)

#### HUD Layout

```
┌─────────────────────────────────────────┐
│ Speed  Signal  Margin  │  🪟             │
│  45 km/h               │  Mirror         │
├─────────────────────────────────────────┤
│                                         │
│  3D Game View                           │
│                                         │
├─────────────────────────────────────────┤
│ [◄─ Steering Wheel ─►]  [⬆Accel] [⬇Brake]
└─────────────────────────────────────────┘
```

#### HUD Elements

```javascript
const HUD = {
  speed: 0,           // km/h
  signal: 0,          // Distance to metro in seconds
  margin: 0,          // Time before mudslide catches you
  route: 'A',         // Current route
  level: 'LEVEL_1',   // Current difficulty
  hazardsHit: 0       // Total hazards hit
}
```

## Building & Compilation

### Build Process

The game is compiled from source files into a single HTML artifact:

```bash
python3 build.py
```

**Build Steps:**

1. Read `game.src.html` template
2. Concatenate all `src/*.js` files
3. Minify JavaScript (optional)
4. Inline CSS styles
5. Write to `arranca-del-aluvion.html`

### Source Files Order (Critical)

The concatenation order matters:

1. `sim.js` - Utilities (no dependencies)
2. `world.js` - World data (depends on sim)
3. `gamecore.js` - Car/physics (depends on world)
4. `game.js` - Main loop (depends on gamecore, world)
5. `app.js` - Entry point (final execution)

## Performance Optimization

### Memory Usage

- **Geometry**: Reuse buffers, instancing where possible
- **Materials**: Share materials across objects
- **Textures**: Use atlasing or procedural generation
- **Draw calls**: Batch geometries to reduce calls

### Frame Rate Targets

- **Desktop (60 FPS)**: 16.67ms per frame
- **Mobile (60 FPS)**: 16.67ms per frame
- **Frame budget breakdown**:
  - Physics: 2-3ms
  - Rendering: 10-12ms
  - HUD updates: 1-2ms

### LOD Strategy

Distance-based visibility culling:

```javascript
const LOD_DISTANCES = {
  buildings: 200,
  trees: 150,
  obstacles: 100,
  furniture: 80
}

// Objects beyond distance are hidden
if (distToCam > LOD_DISTANCES[type]) {
  obj.visible = false
}
```

### Instancing

Repeated objects use THREE.InstancedMesh:

```javascript
const geometry = new THREE.BoxGeometry(1, 1, 1)
const material = new THREE.MeshStandardMaterial()
const instances = new THREE.InstancedMesh(geometry, material, 500)

// Set transforms per instance
for (let i = 0; i < 500; i++) {
  matrix.setPosition(x, y, z)
  instances.setMatrixAt(i, matrix)
}
```

## Debugging

### Browser DevTools

**Performance Tab:**
```
1. Open DevTools (F12)
2. Go to Performance tab
3. Click record
4. Play game for 10-20 seconds
5. Stop recording
6. Analyze frame times and flame graph
```

**Console Profiling:**
```javascript
// Log frame metrics
console.log(`FPS: ${60 / deltaTime}`)
console.log(`Memory: ${performance.memory.usedJSHeapSize / 1e6}MB`)
```

### Three.js Helper Tools

```javascript
// Wireframe mode
scene.traverse(obj => {
  if (obj.material) obj.material.wireframe = true
})

// Show axes
const axes = new THREE.AxesHelper(50)
scene.add(axes)

// Show camera frustum
const helper = new THREE.CameraHelper(camera)
scene.add(helper)
```

## Testing

### Unit Testing

For game logic, use Node.js + Jest:

```javascript
// test/gamecore.test.js
import * as GC from '../src/gamecore.js'

describe('Car Physics', () => {
  test('car accelerates', () => {
    const car = GC.createCar()
    const input = { up: true, left: false, right: false, down: false }
    
    GC.stepCar(car, input, 0.016)  // One frame at 60 FPS
    
    expect(car.v).toBeGreaterThan(0)
  })
})
```

### Integration Testing

Manual testing on devices:

```bash
# Start local server
python3 -m http.server 8000

# On mobile, navigate to:
# http://<YOUR_COMPUTER_IP>:8000/arranca-del-aluvion.html
```

**Test Checklist:**
- [ ] All 3 routes completable on desktop
- [ ] All 3 routes completable on mobile
- [ ] 60 FPS maintained throughout
- [ ] Hazards spawn and cause damage
- [ ] Sound on/off toggle works
- [ ] Mirror toggle reduces CPU load
- [ ] Touch controls responsive

## Common Tasks

### Add a New Route

```javascript
// In world.js
STREETS.push({
  name: 'Route D: Nueva vía',
  pts: [
    [600, -150],      // Start
    [500, -200],      // Waypoint 1
    [-600, -650]      // End
  ]
})

// In gamecore.js
const routes = {
  'D Nueva vía': buildRoute(4, -600)
}
```

### Add a New Hazard Type

```javascript
// In gamecore.js
const HAZARD_TYPES = {
  'flood': {
    name: 'flood water',
    color: 0x1f4f7f,
    effect: (car) => {
      car.v *= 0.5  // Slow significantly
      car.depth += 0.1
    }
  }
}
```

### Adjust Difficulty

```javascript
// In gamecore.js - LEVELS config
LEVELS = {
  'LEVEL_2': {
    density: 60,           // More hazards
    speed: 1.2,            // Mudslide faster
    timeout: 120           // Less time
  }
}
```

## Profiling Tools

### Triangle Count

```bash
python3 qa/run_tris.py
# Output:
# Geometry triangles: 2,145,000
# Hazard instances: 250
# Total: 2,145,250
```

### Hazard Distribution

```bash
python3 qa/run_hazards.py
# Output:
# Puddles: 85
# Potholes: 120
# Trees: 45
```

## Resources

- [Three.js Documentation](https://threejs.org/docs)
- [WebGL Specification](https://khronos.org/webgl/)
- [Game Engine Architecture](https://www.gameenginebook.com/)
- [Real-Time Rendering](https://www.realtimerendering.com/)

---

For questions, open an issue or contact j.carcamo.bustamante@gmail.com
