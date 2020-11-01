# Bevy howto
### load sprite
```rust
use bevy::prelude::*;

const SPRITE_PATH: &str =
    "path\\to\\sprite.png";

fn setup(
    mut commands: Commands,
    mut materials: ResMut<Assets<ColorMaterial>>,
    asset_server: Res<AssetServer>,
) {
    let sprite = materials.add(asset_server.load(SPRITE_PATH).into());

    commands.spawn(Camera2dComponents::default());
    commands
        .spawn(SpriteComponents {
            material: sprite,
            transform: Transform::from_scale(Vec3::new(4f32, 4f32, 0.0)),
            ..Default::default()
        });
}

fn main() {
    App::build()
        .add_default_plugins()
        .add_startup_system(setup.system())
        .run();
}
```
