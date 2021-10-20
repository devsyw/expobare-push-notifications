## expobare push 알람 구현

#### lib 설치
`$ expo install expo-notifications`
`$ expo install expo-constants`

#### Usage
* 선언 
`import * as Notifications from 'expo-notifications';`
`import Constants from 'expo-constants';`

* 권한팝업
`const { status } = await Notifications.requestPermissionsAsync();`

* 기존권한
`const { status: existingStatus } = await Notifications.getPermissionsAsync();`

* 나의 notifications 토큰 가져오기
`let token = (await Notifications.getExpoPushTokenAsync()).data;`

* 푸시알람보내기
```javascript
	async (token) => await sendPushNotification(token);
	async function sendPushNotification(token) {
	  const message = {
		to: token,
		sound: 'default',
		title: '푸시알람 제목',
		body: '푸시알람 내용',
		data: 보낼 데이터 (json),
	  };

	  await fetch('https://exp.host/--/api/v2/push/send', {
		method: 'POST',
		headers: {
		  Accept: 'application/json',
		  'Accept-encoding': 'gzip, deflate',
		  'Content-Type': 'application/json',
		},
		body: JSON.stringify(message),
	  });
	}
```

* 받은 데이터 가져오기
```javascript
	const [notification, setNotification] = useState(false);

	useEffect(() => {
		//앱이 포그라운드로 표시되는 동안 알림이 수신될 때마다 발생
		notificationListener.current = Notifications.addNotificationReceivedListener(notification => {
		  setNotification(notification);
		});

		//사용자가 알림에 탭하거나 알림과 상호 작용할 때마다 발생(앱이 포그라운드되거나 백그라운드 처리되거나 실행 중지될 때 작동함).
		responseListener.current = Notifications.addNotificationResponseReceivedListener(response => {
		  console.log(response);
		});
		
		return () => {
		  Notifications.removeNotificationSubscription(notificationListener.current);
		  Notifications.removeNotificationSubscription(responseListener.current);
		};
	},[]);

	return (
		<View>
		    <Text>Title: {notification && notification.request.content.title} </Text> //(푸시알람 제목)
			<Text>Body: {notification && notification.request.content.body}</Text> //(푸시알람 내용)
			<Text>Data: {notification && JSON.stringify(notification.request.content.data)}</Text> //(전달된 데이터 내용) 등등
		</View>
	)
```

#### 사용 커스텀 방향
* 회원가입 시 토큰 저장
* 저장된 토큰을 AppInit 단계에서 Reducer에 보관
* 이모지 전송 또는 그룹원 초대시 이미 리스트에 토큰이 있어야함
* 해당 토큰으로 push data 전송