# Architecture - Arranca del Aluvión

Detailed technical architecture documentation for the Three.js game engine.

## System Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                     Game Application                         │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Input       │  │  Game Loop   │  │  Rendering   │      │
│  │  System      │  │  (60 FPS)    │  │  Engine      │      │
│  │              │  │              │  │              │      │
│  │ • Touch      │  │ • Physics    │  │ • Three.js   │      │
│  │ • Keyboard   │  │ • Hazards    │  │ • Shaders    │      │
│  │ • Gamepad    │  │ • Collision  │  │ • LOD        │      │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘      │
│         │                 │                  │              │
│         └────────────────┬┴──────────────────┘              │
│                          │                                  │
├──────────────────────────┴──────────────────────────────────┤
│                    Core Game State                          │
│                                                              │
│  • Car (position, velocity, damage)                         │
│  • Hazards (positions, types, active)                       │
│  • Mudslide (position, speed)                               │
│  • Score (time, collisions, hazards)                        │
└──────────────────────────────────────────────────────────────┘
         │
         ▼
┌─────────────────────────────────────────────────────────────┐
│             Three.js Scene Graph                            │
│                                                              │
│  Scene                                                       │
│  ├── Camera (follow car)                                    │
│  ├── Lights                                                 │
│  │   ├── AmbientLight                                       │
│  │   └── DirectionalLight (sun)                             │
│  ├── Environment                                            │
│  │   ├── Sky (procedural)                                   │
│  │   └── Fog (mudslide veil)                                │
│  ├── World                                                  │
│  │   ├── Streets                                            │
│  │   ├── Buildings (LOD)                                    │
│  │   ├── Trees (instanced)                                  │
│  │   └── Furniture                                          │
│  ├── Hazards                                                │
│  │   ├── InstancedMesh (puddles)                            │
│  │   ├── InstancedMesh (potholes)                           │
│  │   └── Group (obstacles - trees, cars)                    │
│  ├── Car                                                    │
│  │   ├── Geometry (body, wheels)                            │
│  │   ├── PBR Material (paint)                               │
│  │   └── Lights (headlights, taillights)                    │
│  └── Minimap                                                │
│      └── CSS overlay HUD                                    │
└─────────────────────────────────────────────────────────────┘
```

## Data Flow

### Game Loop

```
┌─ Start Frame ──────────────────────────┐
│                                         │
├─ Check Input ─────────────────────────┤
│  • Read keyboard/touch/gamepad        │
│  • Update control state               │
│                                        │
├─ Update Physics ──────────────────────┤
│  • Car acceleration/deceleration      │
│  • Car steering/heading               │
│  • Car position update                │
│  • Collision detection                │
│                                        │
├─ Update Hazards ──────────────────────┤
│  • Spawn new hazards at distance      │
│  • Update hazard positions            │
│  • Check car-hazard collisions        │
│  • Remove far hazards                 │
│                                        │
├─ Update Mudslide ─────────────────────┤
│  • Move mudslide forward              │
│  • Check if car caught                │
│                                        │
├─ Update Camera ────────────────────────┤
│  • Follow car with offset             │
│  • Calculate viewing angle            │
│                                        │
├─ Render Frame ────────────────────────┤
│  • Update LOD based on distance       │
│  • Render scene to canvas             │
│  • Apply post-processing              │
│                                        │
├─ Update HUD ──────────────────────────┤
│  • Speed display                      │
│  • Signal strength (distance)         │
│  • Time to mudslide                   │
│  • Collision counter                  │
│                                        │
└─ End Frame ───────────────────────────┘
     ↓
  Repeat (target: 16.67ms per frame)
```

### State Management

```javascript
// Global game state
const gameState = {
  status: 'playing',        // 'playing', 'won', 'caught', 'timeout'
  time: 0,                  // Elapsed time (seconds)
  
  // Entity state
  car: {                    // Player vehicle
    x, z, v, h,           // Position, velocity, heading
    depth, crashes,       // Damage, collision count
    puddles, holes        // Hazard interactions
  },
  
  hazards: {               // Environmental threats
    list: [],             // All active hazards
    falls: [],            // Falling trees
    spawned: 0            // Total created
  },
  
  mudslide: {              // Chase threat
    x,                    // Mudslide leading edge position
    speed: 1.2,          // Advance speed (level-dependent)
    caught: false
  },
  
  score: {                 // Player performance
    time: 0,              // Time survived
    collisions: 0,        // Objects hit
    level: 1,             // Current difficulty
    route: 'A'            // Current path
  }
}
```

## Module Details

### sim.js (Utilities)

**Responsibilities:**
- Mathematical helpers
- Vector operations
- Random number generation
- Debug utilities

**Key Functions:**

```javascript
// Random in range
rnd(min, max) → number

// Vector distance
dist(x1, z1, x2, z2) → number

// Angle normalization
norm(angle) → radians [-π, π]

// Interpolation
lerp(a, b, t) → number

// Noise functions
perlin(x, y) → number [0, 1]
```

### world.js (Map Definition)

**Responsibilities:**
- Street/path definitions
- Landmark positions
- Procedural generation parameters
- World constants

**Key Data:**

```javascript
STREETS = [
  {
    name: 'Av. General Blanche',
    pts: [[x1, z1], [x2, z2], ...]  // Path points
  },
  // ...
]

LANDMARKS = {
  'START': { x, z },      // Player start
  'METRO': { x, z, r },   // Goal (radius)
  // ...
}

BUILDING_TEMPLATES = [
  proto1, proto2, proto3  // 3D geometry definitions
]
```

### gamecore.js (Physics & Systems)

**Responsibilities:**
- Car physics simulation
- Hazard management
- Level definitions
- Collision detection

**Key Functions:**

```javascript
// Entity lifecycle
createCar() → Car
makeHazards() → HazardSystem
makeObstacles() → Obstacle[]

// Physics updates
stepCar(car, input, dt, env) → void
checkHazards(car, env) → void

// Level management
setHazardLevel(hazards, level) → void
computeArrival(level) → ArrivalGrid

// Collision
sampleT(grid, x, z) → number  // Time to metro
```

**Physics Constants:**

```javascript
const ACCEL = 45        // m/s²
const BRAKE = 60        // m/s² deceleration
const MAX_SPEED = 30    // m/s
const TURN_RATE = 3.5   // rad/s
const FRICTION = 0.94   // per frame
const MUDSLIDE_SPEED = 15 // m/s (varies by level)

const HAZARD_RADIUS = {
  puddle: 8,
  pothole: 6,
  tree: 4,
  car: 5
}
```

### game.js (Main Game Loop)

**Responsibilities:**
- Scene initialization
- Game loop orchestration
- Camera management
- HUD updates
- Input handling

**Key Functions:**

```javascript
// Initialization
async function main() {
  // Setup Three.js scene
  // Load assets
  // Create world
  // Start game loop
}

// Game loop (called each frame)
function update(dt) {
  // Physics
  // Rendering
  // HUD
}

// Input handlers
function onKeyDown(event) { ... }
function onTouchMove(event) { ... }
function onInput(input) { ... }
```

### app.js (Entry Point)

**Responsibilities:**
- Bootstrap application
- DOMContentLoaded handler
- Resource loading
- Error handling

## Rendering Pipeline

### Shader System

**Vertex Shader:**
```glsl
// Transforms vertices to screen space
// Applies: position, normal, color, texture
varying vec3 vNormal;
varying vec3 vPosition;

void main() {
  vNormal = normalize(normalMatrix * normal);
  vPosition = (modelViewMatrix * vec4(position, 1.0)).xyz;
  gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
}
```

**Fragment Shader:**
```glsl
// Computes pixel color
// Applies: lighting, materials, effects
varying vec3 vNormal;
varying vec3 vPosition;

void main() {
  vec3 normal = normalize(vNormal);
  vec3 light = normalize(lightPosition - vPosition);
  float diffuse = max(dot(normal, light), 0.0);
  
  gl_FragColor = vec4(baseColor * (0.3 + 0.7 * diffuse), 1.0);
}
```

### Material System

**PBR Materials:**

```javascript
const carMaterial = new THREE.MeshPhysicalMaterial({
  color: 0xff2200,        // Base color
  metalness: 0.8,         // Metal reflectivity
  roughness: 0.1,         // Surface smoothness
  clearcoat: 1.0,         // Top coat intensity
  clearcoatRoughness: 0.05, // Coat smoothness
  envMap: envTexture      // Environment reflection
})
```

**Custom Shader Material:**

```javascript
const customMaterial = new THREE.ShaderMaterial({
  vertexShader: vertexCode,
  fragmentShader: fragmentCode,
  uniforms: {
    uTime: { value: 0 },
    uSurfaceType: { value: 0 }
  }
})
```

## Performance Optimizations

### 1. Geometry Instancing

Instead of creating 500 tree objects:

```javascript
// ❌ Inefficient
for (let i = 0; i < 500; i++) {
  const tree = new THREE.Mesh(geometry, material)
  tree.position.set(x, y, z)
  scene.add(tree)  // 500 draw calls!
}

// ✅ Efficient
const instanced = new THREE.InstancedMesh(geometry, material, 500)
for (let i = 0; i < 500; i++) {
  const matrix = new THREE.Matrix4()
  matrix.setPosition(x, y, z)
  instanced.setMatrixAt(i, matrix)
}
scene.add(instanced)  // 1 draw call!
```

### 2. Level of Detail (LOD)

```javascript
function updateLOD(camera, world) {
  for (const building of world.buildings) {
    const dist = camera.position.distanceTo(building.position)
    
    if (dist < 100) {
      building.lodLevel = 0  // High detail
      building.visible = true
    } else if (dist < 300) {
      building.lodLevel = 1  // Medium detail
      building.visible = true
    } else {
      building.visible = false  // Culled
    }
  }
}
```

### 3. Polygon Offset

Prevent z-fighting between overlapping geometries:

```javascript
material.polygonOffset = true
material.polygonOffsetFactor = 1
material.polygonOffsetUnits = 1
```

### 4. Frustum Culling

Only render objects within camera view:

```javascript
camera.updateMatrixWorld()
const frustum = new THREE.Frustum()
frustum.setFromProjectionMatrix(
  new THREE.Matrix4().multiplyMatrices(
    camera.projectionMatrix,
    camera.matrixWorldInverse
  )
)

if (frustum.containsPoint(object.position)) {
  object.visible = true  // In view
}
```

## Memory Layout

### Typical Memory Usage

```
Three.js Library:          ~5 MB
Textures & Materials:      ~10 MB
Geometry Buffers:          ~40 MB
Scene Graph:               ~15 MB
Game State:                ~2 MB
Hazard Arrays:             ~8 MB
Browser Overhead:          ~20 MB
─────────────────────────────
Total:                     ~100 MB (typical)
Peak:                      ~150 MB (hazard-heavy)
```

## Bottlenecks & Solutions

| Issue | Symptom | Solution |
|-------|---------|----------|
| Too many draw calls | Low FPS, GPU stalling | Use instancing, batching |
| Excessive poly count | Frame drops on mobile | Enable LOD culling |
| Texture thrashing | Stutter, memory spikes | Use atlasing, mipmaps |
| Physics loop lag | Inconsistent car movement | Reduce hazard checks/frame |
| Memory bloat | App crash on mobile | Clear unused geometries, cap hazards |

## Future Optimization Ideas

- [ ] **WebWorkers**: Move physics calculations off main thread
- [ ] **WASM**: Implement physics in WebAssembly for speed
- [ ] **Texture Atlasing**: Combine textures to reduce bindings
- [ ] **Baked Lighting**: Pre-compute light maps
- [ ] **Mesh Compression**: Use draco compression
- [ ] **Streaming**: Load/unload map sections dynamically

## Testing Strategy

```
Unit Tests (sim.js, gamecore.js)
    ↓
Integration Tests (all systems together)
    ↓
Performance Tests (FPS, memory)
    ↓
Cross-device Tests (desktop, mobile)
    ↓
User Acceptance Testing (gameplay feel)
```

## Deployment Checklist

- [ ] Triangle count < 2.5M
- [ ] Frame rate = 60 FPS (min)
- [ ] Load time < 3s
- [ ] File size < 250 KB
- [ ] Mobile tested
- [ ] Desktop tested
- [ ] All routes completable
- [ ] No console errors
- [ ] Accessibility checked

---

For architecture questions, see DEVELOPMENT.md or open an issue.
