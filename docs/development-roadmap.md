# Development Roadmap: Vault Haven

## Overview

The project is broken into 6 phases over an estimated 6-9 month timeline (solo/small team). Each phase builds on the previous one, with a playable prototype as early as Phase 2.

---

## Phase 1: Foundation (Weeks 1-3)

**Goal:** Project scaffold, core engine, and basic rendering.

### Tasks

- [ ] Initialize project with Vite + TypeScript + Phaser 3
- [ ] Set up ESLint, Prettier, and Vitest
- [ ] Implement ECS core (Entity, Component, System, World)
- [ ] Implement EventBus (pub/sub messaging)
- [ ] Implement GameLoop with configurable tick rate
- [ ] Create grid system with cell management
- [ ] Basic camera (pan, zoom) with mouse/touch input
- [ ] Render a static shelter background with grid overlay
- [ ] Placeholder room rendering (colored rectangles)
- [ ] Basic room placement (click to place on grid)
- [ ] Set up CI pipeline (lint + test on push)

### Deliverable
A canvas with a grid where you can place colored rectangles (rooms) and pan/zoom around.

### Key Risks
- Phaser 3 + React integration complexity
- ECS design decisions that may need refactoring later

---

## Phase 2: Core Gameplay Loop (Weeks 4-7)

**Goal:** Playable prototype with resource production and dwellers.

### Tasks

- [ ] Implement Room data model and RoomFactory
- [ ] Room types: Power Generator, Cafeteria, Water Pump, Living Quarters, Elevator, Storage
- [ ] Room placement validation (adjacency, elevator connectivity)
- [ ] Room merging (same-type adjacent rooms combine)
- [ ] Implement Dweller data model and DwellerFactory
- [ ] Procedural name generation
- [ ] Dweller stat generation (S.H.I.F.T.)
- [ ] Dweller assignment to rooms
- [ ] Dweller sprite rendering with basic idle animation
- [ ] ProductionSystem — rooms generate resources over time
- [ ] ConsumptionSystem — dwellers consume food and water
- [ ] Resource UI (HUD with power/food/water/caps bars)
- [ ] Build menu UI (select room type, see cost, place)
- [ ] Dweller panel UI (click dweller to see stats)
- [ ] Room panel UI (click room to see details, assign dwellers)
- [ ] Basic save/load to IndexedDB
- [ ] Auto-save every 60 seconds

### Deliverable
A playable loop: build rooms, assign dwellers, produce resources, manage consumption. Save and load your shelter.

### Key Risks
- Balancing production vs consumption rates
- Save/load reliability

---

## Phase 3: Combat & Events (Weeks 8-11)

**Goal:** Dynamic gameplay with threats and random events.

### Tasks

- [ ] Implement Enemy data model
- [ ] Raider attack system (enemies enter from vault door)
- [ ] Room-by-room combat: enemies advance, dwellers fight
- [ ] Combat UI (health bars, damage numbers)
- [ ] Player combat interactions (use stimpaks, relocate dwellers)
- [ ] Guard Post room type
- [ ] Medbay room type (produces stimpaks)
- [ ] Death system (dwellers can die, revival mechanic)
- [ ] EventSystem with weighted random triggers
- [ ] Event types: raids, fires, infestations, newcomers, traders, windfalls
- [ ] Fire event (spreads between rooms, requires dwellers to extinguish)
- [ ] Notification system (toast alerts for events)
- [ ] Event log UI (scrollable history)
- [ ] Difficulty scaling (events get harder as shelter grows)
- [ ] Sound effects for combat and events

### Deliverable
A dynamic shelter where raids happen, fires break out, and new survivors arrive. The player must defend and react.

### Key Risks
- Combat balance (too hard = frustrating, too easy = boring)
- Event frequency tuning

---

## Phase 4: Exploration & Progression (Weeks 12-16)

**Goal:** Depth through exploration, crafting, and progression.

### Tasks

- [ ] Exploration system (send dwellers to surface)
- [ ] Mission log with procedural text events
- [ ] Loot generation from exploration
- [ ] Return mechanic (recall dwellers or wait for auto-return)
- [ ] Quest locations (fixed points of interest with multi-step encounters)
- [ ] Item system with rarity tiers
- [ ] Equipment system (weapon, armor, accessory slots)
- [ ] Workshop room (craft items from materials)
- [ ] Dweller leveling and XP system
- [ ] Trait system (special abilities on level-up)
- [ ] Classroom room (train dweller stats)
- [ ] Research Lab room (generates Tech points)
- [ ] Tech tree with unlock progression
- [ ] Tier 2 rooms (Advanced Generator, Greenhouse, Purification Plant, Apartments)
- [ ] Explore panel UI
- [ ] Inventory/equipment UI
- [ ] Tech tree UI

### Deliverable
Full progression loop: explore, loot, craft, equip, train, research, unlock, build better rooms.

### Key Risks
- Procedural text quality
- Loot balance and item variety
- Tech tree depth vs development time

---

## Phase 5: Polish & Content (Weeks 17-22)

**Goal:** Visual polish, audio, content depth, and quality of life.

### Tasks

- [ ] Proper sprite art for all room types (or asset pack integration)
- [ ] Dweller sprite variations (appearance, equipment shown on sprite)
- [ ] Room animations (machines running, steam, sparks)
- [ ] Particle effects (fire, radiation glow, construction dust)
- [ ] Background music (ambient, combat, exploration themes)
- [ ] Full sound effect pass
- [ ] Morale system implementation (happiness factors)
- [ ] Relationship system between dwellers
- [ ] Radio Room (attract new dwellers over time)
- [ ] Tier 3 rooms (Fusion Reactor, Hydroponics, etc.)
- [ ] Story events with narrative choices
- [ ] Seasonal events (time-limited large events)
- [ ] Tutorial / onboarding flow
- [ ] Settings menu (audio, game speed, auto-save)
- [ ] Statistics screen (lifetime stats)
- [ ] Offline progression (calculate production while away)
- [ ] Performance optimization pass
- [ ] Mobile-responsive touch controls polish
- [ ] Accessibility pass (keyboard nav, screen reader support)

### Deliverable
A polished, content-rich game ready for public playtesting.

### Key Risks
- Art asset pipeline (creating vs purchasing vs AI-generating)
- Scope creep in content creation
- Performance on low-end mobile devices

---

## Phase 6: Launch & Live (Weeks 23-28+)

**Goal:** Public release, community building, ongoing content.

### Tasks

- [ ] Beta testing with feedback collection
- [ ] Bug fixes and balance patches
- [ ] Cloud save backend (optional)
- [ ] Leaderboards and achievements
- [ ] Landing page and marketing site
- [ ] Analytics integration (anonymous gameplay telemetry)
- [ ] Monetization implementation (if F2P: cosmetics, time-skips)
- [ ] Community Discord / feedback channels
- [ ] Post-launch content updates
- [ ] Modding support exploration (custom rooms, events)
- [ ] Native mobile wrapper (Capacitor/Cordova) if demand exists

### Deliverable
Live game with active players, analytics, and a content update pipeline.

---

## Milestone Summary

| Milestone         | Week | Description                                    |
|-------------------|------|------------------------------------------------|
| **M1: Scaffold**  | 3    | Project running, grid + rooms rendering        |
| **M2: Prototype** | 7    | Playable core loop with save/load              |
| **M3: Combat**    | 11   | Raids, events, death/survival dynamics         |
| **M4: Depth**     | 16   | Exploration, crafting, tech tree, progression  |
| **M5: Polish**    | 22   | Art, audio, content, tutorial, optimization    |
| **M6: Launch**    | 28   | Public release with live service plan          |

---

## Priority Matrix

### Must Have (MVP — Phases 1-2)
- Grid-based room construction
- Resource production and consumption
- Dweller assignment and basic stats
- Save/load system
- Basic UI (HUD, build menu, panels)

### Should Have (Phases 3-4)
- Combat system with raids
- Random events
- Exploration system
- Item/equipment system
- Tech tree progression

### Nice to Have (Phase 5)
- Relationships and morale depth
- Story events with choices
- Full art and audio
- Seasonal events
- Offline progression

### Future Considerations (Phase 6+)
- Multiplayer trading
- PvP raid system
- Modding support
- Native mobile apps
- Procedural world map

---

## Technical Debt Strategy

- **Phase 1-2**: Accept some shortcuts for speed; document all TODOs
- **Phase 3**: Refactor ECS if needed based on real usage patterns
- **Phase 4**: Audit and clean up save format before it grows too complex
- **Phase 5**: Dedicated performance and code quality pass
- **Each phase**: Maintain test coverage > 70% for core systems
