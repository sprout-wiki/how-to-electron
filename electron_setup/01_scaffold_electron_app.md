## init


create-electron-app : electron 공식지원 CLI

npx : 패키지를 실행만하고 파일로 다운로드하지 않음

### create-electron-app 기본버전(간단한 앱 개발)

- `index.js` 하나만 생성됨
- 핫 리로드 지원 안함

```bash
npm init -y
npx create-electron-app my-app
```√## init

<aside>
💡

create-electron-app : electron 공식지원 CLI

npx : 패키지를 실행만하고 파일로 다운로드하지 않음

</aside>

create-electron-app 기본버전(간단한 앱 개발)

- `index.js` 하나만 생성됨
- 핫 리로드 지원 안함

```bash
npm init -y
npx create-electron-app my-app
```

### Webpack 버전 (복잡한 앱 개발)

- Webpack 버전은 `main.js`, `renderer.js` 로 구성됨.
- 핫 리로드 지원

```bash
npx create-electron-app my-app --template=webpack
```

### 테스트

```bash
cd my-app
npm run start
```

빌드

```bash
npm run build
```

## packaging

electron-builder : 공식은 아니지만 널리 쓰이는 서드파티 빌드 도구

electron-forge : 공식 빌드/패키징 도구


### electron-forge

create-electron-app CLI 를 사용하여 프로젝트를 init 하면 기본적으로 electron-forge 를 사용하도록 설정됨. (`package.json` - `devDependencies` 에 추가됨)


### electron-builder

```bash
npm install --save-dev electron-builder
```

```bash
npx electron-builder
```

## Hot Realoading

```bash
npm install --save-dev electron-reloader
```

`index.js` 에 아래 추가

```jsx
try {
  require('electron-reloader')(module);
} catch (_) {}
```