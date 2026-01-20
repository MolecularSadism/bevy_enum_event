# Changelog

## [0.3.0] - 2026-01-20

### Changed
- Migrated to Bevy 0.18

### Features
- `EnumEvent` derive macro for global events
- `EnumEntityEvent` derive macro for entity-targeted events
- `#[enum_event(target)]` for custom target fields
- `#[enum_event(propagate)]` for event propagation
- `#[enum_event(auto_propagate)]` for automatic propagation
- Custom propagation relationships via `#[enum_event(propagate = &'static Type)]`
- Variant-level attribute overrides
- `deref` feature (default) for ergonomic field access
- Full support for generics and lifetimes
