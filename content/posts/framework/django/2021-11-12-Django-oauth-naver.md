---
title: "Django 소셜로그인(oauth) naver 연동"
date: 2021-11-12T11:49:25+09:00
description: Django oauth naver login
tags: ["django", "oauth", "naver"]
categories: ["Django", "oauth", "naver"]
---



django restframework 기반의 네이버(naver) 인증 로그인 백엔드서버로 별도의 `auth`관련 라이브러리는 설치하지 않고 구현한다.

[네이버 개발자 사이트](https://developers.naver.com/)에서 로그인에 사용하는 환경변수를 설정하고 진행한다.


---

## 1. 로그인 환경변수 설정

### 1.1. 애플리케이션 생성 및 웹 로그인 설정

네이버 개발자 사이트 가입하고 아래의 과정을 진행한다.

1. [Application] → [애플리케이션 등록] → 애플리케이션 이름 지정 → [사용 API] `네이버 로그인 선택`한다.

    ![](../images/django-oauth-naver/oauth_naver-1.png?raw=true)

2. 사용자 제공 정보 선택에서 `[이메일 주소]를 필수`로 선택한다.(회원가입 확인 용도로 사용예정)

    ![](../images/django-oauth-naver/oauth_naver-2.png?raw=true)

3. 서비스 환경의 `PC 웹`을 선택한다.

    ![](../images/django-oauth-naver/oauth_naver-3.png?raw=true)

4. `서비스 URL` 과 `로그인 Callback URL`을 작성하고 [등록하기]를 클릭한다.

    ![](../images/django-oauth-naver/oauth_naver-4.png?raw=true)

5. 생성된 앱을 확인할 수 있고, 해당 앱을 클릭하여 정보를 확인한다.

    ![](../images/django-oauth-naver/oauth_naver-5.png?raw=true)

6. 네이버 로그인연동에 사용할 `Client ID`, `Client Secret`를 확인한다.

    ![](../images/django-oauth-naver/oauth_naver-6.png?raw=true)

7. 테스트 계정을 추가하고 싶다면 해당 앱 메뉴의 멤버관리에서 추가할 수 있다.

---

## 2. naver 로그인 변수 설정

```python
import os

NAVER_CONFIG = {
    "NAVER_CLIENT_ID": Client ID,
    "NAVER_REDIRECT_URI": "http://localhost:8000/oauth/naver/login/callback/",
    "NAVER_CLIENT_SECRET": Client Secret, 
    "STATE" : 임의의 랜덤코드,
}

naver_login_url = "https://nid.naver.com/oauth2.0/authorize"
naver_token_url = "https://nid.naver.com/oauth2.0/token"
naver_profile_url = "https://openapi.naver.com/v1/nid/me"

```

- `naver_login_url`: 로그인 페이지 주소
- `naver_token_url`: 엑세스 토큰 발급받기 위한 주소
- `naver_profile_url`: 프로필 정보 조회를 위한 주소

---

## 3. naver 로그인 페이지

사용자가 로그인 테스트 서버로 접속시 redirect URI를 반환한다.


```python
class NaverLoginView(APIView):
    permission_classes = (AllowAny,)

    def get(self, request):
        '''
        naver code 요청
        '''
        client_id = NAVER_CONFIG['NAVER_CLIENT_ID']
        redirect_uri = NAVER_CONFIG['NAVER_REDIRECT_URI']
        state = NAVER_CONFIG['STATE']

        uri = f"{naver_login_url}?client_id={client_id}&redirect_uri={redirect_uri}&state={state}&response_type=code"
        res = redirect(uri)
        return res
```

uri 파라미터 설명

- response_type: 인증 과정에 대한 내부 구분값으로 'code'로 전송해야 함
- client_id: 애플리케이션 등록 시 발급받은 Client ID 값
- redirect_uri: 애플리케이션을 등록 시 입력한 Callback URL 값으로 URL 인코딩을 적용한 값
- state: 사이트 간 요청 위조(cross-site request forgery) 공격을 방지하기 위해 애플리케이션에서 생성한 상태 토큰값으로 URL 인코딩을 적용한 값을 사용

---


## 4. naver Callback 함수

사용자가 oauth 로그인시 code 검증 및 로그인 처리한다.

```python
class NaverCallbackView(APIView):
    permission_classes = (AllowAny,)

    @swagger_auto_schema(query_serializer=CallbackUserCSRFInfoSerializer)
    def get(self, request):
        '''
        naver access_token 요청 및 user_info 요청
        '''
        data = request.query_params.copy()

        # access_token 발급 요청
        code = data.get('code')
        user_state = data.get('state')
        if (not code) or (user_state != NAVER_CONFIG['STATE)']:
            return Response(status=status.HTTP_400_BAD_REQUEST)

        request_data = {
            'grant_type': 'authorization_code',
            'client_id': NAVER_CONFIG['NAVER_CLIENT_ID'],
            'client_secret': NAVER_CONFIG['NAVER_CLIENT_SECRET'],
            'code': code,
            'state': user_state,
        }
        token_headers = {
            'Content-type': 'application/x-www-form-urlencoded;charset=utf-8'
        }
        token_res = requests.post(naver_token_url, data=request_data, headers=token_headers)

        token_json = token_res.json()
        access_token = token_json.get('access_token')

        if not access_token:
            return Response(status=status.HTTP_400_BAD_REQUEST)
        access_token = f"Bearer {access_token}"  # 'Bearer ' 마지막 띄어쓰기 필수

        # naver 회원정보 요청
        auth_headers = {
            "X-Naver-Client-Id": NAVER_CONFIG['NAVER_CLIENT_ID'],
            "X-Naver-Client-Secret": NAVER_CONFIG['NAVER_CLIENT_SECRET'],
            "Authorization": access_token,
            "Content-type": "application/x-www-form-urlencoded;charset=utf-8",
        }
        user_info_res = requests.get(naver_profile_url, headers=auth_headers)
        user_info_json = user_info_res.json()
        user_info = user_info_json.get('response')
        if not user_info:
            return Response(status=status.HTTP_400_BAD_REQUEST)

        social_type = 'naver'
        social_id = f"{social_type}_{user_info.get('id')}"
        user_email = user_info.get('email')

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


---

## 5. 로그인

1. 웹서버에서 설정한 naver oauth 로그인 페이지로 접근하면 사전에 설정한 리다이렉트 URI로 접근하는데 설정에 문제가 없다면 naver 로그인 페이지로 접속된다.

    ![](../images/django-oauth-naver/login-1.png?raw=true)

2. 해당 로그인 테스트 서버에서 개인정보 제공에 대한 동의화면이 나오며 동의시 Callback 함수로 넘어가 회원가입 및 로그인 과정을 거치게 된다.

    ![](../images/django-oauth-naver/login-2.png?raw=true)


---

## 6. 예시 코드 Git 

- [django_oauth](https://github.com/SangjunCha-dev/django_oauth)


---

## 참고(Reference)

- [네이버 로그인 API 명세](https://developers.naver.com/docs/login/api/api.md)
- [네이버 회원 프로필 조회 API 명세](https://developers.naver.com/docs/login/profile/profile.md)