
Webpack 으로 번들링된 electron 프로젝트가 실행되며 스크립트가 로드되는 과정은 다음과 같다.
- 번들링 : 여러개의 javascript 파일을 하나로 합침


처음 앱이 실행되면, `main.js` 가 실행됨.
- 새 창이 열리고, 이 창은 `mainWindow` 라는 js변수에 저장됨
- `preload` 값이 `MAIN_WINDOW_PRELOAD_WEBPACK_ENTRY` 라는 Webpack 플러그인이 자동으로 주입하는 전역 상수가 입력되어있음.
- 실행 시, Webpack 은 자동으로 이 문자열을 `plugin-webpack` 에 등록된 javascript 파일들로 대치함.

`main.js`
```javascript
const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: MAIN_WINDOW_PRELOAD_WEBPACK_ENTRY,
    },
  });
```

plugin-webpack 는 `forge.config.js` 에 적혀있다.
- `forge.config.js` 에는 `@electron-forge/plugin-webpack` 라는 이름의 plugin 이 등록되어있다.
- 여기에 `renderer.js` 가 명시되어있음.
- 개발자가 원한다면 다른 js 파일도 추가하면 됨. 다만 새 js파일을 개발한 경우, `forge.config.js`는 건드리지말고, `renderer.js`에서 import 나 require 로 불러오는게 더 적절함.

```javascript
plugins: [
    {
      name: '@electron-forge/plugin-auto-unpack-natives',
      config: {},
    },
    {
      name: '@electron-forge/plugin-webpack',
      config: {
        mainConfig: './webpack.main.config.js',
        renderer: {
          config: './webpack.renderer.config.js',
          entryPoints: [
            {
              html: './src/index.html',
              js: './src/renderer.js',
              name: 'main_window',
              preload: {
                js: './src/preload.js',
              },
            },
          ],
        },
      },
    },
```