### 주로 개발이 이루어지는 곳

`index.js` : Electron App Entrypoint

`renderer.js` : 앱의 기능 구현

`preload.js` : 바닐라 자바스크립트가 아니라, nodejs 에만 있는 기능(로컬 파일 접근과 같은 js 권한을 넘어서는 행위)을 사용할 때, 여기에 

| **파일** | **역할** | **수시로 수정?** |
| --- | --- | --- |
| `renderer.js` | 앱의 실제 기능 구현 (클릭 등) | ✅ 많이 수정 |
| `preload.js` | Node 기능을 안전하게 expose | ✅ 자주 수정 |
| `index.js (main.js)` | 창 생성, 앱 초기화 | ⭕ 보통 1회 세팅 |
| `index.html` | HTML UI 구성 | ✅ 많이 수정 |