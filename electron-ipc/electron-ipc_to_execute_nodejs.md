# Electron 앱에서 브라우저 javascript 가 아니라, 로컬의 nodejs 를 실행하기
> Electron 앱에서 작동하는 브라우저 javascript 는 기능이 제한됨.

> 브라우저 javascript는 브라우저의 권한을 벗어나는 작업, 예를들어 파일시스템에 접근하는 등의 기능을 수행할 수 없음.

> Electron 앱은 브라우저창에서 작동하므로, 브라우저 권한을 벗어나는 작업을 하려면,
브라우저에서 ipc 메세지를 보내고, 이 메세지를 수신 시 main 프로세스에서 nodejs 함수를 실행시키는 ipc 채널 이벤트 리스너 방식을 사용해야함.

## 작동 순서

renderer.js 는 "브라우저 자바스크립트" 임.

-> renderer.js 는 nodejs api 는 사용할 수 없지만, electron API는 사용할 수 있음.

1. preload.js 에서 electronAPI 를 하나 새로 만든다음, 이 API 를 call 하면 ipc 채널로 "ipc 채널명"을 ipc 메세지로 보내도록 한다.

2. main.js 에서 "ipc 채널명" 이벤트 리스너를 등록. 이벤트 리스너로 등록된 nodejs 함수를 실행할 수 있도록 한다.

3. renderer.js 에서 아까 만든 electronAPI 를 call 한다.

> renderer.js -> electronAPI -> preload.js -> ipc -> main.js -> eventlistener


# 코드 작성

1. `preload.js`

렌더러에서 사용할 수 있도록 API를 노출

```javascript
// See the Electron documentation for details on how to use preload scripts:
// https://www.electronjs.org/docs/latest/tutorial/process-model#preload-scripts

const { contextBridge, ipcRenderer } = require('electron');

contextBridge.exposeInMainWorld('electronAPI', {
  openUrl: (url) => ipcRenderer.send('open-url', url)
});
```

2. `main.js`

ipc 채널에 대한 이벤트 리스너 등록

```javascript
...
// Handle creating/removing shortcuts on Windows when installing/uninstalling.
if (require('electron-squirrel-startup')) {
  app.quit();
}

const createWindow = () => {
  // Create the browser window.
  const mainWindow = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      preload: MAIN_WINDOW_PRELOAD_WEBPACK_ENTRY,
    },
  });

  // and load the index.html of the app.
  mainWindow.loadURL(MAIN_WINDOW_WEBPACK_ENTRY);

  // Open the DevTools.
  mainWindow.webContents.openDevTools();
};

const { ipcMain } = require('electron'); // ✅ 이 줄을 추가

const handleOpenUrl = require('./main-api/open-url'); // ✅ 이 줄을 추가
ipcMain.on('open-url', handleOpenUrl); // ✅ 이 줄을 추가

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.whenReady().then(() => {
  createWindow();

  // On OS X it's common to re-create a window in the app when the
  // dock icon is clicked and there are no other windows open.
  app.on('activate', () => {
    if (BrowserWindow.getAllWindows().length === 0) {
      createWindow();
    }
  });
});
...
```

3. `main-api/open-url.js`

```javascript
const { BrowserWindow } = require('electron');

module.exports = (event, url) => {
  const win = new BrowserWindow({
    width: 1024,
    height: 768,
    webPreferences: {
      contextIsolation: true,
    }
  });

  win.loadURL(url);
};
```


3. `renderer.js`

html 의 버튼을 클릭 시 electronAPI 를 call 하도록 구현

```javascript
/**
 * This file will automatically be loaded by webpack and run in the "renderer" context.
 * To learn more about the differences between the "main" and the "renderer" context in
 * Electron, visit:
 *
 * https://electronjs.org/docs/tutorial/process-model
 *
 * By default, Node.js integration in this file is disabled. When enabling Node.js integration
 * in a renderer process, please be aware of potential security implications. You can read
 * more about security risks here:
 *
 * https://electronjs.org/docs/tutorial/security
 *
 * To enable Node.js integration in this file, open up `main.js` and enable the `nodeIntegration`
 * flag:
 *
 * ```
 *  // Create the browser window.
 *  mainWindow = new BrowserWindow({
 *    width: 800,
 *    height: 600,
 *    webPreferences: {
 *      nodeIntegration: true
 *    }
 *  });
 * ```
 */

import './index.css';

console.log('👋 This message is being logged by "renderer.js", included via webpack');

const openBtn = document.getElementById('open-naver');

if (openBtn) {
  openBtn.addEventListener('click', () => {
    window.electronAPI?.openUrl?.('https://www.naver.com');
  });
}
```

4. `index.html` 에 버튼 추가


```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello Electron!</title>

  </head>
  <body>
    <button id="open-naver">네이버 접속하기</button>
    <script src="./renderer.js"></script>
  </body>
</html>
```