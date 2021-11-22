---
title: "Django 소셜로그인(oauth) google 연동"
date: 2021-11-22T23:59:02+09:00
description: Django oauth google login
tags: ["django", "oauth", "google"]
categories: ["Django", "oauth", "google"]
---



django restframework 기반의 구글(google) 인증 로그인 백엔드서버로 별도의 `auth`관련 라이브러리는 설치하지 않고 구현한다.

[Google Cloud Platform](https://console.cloud.google.com/projectselector2/apis/dashboard)에서 로그인에 사용하는 환경변수를 설정하고 진행한다.


---

## 1. 로그인 환경변수 설정

### 1.1. 프로젝트 생성

1. [Google Cloud Platform](https://console.cloud.google.com/projectselector2/apis/dashboard?supportedpurview=project)에서 `[프로젝트 만들기]` 메뉴로 간다.
    ![](../images/django-oauth-google/oauth_google-1.png?raw=true)

2. 프로젝트 이름을 지정하고 `[만들기]` 클릭한다.

    ![](../images/django-oauth-google/oauth_google-2.png?raw=true)


### 1.2. 사용자 인증 정보 설정

1. [사용자 인증 정보] → [사용자 인증 정보 만들기] → `[OAuth 클라이언트 ID]` 선택

    ![](../images/django-oauth-google/oauth_google-3.png?raw=true)

2. `애플리케이션 유형`: 웹 애플리케이션 선택 후 앱 이름 지정
3. `승인된 자바스크립트 원본`: 로그인 테스트 서버 주소입력
4. `승인된 리디렉션 URI`: 로그인시 콜백주소 입력

    ![](../images/django-oauth-google/oauth_google-4.png?raw=true)

5. 생성된 `클라이언트ID`, `보안 비밀번호`를 확인한다.

    ![](../images/django-oauth-google/oauth_google-5.png?raw=true)


### 1.3. oauth 동의화면 구성

1. [OAuth 동의 화면] → [외부] → [만들기] 클릭

    ![](../images/django-oauth-google/oauth_google-6.png?raw=true)

2. 앱 이름: 사용자에게 노출된 앱 이름 지정
3. 사용자 지원 이메일, 애플리케이션 홈페이지, 개발자 연락처 정보 입력 후 [저장 후 계속] 클릭한다.

    ![](../images/django-oauth-google/oauth_google-7.png?raw=true)

4. 사용자 최초 접속시 정보제공 동의항목 지정을 위해 `[범위 추가 또는 삭제]` 클릭한다.

    ![](../images/django-oauth-google/oauth_google-8.png?raw=true)

5. 이메일 주소를 받기위해 `userinfo.email` 항목을 체크하고 업데이트 클릭한다.

    ![](../images/django-oauth-google/oauth_google-9.png?raw=true)

6. 설정된 정보 제공항목을 확인하고 [저장 후 계속] 클릭한다.

    ![](../images/django-oauth-google/oauth_google-10.png?raw=true)

7. `테스트 사용자를 추가`하고 [저장 후 계속] 클릭한다.    

    ![](../images/django-oauth-google/oauth_google-11.png?raw=true)


---

## 2. google 로그인 변수 설정

```python
import os

GOOGLE_CONFIG = {
    "GOOGLE_CLIENT_ID" : 클라이언트ID,
    "GOOGLE_REDIRECT_URI": "http://localhost:8000/oauth/google/login/callback/",
    "GOOGLE_SECRET" : 클라이언트 보안 비밀번호,
}


google_login_url = "https://accounts.google.com/o/oauth2/v2/auth"
google_scope = "https://www.googleapis.com/auth/userinfo.email"
google_token_url = "https://oauth2.googleapis.com/token"
google_profile_url = "https://www.googleapis.com/oauth2/v2/tokeninfo"
```

- `google_login_url`: 로그인 페이지 주소
- `google_scope`: 사용자에게 제공받을 정보항목
- `google_token_url`: 엑세스 토큰 발급받기 위한 주소
- `google_profile_url`: 프로필 정보 조회를 위한 주소


## 3. google 로그인 페이지

사용자가 로그인 테스트 서버로 접속시 redirect URI를 반환한다.


```python
class GoogleLoginView(APIView):
    permission_classes = (AllowAny,)

    def get(self, request):
        '''
        google code 요청
        '''
        client_id = GOOGLE_CONFIG['GOOGLE_CLIENT_ID']
        redirect_uri = GOOGLE_CONFIG['GOOGLE_REDIRECT_URI']
        uri = f"{google_login_url}?client_id={client_id}&redirect_uri={redirect_uri}&scope={google_scope}&response_type=code"

        res = redirect(uri)
        return res
```


## 4. google Callback 함수

사용자가 oauth 로그인시 code 검증 및 로그인 처리한다.

```python
class GoogleCallbackView(APIView):
    permission_classes = (AllowAny,)

    @swagger_auto_schema(query_serializer=CallbackUserInfoSerializer)
    def get(self, request):
        '''
        google access_token 요청 및 user_info 요청
        '''
        data = request.query_params.copy()

        # access_token 발급 요청
        code = data.get('code')
        if not code:
            return Response(status=status.HTTP_400_BAD_REQUEST)

        request_data = {
            'client_id': GOOGLE_CONFIG['GOOGLE_CLIENT_ID'],
            'client_secret': GOOGLE_CONFIG['GOOGLE_SECRET'],
            'code': code,
            'grant_type': 'authorization_code',
            'redirect_uri': GOOGLE_CONFIG['GOOGLE_REDIRECT_URI'],
        }
        token_res = requests.post(google_token_url, data=request_data)

        token_json = token_res.json()
        access_token = token_json['access_token']

        if not access_token:
            return Response(status=status.HTTP_400_BAD_REQUEST)

        # google 회원정보 요청
        query_string = {
            'access_token': access_token
        }
        user_info_res = requests.get(google_profile_url, params=query_string)
        user_info_json = user_info_res.json()
        if (user_info_res.status_code != 200) or (not user_info_json):
            return Response(status=status.HTTP_400_BAD_REQUEST)

        social_type = 'google'
        social_id = f"{social_type}_{user_info_json.get('user_id')}"
        user_email = user_info_json.get('email')

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

1. 웹서버에서 설정한 google oauth 로그인 페이지로 접근하면 사전에 설정한 리다이렉트 URI로 접근하는데 설정에 문제가 없다면 google 로그인 페이지로 접속된다.


---

## 참고(Reference)

- [OAuth 2.0을 사용하여 Google API에 액세스](https://developers.google.com/identity/protocols/oauth2)
- [웹 서버 애플리케이션에 OAuth 2.0 사용](https://developers.google.com/identity/protocols/oauth2/web-server#python)