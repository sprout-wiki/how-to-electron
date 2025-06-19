# Electron 앱에서 브라우저 javascript 가 아니라, 로컬의 nodejs 를 실행하기
> Electron 앱에서 작동하는 브라우저 javascript 는 기능이 제한됨.

> 브라우저 javascript는 브라우저의 권한을 벗어나는 작업, 예를들어 파일시스템에 접근하는 등의 기능을 수행할 수 없음.

> Electron 앱은 브라우저창에서 작동하므로, 브라우저 권한을 벗어나는 작업을 하려면,
브라우저에서 ipc 메세지를 보내고, 이 메세지를 수신 시 main 프로세스에서 nodejs 함수를 실행시키는 ipc 채널 이벤트 리스너 방식을 사용해야함.

## 작동 순서

1. preload.js 에서 electronAPI 를 하나 새로 만든다음, 이 API 를 call 하면 ipc 채널로 "ipc 채널명"을 ipc 메세지로 보내도록 한다.

2. main.js 에서 "ipc 채널명" 이벤트 리스너를 등록. 이벤트 리스너로 등록된 nodejs 함수를 실행할 수 있도록 한다.

3. renderer.js 에서 아까 만든 electronAPI 를 call 한다.

> renderer.js -> electronAPI -> preload.js -> ipc -> main.js -> eventlistener

---
# 코드 작성

1. preload.js

렌더러에서 사용할 수 있도록 안전한 API를 노출

contextBridge.exposeInMainWorld('electronAPI', {
  openUrl: (url, title) => ipcRenderer.send('open-url', url, title)
});

---

2. main.js

ipc 채널에 대한 이벤트 리스너 등록

ipcMain.on('open-url', handleOpenUrl);  // 📌 이게 main 프로세스에서 동작할 로직 연결


---

3. renderer.js

버튼 클릭 등으로 사용자 이벤트 발생 시 해당 채널 호출

window.electronAPI.openUrl('https://ticketlink.co.kr', '티켓링크');