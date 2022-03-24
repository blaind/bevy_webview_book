# Webview Engines

## Linux

The plugin uses ([webkitgtk](https://webkitgtk.org/) as an underlying engine on Linux. Windowing (in headless mode too) is handled by [gtk](https://www.gtk.org/).

### headless

Default mode. Implemented by using [gtk::OffscreenWindow](https://docs.gtk.org/gtk3/class.OffscreenWindow.html) as a canvas, where the webview is initialized.

```rust
# extern crate bevy;
# extern crate bevy_webview;
# use bevy::prelude::*;
# use bevy_webview::prelude::*;
let webview_plugin = WebviewPlugin::with_engine(webview_engine::headless);

App::new()
    // initializations...
   .add_plugin(webview_plugin);
```

### windowed

For debugging purposes. This will start the webview in a windowed mode. Implemented by using [gtk::Window](https://docs.gtk.org/gtk3/class.Window.html).

```rust
# extern crate bevy;
# extern crate bevy_webview;
# use bevy::prelude::*;
# use bevy_webview::prelude::*;
let webview_plugin = WebviewPlugin::with_engine(webview_engine::windowed);

App::new()
    // initializations...
   .add_plugin(webview_plugin);
```

## Windows

The plugin uses [WebView2](https://developer.microsoft.com/en-us/microsoft-edge/webview2/) as an underlying webview engine on Windows. Windowing is handled by [Win32 C++](https://docs.microsoft.com/en-us/windows/win32/learnwin32/learn-to-program-for-windows) API.

# Integrating an own engine

It is also possible to use your own webview engine. For now, see the [dummy.rs](https://github.com/blaind/bevy_webview/blob/main/crates/headless_webview/src/engines/dummy.rs) -example for API usage.

## Unimplemented

Help needed! Currently the following platforms are unimplemented.

- Mac
- IOS
- Android
