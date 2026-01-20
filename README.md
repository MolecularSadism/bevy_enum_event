# bevy_enum_event

Derive macros that generate Bevy event types from enum variants.

## Overview

Transform enum variants into distinct Bevy event structs automatically. Each variant becomes a separate event type in a snake_case module, eliminating boilerplate while preserving type safety.

```rust
use bevy::prelude::*;
use bevy_enum_event::EnumEvent;

#[derive(EnumEvent, Clone)]
enum GameEvent {
    Victory(String),
    ScoreChanged { team: u32, score: i32 },
    GameOver,
}

// Generates: game_event::Victory, game_event::ScoreChanged, game_event::GameOver
```

## Bevy Compatibility

| Bevy | bevy_enum_event |
|------|-----------------|
| 0.18 | 0.3             |

## Installation

```toml
[dependencies]
bevy_enum_event = "0.3"
```

## Macros

- **`EnumEvent`** - Generates global `Event` types
- **`EnumEntityEvent`** - Generates entity-targeted `EntityEvent` types with optional propagation

## EnumEvent

Supports unit, tuple, and named field variants:

```rust
use bevy_enum_event::EnumEvent;

#[derive(EnumEvent, Clone)]
enum PlayerState {
    Idle,                           // Unit variant
    Moving(f32),                    // Tuple variant
    Attacking { target: Entity },   // Named field variant
}
```

### Using with Observers

```rust
fn setup(app: &mut App) {
    app.observe(on_attacking);
}

fn on_attacking(event: On<player_state::Attacking>) {
    println!("Attacking {:?}", event.event().target);
}
```

### Deref Feature (default)

Single-field variants automatically implement `Deref`/`DerefMut`:

```rust
#[derive(EnumEvent, Clone)]
enum NetworkEvent {
    MessageReceived(String),  // Automatic deref to String
}

fn on_message(msg: On<network_event::MessageReceived>) {
    let content: &String = &*msg.event();
}
```

For multi-field variants, mark one field with `#[enum_event(deref)]`:

```rust
#[derive(EnumEvent, Clone)]
enum GameEvent {
    PlayerScored {
        #[enum_event(deref)]
        player: Entity,
        points: u32
    },
}
```

Disable with `default-features = false`.

## EnumEntityEvent

Entity-targeted events that trigger entity-specific observers.

### Requirements

- Named fields only (`{ field: Type }` syntax)
- Must have `entity: Entity` field or `#[enum_event(target)]` on a custom field

```rust
use bevy::prelude::*;
use bevy_enum_event::EnumEntityEvent;

#[derive(EnumEntityEvent, Clone, Copy)]
enum PlayerEvent {
    Spawned { entity: Entity },
    Damaged { entity: Entity, amount: f32 },
}

// Entity-specific observer
fn setup(mut commands: Commands) {
    commands.spawn_empty().observe(|damaged: On<player_event::Damaged>| {
        println!("This player took {} damage", damaged.amount);
    });
}
```

### Custom Target Field

```rust
#[derive(EnumEntityEvent, Clone, Copy)]
enum CombatEvent {
    Attack {
        #[enum_event(target)]
        attacker: Entity,
        defender: Entity,
    },
}
```

### Event Propagation

Events can bubble up entity hierarchies:

```rust
// Basic propagation (uses ChildOf)
#[derive(EnumEntityEvent, Clone, Copy)]
#[enum_event(propagate)]
enum UiEvent {
    Click { entity: Entity },
}

// Auto propagation (always bubbles)
#[derive(EnumEntityEvent, Clone, Copy)]
#[enum_event(auto_propagate, propagate)]
enum SystemEvent {
    Update { entity: Entity },
}

// Custom relationship
#[derive(EnumEntityEvent, Clone, Copy)]
#[enum_event(propagate = &'static ::bevy::prelude::ChildOf)]
enum CustomEvent {
    Action { entity: Entity },
}
```

Control propagation in observers:

```rust
fn on_click(mut click: On<ui_event::Click>) {
    click.propagate(true);  // Continue bubbling
    let original = click.original_event_target();
}
```

### Variant-Level Overrides

Override enum-level settings per variant:

```rust
#[derive(EnumEntityEvent, Clone, Copy)]
#[enum_event(propagate)]
enum MixedEvent {
    Normal { entity: Entity },  // Uses enum-level

    #[enum_event(auto_propagate, propagate)]
    AutoEvent { entity: Entity },  // Overrides with auto
}
```

## Generics & Lifetimes

Full support for generic parameters and lifetimes:

```rust
#[derive(EnumEvent, Clone)]
enum GenericEvent<'a, T>
where
    T: Clone + 'a,
{
    Borrowed(&'a T),
    Owned(T),
    Done,
}
```

## License

MIT OR Apache-2.0
