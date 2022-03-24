# Javascript-Rust RPC

It is possible to configure Webview-RPC events for communication between Javascript and Rust. RPC responses are not supported currently.

## Serde setup

First, make sure that your crate has serde installed. Add to `Cargo.toml`:

```toml
serde = { version = "1.0", features = [ "derive" ] }
```

In the application, import serde:

```rust,ignore
use serde::{Serialize, Deserialize};
```

## RPC from Javascript to Rust

First, define a struct for the message (or enum, or other deserializable type). Make it deserializable with the serde's `Deserialize` -trait. `Debug` is optional.

```rust
# extern crate bevy_webview;
# use bevy_webview::serde::Deserialize;
#[derive(Deserialize, Debug)]
pub struct LoginRequest {
    username: String,
}
```

Configure an event in the application setup code. The first parameter is a message `method`, used as a key when sending events.

```rust
# extern crate bevy;
# extern crate bevy_webview;
# use bevy::prelude::*;
# use bevy_webview::prelude::*;
# use bevy_webview::serde::Deserialize;
# #[derive(Deserialize)]
# struct LoginRequest {};
App::new()
# .add_plugin(WebviewPlugin::with_engine(webview_engine::headless))
    // initializations...
    .add_webview_input_event::<LoginRequest>("login");
```

And define a system to receive events, using a `WebviewEventReader<T>`:

```rust
# extern crate bevy;
# extern crate bevy_webview;
# use bevy::prelude::*;
# use bevy_webview::prelude::*;
# struct LoginRequest { username: String };
fn login_handler(mut login_request_events: WebviewEventReader<LoginRequest>) {
    for event in login_request_events.iter() {
        println!("Received a login request, username={:?}", event.username);
    }
}
```

Finally, call the RPC method from Javascript. `bevy_webview` will try to unmarshal the Javascript-provided JSON into an event struct defined and referenced by `add_webview_input_event` key string.

```javascript
await rpc.call("login", { username: "test" });
```

Be mindful of not registering too many (hundreds) simultaneous events, as each call to `add_webview_input_event` will create two new Bevy-systems (standard Bevy event creates one system).

It is also possible to create a Rust `enum` and reference that by Javascript.

## RPC from Rust to Javascript

First, define a struct for the message (or enum, or other deserializable type). Make it serializable with the serde's `Serialize` -trait. `Debug` is optional.

```rust
# extern crate bevy_webview;
# use bevy_webview::serde::Serialize;
#[derive(Serialize, Debug)]
pub struct AppTime {
    seconds_since_startup: f64,
}
```

Then, configure an outgoing event in the setup code. The first parameter is the event method key, by which Javascript will subscribe into the event.

```rust
# extern crate bevy;
# extern crate bevy_webview;
# use bevy::prelude::*;
# use bevy_webview::prelude::*;
# use bevy_webview::serde::Serialize;
# #[derive(Serialize)]
# struct AppTime {};
App::new()
# .add_plugin(WebviewPlugin::with_engine(webview_engine::headless))
    // initializations...
    .add_webview_output_event::<AppTime>("app_time");
```

In Javascript, register an event handler by calling `rpc.on(method, callback)`. You can have multiple registrations for one method. The parameter for callback will be a Javascript object unmarshalled from JSON by `JSON.parse`.

```javascript
rpc.on("app_time", (app_time) => {
  console.log(
    "Seconds since startup: " + app_time.seconds_since_startup.toFixed(4)
  );
});
```

Finally, send an event from a Bevy system using a `WebviewEventWriter<T>`:

```rust
# extern crate bevy;
# extern crate bevy_webview;
# use bevy::prelude::*;
# use bevy_webview::prelude::*;
# struct AppTime { seconds_since_startup: f64 };
fn send_time_system(mut app_time: WebviewEventWriter<AppTime>, time: Res<Time>) {
    app_time.send(AppTime {
        seconds_since_startup: time.seconds_since_startup(),
    });
}
```

For now, you should avoid redrawing on Javascript by every frame. See [performance](performance.md) for more information.
