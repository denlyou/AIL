# Firebase for Web

## Cloud Messaging

- 푸시알림(Push Notification)을 구현하는 서비스

### 사전 체크

- HTTPS 프로토콜을 사용하는 사이트만 지원됨
  - [ServiceWorker](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers#you_need_https)를 사용하는데
    - ServiceWorker가 HTTPS만 지원

- 지원되는 웹 브라우저
  - 크롬(50+)
  - 파이어폭스(44+)
  - 오페라 모바일(37+)

> Quick Start Sample : https://github.com/firebase/quickstart-js/tree/master/messaging

### 클라이언트에서 설정 (웹사이트)

- [Firebase SDK 사용 설정](https://firebase.google.com/docs/web/setup)

```html
<script src="https://www.gstatic.com/firebasejs/4.2.0/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/4.2.0/firebase-messaging.js"></script>
<script>
  // Initialize Firebase
  // TODO: Replace with your project's customized code snippet
  var config = {
    apiKey: "<API_KEY>",
    authDomain: "<PROJECT_ID>.firebaseapp.com",
    databaseURL: "https://<DATABASE_NAME>.firebaseio.com",
    storageBucket: "<BUCKET>.appspot.com",
    messagingSenderId: "<SENDER_ID>",
  };
  firebase.initializeApp(config);
</script>
```

- [앱매니페스트](https://developers.google.com/web/fundamentals/engage-and-retain/web-app-manifest/) 설정
	- [호스팅ROOT]/`manifest.json` 파일

```json
{
  "//": "Some browsers will use this to enable push notifications.",
  "//": "It is the same for all projects, this is not your project's sender ID",
  "gcm_sender_id": "103953800507"
}
```
> 주의 : gcm_sender_id는 자신의 프로젝트의 설정에 있는 값이 아니라 '103953800507'로 고정값  

- 서비스워커 설정
	- [호스팅ROOT]/`firebase-messaging-sw.js` 파일



```js
// Give the service worker access to Firebase Messaging.
// Note that you can only use Firebase Messaging here, other Firebase libraries
// are not available in the service worker.
importScripts('https://www.gstatic.com/firebasejs/3.9.0/firebase-app.js');
importScripts('https://www.gstatic.com/firebasejs/3.9.0/firebase-messaging.js');

// Initialize the Firebase app in the service worker by passing in the
// messagingSenderId.
firebase.initializeApp({
  'messagingSenderId': 'YOUR-SENDER-ID'
});

// Retrieve an instance of Firebase Messaging so that it can handle background
// messages.
const messaging = firebase.messaging();
```

### 포그라운드(웹페이지)에서 수신
- 수신할 페이지 (도메인에 설정된 모든 페이지에서 수신해야...)

```js
// Handle incoming messages. Called when:
// - a message is received while the app has focus
// - the user clicks on an app notification created by a sevice worker
//   `messaging.setBackgroundMessageHandler` handler.
messaging.onMessage(function(payload) {
  console.log("Message received. ", payload);
  // ...
});
```

### 백그라운드에서 수신 처리

```js
messaging.setBackgroundMessageHandler(function(payload) {
  console.log('[firebase-messaging-sw.js] Received background message ', payload);
  // Customize notification here
  const notificationTitle = 'Background Message Title';
  const notificationOptions = {
    body: 'Background Message body.',
    icon: '/firebase-logo.png'
  };

  return self.registration.showNotification(notificationTitle,
      notificationOptions);
});
```

> (작성중)
