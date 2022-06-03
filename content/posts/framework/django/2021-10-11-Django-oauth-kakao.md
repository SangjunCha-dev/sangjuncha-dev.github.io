---
title: "Django 소셜로그인(oauth) kakao 연동"
date: 2021-10-11T21:44:42+09:00
description: Django oauth kakao login
tags: ["django", "oauth", "kakao"]
categories: ["Django", "oauth", "kakao"]
---



django restframework 기반의 카카오(kakao) 인증 로그인 백엔드서버로 별도의 `auth`관련 라이브러리는 설치하지 않고 구현한다.

[카카오 개발자 사이트](https://developers.kakao.com/)에서 로그인에 사용하는 환경변수를 설정하고 진행한다.


---

## 1. 로그인 환경변수 설정

### 1.1. 애플리케이션 생성

1. 카카오 개발자 사이트에 가입후 [내 애플리케이션] → `[애플리케이션 추가하기]` 클릭한다.

    ![](../images/django-oauth-kakao/oauth_kakao-1.png?raw=true)

2. 앱 아이콘(선택), 앱 이름, 사업자명을 등록한다.

    ![](../images/django-oauth-kakao/oauth_kakao-2.png?raw=true)

3. 정상적으로 등록되어 애플리케이션 목록에서 확인할 수 있다.

    ![](../images/django-oauth-kakao/oauth_kakao-3.png?raw=true)


### 1.2. 웹 로그인 설정

1. 생성한 앱을 클릭하면 키 정보를 확인하고 `[플랫폼 설정하기]` 클릭한다.

    ![](../images/django-oauth-kakao/oauth_kakao-4.png?raw=true)

- 예시코드 kakao login 기능에서는 `REST API 키`를 사용한다.
    - 네이티브 앱 키: Android, iOS SDK에서 API를 호출할 때 사용한다.
    - JavaScript 키: JavaScript SDK에서 API를 호출할 때 사용한다.
    - REST API 키: REST API를 호출할 때 사용한다.
    - Admin 키: 모든 권한을 갖고 있는 키다. 노출이 되지 않도록 주의가 필요하다.

2. 웹 로그인기능을 테스트하기 때문에 `[Web 플랫폼 등록]` 메뉴로 간다.

    ![](../images/django-oauth-kakao/oauth_kakao-5.png?raw=true)

3. 로그인 테스트에 사용할 사이트 도메인 주소를 등록한다.
    - http://localhost 주소 사용이 가능하다.

    ![](../images/django-oauth-kakao/oauth_kakao-6.png?raw=true)

4. 저장하면 위와 같이 사이트 도메인 주소가 등록된다. `[등록하러 가기]` 메뉴로 간다.

    ![](../images/django-oauth-kakao/oauth_kakao-7.png?raw=true)

5. 내 애플리케이션 > 제품 설정 > 카카오 로그인 > 동의항목 에서 카카오 계정(이메일)를 `선택 동의 [수집]` 설정한다.

    ![](../images/django-oauth-kakao/oauth_kakao-12.png?raw=true)

6. 내 애플리케이션 > 제품 설정 > 카카오 로그인 > 보안 에서 `Client Secret` 코드를 생성한다

    ![](../images/django-oauth-kakao/oauth_kakao-13.png?raw=true)

    ![](../images/django-oauth-kakao/oauth_kakao-14.png?raw=true)

7. 카카오 로그인 API를 사용하기 위해서는 `활성화 설정 ON `상태로 수정한다. 그 다음 로그인 `[Redirect URI 등록]` 메뉴로 이동한다.

    ![](../images/django-oauth-kakao/oauth_kakao-8.png?raw=true)

8. 로그인 테스트 서버에서 받을 Redirect URI를 등록한다.

    ![](../images/django-oauth-kakao/oauth_kakao-9.png?raw=true)

9. 위의 그림처럼 Redirect URI가 등록되면 kakao 개발자 사이트에서 로그인하기 위한 모든 준비가 끝났다.

    ![](../images/django-oauth-kakao/oauth_kakao-10.png?raw=true)


---

## 2. Kakao 로그인 변수 설정

```python
import os

KAKAO_CONFIG = {
    "KAKAO_REST_API_KEY": REST API 키,
    "KAKAO_REDIRECT_URI": "http://localhost:8000/oauth/kakao/login/callback/",
    "KAKAO_CLIENT_SECRET_KEY": Client Secret 키, 
}

kakao_login_uri = "https://kauth.kakao.com/oauth/authorize"
kakao_token_uri = "https://kauth.kakao.com/oauth/token"
kakao_profile_uri = "https://kapi.kakao.com/v2/user/me"
```

- REST API 키: REST API를 호출할 때 사용한다.
- Admin 키: 모든 권한을 갖고 있는 키다. 노출이 되지 않도록 주의가 필요하다.
- `kakao_login_uri`: 로그인 페이지 주소
- `kakao_token_uri`: 엑세스 토큰 발급받기 위한 주소
- `kakao_profile_uri`: 프로필 정보 조회를 위한 주소

---

## 3. Kakao 로그인 페이지

사용자가 로그인 테스트 서버로 접속시 redirect URI를 반환한다.

```python
class KakaoLoginView(APIView):
    permission_classes = (AllowAny,)

    def get(self, request):
        '''
        kakao code 요청
        '''
        client_id = KAKAO_CONFIG['KAKAO_REST_API_KEY']
        redirect_uri = KAKAO_CONFIG['KAKAO_REDIRECT_URI']

        uri = f"{kakao_login_uri}?client_id={client_id}&redirect_uri={redirect_uri}&response_type=code"
        
        res = redirect(uri)
        return res
```

uri 파라미터 설명
- client_id: 앱 REST API 키
- redirect_uri: 인가 코드가 리다이렉트될 URI
- response_type: code로 고정


---

## 4. Kakao Callback 함수

사용자가 oauth 로그인시 code 검증 및 로그인 처리한다.

```python
class KakaoCallbackView(APIView):
    permission_classes = (AllowAny,)

    def get(self, request):
        '''
        kakao access_token 요청 및 user_info 요청
        '''
        data = request.query_params.copy()

        # access_token 발급 요청
        code = data.get('code')
        if not code:
            return Response(status=status.HTTP_400_BAD_REQUEST)

        request_data = {
            'grant_type': 'authorization_code',
            'client_id': KAKAO_CONFIG['KAKAO_REST_API_KEY'],
            'redirect_uri': KAKAO_CONFIG['KAKAO_REDIRECT_URI'],
            'client_secret': KAKAO_CONFIG['KAKAO_CLIENT_SECRET_KEY'],
            'code': code,
        }
        token_headers = {
            'Content-type': 'application/x-www-form-urlencoded;charset=utf-8'
        }
        token_res = requests.post(kakao_token_uri, data=request_data, headers=token_headers)

        token_json = token_res.json()
        access_token = token_json.get('access_token')

        if not access_token:
            return Response(status=status.HTTP_400_BAD_REQUEST)
        access_token = f"Bearer {access_token}"  # 'Bearer ' 마지막 띄어쓰기 필수

        # kakao 회원정보 요청
        auth_headers = {
            "Authorization": access_token,
            "Content-type": "application/x-www-form-urlencoded;charset=utf-8",
        }
        user_info_res = requests.get(kakao_profile_uri, headers=auth_headers)
        user_info_json = user_info_res.json()

        social_type = 'kakao'
        social_id = f"{social_type}_{user_info_json.get('id')}"

        kakao_account = user_info_json.get('kakao_account')
        if not kakao_account:
            return Response(status=status.HTTP_400_BAD_REQUEST)
        user_email = kakao_account.get('email')

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

1. 웹서버에서 설정한 kakao oauth 로그인 페이지로 접근하면 사전에 설정한 리다이렉트 URI로 접근하는데 설정에 문제가 없다면 kakao 로그인 페이지로 접속된다.

    ![](../images/django-oauth-kakao/oauth_kakao-11.png?raw=true)


---

## 참고(Reference)

[카카오 로그인 REST API](https://developers.kakao.com/docs/latest/ko/kakaologin/rest-api)
