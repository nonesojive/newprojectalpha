# Art & UI Plan: Vault Haven

## 1. Visual Style

### Art Direction

- **Style**: Pixel art with a modern twist (16x16 or 32x32 base tiles, scaled up)
- **Palette**: Warm underground tones (earthy browns, industrial grays) contrasted with colorful room interiors
- **Mood**: Cozy-yet-tense — the shelter should feel like a home worth protecting
- **Inspiration**: Fallout Shelter's clean cross-section, RimWorld's readable layouts, Stardew Valley's warm pixel art

### Color Language

| Context              | Colors                          | Purpose                        |
|---------------------|---------------------------------|--------------------------------|
| Underground/dirt     | #4A3728, #5C4033, #6B4F3A     | Background, excavated areas    |
| Rock/deep layers     | #3E3E3E, #555555, #6E6E6E     | Deeper floor backgrounds       |
| Room floors          | #8B8B8B, #A0A0A0               | Clean, industrial feel         |
| Power rooms          | #FFD700, #FFA500               | Energy, electricity            |
| Food rooms           | #4CAF50, #8BC34A               | Growth, nourishment            |
| Water rooms          | #2196F3, #03A9F4               | Clean water, purity            |
| Medical rooms        | #F44336, #E91E63               | Health, urgency                |
| Tech/research rooms  | #9C27B0, #673AB7               | Knowledge, advancement         |
| Defense rooms        | #795548, #607D8B               | Strength, protection           |
| UI accent            | #00BCD4                         | Teal — primary action color    |
| UI warning           | #FF9800                         | Orange — attention needed      |
| UI danger            | #F44336                         | Red — critical alerts          |
| UI success           | #4CAF50                         | Green — positive outcomes      |

### Sprite Specifications

| Asset Type      | Base Size   | Scale | Notes                              |
|----------------|-------------|-------|------------------------------------|
| Grid cell       | 32x32 px    | 2x    | 64x64 rendered                     |
| Dweller         | 16x32 px    | 2x    | 32x64 rendered (tall sprites)      |
| Room furniture  | 16x16 px    | 2x    | Scaled to fit room grid            |
| Items/icons     | 16x16 px    | 1x    | UI inventory icons                 |
| Enemy sprites   | 16x32 px    | 2x    | Match dweller proportions          |
| Particles       | 4x4 px      | 1x    | Fire, sparks, radiation            |

---

## 2. UI Layout

### Main Game Screen

```
┌─────────────────────────────────────────────────────────┐
│  ┌─────────────────────────────────────────────────┐    │
│  │              TOP HUD BAR                         │    │
│  │  ⚡ 120/200  🍖 85/150  💧 90/150  💰 1,250   │    │
│  │  👤 24/30    📅 Day 47   ⏩ 2x    ⚙️ Settings │    │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
│  ┌──────────────────────────────────────────┐  ┌──────┐ │
│  │                                          │  │      │ │
│  │           GAME VIEWPORT                  │  │ SIDE │ │
│  │         (Shelter Cross-Section)          │  │PANEL │ │
│  │                                          │  │      │ │
│  │    ┌────┐┌────┐┌────┐                   │  │Dwellr│ │
│  │    │Gen ││Cafe││H2O │   Floor 1         │  │ or   │ │
│  │    └────┘└────┘└────┘                   │  │Room  │ │
│  │    ┌────┐┌────────────┐                 │  │Detail│ │
│  │    │Elev││  Living    │   Floor 2       │  │      │ │
│  │    └────┘└────────────┘                 │  │      │ │
│  │    ┌────┐┌────┐                         │  │      │ │
│  │    │Elev││Med │         Floor 3         │  │      │ │
│  │    └────┘└────┘                         │  │      │ │
│  │                                          │  │      │ │
│  └──────────────────────────────────────────┘  └──────┘ │
│                                                          │
│  ┌─────────────────────────────────────────────────┐    │
│  │              BOTTOM ACTION BAR                   │    │
│  │  [🔨 Build] [👤 Dwellers] [🗺 Explore] [📜 Log]│    │
│  └─────────────────────────────────────────────────┘    │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Mobile Layout (Portrait)

```
┌───────────────────────┐
│  ⚡120 🍖85 💧90 💰1250│  ← Compact HUD
│  👤24/30  Day 47  ⏩2x │
├───────────────────────┤
│                       │
│    GAME VIEWPORT      │
│    (touch to pan,     │
│     pinch to zoom)    │
│                       │
│   ┌────┐┌────┐┌────┐ │
│   │Gen ││Cafe││H2O │ │
│   └────┘└────┘└────┘ │
│   ┌────┐┌──────────┐ │
│   │Elev││ Living   │ │
│   └────┘└──────────┘ │
│                       │
├───────────────────────┤
│ [Build][People][Map]  │  ← Bottom tabs
│                       │
│  ┌─────────────────┐  │
│  │  Slide-up Panel  │ │  ← Detail panel
│  │  (room/dweller)  │ │     (swipe up)
│  └─────────────────┘  │
└───────────────────────┘
```

---

## 3. UI Screens & Components

### 3.1 HUD (Always Visible)

- **Resource bars**: Power, Food, Water with fill indicators and color coding
- **Caps counter**: Currency with icon
- **Population**: Current/max with dweller icon
- **Day counter**: Current in-game day
- **Speed controls**: 1x, 2x, 3x toggle buttons
- **Settings gear**: Opens settings overlay
- **Alert indicator**: Flashing icon when events need attention

### 3.2 Build Menu

```
┌─────────────────────────────────┐
│  BUILD MENU                  ✕  │
├─────────────────────────────────┤
│ [Production] [Utility] [Defense]│  ← Category tabs
│ [Services]  [Social]  [Craft]  │
├─────────────────────────────────┤
│ ┌─────────────────────────────┐ │
│ │ 🔋 Power Generator          │ │
│ │    Produces power            │ │
│ │    Cost: 100 caps            │ │
│ │    Requires: Strength        │ │
│ │    [Build]                   │ │
│ ├─────────────────────────────┤ │
│ │ 🍳 Cafeteria                │ │
│ │    Produces food             │ │
│ │    Cost: 100 caps            │ │
│ │    Requires: Handiness       │ │
│ │    [Build]                   │ │
│ ├─────────────────────────────┤ │
│ │ 🔒 Greenhouse (Locked)      │ │
│ │    Requires: Tier 2 Tech     │ │
│ └─────────────────────────────┘ │
└─────────────────────────────────┘
```

### 3.3 Dweller Panel (Side Panel)

```
┌─────────────────────┐
│  DWELLER DETAILS  ✕ │
├─────────────────────┤
│  [Avatar]           │
│  Jane "Sparks" Chen │
│  Level 12           │
│  ████████░░ XP      │
├─────────────────────┤
│  STATS (S.H.I.F.T.) │
│  S: ████████░░  8   │
│  H: ██████░░░░  6   │
│  I: ████░░░░░░  4   │
│  F: ██████████  10  │
│  T: ██████░░░░  6   │
├─────────────────────┤
│  HEALTH             │
│  HP: █████████░ 90% │
│  RAD: ██░░░░░░ 20%  │
│  😊 Happiness: 78%  │
├─────────────────────┤
│  EQUIPMENT          │
│  🗡 Laser Pistol    │
│  🛡 Reinforced Suit │
│  💍 Lucky Charm     │
├─────────────────────┤
│  TRAITS             │
│  🌿 Green Thumb     │
│  💪 Iron Will       │
├─────────────────────┤
│  ASSIGNED TO:       │
│  Cafeteria (F1)     │
│  [Unassign] [Equip] │
└─────────────────────┘
```

### 3.4 Room Panel (Side Panel)

```
┌─────────────────────┐
│  ROOM DETAILS     ✕ │
├─────────────────────┤
│  🔋 Power Generator │
│  Tier 2 / 3         │
│  Floor 1, Col 1-2   │
├─────────────────────┤
│  OUTPUT              │
│  ⚡ 12 power/cycle   │
│  Efficiency: 85%     │
│  ████████░░ Cycle    │
├─────────────────────┤
│  WORKERS (2/4)       │
│  👤 Jane Chen (S:8)  │
│  👤 Tom Blake (S:5)  │
│  [+ Assign Worker]   │
├─────────────────────┤
│  HEALTH              │
│  ██████████ 100%     │
├─────────────────────┤
│  [⬆ Upgrade: 500💰]  │
│  [🗑 Demolish]       │
└─────────────────────┘
```

### 3.5 Exploration Panel

```
┌─────────────────────────────────┐
│  EXPLORATION                 ✕  │
├─────────────────────────────────┤
│  ACTIVE MISSIONS (2/3)          │
│                                 │
│  ┌─────────────────────────────┐│
│  │ 🗺 Surface Scavenge         ││
│  │ 👤 Mike, Sarah              ││
│  │ Status: Exploring (2h left) ││
│  │ Loot: 3 items found         ││
│  │ [View Log] [Recall]         ││
│  ├─────────────────────────────┤│
│  │ ⚔ Abandoned Factory (Quest) ││
│  │ 👤 Rex, Luna, Drake         ││
│  │ Status: In Combat           ││
│  │ Progress: ████░░ 60%        ││
│  │ [View Log] [Recall]         ││
│  └─────────────────────────────┘│
│                                 │
│  [+ Send New Expedition]        │
│                                 │
│  DISCOVERED LOCATIONS           │
│  📍 Abandoned Factory ⚔⚔⚔     │
│  📍 Old Mall ⚔⚔               │
│  📍 Radio Tower ⚔              │
└─────────────────────────────────┘
```

### 3.6 Event Notification (Toast)

```
┌──────────────────────────────┐
│ ⚠️ RAIDER ATTACK!            │
│ Raiders are approaching       │
│ your vault entrance!          │
│ [Defend] [View]              │
└──────────────────────────────┘
```

### 3.7 Event Log

```
┌─────────────────────────────────┐
│  EVENT LOG                   ✕  │
├─────────────────────────────────┤
│  Day 47, 14:32                  │
│  ⚔ Raider attack repelled.     │
│     2 raiders defeated.         │
│     Loot: 50 caps, Pipe Rifle   │
│                                 │
│  Day 47, 10:15                  │
│  👤 New survivor arrived:       │
│     Marcus Webb (S:6 H:4 I:7)  │
│                                 │
│  Day 46, 22:00                  │
│  🗺 Exploration team returned.  │
│     Found: Combat Armor, 120💰  │
│                                 │
│  Day 46, 18:45                  │
│  🔥 Fire in Cafeteria!          │
│     Extinguished by Tom Blake.  │
│     Minor damage to room.       │
└─────────────────────────────────┘
```

---

## 4. Animation Plan

### Dweller Animations

| State      | Frames | Speed    | Description                           |
|-----------|--------|----------|---------------------------------------|
| Idle       | 4      | 0.5 FPS  | Slight breathing/shifting             |
| Walking    | 6      | 8 FPS    | Moving between rooms                  |
| Working    | 4      | 2 FPS    | Arm movement, tool use                |
| Fighting   | 6      | 10 FPS   | Attack swing/shoot                    |
| Injured    | 2      | 1 FPS    | Limping/clutching side                |
| Happy      | 4      | 2 FPS    | Bouncy idle with smile                |
| Sad        | 2      | 0.5 FPS  | Slumped posture                       |

### Room Animations

| Room Type        | Animation                                |
|-----------------|------------------------------------------|
| Power Generator | Spinning turbine, electrical sparks      |
| Cafeteria       | Steam from pots, flickering stove        |
| Water Pump      | Water flow, dripping pipes               |
| Medbay          | Heart monitor beep, blinking lights      |
| Research Lab    | Bubbling beakers, scrolling screens      |
| Workshop        | Hammer strikes, forge glow               |

### Effects

| Effect          | Type        | Trigger                          |
|----------------|-------------|----------------------------------|
| Construction   | Particles   | Building a new room              |
| Fire           | Animated    | Fire event in a room             |
| Radiation      | Glow/pulse  | Irradiated dweller or area       |
| Level up       | Burst       | Dweller levels up                |
| Loot drop      | Bounce      | Item received                    |
| Resource +     | Float text  | Resource produced (+5 ⚡)        |

---

## 5. Audio Plan

### Music Tracks

| Track           | Context              | Mood                    | BPM    |
|----------------|----------------------|-------------------------|--------|
| Main Theme     | Title screen         | Hopeful, atmospheric    | 90     |
| Shelter Calm   | Normal gameplay      | Ambient, cozy           | 70     |
| Shelter Busy   | High activity        | Upbeat, industrious     | 100    |
| Combat         | Raid/attack events   | Tense, urgent           | 130    |
| Exploration    | Surface missions     | Mysterious, adventurous | 85     |
| Night/Idle     | Low activity period  | Peaceful, sparse        | 60     |

### Sound Effects Categories

- **UI**: Button click, menu open/close, tab switch, notification pop
- **Construction**: Hammering, drilling, room completion chime
- **Production**: Machine hum, water flow, cooking sizzle, generator buzz
- **Combat**: Gunshot, melee hit, explosion, shield block, death cry
- **Events**: Alarm siren, fire crackle, radio static, door knock
- **Dweller**: Footsteps, happy cheer, sad sigh, level-up fanfare
- **Ambient**: Underground rumble, distant machinery, dripping water

---

## 6. Asset Pipeline

### Art Creation Strategy

**Option A: Custom Pixel Art** (recommended for unique identity)
- Use Aseprite for sprite creation and animation
- Create a base dweller template with modular parts (hair, clothes, skin)
- Room tiles as modular pieces (walls, floors, furniture)

**Option B: Asset Pack + Custom** (faster start)
- Purchase/use open pixel art packs for base assets
- Customize and extend with original art
- Suggested packs: [to be researched during Phase 1]

**Option C: AI-Assisted** (experimental)
- Use AI image generation for concept art and backgrounds
- Manually clean up and pixel-ify for consistency
- Best for backgrounds and promotional art, not sprites

### Asset Organization

```
public/assets/
├── sprites/
│   ├── dwellers/
│   │   ├── dweller-base.png        # Base sprite sheet
│   │   ├── dweller-hair.png        # Hair variations overlay
│   │   ├── dweller-clothes.png     # Clothing variations overlay
│   │   └── dweller-anims.json      # Animation frame data
│   ├── rooms/
│   │   ├── room-tiles.png          # Room wall/floor tiles
│   │   ├── room-objects/           # Furniture sprite sheets per room type
│   │   └── room-anims.json         # Room animation data
│   ├── enemies/
│   │   ├── raider.png
│   │   ├── mutant-rat.png
│   │   └── ...
│   ├── items/
│   │   └── item-icons.png          # Item icon atlas
│   └── effects/
│       ├── particles.png           # Particle sprite sheet
│       └── effects-anims.json
├── audio/
│   ├── music/
│   │   ├── theme-main.ogg
│   │   ├── shelter-calm.ogg
│   │   ├── combat.ogg
│   │   └── exploration.ogg
│   └── sfx/
│       ├── ui/
│       ├── combat/
│       ├── construction/
│       └── ambient/
├── fonts/
│   ├── pixel-main.woff2           # Primary game font
│   └── pixel-ui.woff2             # UI/HUD font
└── ui/
    ├── icons/                      # UI icons (resource types, etc.)
    ├── panels/                     # Panel backgrounds/borders
    └── buttons/                    # Button states (normal, hover, pressed)
```
