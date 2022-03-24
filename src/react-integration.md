# React Integration

It is possible to use [React](https://reactjs.org/) (or any other similar framework) together with `bevy_webview`. Follow the normal React-project creation, and start react in the local development mode. Finally, you should see the server listening in an local address (`localhost:3000`).

```ignore
Compiled successfully!

The app is running at:

  http://localhost:3000/

Note that the development build is not optimized.
To create a production build, use npm run build.
```

Connect the webview to the React app using an URL. Note the `transparent: true` flag, which works especially well in the UI mode.

```rust
# extern crate bevy;
# extern crate bevy_webview;
# use bevy::prelude::*;
# use bevy_webview::prelude::*;
fn setup(mut commands: Commands) {
    commands.spawn_bundle(WebviewUIBundle {
        webview: Webview {
            uri: Some(String::from("http://localhost:3000/")),
            color: Color::rgba(0., 0., 0., 0.0),
            ..Default::default()
        },
        style: Style {
            size: Size::new(Val::Percent(80.0), Val::Percent(80.)),
            margin: Rect::all(Val::Auto),
            justify_content: JustifyContent::Center,
            align_items: AlignItems::Center,
            ..Default::default()
        },
        ..Default::default()
    });
}
```

See [RPC](rpc.md) for communication between Javascript and Rust code.

After [asset bundling](bundling.md) is implemented, the code should be released conditionally:

```rust,ignore
# extern crate bevy;
# extern crate bevy_webview;
# use bevy::prelude::*;
# use bevy_webview::prelude::*;
fn setup(mut commands: Commands) {
    commands.spawn_bundle(WebviewUIBundle {
        webview: Webview {
            #[cfg(debug_assertions)]
            uri: Some(String::from("http://localhost:3000/")),
            #[cfg(not(debug_assertions))]
            uri: Some(String::from("webview://react_app/index.html")),
            // NOTE! The webview:// method is not yet implemented!
            transparent: true,
            ..Default::default()
        }
    });
}
```
