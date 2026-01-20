# Changelog

## [0.3.0] - 2026-01-20

### Changed
- Migrated to Bevy 0.18
- Simplified documentation

## [0.2.0] - 2025-10-20

### Added
- `EnumEntityEvent` derive macro for entity-targeted events
- `#[enum_event(target)]` for custom target fields
- `#[enum_event(propagate)]` for event propagation
- `#[enum_event(auto_propagate)]` for automatic propagation
- Custom propagation relationships via `#[enum_event(propagate = &'static Type)]`
- Variant-level attribute overrides

### Changed
- Migrated to Bevy 0.17

## [0.1.0] - 2025-10-20

### Added
- `EnumEvent` derive macro for global events
- Support for unit, tuple, and named field variants
- `deref` feature (default) for ergonomic field access
- Full support for generics and lifetimes
