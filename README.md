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

### load sprite sheet
```rust
use bevy::prelude::*;

const SPRITE_PATH: &str =
    "path\\to\\spritesheet.png";
fn main() {
    App::build()
        .add_default_plugins()
        .add_startup_system(setup.system())
        .add_system(animate_sprite_system.system())
        .run();
}

// select sprite to draw
fn animate_sprite_system(
    texture_atlases: Res<Assets<TextureAtlas>>,
    mut query: Query<(&mut Timer, &mut TextureAtlasSprite, &Handle<TextureAtlas>)>,
) {
    for (timer, mut sprite, texture_atlas_handle) in query.iter_mut() {
        if timer.finished {
            let texture_atlas = texture_atlases.get(texture_atlas_handle).unwrap();
            sprite.index = ((sprite.index as usize + 1) % texture_atlas.textures.len()) as u32;
        }
    }
}

fn setup(
    mut commands: Commands,
    asset_server: Res<AssetServer>,
    mut texture_atlases: ResMut<Assets<TextureAtlas>>,
) {
    let texture_handle = asset_server.load(SPRITE_PATH);
    let texture_atlas =  TextureAtlas::from_grid_with_padding(
        texture_handle, 
        Vec2::new(24.0, 24.0), // sprite dimensions
        23, // number of columns
        4, // number of rows 
        Vec2::new(6f32,0f32)); // padding
    let texture_atlas_handle = texture_atlases.add(texture_atlas);
    commands
        .spawn(Camera2dComponents::default())
        .spawn(SpriteSheetComponents {
            texture_atlas: texture_atlas_handle,
            transform: Transform::from_scale(Vec3::splat(6.0)),
            ..Default::default()
        })
        .with(Timer::from_seconds(0.1, true));
}
```
### move sprite with keyboard
```rust
use bevy::prelude::*;

const SPRITE_PATH: &str = "..\\res\\sprite.png";

struct Position {
    x: i32,
    y: i32
}

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
        }).with(Position {x: 0, y: 0});
}

fn sprite_update(pos: &Position, mut transform: Mut<Transform>) {
    (*transform).translation = Vec3::new(pos.x as f32, pos.y as f32, 0.0);
}

fn keyboard_input_system(input: Res<Input<KeyCode>>, mut pos: Mut<Position>) {
    if input.pressed(KeyCode::W) {
        pos.y += 24;
    } else if input.pressed(KeyCode::A) {
        pos.x -= 24;
    } else if input.pressed(KeyCode::S) {
        pos.y -= 24;
    } else if input.pressed(KeyCode::D) {
        pos.x += 24;
    }
}

fn main() {
    App::build()
        .add_default_plugins()
        .add_startup_system(setup.system())
        .add_system(sprite_update.system())
        .add_system(keyboard_input_system.system())
        .run();
}
```
