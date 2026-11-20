# Sample Rust Core · Web SDK

[한국어](README.ko.md)

The web SDK lets a browser app fetch the signed-in user's profile with `account.getMe()`. The Rust library sends the authenticated request to `GET /v1/me`. Rust Inside builds the library for WebAssembly and publishes the SDK as the `sample-rust-core-web` npm package. [Rust implementation](https://github.com/Rust-Inside/sample-rust-core/blob/v0.2.0/src/api.rs)

## Install with npm

Run this command from your web project. npm installs the package from the release archive. [Release v0.2.0](https://github.com/Rust-Inside/sample-web-sdk/releases/tag/v0.2.0)

```sh
npm install https://github.com/Rust-Inside/sample-web-sdk/releases/download/v0.2.0/sample-rust-core-web-0.2.0.tgz
```

The browser must support ES modules and WebAssembly. Make sure your bundler includes the package's `index_bg.wasm` file in the deployed app.

## Get the signed-in user's profile

Call `init()` once at startup to load WebAssembly. After sign-in, create an `AccountClient` with the API server's origin and the full `Authorization` header value, including the `Bearer` prefix if your server uses it.

```js
import init, { AccountClient } from 'sample-rust-core-web';

await init();

export function openSession(apiOrigin, authorization) {
  return new AccountClient(apiOrigin, authorization);
}

export async function profileName(account) {
  const me = await account.getMe();
  try {
    return me.displayName;
  } finally {
    me.free();
  }
}
```

`getMe()` returns a `Me` object with `id`, `handle`, and `displayName`. Read the fields you need before calling `me.free()`. Handle failed requests with `try/catch` at the call site. Reuse the client for the session and call `account.free()` at sign-out.

## License

[MIT](LICENSE)
