# Data Models: Vault Haven

This document defines the core data structures and their relationships. These models serve as the foundation for the ECS components and the save/load system.

---

## 1. Entity Relationship Diagram

```
┌──────────┐       ┌──────────┐       ┌──────────┐
│  Shelter │1────*│   Room   │1────*│  Dweller │
│          │       │          │       │          │
│ id       │       │ id       │       │ id       │
│ name     │       │ type     │       │ name     │
│ level    │       │ tier     │       │ stats    │
│ resources│       │ position │       │ health   │
│ settings │       │ assigned │       │ morale   │
└──────────┘       │ output   │       │ equipment│
                   └──────────┘       │ roomId   │
                                      └─────┬────┘
                                            │
                                      ┌─────▼────┐
                                      │   Item   │
                                      │          │
                                      │ id       │
                                      │ name     │
                                      │ type     │
                                      │ stats    │
                                      │ rarity   │
                                      └──────────┘
```

---

## 2. Shelter

The root game state object representing the entire vault.

```typescript
interface Shelter {
  id: string;                    // Unique shelter identifier
  name: string;                  // Player-chosen shelter name
  createdAt: number;             // Unix timestamp of creation
  lastSaved: number;             // Unix timestamp of last save
  lastTick: number;              // Unix timestamp of last simulation tick
  day: number;                   // In-game day counter

  // Resources
  resources: Resources;

  // Grid state
  grid: GridState;

  // Population tracking
  population: PopulationState;

  // Global settings
  settings: ShelterSettings;

  // Active events
  activeEvents: GameEvent[];

  // Exploration missions
  activeMissions: Mission[];

  // Unlocked tech
  unlockedTech: string[];

  // Save metadata
  saveVersion: number;           // For migration support
}

interface Resources {
  power: number;                 // Current power supply
  powerMax: number;              // Maximum power storage
  food: number;                  // Current food supply
  foodMax: number;               // Maximum food storage
  water: number;                 // Current water supply
  waterMax: number;              // Maximum water storage
  caps: number;                  // Currency (no max)
  materials: number;             // Crafting materials
  materialsMax: number;          // Maximum material storage
  tech: number;                  // Research points
}

interface GridState {
  width: number;                 // Grid columns
  depth: number;                 // Grid rows (floors)
  unlockedDepth: number;         // Deepest excavated floor
  cells: Record<string, string>; // "x,y" → roomId mapping
}

interface PopulationState {
  current: number;               // Current dweller count
  max: number;                   // Maximum capacity (from living rooms)
  births: number;                // Total births (stat tracking)
  deaths: number;                // Total deaths (stat tracking)
  desertions: number;            // Total desertions
}

interface ShelterSettings {
  gameSpeed: 1 | 2 | 3;         // Simulation speed multiplier
  soundEnabled: boolean;
  musicVolume: number;           // 0.0 - 1.0
  sfxVolume: number;             // 0.0 - 1.0
  notificationsEnabled: boolean;
  autoSaveInterval: number;      // Seconds between auto-saves
}
```

---

## 3. Room

Represents a single room in the shelter grid.

```typescript
interface Room {
  id: string;                    // Unique room identifier
  type: RoomType;                // Room type enum
  tier: 1 | 2 | 3;              // Upgrade level
  position: GridPosition;        // Grid coordinates (top-left corner)
  size: RoomSize;                // Width and height in grid cells
  mergedWith: string[];          // IDs of merged adjacent rooms

  // Production
  productionType: ResourceType | null;  // What this room produces
  productionRate: number;        // Base units per cycle
  productionTimer: number;       // Current cycle progress (0.0 - 1.0)
  efficiency: number;            // Multiplier from dweller skills

  // State
  isActive: boolean;             // Whether the room is powered
  isPowered: boolean;            // Whether power reaches this room
  health: number;                // Room durability (for damage events)
  maxHealth: number;

  // Assigned dwellers
  assignedDwellers: string[];    // Dweller IDs assigned to this room
  maxDwellers: number;           // Maximum dweller capacity

  // Construction
  buildCost: RoomCost;           // What it cost to build
  upgradeCost: RoomCost | null;  // Cost for next tier (null if max)
  buildTime: number;             // Seconds to construct
  isUnderConstruction: boolean;
  constructionProgress: number;  // 0.0 - 1.0
}

interface GridPosition {
  x: number;                     // Column index
  y: number;                     // Row (floor) index
}

interface RoomSize {
  width: number;                 // 1, 2, or 3 cells wide
  height: number;                // Always 1 for standard rooms
}

interface RoomCost {
  caps: number;
  materials: number;
  tech: number;                  // For advanced rooms
}

// Room type definitions
enum RoomType {
  // Production
  POWER_GENERATOR = "power_generator",
  ADVANCED_GENERATOR = "advanced_generator",
  FUSION_REACTOR = "fusion_reactor",
  CAFETERIA = "cafeteria",
  GREENHOUSE = "greenhouse",
  HYDROPONICS = "hydroponics",
  WATER_PUMP = "water_pump",
  PURIFICATION_PLANT = "purification_plant",
  DESALINATION_CORE = "desalination_core",

  // Utility
  LIVING_QUARTERS = "living_quarters",
  APARTMENTS = "apartments",
  LUXURY_SUITES = "luxury_suites",
  STORAGE_ROOM = "storage_room",
  WAREHOUSE = "warehouse",
  ELEVATOR = "elevator",

  // Services
  MEDBAY = "medbay",
  SURGERY = "surgery",
  CLONING_LAB = "cloning_lab",
  CLASSROOM = "classroom",
  RESEARCH_LAB = "research_lab",
  AI_CORE = "ai_core",

  // Defense
  GUARD_POST = "guard_post",
  ARMORY = "armory",
  DEFENSE_MATRIX = "defense_matrix",

  // Social
  RADIO_ROOM = "radio_room",
  BROADCAST_TOWER = "broadcast_tower",
  SATELLITE_UPLINK = "satellite_uplink",

  // Crafting
  WORKSHOP = "workshop",
}
```

---

## 4. Dweller

Represents a survivor/resident of the shelter.

```typescript
interface Dweller {
  id: string;                    // Unique identifier
  firstName: string;
  lastName: string;

  // Core stats (S.H.I.F.T.)
  stats: DwellerStats;

  // Health & status
  health: HealthState;
  morale: MoraleState;
  level: number;                 // 1 - 50
  experience: number;            // XP toward next level
  experienceToNext: number;      // XP needed for next level

  // Assignment
  assignedRoomId: string | null; // Currently assigned room
  currentRoomId: string | null;  // Where the dweller physically is
  state: DwellerState;           // Current activity state

  // Equipment
  equipment: Equipment;

  // Traits (unlocked through leveling or events)
  traits: string[];              // Trait IDs

  // Social
  relationships: Relationship[];
  partnerId: string | null;      // Romantic partner dweller ID

  // Metadata
  arrivalDay: number;            // In-game day they arrived
  isChild: boolean;              // Children can't work or fight
  growthTimer: number;           // Time until child becomes adult
}

interface DwellerStats {
  strength: number;              // 1 - 10 (base), can exceed with gear
  handiness: number;             // 1 - 10
  intellect: number;             // 1 - 10
  fortitude: number;             // 1 - 10
  tenacity: number;              // 1 - 10
}

interface HealthState {
  current: number;               // Current HP
  max: number;                   // Max HP (scales with level + fortitude)
  radiation: number;             // Radiation damage (reduces max HP)
  isAlive: boolean;
  isDead: boolean;
}

interface MoraleState {
  happiness: number;             // 0 - 100
  factors: MoraleFactor[];       // Active modifiers
}

interface MoraleFactor {
  id: string;                    // Factor identifier
  name: string;                  // Display name
  value: number;                 // Modifier amount (-50 to +50)
  duration: number;              // Remaining ticks (-1 for permanent)
  source: string;                // What caused this factor
}

enum DwellerState {
  IDLE = "idle",                 // Not assigned, wandering
  WORKING = "working",           // Producing resources in a room
  TRAINING = "training",         // In a training room
  FIGHTING = "fighting",         // In combat
  HEALING = "healing",           // In medbay
  EXPLORING = "exploring",       // On surface mission
  FLEEING = "fleeing",           // Running from danger
  RESTING = "resting",          // In living quarters
}

interface Equipment {
  weapon: Item | null;
  armor: Item | null;
  accessory: Item | null;
}

interface Relationship {
  dwellerId: string;             // The other dweller
  type: RelationshipType;
  affinity: number;              // -100 to 100
}

enum RelationshipType {
  ACQUAINTANCE = "acquaintance",
  FRIEND = "friend",
  BEST_FRIEND = "best_friend",
  PARTNER = "partner",
  RIVAL = "rival",
  PARENT = "parent",
  CHILD = "child",
}
```

---

## 5. Item

Equipment, consumables, and crafting materials.

```typescript
interface Item {
  id: string;                    // Unique instance ID
  templateId: string;            // References static item data
  name: string;
  description: string;
  type: ItemType;
  rarity: ItemRarity;

  // Stats (for equipment)
  stats: ItemStats;

  // Consumable effects
  effect: ItemEffect | null;

  // Metadata
  value: number;                 // Sale value in caps
  isEquipped: boolean;
  equippedBy: string | null;     // Dweller ID
}

enum ItemType {
  WEAPON_MELEE = "weapon_melee",
  WEAPON_RANGED = "weapon_ranged",
  ARMOR = "armor",
  ACCESSORY = "accessory",
  CONSUMABLE = "consumable",
  MATERIAL = "material",
  JUNK = "junk",                 // Recyclable for materials
}

enum ItemRarity {
  COMMON = "common",             // White
  UNCOMMON = "uncommon",         // Green
  RARE = "rare",                 // Blue
  EPIC = "epic",                 // Purple
  LEGENDARY = "legendary",       // Gold
}

interface ItemStats {
  damage?: number;               // Weapon damage
  defense?: number;              // Armor defense
  statBonuses?: Partial<DwellerStats>;  // Stat modifiers
  special?: string;              // Special effect description
}

interface ItemEffect {
  type: "heal" | "cure_radiation" | "boost_stat" | "boost_morale";
  value: number;                 // Effect magnitude
  duration?: number;             // Duration in ticks (for buffs)
  targetStat?: keyof DwellerStats;
}
```

---

## 6. Events

Dynamic events that occur during gameplay.

```typescript
interface GameEvent {
  id: string;                    // Unique event instance ID
  templateId: string;            // References static event data
  type: EventType;
  severity: EventSeverity;
  name: string;
  description: string;

  // Timing
  startTick: number;             // When the event started
  duration: number;              // Duration in ticks (-1 for instant)
  remainingTicks: number;

  // Effects
  effects: EventEffect[];

  // For combat events
  enemies?: Enemy[];
  targetRoomId?: string;

  // For choice events
  choices?: EventChoice[];
  resolved: boolean;
}

enum EventType {
  RAID = "raid",                 // External attack
  FIRE = "fire",                 // Internal fire
  INFESTATION = "infestation",   // Pest/creature outbreak
  DISEASE = "disease",           // Illness spreading
  TRADER = "trader",             // Traveling merchant
  NEWCOMER = "newcomer",         // New dweller arrives
  WINDFALL = "windfall",         // Resource bonus
  STORY = "story",               // Narrative event with choices
  SEASONAL = "seasonal",         // Large periodic event
}

enum EventSeverity {
  MINOR = "minor",
  MODERATE = "moderate",
  MAJOR = "major",
  CRITICAL = "critical",
}

interface EventEffect {
  type: "add_resource" | "remove_resource" | "damage_room" | "damage_dweller"
        | "add_dweller" | "add_item" | "modify_morale" | "unlock_tech";
  target?: string;               // Entity ID
  resource?: ResourceType;
  amount?: number;
}

interface EventChoice {
  id: string;
  text: string;                  // Choice description
  effects: EventEffect[];        // What happens if chosen
  requirements?: ChoiceRequirement[];  // Stat/resource checks
}

interface ChoiceRequirement {
  type: "stat" | "resource" | "dweller_count" | "tech";
  key: string;
  minValue: number;
}
```

---

## 7. Exploration / Missions

```typescript
interface Mission {
  id: string;
  type: MissionType;
  name: string;
  description: string;

  // Party
  dwellerIds: string[];          // Assigned dwellers
  supplies: MissionSupplies;

  // Progress
  state: MissionState;
  departureTick: number;
  estimatedReturnTick: number;
  currentDistance: number;        // How far they've traveled
  maxDistance: number;            // How far they'll go

  // Results
  log: MissionLogEntry[];        // Event log
  loot: Item[];                  // Collected items
  experienceGained: number;

  // Location (for quest missions)
  locationId?: string;
  questProgress?: number;        // 0.0 - 1.0
}

enum MissionType {
  EXPLORE = "explore",           // Open-ended exploration
  QUEST = "quest",               // Specific location quest
  SCAVENGE = "scavenge",         // Targeted resource gathering
  TRADE = "trade",               // Visit a trade post
}

enum MissionState {
  OUTBOUND = "outbound",         // Traveling to destination
  ACTIVE = "active",             // At destination, encountering events
  RETURNING = "returning",       // Heading back to shelter
  COMPLETED = "completed",       // Back at shelter
  FAILED = "failed",             // Party wiped or recalled
}

interface MissionSupplies {
  stimpaks: number;
  radAway: number;
  food: number;
  water: number;
}

interface MissionLogEntry {
  tick: number;                  // When it happened
  type: "combat" | "loot" | "event" | "rest" | "discovery";
  text: string;                  // Narrative text
  rewards?: { item?: Item; caps?: number; xp?: number };
  damage?: number;               // Damage taken
}
```

---

## 8. Combat Entities

```typescript
interface Enemy {
  id: string;
  templateId: string;
  name: string;
  type: EnemyType;

  health: number;
  maxHealth: number;
  damage: number;                // Damage per attack
  defense: number;
  attackSpeed: number;           // Ticks between attacks
  loot: LootTable;

  currentRoomId?: string;        // Which room they're in
  state: "advancing" | "fighting" | "fleeing" | "dead";
}

enum EnemyType {
  RAIDER = "raider",
  RAIDER_BOSS = "raider_boss",
  MUTANT_RAT = "mutant_rat",
  FERAL_GHOUL = "feral_ghoul",
  RADSCORPION = "radscorpion",
  DEATHCLAW = "deathclaw",
}

interface LootTable {
  entries: LootEntry[];
}

interface LootEntry {
  itemTemplateId: string;
  dropChance: number;            // 0.0 - 1.0
  minQuantity: number;
  maxQuantity: number;
}
```

---

## 9. Static Data Schema (JSON Configs)

These define the templates loaded at game start:

### rooms.json schema
```typescript
interface RoomTemplate {
  type: RoomType;
  name: string;
  description: string;
  category: "production" | "utility" | "services" | "defense" | "social" | "crafting";
  baseCost: RoomCost;
  costMultiplier: number;        // Cost increase per room built
  baseProduction: number;        // Base output per cycle
  productionResource: ResourceType | null;
  requiredStat: keyof DwellerStats | null;
  maxDwellers: number;
  baseHealth: number;
  unlockRequirement: UnlockRequirement;
  tiers: TierUpgrade[];
}
```

### events.json schema
```typescript
interface EventTemplate {
  id: string;
  type: EventType;
  name: string;
  description: string;
  weight: number;                // Probability weight
  minDay: number;                // Earliest day it can occur
  minDwellers: number;           // Minimum population to trigger
  severity: EventSeverity;
  duration: number;
  effects: EventEffect[];
  choices?: EventChoice[];
}
```

---

## 10. Save File Format

```typescript
interface SaveFile {
  version: number;               // Schema version for migrations
  timestamp: number;             // When the save was created
  shelter: Shelter;
  rooms: Room[];
  dwellers: Dweller[];
  inventory: Item[];             // Unequipped items in storage
  completedQuests: string[];     // Quest IDs
  statistics: GameStatistics;    // Lifetime stats
}

interface GameStatistics {
  totalCapsEarned: number;
  totalRoomsBuilt: number;
  totalDwellersRecruited: number;
  totalRaidsDefended: number;
  totalItemsCrafted: number;
  totalExplorationsCompleted: number;
  longestSurvivorStreak: number; // Days without a death
  playTimeSeconds: number;
}
```
