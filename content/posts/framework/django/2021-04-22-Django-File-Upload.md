---
title: "Django 파일 업로드"
date: 2021-04-22T13:18:18+09:00
description: Django 파일 업로드
# menu:
#   sidebar:
#     name: 파일 업로드
#     identifier: django-file-upload
#     parent: django
#     weight: 30
tags: ["django"]
categories: ["Django"]
---


---

## 청크단위 파일 업로드

- 용량제한 없이 대용량파일 업로드 가능
- 실시간 업로드 상황을 프로그래스바에 응용가능

```python
from django.conf import settings

def upload(request):
	if request.method == 'POST':
		path = settings.MEDIA_ROOT
		files = request.FILES['file']

		for file in files:
            file_path = f'{path}/{file.name}'
			with open(file_path,'wb+') as dst_file:
				for chunk in file.chunks():
					dst_file.write(chunk)
```