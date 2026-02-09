# Vault Haven

**A post-apocalyptic shelter management game inspired by Fallout Shelter.**

Build and manage an underground shelter, assign survivors to rooms, produce resources, defend against threats, and explore the wasteland above.

---

## Project Status: Planning Phase

This project is currently in the planning and design phase. See the `docs/` folder for full documentation.

## Documentation

| Document | Description |
|----------|-------------|
| [Game Design Document](docs/game-design-document.md) | Core concept, mechanics, features, room types, and game systems |
| [Technical Architecture](docs/technical-architecture.md) | Tech stack, system design, ECS architecture, project structure |
| [Data Models](docs/data-models.md) | Entity schemas, relationships, save format, static data definitions |
| [Development Roadmap](docs/development-roadmap.md) | 6-phase plan with milestones, task breakdowns, and priority matrix |
| [Art & UI Plan](docs/art-and-ui-plan.md) | Visual style, UI layouts, animation plan, audio plan, asset pipeline |

## Quick Overview

### Core Gameplay Loop

1. **Build** rooms to expand your shelter
2. **Assign** survivors with matching skills for optimal output
3. **Produce** resources (power, food, water, caps, materials, tech)
4. **Manage** threats like raids, fires, and disease
5. **Explore** the surface for loot and story
6. **Upgrade** with new tech and better rooms

### Tech Stack

- **TypeScript** with **Phaser 3** (game engine) and **React** (UI overlay)
- **Vite** for build tooling
- **ECS architecture** for performant, modular game logic
- **IndexedDB** for local saves, optional cloud sync

### Key Features (Planned)

- Grid-based shelter construction with room merging and upgrading
- S.H.I.F.T. stat system for survivors (Strength, Handiness, Intellect, Fortitude, Tenacity)
- Real-time resource production with offline progression
- Combat system with raids and defense mechanics
- Surface exploration with procedural events and loot
- Tech tree with 3 tiers of room upgrades
- Morale, relationships, and trait systems
- Original world and lore (no IP dependency)

## Getting Started

> Development has not started yet. This section will be updated when the project scaffold is created in Phase 1.

```bash
# (Future) Install dependencies
npm install

# (Future) Start development server
npm run dev

# (Future) Run tests
npm test

# (Future) Build for production
npm run build
```

## License

TBD
