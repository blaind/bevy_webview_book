# Asset bundling

**Unfortunately, not yet implemented!**

The plan is to have web-assets (HTML, JS, etc.) to be bundled into the bevy app.

## API usage

**Unfortunately, not yet implemented!**

The bundler will bundle assets from `assets/webview/...` folder into the application built **in release mode**. These assets can be referenced by `webview://` protocol, e.g. `assets/webview/react_app/index.html` will be resolvable through `webview://react_app/index.html`.

```rust,ignore
# extern crate bevy;
# extern crate bevy_webview;
# use bevy::prelude::*;
# use bevy_webview::prelude::*;
fn setup(mut commands: Commands) {
    commands.spawn_bundle(WebviewUIBundle {
        webview: Webview {
            url: Some(String::from("webview://react_app/index.html")),
            ..Default::default()
        }
    });
}
```
