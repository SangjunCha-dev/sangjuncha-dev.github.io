---
title: Django 파일 업로드
author: Sangjun Cha
date: 2021-04-22 13:18:18 +0900
categories: [Django]
tags: [django]
pin: false
---

django 파일 업로드

# 청크단위 파일 업로드

- 대용량 업로드 가능
- 실시간 업로드 프로그래스바에 응용가능

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