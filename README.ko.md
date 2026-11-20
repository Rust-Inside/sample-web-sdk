# Sample Rust Core · 웹 SDK

[English](README.md) | [한국어](README.ko.md)

웹 앱에서 SDK의 `account.getMe()`를 호출하면 로그인한 사용자의 프로필을 받습니다. 인증 헤더를 붙여 `GET /v1/me`를 요청하는 코드는 Rust 라이브러리에 있습니다. Rust Inside는 이 라이브러리를 WebAssembly로 빌드하고 웹 SDK를 `sample-rust-core-web` npm 패키지로 배포합니다. [Rust 구현](https://github.com/Rust-Inside/sample-rust-core/blob/v0.2.0/src/api.rs)

## npm으로 설치

웹 프로젝트에서 다음 명령을 실행하세요. npm이 릴리스에서 패키지를 내려받아 설치합니다. [v0.2.0 릴리스](https://github.com/Rust-Inside/sample-web-sdk/releases/tag/v0.2.0)

```sh
npm install https://github.com/Rust-Inside/sample-web-sdk/releases/download/v0.2.0/sample-rust-core-web-0.2.0.tgz
```

브라우저는 ES 모듈과 WebAssembly를 지원해야 합니다. 번들러가 배포 결과물에 패키지의 `index_bg.wasm`을 포함하는지 확인하세요.

## 로그인한 사용자 조회

앱을 시작할 때 `init()`을 한 번 호출해 WebAssembly를 불러옵니다. 로그인 후 API 서버 주소와 `Authorization` 헤더 값으로 `AccountClient`를 만드세요. 서버가 Bearer 인증을 쓰면 `Bearer` 접두사까지 전달합니다.

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

`getMe()`가 반환하는 `Me`에서 `id`, `handle`, `displayName` 중 필요한 값을 읽은 뒤 `me.free()`를 호출하세요. 요청 오류는 호출부에서 `try/catch`로 처리합니다. 클라이언트는 로그인 세션 동안 재사용하고 로그아웃할 때 `account.free()`로 해제하세요.

## 라이선스

[MIT](LICENSE)
