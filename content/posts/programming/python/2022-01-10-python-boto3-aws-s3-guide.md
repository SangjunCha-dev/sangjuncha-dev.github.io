---
title: "python boto3라이브러리 이용한 AWS s3 사용법"
date: 2022-01-10T09:36:00+09:00
description: python boto3라이브러리 이용한 AWS s3 사용법
tags: ["python", "boto3", "aws"]
categories: ["Python", "boto3", "AWS"]
---


---

## 1. 개요 및 설정

Boto3는 python 애플리케이션과 AWS 서비스를 연결시키기 위해 사용하는 python 라이브러리이다. 

boto3 라이브러리 설치

```bash
pip install boto3
```

라이브러리 import 및 AWS key 설정

`conf.py`

```python
import os

AWS_ACCESS_KEY_ID = "ACCESS_KEY_ID"
AWS_SECRET_ACCESS_KEY = "SECRET_ACCESS_KEY"
AWS_REGION = "REGION"
AWS_BUCKET_NAME = "BUCKET_NAME"
```


---

## 2. boto3 기본 사용법

### 2.1. client

[client](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.list_buckets)

- Low-level 인터페이스
- service description에 의해 만들어짐
- botocore 수준제어 (botocore는 AWS CLI와 boto3의 기초가 되는 라이브러리)
- AWS API와 1:1 매핑
- 메소드가 스네이크 케이스로 정의되어 있음

```python
import boto3
from conf import *

client = boto3.client(
    's3',
    aws_access_key_id=AWS_ACCESS_KEY_ID,
    aws_secret_access_key=AWS_SECRET_ACCESS_KEY,
    region_name=AWS_REGION
)

# bucket 목록
response = client.list_buckets()
print(f"list_buckets = {response}")

# bucket 모든 파일 정보 조회
response = client.list_objects(Bucket=AWS_STORAGE_BUCKET_NAME)
for content in response['Contents']:
    obj_dict = client.get_object(Bucket=AWS_STORAGE_BUCKET_NAME, Key=content['Key'])
    print(content['Key'], obj_dict['LastModified'])

```

Response Syntax

```bash
{
    'ResponseMetadata'" {
        'RequestId': 'RequestId',
		'HostId': 'HostId', 
		'HTTPStatusCode': 200, 
		'HTTPHeaders': {
			'x-amz-id-2': 'HostId',
			'x-amz-request-id': 'RequestId',
			'date': 'Mon, 10 Jan 2022 00:45:42 GMT',
			'content-type': 'application/xml',
			'transfer-encoding': 'chunked', 
			'server': 'AmazonS3'
		},
		'RetryAttempts': 0
    }
    'Buckets': [
        {
            'Name': 'string',
            'CreationDate': datetime(2022, 1, 10)
        },
    ],
    'Owner': {
        'DisplayName': 'string',
        'ID': 'string'
    }
}
```

S3 IAM에 적절한 권한이 없을 경우 아래와 같은 에러가 발생한다.

```
operation: Access Denied
```


---

### 2.2. Session과 resource

[Session](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/core/session.html#module-boto3.session)

- AWS 설정상태를 저장
- client & resource 서비스를 생성하기 위한 권한부여

[resource](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/core/resources.html)

- high-level, 객체지향적 인터페이스
- resource description에 의해 만들어짐
- 식별자(identifier)와 속성(attribute)을 사용
- s3, object 같은 자원에 대한 조작 위주

Bucket
- 특정 S3 bucket 사용

```python
import boto3
from conf import *

session = boto3.Session(
    aws_access_key_id=AWS_ACCESS_KEY_ID,
    aws_secret_access_key=AWS_SECRET_ACCESS_KEY,
    region_name=AWS_REGION
)

# s3에 대한 권한 및 상태를 s3변수에 저장
s3 = session.resource('s3')

# bucket 목록조회
for bucket in s3.buckets.all():
    print(f"bucket.name = {bucket.name}")

print("------------------------------")

# bucket 모든 파일 이름 및 수정시간 조회
bucket = s3.Bucket(AWS_BUCKET_NAME)
for obj in bucket.objects.all():
    print(obj.key, obj.last_modified)
```

output

```python
bucket.name = BUCKET_NAME
------------------------------
media/ 2022-01-10 09:35:38+00:00
media/6514A2A9-8CC9-493C-4009-0F313BA8724A.jpeg 2022-01-10 09:50:48+00:00
```


---

### 2.3. Object

[Object](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#object)

- bucket에 저장된 객체(파일)에 접근

[ObjectSummary](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#id242)

[ObjectAcl](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#id241)

```python
import boto3
from conf import *

session = boto3.Session(
    aws_access_key_id=AWS_ACCESS_KEY_ID,
    aws_secret_access_key=AWS_SECRET_ACCESS_KEY,
    region_name=AWS_REGION
)

# s3에 대한 권한 및 상태를 s3변수에 저장
s3 = session.resource('s3')

object_key = "media/6514A2A9-8CC9-493C-4009-0F313BA8724A.jpeg"

object_ = s3.Object(AWS_BUCKET_NAME, object_key)
print(object_)

print("------------------------------")

object_ = s3.ObjectSummary(AWS_BUCKET_NAME, object_key)
print(object_)
print(f"object_.size = {object_.size}")
print(f"object_.last_modified = {object_.last_modified}")
print(f"object_.Acl() = {object_.Acl()}")

print("------------------------------")

object_acl = s3.ObjectAcl(AWS_BUCKET_NAME, object_key)
print(object_acl)
print(f"object_acl.owner = {object_acl.owner}")
print(f"object_acl.grants = {object_acl.grants}")

```

ouput

```python
s3.Object(bucket_name='BUCKET_NAME', key='media/6514A2A9-8CC9-493C-4009-0F313BA8724A.jpeg')
------------------------------
s3.ObjectSummary(bucket_name='BUCKET_NAME', key='media/6514A2A9-8CC9-493C-4009-0F313BA8724A.jpeg')
object_.size = 64738
object_.last_modified = 2022-01-10 09:50:48+00:00
object_.Acl() = s3.ObjectAcl(bucket_name='BUCKET_NAME', object_key='media/6514A2A9-8CC9-493C-4009-0F313BA8724A.jpeg')
------------------------------
s3.ObjectAcl(bucket_name='BUCKET_NAME', object_key='media/6514A2A9-8CC9-493C-4009-0F313BA8724A.jpeg')
object_acl.owner = {'ID': 'owner_id'}
object_acl.grants = [{'Grantee': {'ID': 'owner_id', 'Type': 'CanonicalUser'}, 'Permission': 'FULL_CONTROL'}]
```


---

### 2.4. delete

버킷에 저장된 객체 삭제

[Object.delete()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html?highlight=delete#S3.Object.delete)

example 1

```python
import boto3
from conf import *

key_name = "copy_cat.jpg"

session = boto3.Session(
    aws_access_key_id=AWS_ACCESS_KEY_ID,
    aws_secret_access_key=AWS_SECRET_ACCESS_KEY,
    region_name=AWS_REGION
)

s3 = boto3.resource('s3')
buckets = s3.Bucket(name=AWS_BUCKET_NAME)
s3.Object(AWS_BUCKET_NAME, key_name).delete()
```

example 2

```python
import boto3
from conf import *

key_name = "copy_cat.jpg"

client = boto3.client(
    's3',
    aws_access_key_id=AWS_ACCESS_KEY_ID,
    aws_secret_access_key=AWS_SECRET_ACCESS_KEY,
    region_name=AWS_REGION
)

client = boto3.client('s3')
client.delete_object(Bucket=AWS_BUCKET_NAME, Key=key_name)
```

output

aws configure 설정되지 않을 경우 error가 발생한다.

```bahs
botocore.exceptions.NoCredentialsError: Unable to locate credentials
```

### 2.5. aws configure 로컬 설정

#### 방법1

awscli 라이브러리 설치

```
pip install awscli
```

'aws configure' 명령어를 이용하여 계정정보 등록

```bash
AWS Access Key ID [None]: Access Key ID
AWS Secret Access Key [None]: Secret Access Key
Default region name [None]: region name
Default output format [None]:
```

#### 방법2

awscli를 설치하지 않고 별도로 credentials를 지정할 수 있다. 

AWS에서는 해당 credentials을 바라보는 경로로 `~/.aws/` 디렉토리가 있습니다.

`credentials`

```
[default]
aws_access_key_id = Access Key ID
aws_secret_access_key = Secret Access Key
```

`config`

output 설정은 지정하지 않아도 된다.

```
[default]
region = region name
output = json
```


aws configure 설정 후 다시 delete 테스트하면 실행된다.



---

## 3. 업로드 및 다운로드

### 3.1. upload_file

[upload_file](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.upload_file)

- 버킷에 파일 업로드
- S3Transfer의 upload_file() 메서드와 비슷하게 동작

docs example

```python
import boto3
s3 = boto3.resource('s3')
s3.meta.client.upload_file('/tmp/hello.txt', 'mybucket', 'hello.txt')
```

example

```python
import boto3
from conf import *

session = boto3.Session(
    aws_access_key_id=AWS_ACCESS_KEY_ID,
    aws_secret_access_key=AWS_SECRET_ACCESS_KEY,
    region_name=AWS_REGION
)

# s3에 대한 권한 및 상태를 s3변수에 저장
s3 = session.resource('s3')
buckets = s3.Bucket(name=AWS_BUCKET_NAME)

file_name = 'cat.jpg'
file_path = os.path.join(IMAGE_DIR, file_name)
key_name = "copy_cat.jpg"

# 방법1
# upload_file(local_filepath, bucket_filepath)
if os.path.exists(file_path):
    buckets.upload_file(file_path, key_name)

# 방법2
# with open(file_path, 'rb') as data:
#     buckets.upload_file(data.name, key_name)
```


---

### 3.2. download_file

[download_file](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.download_file)

- 버킷에 저장된 파일 다운로드

docs example

```python
import boto3
s3 = boto3.resource('s3')
s3.meta.client.download_file('mybucket', 'hello.txt', '/tmp/hello.txt')
```

example

```python
import boto3
from conf import *

session = boto3.Session(
    aws_access_key_id=AWS_ACCESS_KEY_ID,
    aws_secret_access_key=AWS_SECRET_ACCESS_KEY,
    region_name=AWS_REGION
)

# s3에 대한 권한 및 상태를 s3변수에 저장
s3 = session.resource('s3')

buckets = s3.Bucket(name=AWS_BUCKET_NAME)

file_name = 'cat2.jpg'
file_path = os.path.join(IMAGE_DIR, file_name)
key_name = "copy_cat.jpg"

# buckets.download_file(bucket_filepath, local_filepath)
buckets.download_file(key_name, file_path)
```


---

### 3.3. generate_presigned_url

[generate_presigned_url](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/s3-presigned-urls.html)

- 다운로드 할 수 있는 URL 생성

IAM 설정은 AmazonS3FullAccess 설정이 되있다는 가정하에 진행한다.  
IAM : AWS 리소스에 대한 접근권한 제어할 수 있는 웹 서비스  

익명 접근가능한 버킷 정책  
설정 경로 (AWS - S3 - AWS_BUCKET_NAME - 버킷 정책)  

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicRead",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": "arn:aws:s3:::AWS_BUCKET_NAME/*"
        }
    ]
}
```

example

```python
import boto3
from conf import *

s3_client  = boto3.client(
    's3',
    aws_access_key_id=AWS_ACCESS_KEY_ID,
    aws_secret_access_key=AWS_SECRET_ACCESS_KEY,
    region_name=AWS_REGION
)

key_name = "copy_cat.jpg"

url = s3_client.generate_presigned_url(
    ClientMethod='get_object',
    Params={
        'Bucket': AWS_BUCKET_NAME,
        'Key': key_name,
    },
    # url 유효기간 (단위:second)
    ExpiresIn=10
)

print(url)
```


---

## 4. 예시코드 Git

[python-boto3-aws-s3](https://github.com/SangjunCha-dev/blog/tree/main/python/python-boto3-aws-s3)


---

## 참고(Reference)
- [Boto3 기본 설정 및 사용법](https://dev-navill.tistory.com/12?category=786913)
- [Boto3 파일 업로드 & 다운로드- upload and download](https://dev-navill.tistory.com/14?category=786913)
- [[AWS] 파이썬 - ec2 연동 에러 (botocore.exceptions.NoCredentialsError: Unable to locate credentials)](https://blog.naver.com/PostView.nhn?blogId=ksh60706&logNo=222143303320&parentCategoryNo=22&categoryNo=32&viewDate=&isShowPopularPosts=false&from=postView)