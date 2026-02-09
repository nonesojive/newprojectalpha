# Game Design Document: Vault Haven

## 1. Game Overview

**Title:** Vault Haven (working title)
**Genre:** Simulation / Management / Survival
**Platform:** Web (desktop & mobile browsers), with potential for native mobile
**Inspiration:** Fallout Shelter, RimWorld, Oxygen Not Included
**Perspective:** 2D side-view cross-section (ant-farm style)

### Elevator Pitch

Build and manage an underground shelter in a post-apocalyptic world. Assign survivors to rooms, produce resources, defend against threats, and expand your vault deeper underground. Balance the needs of your growing population against dwindling resources and an increasingly hostile surface world.

### Core Fantasy

The player is the Overseer of a shelter, responsible for the survival and prosperity of a small community of survivors. Every decision matters — from room placement to resource allocation to who gets sent on dangerous surface expeditions.

---

## 2. Core Gameplay Loop

```
┌─────────────────────────────────────────────────┐
│                  CORE LOOP                       │
│                                                  │
│   Build Rooms ──► Assign Survivors ──► Produce   │
│       ▲              Resources                   │
│       │                  │                       │
│       │                  ▼                       │
│   Expand/Upgrade ◄── Manage Threats ◄── Explore  │
│                                                  │
└─────────────────────────────────────────────────┘
```

### Loop Breakdown

1. **Build** — Construct new rooms to expand shelter capacity and capabilities
2. **Assign** — Place survivors in rooms matching their skills for optimal output
3. **Produce** — Rooms generate resources over time (power, food, water, materials)
4. **Manage** — Handle events, threats, survivor morale, and resource shortages
5. **Explore** — Send survivors to the surface for loot, encounters, and story
6. **Expand** — Use gathered resources to build more, upgrade, and grow

---

## 3. Game Mechanics

### 3.1 Shelter Construction

- **Grid-based layout**: The shelter is built on a 2D grid, expanding horizontally and vertically (deeper underground)
- **Room types**: Each room serves a specific purpose (see Section 4)
- **Room merging**: Adjacent rooms of the same type can merge into larger, more efficient rooms (up to 3-wide)
- **Room upgrading**: Rooms can be upgraded (3 tiers) to improve output and capacity
- **Elevators**: Required to connect floors vertically
- **Dirt/Rock layers**: Deeper floors require more resources to excavate, but unlock advanced room types

### 3.2 Resource Management

| Resource     | Source               | Usage                              | Depletion Effect           |
|-------------|----------------------|------------------------------------|----------------------------|
| **Power**   | Power generators     | Keeps rooms operational            | Rooms shut down             |
| **Food**    | Farms, kitchens      | Feeds survivors                    | Survivors lose health       |
| **Water**   | Water treatment      | Hydrates survivors                 | Survivors get irradiated    |
| **Caps**    | Trade, quests        | Currency for building/upgrading    | Cannot expand               |
| **Materials** | Scavenging, recycling | Crafting weapons/armor/items     | Cannot craft                |
| **Tech**    | Research labs         | Unlocking new room types/upgrades  | Progress stalls             |

### 3.3 Survivor System

Each survivor (called **Dwellers**) has:

- **Name**: Procedurally generated or player-assigned
- **Stats (S.H.I.F.T.):**
  - **S**trength — Power production, melee combat
  - **H**andiness — Construction speed, repair efficiency
  - **I**ntellect — Research output, medical effectiveness
  - **F**ortitude — Health pool, radiation resistance
  - **T**enacity — Exploration endurance, morale recovery
- **Level**: 1–50, gained through work and combat
- **Health**: Depletes from combat, starvation, dehydration, radiation
- **Morale**: Affected by living conditions, events, workload
- **Equipment**: Weapon slot, armor slot, accessory slot
- **Traits**: Special abilities gained through leveling or events (e.g., "Green Thumb" — +20% food production)

### 3.4 Combat System

- **Defense events**: Raiders, mutant creatures, or internal incidents (fires, infestations)
- **Room-by-room combat**: Attackers move through rooms; survivors in each room fight them
- **Auto-combat with player intervention**: Survivors fight automatically, but the player can:
  - Use consumables (stimpaks, rad-away)
  - Relocate survivors to reinforce rooms
  - Activate room defenses
- **Damage calculation**: Based on weapon damage, survivor stats, and armor values
- **Surface combat**: Turn-based encounters during exploration missions

### 3.5 Exploration & Quests

- **Surface exploration**: Send 1–3 dwellers out with supplies; they encounter events over real time
- **Exploration log**: Text-based event log with loot, combat, story snippets
- **Quest locations**: Discoverable points of interest with multi-room dungeon-style encounters
- **Risk/reward**: Better loot in more dangerous areas; dwellers can die permanently if not recalled in time
- **Cooldown**: Explored areas have a refresh timer before new loot spawns

### 3.6 Progression & Tech Tree

```
Tier 1 (Starting)          Tier 2 (Mid-game)           Tier 3 (Late-game)
─────────────────          ─────────────────           ─────────────────
Basic Generator            Advanced Generator          Fusion Reactor
Cafeteria                  Greenhouse                  Hydroponics Bay
Water Pump                 Purification Plant          Desalination Core
Living Quarters            Apartments                  Luxury Suites
Storage Room               Warehouse                   Quantum Vault
Guard Post                 Armory                      Defense Matrix
Medbay                     Surgery                     Cloning Lab
Classroom                  Research Lab                AI Core
Radio Room                 Broadcast Tower             Satellite Uplink
```

### 3.7 Events System

Random and scripted events that keep gameplay dynamic:

- **Positive**: New survivor arrives, trader caravan, resource windfall
- **Negative**: Raider attack, fire outbreak, pest infestation, disease
- **Story**: Narrative quests that reveal lore about the world and shelter's past
- **Seasonal**: Periodic large-scale events (e.g., "The Great Storm" — increased attacks but better loot)

### 3.8 Morale & Happiness

- Each dweller has a happiness value (0–100%)
- Affected by: room assignment (matching skills), living space quality, food/water availability, social bonds, events
- **Shelter-wide happiness** is the average of all dwellers
- High happiness: increased productivity, baby boom chance, new survivors attracted
- Low happiness: decreased productivity, desertion risk, internal conflicts

---

## 4. Room Types (Detailed)

### Production Rooms
| Room             | Resource  | Required Stat | Unlock         |
|-----------------|-----------|---------------|----------------|
| Power Generator | Power     | Strength      | Starting       |
| Cafeteria       | Food      | Handiness     | Starting       |
| Water Pump      | Water     | Intellect     | Starting       |
| Greenhouse      | Food      | Handiness     | Tier 2 Tech    |
| Advanced Gen    | Power     | Strength      | Tier 2 Tech    |
| Purification    | Water     | Intellect     | Tier 2 Tech    |

### Utility Rooms
| Room             | Function                    | Unlock        |
|-----------------|-----------------------------|---------------|
| Living Quarters | Housing for survivors       | Starting      |
| Storage Room    | Increases inventory cap     | Starting      |
| Elevator        | Connects floors             | Starting      |
| Medbay          | Produces stimpaks           | 14 dwellers   |
| Classroom       | Trains dweller stats        | 18 dwellers   |
| Radio Room      | Attracts new dwellers       | 20 dwellers   |

### Advanced Rooms
| Room             | Function                    | Unlock        |
|-----------------|-----------------------------|---------------|
| Workshop        | Craft weapons/armor         | 22 dwellers   |
| Research Lab    | Generates Tech resource     | 25 dwellers   |
| Guard Post      | Defends shelter entrance    | 10 dwellers   |
| Armory          | Stores/displays equipment   | 30 dwellers   |
| Cloning Lab     | Duplicate dwellers          | 40 dwellers   |

---

## 5. Monetization Strategy (Optional)

If pursuing a free-to-play model:

- **Cosmetic-only premium items**: Shelter themes, dweller outfits, room decorations
- **Time skips**: Speed up construction, production, or exploration (but never required)
- **Starter packs**: Bonus resources for new players
- **NO pay-to-win**: All gameplay content earnable through play
- **Battle Pass / Season system**: Cosmetic reward tracks tied to seasonal events

---

## 6. Target Audience

- **Primary**: Casual-to-mid-core gamers (ages 16–35) who enjoy management/simulation games
- **Secondary**: Fans of post-apocalyptic fiction and survival games
- **Session length**: 5–15 minutes (mobile), 30–60 minutes (desktop)
- **Retention hooks**: Real-time production, daily events, exploration returns, progression unlocks

---

## 7. Unique Selling Points (vs. Fallout Shelter)

1. **Deeper tech tree** with meaningful progression choices
2. **Morale and social system** — dwellers form relationships that affect gameplay
3. **Procedural story events** — richer narrative through exploration
4. **Defense strategy** — more tactical control during attacks
5. **Modding support** (late roadmap) — custom rooms, events, and scenarios
6. **No IP dependency** — original world and lore
