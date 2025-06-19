
https://www.electronforge.io/guides/create-and-add-icons

### ico, icns 파일 생성

- ico : Windows 용
- icns : MacOS 용

https://cloudconvert.com/png-to-ico

https://cloudconvert.com/png-to-icns


> 각 운영체제별로 올바른 아이콘이 없으면 빌드 시 아이콘이 적용 안됨.

### 아이콘 파일 추가

앱 이름(`my-app`) 기준으로 `my-app/assets/icon.ico` 또는 `my-app/assets/icon.icns`

- `assets` 폴더는 직접 만든다

### 설정파일에 아이콘 파일 경로 추가

앱 이름(`my-app`) 기준으로 아래와 같이 `forge.config.js` 를 수정하면 됨.

- `icon.ico` 처럼 파일 확장자를 붙이지 않고, `icon` 으로 파일 이름만 적는다.
- `/assets/icon` 이 아니라 `assets/icon` 임에 주의. (맨 처음 슬래쉬가 없다.)

```jsx
module.exports = {
  // ...
  packagerConfig: {
	  asar: true,
    icon: 'assets/icon'
  }
  // ...
};
```

### 디버그 모드로 실행했을때는 아이콘 적용안되고, 빌드해야 적용됨

디버그 모드 : 아이콘 적용안됨

```bash
npm run start
```

빌드 후 실행 : 아이콘 적용됨

```bash
npm run make
# my-app/out 폴더에 운영체제별로 실행파일이 생성된다.
```