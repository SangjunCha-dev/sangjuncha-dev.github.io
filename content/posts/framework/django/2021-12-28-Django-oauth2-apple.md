---
title: "Django oauth apple login"
date: 2021-12-28T19:37:11+09:00
description: Django oauth apple login
tags: ["django", "oauth", "apple"]
categories: ["Django", "oauth", "apple"]
---



django restframework 기반의 애플 인증 로그인 백엔드서버 구현 설명이다.  

## 라이브러리 설치

```bash
$ pip install django

# restframework
$ pip install djangorestframework
$ pip install djangorestframework-simplejwt

# pyjwt[crypto]
$ pip install pyjwt[crypto]
```



## Apple 로그인 변수 설정

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

APPLE_CLIENT_ID는 모바일 로그인시 Bundle ID, 웹 로그인시 Service ID를 사용한다.



## Apple 로그인 페이지

```python
class AppleLoginView(APIView):
    permission_classes = (AllowAny,)
    authentication_classes = ()

    def get(self, reqeust):
        '''
        apple code 요청
        '''
        client_id = APPLE_CONFIG['APPLE_CLIENT_ID
        redirect_uri = APPLE_CONFIG['APPLE_REDIRECT_URI

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



## Apple Callback 함수

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



## 참고 사이트

- [What the Heck is Sign In with Apple?](https://developer.okta.com/blog/2019/06/04/what-the-heck-is-sign-in-with-apple)
- [configuring_your_webpage_for_sign_in_with_apple](https://developer.apple.com/documentation/sign_in_with_apple/sign_in_with_apple_js/configuring_your_webpage_for_sign_in_with_apple)
    - Handle the Authorization Response
- [generate_and_validate_tokens](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)
- [incorporating_sign_in_with_apple_into_other_platforms](https://developer.apple.com/documentation/sign_in_with_apple/sign_in_with_apple_js/incorporating_sign_in_with_apple_into_other_platforms)
    - Send the Required Query Parameters