---
title: "Django 파일 다운로드"
date: 2022-02-05T13:06:18+09:00
description: Django 파일 다운로드
tags: ["django"]
categories: ["Django"]
---


---

django에서 구현가능한 파일 다운로드 방법 예시

## 로컬파일 다운로드 - Blob

장점
- 브라우저에서 파일다운로드 완료시점을 알 수 있음
- 임시로 생성된 파일을 다운받을 경우 완료시점에 따른 파일 삭제가 가능함

단점
- 파일 용량만큼 브라우저 메모리 사용
- 대용량 파일다운로드에 부적합

```python
from django.http import FileResponse
from rest_framework.views import APIView
import os

class DownloadView(APIView):
    def get(self, request):
        ...

        file_handle = open(filepath, 'rb')
        response = FileResponse(file_handle, content_type='application/zip')
        response['Content-Length'] = os.path.getsize(filepath)
        response['Content-Disposition'] = 'attachment; filename="%s"' % os.path.basename(filepath)
        return response
```


---

## 로컬파일 다운로드 - URL

장점
- 브라우저 메모리 사용없이 다운로드 가능
- 대용량 파일 다운로드에 적합

단점
- 브라우저에서 파일다운로드 완료시점을 알 수 없음
- 임시로 생성된 파일을 다운받을 경우 별도의 삭제 알고리즘 적용이 필요함

```python
from rest_framework.views import APIView

class DownloadView(APIView):
    def get(self, request):
        ...

        file_url = self._get_object(object)

        response = Response(status=status.HTTP_200_OK)
        response.data = {'download_url': file_url}
        return response
```

삭제 알고리즘  
> DB 테이블에 파일 수정상태 여부 저장하여  
> 파일 정보가 수정되지 않을 경우 전에 생성된 파일의 URL 반환  
> 파일 정보가 수정되었을 경우 전에 생성된 파일 삭제하고 새로 생성한 파일의 URL 반환  