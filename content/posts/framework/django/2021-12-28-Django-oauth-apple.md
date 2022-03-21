---
title: "Django 소셜로그인(oauth) apple 연동"
date: 2021-12-28T19:37:11+09:00
description: Django oauth apple login
tags: ["django", "oauth", "apple"]
categories: ["Django", "oauth", "apple"]
---



django restframework 기반의 애플(apple) 인증 로그인 백엔드서버로 별도의 `auth`관련 라이브러리는 설치하지 않고 구현한다.

[애플 개발자 사이트](https://developer.apple.com/)에서 사용하는 환경변수들이 등록되어있다는 가정하에 진행한다.


---

## 1. 라이브러리 설치

```bash
$ pip install django

# restframework
$ pip install djangorestframework
$ pip install djangorestframework-simplejwt

# pyjwt[crypto]
$ pip install pyjwt[crypto]
```


---

## 2. Apple 로그인 변수 설정

```python
import os

APPLE_CONFIG = {
    "APPLE_TEAM_ID": TEAM_ID,
    "APPLE_CLIENT_ID": 모바일 로그인시 Bundle ID or 웹 로그인시 Service ID,
    "APPLE_REDIRECT_URI": "https://domain/REDIRECT_URI",
    "APPLE_KEY_ID": KEY_ID,
    "APPLE_KEY_PATH": "./AuthKey_KEY_ID.p8",
    "APPLE_PRIVATE_KEY": "",
}

apple_key_path = os.path.join(CONFIG_BASE_DIR, f"./{APPLE_CONFIG['APPLE_KEY_PATH']}")
with open(apple_key_path, 'r') as apple_key_file:
    APPLE_CONFIG['APPLE_PRIVATE_KEY'] = apple_key_file.read()

apple_base_url = "https://appleid.apple.com"
apple_auth_url = f"{apple_base_url}/auth/authorize"
apple_token_url = f"{apple_base_url}/auth/token"
```

APPLE_REDIRECT_URI: https 프로토콜을 사용하는 도메인 주소만 사용가능하다.(localhost 또는 IP주소는 사용할 수 없다.)


### 2.1. APP ID구성 및 편집

[APP ID구성 및 편집](https://help.apple.com/developer-account/?lang=ko#/devde676e696)

#### 2.1.1. Certificates, Identifiers & Profiles

![](../images/django-oauth-apple/oauth_apple-1.png?raw=true)

- Description: 사용자가 로그인시 보여줄 사이트 이름등을 작성한다.
- App Id Prefix: 애플 로그인시 필요한 Team ID
- Bundle ID: 모바일 애플 로그인시 필요한 값이다.

#### 2.1.2. Server To Server Notification Endpoint

![](../images/django-oauth-apple/oauth_apple-2.png?raw=true)

- Notification Endpoint: 사용자가 애플의 정보를 업데이트 할때 서버에서 수신받을 path 설정이다.

#### 2.1.3. Edit your Services ID Configuration

![](../images/django-oauth-apple/oauth_apple-3.png?raw=true)

- Description: 사용자가 로그인시 보여줄 사이트 이름등을 작성한다.
- Identifier: 웹 로그인 애플 로그인시 필요한 값이다.

#### 2.1.4. View Key Details

![](../images/django-oauth-apple/oauth_apple-5.png?raw=true)

- Key ID: 애플로그인시 사용할 개인키 값이다. 
    - 우측 상단에서 `AuthKey_개인키.p8` key 파일을 다운받을 수 있으며 해당 키값을 별도의 설정파일에 넣거나 혹은 불러오는 식으로 사용하면 된다.
    - 현재 위의 예시 코드는 개인키 파일을 불러오는 방법을 사용하였다. 


---

## 3. Apple 로그인 페이지

사용자가 로그인 테스트 서버로 접속시 redirect URI를 반환한다.

```python
class AppleLoginView(APIView):
    permission_classes = (AllowAny,)
    authentication_classes = ()

    def get(self, reqeust):
        '''
        apple code 요청
        '''
        client_id = APPLE_CONFIG['APPLE_CLIENT_ID']
        redirect_uri = APPLE_CONFIG['APPLE_REDIRECT_URI']

        uri = f"{apple_auth_url}?client_id={client_id}&&redirect_uri={redirect_uri}&response_type=code"

        res = redirect(uri)
        return res
```

uri 파라미터 설명

- client_id : 모바일 로그인시 Bundle ID, 웹 로그인시 Service ID를 사용한다.
- redirect_uri : APPLE 로그인 정보를 전송받을 URI
- response_type : code, id_token 옵션이 있다.
    - "code" or "code id_token" 2가지 유형만 사용가능하다.
    - 입력값의 구분은 " " 공백으로 한다.
- scope : email, name 옵션이 있다.
    - `scope` 요청하였을 경우 response_mode는 `form_post`를 사용해야한다.
- response_mode : 응답 형식 지정
    - form_post
        - Method : `POST`
        - Content-Type : `application/x-www-form-urlencoded`
        - RestAPI 서버로 요청 처리할 경우 `415 Unsupported Media Type` 에러가 발생한다.
    - 지정하지 않을 경우
        - Method : `GET`
        - Content-Type : `application/json`
        - RestAPI 서버로 요청 처리할 경우 response_mode 지정하지 않고 사용해야 한다.


Content-Type 설명

- html form 태그를 사용하여 post 방식으로 요청하거나, jQuery의 ajax 등의 요청을 할때 default Content-Type은 `application/x-www-form-urlencoded` 이다.
- RestAPI의 경우 보통 JSON(`application/json`)타입으로 요청 처리한다.
- 415 Unsupported Media Type 에러가 발생한 경우 위와 같이 Content-type이 일치하지 않을때 발생한다.


---

## 4. Apple Callback 함수

사용자가 oauth 로그인시 code 검증 및 로그인 처리한다.

```python
class AppleCallbackView(APIView):
    def get_key_and_secret(self):
        '''
        CLIENT_SECRET 생성       
        '''
        headers = {
            'alg': 'ES256',
            'kid': APPLE_CONFIG['APPLE_KEY_ID'],
        }

        payload = {
            'iss': APPLE_CONFIG['APPLE_TEAM_ID'],
            'iat': time.time(),
            'exp': time.time() + 600,  # 인증유효기간 10분
            'aud': apple_base_url,
            'sub': APPLE_CONFIG['APPLE_CLIENT_ID'],
        }

        client_secret = jwt.encode(
            payload=payload, 
            key=APPLE_CONFIG['APPLE_PRIVATE_KEY'], 
            algorithm='ES256', 
            headers=headers
        )

        return client_secret

    def get(self, request):
        '''
        apple id_token 요청 및 user_info 조회
        '''
        data = request.query_params.copy()
        code = data.get('code')
        # id_token 서명을 에러로 인해 검증할 수 없어 자체 발급한 id_token 사용

        # CLIENT_SECRET 생성
        client_secret = self.get_key_and_secret()

        headers = {'Content-type': "application/x-www-form-urlencoded"}
        request_data = {
            'client_id': APPLE_CONFIG['APPLE_CLIENT_ID'],
            'client_secret': client_secret,
            'code': code,
            'grant_type': 'authorization_code',
            'redirect_uri': APPLE_CONFIG['APPLE_REDIRECT_URI'],
        }

        # client_secret 유효성 검사
        res = requests.post(apple_token_url, data=request_data, headers=headers)
        response_json = res.json()
        id_token = response_json.get('id_token')
        if not id_token:
            return Response(status=status.HTTP_400_BAD_REQUEST)
        
        # 백엔드 자체적으로 id_token 발급받은 경우 서명을 검증할 필요 없음
        token_decode = jwt.decode(id_token, '', options={"verify_signature": False})
        # sub : (subject) is the unique user id
        # email : is the email address of the user

        if (not token_decode.get('sub')) or (not token_decode.get('email')) or (not token_decode.get('email_verified')):
            return Response(status=status.HTTP_400_BAD_REQUEST)

        # Apple에서 받은 id_token에서 sub, email 조회
        social_type = 'apple'
        social_id = f"{social_type}_{token_decode['sub']}"
        user_email = token_decode['email']

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
        return response
```


- id_token 검증하려고 할 경우 '_EllipticCurvePrivateKey' object has no attribute 'verify' 에러가 발생한다.
    ```python
    token = jwt.decode(id_token, key=APPLE_CONFIG['APPLE_PRIVATE_KEY'], algorithms='RS256')
    ```

id_token 복호화시 아래의 값들이 있다.
- iss
- aud
- exp
- iat
- sub : 사용자 고유식별자
- at_hash
- email : 사용자 email or 임의의코드@privaterelay.appleid.com
- email_verified : 이메일 인증여부
- auth_time

<!-- 

```python
class AppleEndpoint(APIView):
    permission_classes = (AllowAny,)

    def post(self, request):
        '''
        apple 유저의 이메일 변경, 서비스 해지, 계정 탈퇴에 대한 정보를 받는용
        '''
        data = request.data.copy()

        print(f"AppleEndpoint = {data}")

        response = Response(status=status.HTTP_200_OK)
        return response
```
-->


---

## 5. 로그인

웹서버에서 설정한 apple oauth 로그인 페이지로 접근하면 사전에 설정한 애플 리다이렉트 URI로 접근하는데 설정에 문제가 없다면 아래와 같은 로그인 페이지로 접속된다.

로그인 페이지에서는 어떤 사이트로 로그인할지 같이 안내한다.
- 애플 개발자 사이트에서 설정한 `Description`값이 노출된다.

![](../images/django-oauth-apple/login-1.png?raw=true)

최초 로그인시 아래와 같이 안내된다.

![](../images/django-oauth-apple/login-2.png?raw=true)

이후는 Apple Callback 함수 처리로 넘어가며 알고리즘에 문제가 없다면 json 타입의 `social_type`, `social_id`, `user_email` 값이 반환된다.


---

## 참고(Reference)

- [What the Heck is Sign In with Apple?](https://developer.okta.com/blog/2019/06/04/what-the-heck-is-sign-in-with-apple)
- [configuring_your_webpage_for_sign_in_with_apple](https://developer.apple.com/documentation/sign_in_with_apple/sign_in_with_apple_js/configuring_your_webpage_for_sign_in_with_apple)
    - Handle the Authorization Response
- [generate_and_validate_tokens](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)
- [incorporating_sign_in_with_apple_into_other_platforms](https://developer.apple.com/documentation/sign_in_with_apple/sign_in_with_apple_js/incorporating_sign_in_with_apple_into_other_platforms)
    - Send the Required Query Parameters