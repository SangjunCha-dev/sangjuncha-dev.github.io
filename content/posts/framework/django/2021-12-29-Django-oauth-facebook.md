---
title: "Django 소셜로그인(oauth) facebook 연동"
date: 2022-01-07T19:37:11+09:00
description: Django 소셜로그인(oauth) facebook 연동
tags: ["django", "oauth", "facebook"]
categories: ["Django", "oauth", "facebook"]
---



django restframework 기반의 페이스북(facebook) 인증 로그인 백엔드서버로 별도의 `auth`관련 라이브러리는 설치하지 않고 구현한다.

[페이스북 개발자 사이트](https://developers.facebook.com/)에서 사용하는 환경변수들이 등록되어있다는 가정하에 진행한다.

페이스북의 경우 다른 소셜로그인과는 용어나 흐름이 달라 추가로 설명하고 시작한다.


---

## 1. 페이스북 auth 용어 및 각 페이지 설명

### 1.1. Graph API 용어

Graph API는 Facebook 플랫폼에서 데이터를 요청 및 응답받는 기본적인 수단이다. 프로그래밍 방식으로 데이터 쿼리, 새 스토리 게시, 광고 관리, 사진 업로드를 비롯한 다양한 작업을 수행할 수 있는 HTTP 기반 API이다.  

Graph API는 노드, 에지 및 필드로 구성된다. 
- `노드` : 특정 개체에 대한 데이터 조회
- `에지` : 단일 개체에서 개체 컬렉션 조회
- `필드` : 컬렉션의 단일 개체 또는 각 개체에 대한 데이터 조회

#### 1.1.1. HTTP

모든 데이터 전송은 HTTP/1.1을 따르며 모든 엔드포인트에는 `HTTPS`가 필요하다.  
Graph API는 HTTP 기반이므로 cURL, urllib 등 HTTP 라이브러리가 있는 모든 언어에서 사용할 수 있다. 즉 브라우저에서 직접 Graph API를 사용할 수 있다.  


#### 1.1.2. 호스트 URL

거의 모든 요청이 `graph.facebook.com` 호스트 URL에 전달된다.  
한 가지 예외는 `graph-video.facebook.com`을 사용하는 동영상 업로드이다.


#### 1.1.3. 노드

`노드`는 고유 ID를 갖는 개별 개체이다.  
각각 Facebook의 사용자를 나타내는 고유한 ID를 갖는 수많은 사용자 노드 개체가 있고, 페이지, 그룹, 게시물, 사진 및 댓글은 Facebook 소셜 Graph에서 사용하는 노드의 일부이다.


사용자 노드에 대한 호출

```bash
curl -i -X GET \
  "https://graph.facebook.com/USER-ID?access_token=ACCESS-TOKEN"
```

```json
{  
  "name": "Your Name",  
  "id": "YOUR-USER-ID"  
}  
```


#### 1.1.4. /me

`/me` 노드는 현재 API 호출을 보내는 데 사용하는 액세스 토큰을 보유한 사용자나 페이지의 개체 ID로 변환하는 특수한 엔드포인트이다. 사용자 액세스 토큰이 있으면 다음을 사용하여 사용자 이름과 ID를 가져올 수 있다.

```bash
curl -i -X GET \
  "https://graph.facebook.com/me?access_token=ACCESS-TOKEN"
```


#### 1.1.5. 필드

필드는 노드 속성이다. 노드나 에지를 쿼리하면 기본적으로 필드 세트가 반환된다. 하지만 `fields` 매개변수에 각 필드를 나열하여 반환할 필드를 지정하면 지정한 필드와 (항상 반환되는)개체의 ID만 반환된다.

```bash
curl -i -X GET \
  "https://graph.facebook.com/USER-ID?fields=id,name,email&access_token=ACCESS-TOKEN"
```

```json
{
  "id": "USER-ID",
  "name": "EXAMPLE NAME",
  "email": "EXAMPLE@EMAIL.COM"
}
```


#### 1.1.6. 버전

Graph API는 분기별로 릴리스되어 여러 버전이 있다. 예를 들어 버전 12.0을 호출하는 방법은 다음과 같다.  
버전 명시 없이 호출할 경우 가장 오래된 버전으로 호출된다.  

```bash
curl -i -X GET \
  "https://graph.facebook.com/v12.0/USER-ID/photos
    ?access_token=ACCESS-TOKEN"
```


#### 1.1.7. Graph 테스트 URL

[Graph Explorer](https://developers.facebook.com/tools/explorer)


---

### 1.2. 로그인 직접 빌드하기


#### 1.2.1. 로그인 대화 상자 호출 및 리디렉션 URL 설정

```
https://www.facebook.com/v12.0/dialog/oauth?
  client_id={app-id}
  &redirect_uri={redirect-uri}
  &state={state-param}
```

필수 매개변수
- client_id : 앱 대시보드에서 볼수 있는 앱 ID
- redirect_uri : 사용자가 다시 로그인하도록 리디렉션할 URL
- state : 요청과 콜백 사이의 상태를 유지하기 위해 앱에서 생성한 문자열 값, 사이트 간 요청 위조를 방지하는 데 사용

선택적 매개변수
- response_type : 앱으로 다시 리디렉션할 때 포함된 응답 데이터가 URL 매개변수인지 프래그먼트인지 결정
  - code : 응답 데이터는 URL 매개변수로 포함되어 있으며, code 매개변수(각 로그인 요청에 고유한 암호화된 문자열)를 포함
    - response query_string 값을 `callbackurl/?code=` 형식으로 받는다.
  - token : 응답 데이터는 URL 프래그먼트로 포함되어 있으며, 액세스 토큰을 포함
    - response query_string 값을 `callbackurl/?#access_token=` 형식으로 받는다.
  - code%20token : 응답 데이터는 URL 프래그먼트로 포함되어 있으며, 액세스 토큰과 code 매개변수를 모두 포함
    - response query_string 값을 `callbackurl/?#access_token=&code=` 형식으로 받는다.
  - granted_scopes : 사용자가 로그인 시점에 앱에 부여한 모든 권한의 쉼표로 구분한 리스트를 반환
- scope : 앱 사용자에게 요청할 권한의 쉼표 또는 공백으로 구분한 리스트

예시코드에서는 `response_type=code` 형식을 사용한다.


#### 1.2.2. ID 확인

- code : 수신하면 엔드포인트를 사용하여 액세스 토큰과 교환해야 한다. 호출에는 앱 시크릿 코드가 포함되므로 서버 간 호출이어야 한다.
- token : 수신한 후 인증해야 한다. 토큰을 생성할 대상 사용자와 대상 앱을 표시하도록 검사 엔드포인트로 API 호출을 보내야 한다.
- code와 token을 모두 수신했을 때는 두 단계를 모두 실행해야 한다.


#### 1.2.3. 액세스 토큰 발급

액세스 토큰은 앱에서 Graph API 호출에 사용된다.

1. 요청  

    ```
    GET https://graph.facebook.com/v12.0/oauth/access_token?
      client_id={app-id}
      &redirect_uri={redirect-uri}
      &client_secret={app-secret}
      &code={code-parameter}
    ```

    필수 매개변수
    - client_id : 앱 ID
    - redirect_uri : OAuth 로그인 절차를 시작할 때 사용하는 원본 request_uri와 동일해야 한다.
    - client_secret : 고유한 앱 시크릿 코드이며, 앱 대시보드에 표시된다.
    - code : 위의 로그인 대화 상자 리디렉션에서 수신한 매개변수.

2. 응답

    ```json
    {
      "access_token": "{access-token}", 
      "token_type": "{type}",
      "expires_in":  "{seconds-til-expiration}"
    }
    ```


#### 1.2.4. 앱 액세스 토큰 발급

서버에서 Facebook API 요청할 떄 사용한다.

```
"https://graph.facebook.com/oauth/access_token
  ?client_id={your-app-id}
  &client_secret={your-app-secret}
  &grant_type=client_credentials"
```

다른 방법으로 앱 액세스 토큰을 사용하지 않아도 되는 다른 방법으로 그래프 API를 호출할 수 있다.

호출할 때 앱 ID와 앱 시크릿 코드만 access_token 매개변수로 전달할 수 있다.

```
"https://graph.facebook.com/{api-endpoint}&access_token={your-app_id}|{your-app_secret}"
```


#### 1.2.5. 액세스 토큰 검사

response_type 유형으로 사용하는지 여부와 관계없이 액세스 토큰을 처리해야 한다. 

요청

```
GET graph.facebook.com/debug_token?
   input_token={token-to-inspect}
   &access_token={app-token-or-admin-token}
```

매개변수
- input_token : 검사해야 할 토큰
- access_token : 앱 개발자의 액세스 토큰 또는 앱 액세스 토큰

응답

```json
{
  "data": {
    "app_id": 138483919580948, 
    "type": "USER",
    "application": "Social Cafe", 
    "expires_at": 1352419328, 
    "is_valid": true, 
    "issued_at": 1347235328, 
    "metadata": {
      "sso": "iphone-safari"
    }, 
    "scopes": [
      "email", 
      "publish_actions"
    ], 
    "user_id": "1207059"
  }
}
```

서버에서 app_id 값 비교를 통해 유효한 액세스 토큰인지 확인할 수 있다.


---

## 2. Facebook 로그인 변수 설정

```python
import os

FACEBOOK_CONFIG = {
    "FACEBOOK_APP_ID": APP_ID,
    "FACEBOOK_REDIRECT_URI": REDIRECT_URI,
    "FACEBOOK_APP_SECRET_CODE": APP_SECRET_CODE
    "STATE" : 임의의 랜덤코드,
}

facebook_graph_url = "https://graph.facebook.com/v12.0"
facebook_login_url = "https://www.facebook.com/v12.0/dialog/oauth"
facebook_token_url = f"{facebook_graph_url}/oauth/access_token"  
facebook_debug_token_url = "https://graph.facebook.com/debug_token"
facebook_profile_url = f"{facebook_graph_url}/me"  # 사용자 정보 요청
```

- FACEBOOK_REDIRECT_URI: https://localhost 리디렉션은 개발 모드에 있는 동안만 기본으로 허용된다.

### 2.1. Facebook Developers 계정 등록

1. [페이스북 개발자 사이트](https://developers.facebook.com/)에서 페이스북 로그인후 개발자 계정에 가입한다.
  ![](../images/django-oauth-facebook/oauth_facebook-1.png?raw=true)

2. 이메일 인증받을 주소를 입력한다. (기본값 페이스북에 등록된 이메일)
  ![](../images/django-oauth-facebook/oauth_facebook-2.png?raw=true)

3. 관심사를 선택한다.
  ![](../images/django-oauth-facebook/oauth_facebook-3.png?raw=true)


### 2.2. APP 생성

1. 앱을 생성한다.
  ![](../images/django-oauth-facebook/oauth_facebook-4.png?raw=true)

2. 페이스북 소셜 로그인 연동을 위해 생성할 앱으로 `소비자` 유형을 선택한다.
  ![](../images/django-oauth-facebook/oauth_facebook-5.png?raw=true)

3. 표시이름: 사용자가 앱에 로그인할 표시될 앱이름을 설정한다.
  ![](../images/django-oauth-facebook/oauth_facebook-6.png?raw=true)


### 2.3. 앱 로그인 설정

1. 앱 대시보드에서 APP ID를 확인할 수 있다.

2. 앱에 `Facebook 로그인` 설정한다.
  ![](../images/django-oauth-facebook/oauth_facebook-8.png?raw=true)

3. 로그인할 플랫폼 유형 `웹`을 선택한다.
  ![](../images/django-oauth-facebook/oauth_facebook-9.png?raw=true)

4. 리다이렉트 URI를 사용하는 `사이트 주소`를 입력한다.
  ![](../images/django-oauth-facebook/oauth_facebook-10.png?raw=true)

5. Facebook 로그인 → 설정 → 유효한 OAuth `리디렉션 URI`를 입력한다.
  - 리디렉션 URI: 페이스북 로그인 정보를 전송받을 URI
  - URI 리디렉션 유효성 검사기: 실제 웹서버와 통신이 되는지 검증한다.
  ![](../images/django-oauth-facebook/oauth_facebook-11.png?raw=true)

6. `public_profile` 권한 설정에관한 경고문이 나올경우 `Get Advanced Access` 메뉴로 간다.
  ![](../images/django-oauth-facebook/oauth_facebook-12.png?raw=true)

7. `public_profile` 권한에서 `고급 엑세스 이용하기`로 이동한다.
  ![](../images/django-oauth-facebook/oauth_facebook-13.png?raw=true)

8. 고급 엑세스에 대한 개인정보 이용에 `동의`를 한다.
  ![](../images/django-oauth-facebook/oauth_facebook-14.png?raw=true)


---

## 3. Facebook 로그인 페이지

사용자가 로그인 테스트 서버로 접속시 redirect URI를 반환한다.

```python
class FacebookLoginView(APIView):
    permission_classes = (AllowAny,)

    def get(self, reqeust):
        '''
        facebook code 요청
        '''
        client_id = FACEBOOK_CONFIG['FACEBOOK_APP_ID']
        redirect_uri = FACEBOOK_CONFIG['FACEBOOK_REDIRECT_URI']
        state = FACEBOOK_CONFIG['STATE']

        uri = f"{facebook_login_url}?client_id={client_id}&redirect_uri={redirect_uri}&state={state}&response_type=code&scope=email%20public_profile"

        res = redirect(uri)
        return res
```

uri 파라미터 설명

- 필수 매개변수
  - client_id : 앱 대시보드에서 볼수 있는 앱 ID
  - redirect_uri : 페이스북 로그인 정보를 전송받을 URI
  - state : 요청과 콜백 사이의 상태를 유지하기 위해 앱에서 생성한 문자열 값, 사이트 간 요청 위조를 방지하는 데 사용

- 선택적 매개변수
  - response_type : 앱으로 다시 리디렉션할 때 포함된 응답 데이터가 URL 매개변수인지 프래그먼트인지 결정
    - code : 응답 데이터는 URL 매개변수로 포함되어 있으며, code 매개변수(각 로그인 요청에 고유한 암호화된 문자열)를 포함
  - scope : 앱 사용자에게 요청할 권한의 쉼표 또는 공백으로 구분한 리스트


---

## 4. Facebook Callback 함수

사용자가 oauth 로그인시 code 검증 및 로그인 처리한다.

```python
class FacebookCallbackView(APIView):
    permission_classes = (AllowAny,)

    @swagger_auto_schema(query_serializer=CallbackUserInfoSerializer)
    def get(self, request):
        '''
        facebook access_token 요청 및 user_info 요청
        '''
        data = request.query_params.copy()

        code = data.get('code')
        if not code:
            return Response(status=status.HTTP_400_BAD_REQUEST)

        query_string = {
            'client_id': FACEBOOK_CONFIG['FACEBOOK_APP_ID'],
            'redirect_uri': FACEBOOK_CONFIG['FACEBOOK_REDIRECT_URI'],
            'client_secret': FACEBOOK_CONFIG['FACEBOOK_APP_SECRET'],
            'code': code,
        }
        token_res = requests.get(facebook_token_url, params=query_string)

        token_json = token_res.json()
        access_token = token_json.get('access_token')

        if not access_token:
            return Response(status=status.HTTP_400_BAD_REQUEST)

        # 엑세스 토큰 검사 및 계정 정보 조회
        query_string = {
            'input_token': access_token,
            'access_token': f"{FACEBOOK_CONFIG['FACEBOOK_APP_ID']}|{FACEBOOK_CONFIG['FACEBOOK_APP_SECRET']}",
        }
        user_info_res = requests.get(facebook_debug_token_url, params=query_string)
        user_info_json = user_info_res.json()

        facebook_account = user_info_json.get('data')
        if (not facebook_account) or (not facebook_account.get('is_valid')):
            return Response(status=status.HTTP_400_BAD_REQUEST)

        user_id = facebook_account.get('user_id')

        query_string = {
            'fields': 'email', # email,name 형식으로 사용가능
            'access_token': access_token,
        }
        user_profile_res = requests.get(facebook_profile_url, params=query_string)
        user_profile_json = user_profile_res.json()

        social_type = 'facebook'
        social_id = f"{social_type}_{user_id}"
        user_email = user_profile_json.get('email')

        '''
        # 회원가입 및 로그인 처리 알고리즘 추가필요
        '''

        # 테스트 값 확인용
        res = {
            'social_type': social_type,
            'social_id': social_id,
            'user_email': user_email,
        }
        response = Response(status=status.HTTP_200_OK)
        response.data = res
        return res
```

<!-- 
## 사용자 이메일 조회

```
https://graph.facebook.com/v12.0/me?fields=email&access_token=
```

## 로그인 상태 확인

### 페이지를 읽어들이는 중에 사용자의 로그인 상태를 확인하기 위해 실행되는 코드

```
FB.getLoginStatus(function(response) {
    statusChangeCallback(response);
});
```

콜백에 제공되는 response

```
{
    status: 'connected',
    authResponse: {
        accessToken: '...',
        expiresIn:'...',
        signedRequest:'...',
        userID:'...'
    }
}
```

status는 앱 사용자의 로그인 상태는 다음 중 하나이다.
- connected : Facebook과 앱에 로그인한 상태
- not_authorized : Facebook 로그인했지만 앱에는 로그인하지 않은 상태
- unknown : 사용자가 Facebook에 로그인하지 않았다.

connected 상태인 경우 authResponse가 포함되며 다음과 같이 구성되어 있다.
- accessToken : 앱 사용자의 액세스 토큰
- expiresIn : 토큰 만료시간
- signedRequest : 앱 사용자에 대한 정보를 포함하는 서명된 매개변수
- userID : 앱 사용자 ID

앱에서 앱 사용자의 로그인 상태를 알게 되면 다음 중 하나를 수행할 수 있다.
- 사용자가 Facebook과 앱에 로그인한 경우 앱의 로그인된 환경으로 리디렉션한다.
- 사용자가 앱에 로그인하지 않았거나 Facebook에 로그인하지 않은 경우 FB.login()을 사용하여 로그인 대화 상자에 메시지를 표시하거나 로그인 버튼을 표시한다.
-->


---

## 5. 데이터 삭제 요청 콜백

사용자 데이터에 액세스하는 앱은 사용자가 데이터 삭제를 요청할 방법을 제공해야 한다.

데이터 삭제 콜백은 앱 사용자가 앱을 삭제하고 데이터 삭제를 요청할 때마다 호출된다.


### 5.1. 콜백 구현

앱의 앱 대시보드에 있는 Facebook 로그인 > 설정 페이지의 데이터 삭제 요청 URL 필드에 등록되어야 한다.

URL이 포함된 JSON 응답이 반환되며, 이 URL을 통해 사용자가 삭제 요청의 상태와 영숫자 인증 코드를 확인할 수 있다.

```json
{
  "url": "<url>",
  "confirmation_code": "<code>"
}
```


---

## 6. 로그인

웹서버에서 설정한 apple oauth 로그인 페이지로 접근하면 사전에 설정한 애플 리다이렉트 URI로 접근하는데 설정에 문제가 없다면 로그인 페이지로 접속된다.


---

## 7. 예시 코드 Git 

- [django_oauth](https://github.com/SangjunCha-dev/django_oauth)


---

## 참고(Reference)

- [로그인 플로 직접 빌드](https://developers.facebook.com/docs/facebook-login/manually-build-a-login-flow/#login)
- [액세스 토큰](https://developers.facebook.com/docs/facebook-login/access-tokens)

<!-- - [웹용 Facebook 로그인의 액세스 토큰](https://developers.facebook.com/docs/facebook-login/web/accesstokens) -->
- [Graph API 개요](https://developers.facebook.com/docs/graph-api/overview)
- [Graph API 시작하기](https://developers.facebook.com/docs/graph-api/get-started)
- [Graph API User](https://developers.facebook.com/docs/graph-api/reference/user/)
- [데이터 삭제 요청 콜백](https://developers.facebook.com/docs/development/create-an-app/app-dashboard/data-deletion-callback)
- [웹용 Facebook 로그인의 권한 관리](https://developers.facebook.com/docs/facebook-login/web/permissions)