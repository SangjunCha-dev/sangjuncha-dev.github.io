---
title: "golang fcm(firebase cloud messaging) push 메세지 사용법"
date: 2021-12-18T17:49:21+09:00
description: golang fcm push 메세지 사용법
tags: ["go", "fcm"]
categories: ["Go", "fcm"]
---



Firebase Cloud Messaging(FCM)은 firebase에서 무료로 메시지 전송할 수 있는 교차 플랫폼 메시징 솔루션이다.  
최대 4,000바이트의 페이로드를 클라이언트 앱에 전송할 수 있다.

아래의 예제는 `Go언어 기반의 백엔드 서버` 예시이며 fcm token이 이미 발급받았다는 가정하에 진행된다.



## 라이브러리 설치

```bash
> go get firebase.google.com/go/v4
> go get google.golang.org/api
```



## Firebase APP 초기화

[사용자 인증 정보 제공](https://firebase.google.com/docs/cloud-messaging/auth-server?hl=ko#provide-credentials-manually)의 안내에 따라 서비스 계정의 비공개 키 파일을 다운받는다.

해당 파일에는 다음과 같은 정보가 있다.

파일명은 `serviceAccountKey.json`라고 가정한다.

```json
{
  "type": "service_account",
  "project_id": "[project_id]",
  "private_key_id": "[private_key_id]",
  "private_key": "[private_key]",
  "client_email": "[client_email]",
  "client_id": "[client_id]",
  "auth_uri": "https://accounts.google.com/o/oauth2/auth",
  "token_uri": "https://oauth2.googleapis.com/token",
  "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
  "client_x509_cert_url": "[client_x509_cert_url]"
}

```

<br><br>

Firebase APP 초기화

```go
var (
	client *messaging.Client
	ctx    context.Context = context.Background()
)

func initApp() {
	var err error

	type AccountKey struct {
		ProjectID string `json:"project_id"`
	}
	accountkey := AccountKey{}

	serviceAccountKeyPath := "./serviceAccountKey.json"

	keyFile, err := ioutil.ReadFile(serviceAccountKeyPath)
	if err != nil {
		log.Print("[initApp] setConfig error :", err)
	}
	if err := json.Unmarshal(keyFile, &accountkey); err != nil {
		log.Print("[initApp] getConfig error :", err)
	}

	opt := option.WithCredentialsFile(serviceAccountKeyPath)
	config := &firebase.Config{ProjectID: accountkey.ProjectID}

	app, err := firebase.NewApp(ctx, config, opt)
	if err != nil {
		log.Fatalln("[initApp] initializing app error :", err)
	}

	// Obtain a messaging.Client from the App.
	client, err = app.Messaging(ctx)
	if err != nil {
		log.Fatalln("[initApp] getting Messaging client error :", err)
	}
}
```



## 특정 기기에 메시지 전송

```go
func sendNotification(title string, body string, fcmToken string) {

	// This registration token comes from the client FCM SDKs.
	registrationToken := fcmToken

	notification := &messaging.Notification{
		Title: title,
		Body:  body,
	}

	// See documentation on defining a message payload.
	message := &messaging.Message{
		Data: map[string]string{
			"Type":    title,
			"Content": body,
		},
		Token:        registrationToken,
		Notification: notification,
	}

	response, err := client.Send(ctx, message)
	if err != nil {
		fmt.Println("[sendNotification] client.Send error :", err, registrationToken)
	}
	_ = response
}
```



## 여러 기기에 메시지 전송

호출당 최대 500개의 기기 등록 토큰을 지정할 수 있다.

```go
func sendNotifications(title string, body string, fcmTokens []string) {
	// fcm tokens
	registrationTokens := fcmTokens

	notification := &messaging.Notification{
		Title: title,
		Body:  body,
	}

	message := &messaging.MulticastMessage{
		Data: map[string]string{
			"Type":    title,
			"Content": body,
		},
		Tokens:       registrationTokens,
		Notification: notification,
	}

	br, err := client.SendMulticast(ctx, message)
	if err != nil {
		panic(err)
	}

	fmt.Println("[sendNotifications] ", br.SuccessCount, " messages were sent successfully, ", br.FailureCount, " messages fail count")

	if br.FailureCount > 0 {
		var failedTokens []string
		for idx, resp := range br.Responses {
			if !resp.Success {
				// The order of responses corresponds to the order of the registration tokens.
				failedTokens = append(failedTokens, registrationTokens[idx])
			}
		}

		fmt.Printf("[sendNotifications] List of tokens that caused failures: %v\n", failedTokens)
	}
}
```

<br><br>

firebase 서버 상황에 따라 Push 알림 수신까지 수초~수분 걸릴 수 있다.



## 예시코드 Git 주소

[golang-fcm-push](https://github.com/SangjunCha-dev/blog/tree/main/golang/golang-fcm-push)



## 참조 Docs URL

- [Firebase 클라우드 메시징](https://firebase.google.com/docs/cloud-messaging?hl=ko)
- [특정 기기에 메시지 전송](https://firebase.google.com/docs/cloud-messaging/send-message?hl=ko#send-messages-to-specific-devices)
- [여러 기기에 메시지 전송](https://firebase.google.com/docs/cloud-messaging/send-message?hl=ko#send-messages-to-multiple-devices)