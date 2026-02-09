# Technical Architecture: Vault Haven

## 1. Technology Stack

### Recommended Stack: Web-First (TypeScript)

| Layer            | Technology                      | Rationale                                   |
|-----------------|---------------------------------|---------------------------------------------|
| **Language**     | TypeScript                     | Type safety, tooling, large ecosystem       |
| **Game Engine**  | Phaser 3 or PixiJS             | Mature 2D web engines, great performance    |
| **UI Framework** | React (overlay UI)             | HUD, menus, inventory — separate from game  |
| **State Mgmt**   | Zustand or custom ECS          | Lightweight, performant game state          |
| **Build Tool**   | Vite                           | Fast HMR, excellent TypeScript support      |
| **Backend**      | Node.js + Express (optional)   | Save sync, leaderboards, multiplayer later  |
| **Database**     | IndexedDB (local) / PostgreSQL | Local saves + optional cloud persistence    |
| **Testing**      | Vitest + Playwright            | Unit + E2E testing                          |
| **CI/CD**        | GitHub Actions                 | Automated build, test, deploy               |
| **Hosting**      | Vercel / Cloudflare Pages      | Edge deployment, fast global delivery       |

### Alternative: Unity (C#)

If native mobile is a higher priority, Unity with C# would be the alternative. The architecture below is designed for the web stack but the patterns translate well.

---

## 2. High-Level Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                        CLIENT                                 │
│                                                               │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────────────┐ │
│  │  Game Engine │  │  UI Layer    │  │  Audio Manager      │ │
│  │  (Phaser/   │  │  (React)     │  │  (Howler.js)        │ │
│  │   PixiJS)   │  │              │  │                     │ │
│  │             │  │  - HUD       │  │  - Music            │ │
│  │  - Renderer │  │  - Menus     │  │  - SFX              │ │
│  │  - Input    │  │  - Dialogs   │  │  - Ambient          │ │
│  │  - Physics  │  │  - Inventory │  │                     │ │
│  │  - Sprites  │  │  - Tooltips  │  │                     │ │
│  └──────┬──────┘  └──────┬───────┘  └──────────┬──────────┘ │
│         │                │                      │            │
│         └────────────────┼──────────────────────┘            │
│                          │                                    │
│                  ┌───────▼────────┐                           │
│                  │   Game State   │                           │
│                  │   Manager      │                           │
│                  │   (ECS Core)   │                           │
│                  └───────┬────────┘                           │
│                          │                                    │
│              ┌───────────┼───────────┐                       │
│              │           │           │                       │
│      ┌───────▼──┐ ┌─────▼────┐ ┌────▼──────┐               │
│      │ Save/Load│ │ Event Bus│ │ Simulation│               │
│      │ System   │ │          │ │ Engine    │               │
│      └───────┬──┘ └──────────┘ └───────────┘               │
│              │                                               │
│      ┌───────▼──────┐                                       │
│      │  IndexedDB    │                                       │
│      │  (Local Save) │                                       │
│      └───────┬───────┘                                       │
│              │                                               │
└──────────────┼───────────────────────────────────────────────┘
               │ (optional cloud sync)
       ┌───────▼───────┐
       │  Backend API   │
       │  (Node/Express)│
       └───────┬────────┘
               │
       ┌───────▼───────┐
       │  PostgreSQL    │
       │  (Cloud Saves) │
       └───────────────┘
```

---

## 3. Core Systems

### 3.1 Entity-Component-System (ECS)

The game uses an ECS architecture for performance and modularity:

**Entities** — Unique IDs representing game objects (dwellers, rooms, items)
**Components** — Data-only structs attached to entities
**Systems** — Logic that processes entities with specific component combinations

```
Entity: Dweller_042
├── PositionComponent    { roomId: "room_12", x: 3, y: 2 }
├── StatsComponent       { S: 5, H: 3, I: 7, F: 4, T: 6 }
├── HealthComponent      { current: 85, max: 100, radiation: 10 }
├── MoraleComponent      { happiness: 72, factors: [...] }
├── InventoryComponent   { weapon: "laser_pistol", armor: "vault_suit" }
└── AssignmentComponent  { roomId: "room_12", role: "worker" }
```

### 3.2 Simulation Engine

The simulation runs on a tick-based system:

- **Tick rate**: 1 tick = 1 game-second (configurable speed: 1x, 2x, 3x)
- **Production cycle**: Rooms produce resources every N ticks based on efficiency
- **Consumption cycle**: Resources consumed per dweller per tick
- **Event check**: Random event roll each tick (weighted probability)
- **Idle accumulation**: When offline, simulate elapsed time on next load (capped)

```
SimulationLoop:
  1. Calculate elapsed time since last tick
  2. Process resource production (all rooms)
  3. Process resource consumption (all dwellers)
  4. Update dweller states (health, morale, experience)
  5. Check event triggers (random + scheduled)
  6. Process combat (if active)
  7. Update exploration progress
  8. Emit state change events → UI updates
```

### 3.3 Room Management System

- Handles room creation, placement validation, merging, and upgrading
- Grid-based collision detection for placement
- Adjacency detection for room merging
- Power distribution graph (rooms disconnect when power runs out)

### 3.4 Event System (Pub/Sub)

A centralized event bus decouples game systems:

```
EventBus channels:
  - shelter:room:built
  - shelter:room:upgraded
  - shelter:room:destroyed
  - dweller:assigned
  - dweller:leveled
  - dweller:died
  - resource:produced
  - resource:depleted
  - combat:started
  - combat:ended
  - exploration:event
  - exploration:returned
  - ui:notification
```

### 3.5 Save/Load System

- **Auto-save**: Every 60 seconds to IndexedDB
- **Manual save**: Player-triggered, up to 3 slots
- **Save format**: JSON blob with version field for migration support
- **Offline progression**: On load, calculate and apply elapsed time production
- **Cloud sync** (optional): REST API to store/retrieve save data server-side

### 3.6 Input System

- **Desktop**: Mouse click/drag for room placement, keyboard shortcuts for speed/menus
- **Mobile**: Touch input with pinch-to-zoom and drag-to-pan
- **Unified input layer**: Abstracts mouse/touch into common pointer events

---

## 4. Rendering Architecture

### 4.1 Layer System

```
Z-Order (back to front):
  0. Background (underground texture, dirt layers)
  1. Room backgrounds (floor, walls)
  2. Room objects (furniture, machines)
  3. Dwellers (animated sprites)
  4. Room overlays (status effects, damage)
  5. UI overlay (HUD, resource bars, notifications)
  6. Menus/dialogs (modal overlays)
```

### 4.2 Camera System

- **Scrollable viewport**: Pan across the shelter grid
- **Zoom levels**: 3 levels (overview, normal, close-up)
- **Auto-focus**: Camera snaps to events (attacks, new rooms)
- **Minimap** (optional): Small overview of entire shelter

### 4.3 Sprite & Animation

- **Sprite sheets**: Dwellers, rooms, items packed into atlases
- **Animation states**: idle, walking, working, fighting, injured
- **Room animations**: Active rooms have ambient animations (steam, sparks, etc.)
- **Particle effects**: Fire, smoke, radiation glow, construction dust

---

## 5. Performance Targets

| Metric               | Target          |
|----------------------|-----------------|
| Frame rate           | 60 FPS (desktop), 30 FPS (mobile) |
| Initial load         | < 3 seconds     |
| Save/load time       | < 500ms         |
| Memory usage         | < 200MB         |
| Max dwellers         | 200             |
| Max rooms            | 150             |
| Tick processing      | < 16ms          |

### Optimization Strategies

- **Object pooling**: Reuse sprite objects for dwellers and particles
- **Culling**: Only render rooms visible in the viewport
- **Lazy loading**: Load room assets on first build, not at startup
- **Web Workers**: Offload simulation calculations to a worker thread
- **Delta compression**: Save files store only changes from base state

---

## 6. Project Structure

```
vault-haven/
├── public/
│   ├── assets/
│   │   ├── sprites/         # Sprite sheets and atlases
│   │   ├── audio/           # Music and sound effects
│   │   ├── fonts/           # Custom fonts
│   │   └── ui/              # UI graphics
│   └── index.html
├── src/
│   ├── core/                # Engine core
│   │   ├── ecs/             # Entity-Component-System
│   │   │   ├── Entity.ts
│   │   │   ├── Component.ts
│   │   │   ├── System.ts
│   │   │   └── World.ts
│   │   ├── EventBus.ts      # Pub/Sub event system
│   │   ├── GameLoop.ts      # Main game loop / tick manager
│   │   └── SaveManager.ts   # Save/load/auto-save
│   ├── components/          # ECS Components (data)
│   │   ├── Position.ts
│   │   ├── Stats.ts
│   │   ├── Health.ts
│   │   ├── Morale.ts
│   │   ├── Inventory.ts
│   │   ├── Assignment.ts
│   │   ├── RoomData.ts
│   │   └── Exploration.ts
│   ├── systems/             # ECS Systems (logic)
│   │   ├── ProductionSystem.ts
│   │   ├── ConsumptionSystem.ts
│   │   ├── CombatSystem.ts
│   │   ├── ExplorationSystem.ts
│   │   ├── MoraleSystem.ts
│   │   ├── EventSystem.ts
│   │   └── RenderSystem.ts
│   ├── entities/            # Entity factories
│   │   ├── DwellerFactory.ts
│   │   ├── RoomFactory.ts
│   │   └── ItemFactory.ts
│   ├── data/                # Static game data (JSON configs)
│   │   ├── rooms.json       # Room definitions, costs, stats
│   │   ├── items.json       # Weapon/armor/item definitions
│   │   ├── events.json      # Event definitions and triggers
│   │   ├── names.json       # Name generation data
│   │   └── tech-tree.json   # Research/unlock progression
│   ├── rendering/           # Rendering layer
│   │   ├── ShelterRenderer.ts
│   │   ├── DwellerRenderer.ts
│   │   ├── RoomRenderer.ts
│   │   ├── ParticleManager.ts
│   │   └── Camera.ts
│   ├── ui/                  # React UI components
│   │   ├── HUD.tsx          # Resource bars, clock, alerts
│   │   ├── BuildMenu.tsx    # Room construction menu
│   │   ├── DwellerPanel.tsx # Selected dweller details
│   │   ├── RoomPanel.tsx    # Selected room details
│   │   ├── ExplorePanel.tsx # Exploration management
│   │   ├── SettingsMenu.tsx # Game settings
│   │   └── EventLog.tsx     # Scrolling event notifications
│   ├── audio/               # Audio management
│   │   └── AudioManager.ts
│   ├── utils/               # Shared utilities
│   │   ├── random.ts        # Seeded RNG
│   │   ├── grid.ts          # Grid math helpers
│   │   ├── time.ts          # Time formatting, offline calc
│   │   └── constants.ts     # Game-wide constants
│   ├── types/               # TypeScript type definitions
│   │   ├── game.types.ts
│   │   ├── ecs.types.ts
│   │   └── ui.types.ts
│   ├── Game.ts              # Game initialization and orchestration
│   └── main.ts              # Entry point
├── tests/
│   ├── unit/                # Unit tests
│   └── e2e/                 # End-to-end tests
├── docs/                    # Documentation
├── package.json
├── tsconfig.json
├── vite.config.ts
└── README.md
```

---

## 7. Key Technical Decisions

### Why ECS over OOP?

- **Performance**: Data-oriented design enables cache-friendly iteration
- **Flexibility**: Mix-and-match components without deep inheritance
- **Scalability**: Adding new features = adding new components + systems
- **Serialization**: Components are plain data, trivially serializable for save/load

### Why Phaser 3 / PixiJS?

- **Phaser 3**: Full game framework with physics, input, audio, scene management built-in. Best for rapid development.
- **PixiJS**: Lower-level renderer with better performance ceiling. Best if we want full control over game loop.
- **Recommendation**: Start with **Phaser 3** for faster prototyping, evaluate PixiJS migration if performance becomes a concern at scale.

### Why React for UI?

- Game canvas handles the shelter view and dweller rendering
- React handles all overlay UI (menus, panels, HUD, dialogs)
- Clean separation: game engine never touches DOM, UI layer never touches canvas
- React's component model is perfect for complex, nested game UI

### Why Zustand for State?

- Minimal boilerplate compared to Redux
- Works well alongside the ECS (Zustand for UI state, ECS for game state)
- Built-in devtools and middleware support
- Tiny bundle size (~1KB)
