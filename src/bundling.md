# Asset bundling

**Asset bundling not yet implemented**, but local asset usage works already.

## Local Assets

It is possible to use local assets from `assets/webview/...` folder. These assets can be referenced by `webview://` protocol, e.g. `assets/webview/react_app/index.html` will be resolvable through `webview://react_app/index.html`.

```rust,ignore
# extern crate bevy;
# extern crate bevy_webview;
# use bevy::prelude::*;
# use bevy_webview::prelude::*;
fn setup(mut commands: Commands) {
    commands.spawn_bundle(WebviewUIBundle {
        webview: Webview {
            uri: Some(String::from("webview://react_app/index.html")),
            ..Default::default()
        }
    });
}
```
