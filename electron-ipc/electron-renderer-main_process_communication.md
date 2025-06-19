# 1. 렌더러 → 메인 IPC 통신

사용 목적:
•	유저 조작(버튼 클릭 등)에 따라 자식창 생성, 상태 요청, 명령 전달 등

사용 방식:
•	메시지 전송: ipcRenderer.send(...) 또는 invoke(...)
•	메인에서 핸들링: ipcMain.on(...) 또는 handle(...)

```javascript
// renderer
ipcRenderer.send('open-child-window');

// main
ipcMain.on('open-child-window', handleOpenChildWindow);
```


# 2. 메인 프로세스에서 자식창에 자바스크립트 실행

사용 목적:
•	자식창에서 직접 DOM 조작, 클릭, 값 설정 등 자동화 수행

사용 방식:
```javascript
childWindow.webContents.executeJavaScript(`
  document.querySelector('#submit').click();
`);
```

# 3. 메인 프로세스 전역 상태 관리 (store)

사용 목적:
•	자식창 객체 관리
•	현재 자동화 상태, 세션 정보, 사용자 설정 등 저장

예시 구조:

```javascript
// window-store.js
const state = {
  childWindow: null,
  sessionData: {},
};

module.exports = {
  setChildWindow(win) { state.childWindow = win; },
  getChildWindow() { return state.childWindow; },
  setSession(key, val) { state.sessionData[key] = val; },
  getSession(key) { return state.sessionData[key]; },
};
```

# 4. 메인 → 렌더러 메시지 전달 (webContents.send)

사용 목적:
•	자식창 열림/닫힘, 자동화 진행 상황 등 사용자에게 알림
•	렌더러가 계속 폴링하지 않고도 실시간 반응 가능

예시:
```javascript
mainWindow.webContents.send('child-status', 'opened');
```

렌더러에서:
```javascript
ipcRenderer.on('child-status', (_, status) => {
  updateUI(status);
});
```


🧠 결론

이 4가지가 모두 구현되어 있으면, Electron 기반 매크로 컨트롤러의 핵심 구조는 완성된 셈입니다.
나머지는 세부 기능(자동화 명령, 오류 처리, UI 디자인 등)만 붙이면 됩니다.