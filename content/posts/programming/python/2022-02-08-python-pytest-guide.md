---
title: "[python] 테스트 프레임워크 pytest 사용법"
date: 2022-02-08T16:33:25+09:00
description: "[python] 테스트 프레임워크 pytest 사용법"
tags: ["python", "pytest"]
categories: ["Python", "pytest"]
---


---

## 1. 개요 및 설정

pytest는 에러 없는 좋은 코드를 개발만들기 위한 목적으로 개발된 Python 테스트 프레임워크이다.

pytest 특징
- 다른 testing 라이브러리에 비해 사용법이 간단하다.
- 테스트를 병렬로 실행할 수 있다.
- 특정 테스트를 스킵할 수 있다.
- 다양한 서드 파트 라이브러리들이 있다.

설치

```bash
pip install -U pytest
```


---

## 2. 기본 사용법

### 2.1. 기본 테스트

test_sample.py

```python
# 테스트 대상 기능
def inc(x):
    return x + 1

# 테스트 실행 함수
def test_answer1():
    assert inc(3) == 5

def test_answer2():
    assert inc(3) == 4
```

pytest 실행 결과

```bash
> pytest test_sample.py
======================= test session starts ========================
platform win32 -- Python 3.8.10, pytest-7.0.0, pluggy-1.0.0
rootdir: D:\workspace\blog\python\python-pytest
collected 2 items

test_sample.py F.                                             [100%]

============================= FAILURES ============================= 
___________________________ test_answer1 ___________________________ 

    def test_answer1():
>       assert inc(3) == 5
E       assert 4 == 5
E        +  where 4 = inc(3)

test_sample.py:7: AssertionError
===================== short test summary info ====================== 
FAILED test_sample.py::test_answer1 - assert 4 == 5
=================== 1 failed, 1 passed in 0.07s ====================
```

테스트 코드에서 의도한대로 `inc(3) == 5` 부분에서 에러가 발생한다.

### 2.2. 예외 테스트

test_sample_raises.py

```python
import pytest

def f():
    raise SystemExit(1)

def test_mytest():
    with pytest.raises(SystemExit):
        f()
```

```
> pytest test_sample_raises.py 
======================= test session starts ========================
platform win32 -- Python 3.8.10, pytest-7.0.0, pluggy-1.0.0
rootdir: D:\workspace_go\blog\python\python-pytest
collected 1 item                                                     

test_sample_raises.py .                                       [100%] 

======================== 1 passed in 0.01s ========================= 
```

### 2.2. 명칭 규약
- 파일 이름
    - `test_*.py` 또는 `*_test.py` 형식으로 지정한다.
    - `*_test.py` 는 python 3.8 버전 이상부터 적용된다.
- 클래스 명칭
    - `class Test*` 형식으로 지정한다.
- 클래스 메서드 및 함수 명칭
    - `def test_*` 형식으로 지정한다.

### 2.3. 실행 방법

- 해당 작업디렉토리 안에 모든 테스트 파일 실행

    ```bash
    > pytest
    ```

- 특정 디렉토리 내 테스트 파일 실행

    ```bash
    > pytest tests/
    ```

- 특정 테스트 파일 실행

    ```bash
    > pytest test_sample.py
    ```

### 2.4. 테스트 실행시 출력되는 정보

- 플랫폼 정보: python 버전, pytest 라이브러리 버전
- 테스트 실행 디렉토리
- 테스트 파일이름 및 진행률
- 테스트 실패 파일 및 코드 정보
- 총 테스트 케이스 개수 및 실행시간 정보


---

## 3. 테스트 디렉토리 구조

프로젝트의 효율적인 관리를 위해 소스코드, 테스트 코드 파일을 분리하여 저장한다.
테스트 코드는 보통 `tests/` 디렉토리에서 관리한다.

```
project/
    src/
        __init__.py
        calculator.py
    tests/
        __init__.py
        test_calculator.py
```

> python 디렉토리안에 `__init__.py` 파일이 없을 경우  
> `ModuleNotFoundError` 에러가 발생할 수 있다.  


---

## 4. pytest fixtures

`fixtures` 란?

- 테스트 프로세스를 초기화하여 시스템의 모든 전제 조건을 충족하도록 시스템을 설정하는 것
    - 데이터 베이스 등
- 같은 설정의 테스트를 쉽게 반복적으로 수행할 수 있도록 도와주는 것

`pytest fixtures`는 `python decorator`형식으로 사용한다.

src/calculator.py

```python
class Calculator():
    def add(self, x, y):
        return x + y

    def sub(self, x, y):
        return x - y

    def mul(self, x, y):
        return x * y

    def div(self, x, y):
        return x / y
```

tests/test_calculator.py

```python
import pytest

from src.calculator import Calculator

def test_add():
    calculator = Calculator()
    assert calculator.add(1, 2) == 3
    assert calculator.add(2, 2) == 4

def test_sub():
    calculator = Calculator()
    assert calculator.sub(5, 1) == 4
    assert calculator.sub(3, 2) == 1

def test_mul():
    calculator = Calculator()
    assert calculator.mul(2, 2) == 4
    assert calculator.mul(5, 6) == 30

def test_div():
    calculator = Calculator()
    assert calculator.div(8, 2) == 4
    assert calculator.div(9, 3) == 3
```

### 4.1. @pytest.fixture

테스트코드를 작성하다 보면 클래스 호출 등 테스트에 반복 사용되는 코드가 존재한다.
이러한 코드 중복성을 문제를 해결하기 위한 테스트 함수 실행전 실행되는 함수를 `@pytest.fixture` 데코레이터로 선언한다.

tests/test_calculator_fixture_v1.py

```python
import pytest

from src.calculator import Calculator

@pytest.fixture
def calculator():
    calculator = Calculator()
    return calculator

def test_add(calculator):
    assert calculator.add(1, 2) == 3
    assert calculator.add(2, 2) == 4

def test_sub(calculator):
    assert calculator.sub(5, 1) == 4
    assert calculator.sub(3, 2) == 1
...
```

사전에 `fixture 함수`를 정의하고 `test_*` 함수의 파라미터로 사용하여 클래스 선언등의 초기화를 진행할 수 있다.

### 4.2. conftest.py

여러 test 파일에 공통적으로 fixture 함수가 선언하면 중복된 코드를 작성하게 된다. 
이러한 문제는 별도의 `conftest.py` 파일에 fixture 함수를 선언하여 해결할 수 있다.  

tests/conftest.py

```python
import pytest

from src.calculator import Calculator

@pytest.fixture
def calculator():
    calculator = Calculator()
    return calculator
```

tests/test_calculator_fixture_v2.py

```python
def test_add(calculator):
    assert calculator.add(1, 2) == 3
    assert calculator.add(2, 2) == 4

def test_sub(calculator):
    assert calculator.sub(5, 1) == 4
    assert calculator.sub(3, 2) == 1
...
```


---

## 5. Parameterize

테스트 케이스마다 테스트값을 포함한 테스트 함수를 호출하는 코드도 중복 작성하는 부분이다. 이를 위한 테스트 입력값과 결과값을 `@pytest.mark.parametrize(argnames, argvalues)` 데코테이터로 작성하여 테스트 코드를 간결하게 만들 수 있다.

tests/test_calculator_parametrize.py

```python
import pytest

@pytest.mark.parametrize(
    "a, b, result",
    [(1, 2, 3),
     (2, 2, 4)]
)
def test_add(calculator, a, b, result):
    assert calculator.add(a, b) == result

@pytest.mark.parametrize(
    "a, b, expected",
    [(1, 2, 4),
     (2, 2, 6)]
)
def test_add_fail(calculator, a, b, expected):
    assert calculator.add(a, b) != expected
```

실행 결과

```
> pytest tests\test_calculator_parametrize.py
======================= test session starts ========================
platform win32 -- Python 3.8.10, pytest-7.0.0, pluggy-1.0.0
rootdir: D:\workspace_go\blog\python\python-pytest
collected 4 items

tests\test_calculator_parametrize.py ....                     [100%] 

======================== 4 passed in 0.03s =========================
```

기존 테스트와의 차이점은 테스트 함수안에서 여러개의 테스트를 진행한게 아니라 각각의 테스트 케이스들이 별도로 실행된다.


---

## 6. xfail

`@pytest.mark.xfail`는 테스트 실패가 예상되는 함수에 지정하는 데코레이터이다. `reason` 파라미터는 작성자가 실패가 예상되는 이유를 작성하는 부분이다.

tests/test_calculator_xfail_v1.py

```python
import pytest

@pytest.mark.parametrize(
    "a, b, expected",
    [(1, 2, 4),
     (2, 2, 6)]
)
def test_add_fail_parametrize(calculator, a, b, expected):
    assert calculator.add(a, b) != expected

@pytest.mark.xfail(reason="wrong result")
@pytest.mark.parametrize(
    "a, b, expected",
    [(1, 2, 4),
     (2, 2, 6),
     (3, 4, 7)]
)
def test_add_fail_xfail(calculator, a, b, expected):
    assert calculator.add(a, b) == expected
```

실행결과

```
>pytest tests\test_calculator_xfail_v1.py
======================= test session starts ========================
platform win32 -- Python 3.8.10, pytest-7.0.0, pluggy-1.0.0
rootdir: D:\workspace_go\blog\python\python-pytest
collected 5 items                                                    

tests\test_calculator_xfail_v1.py ..xxX                       [100%]

============= 2 passed, 2 xfailed, 1 xpassed in 0.07s ============== 
```

- `passed` : 에러없이 테스트케이스 통과한 개수
- `xfailed` : xfail로 지정한 테스트케이스 중 정상적으로 실패한 개수
- `xpassed` : xfail로 지정한 테스트케이스 중 의도하지않은 성공한 개수


---

## 7. param

`pytest.param`을 사용하여 테스트 케이스별로 옵션을 지정할 수 있다.

tests/test_calculator_param_v1.py

```python
import pytest

@pytest.mark.parametrize(
    "a, b, expected",
    [pytest.param(1, 2, 4, marks=pytest.mark.xfail),
     pytest.param(2, 2, 6, marks=pytest.mark.xfail)]
)
def test_add_fail_xfail(calculator, a, b, expected):
    assert calculator.add(a, b) == expected
```

### 7.1. 성공/실패 케이스 통합

위에서 학습한 `pytest.param xfail`을 사용하면 하나의 테스트 함수에서 성공과 실패 케이스 모두 테스트할 수 있다. 

tests/test_calculator_param_v2.py

```python
import pytest

@pytest.mark.parametrize(
    "a, b, result",
    [(1, 2, 3),
     (2, 2, 4),
     pytest.param(1, 2, 4, marks=pytest.mark.xfail),
     pytest.param(2, 2, 6, marks=pytest.mark.xfail)]
)
def test_add(calculator, a, b, result):
    assert calculator.add(a, b) == result
```

### 7.2. 테스트 케이스 전역변수

가독성 및 재사용성을 위해 테스트케이스를 별도의 변수로 지정하여 사용할 수 있다.

tests/test_calculator_param_v3.py

```python
import pytest

add_test_data = [
    (1, 2, 3),
    (2, 2, 4),
    pytest.param(1, 2, 4, marks=pytest.mark.xfail),
    pytest.param(2, 2, 6, marks=pytest.mark.xfail),
]

@pytest.mark.parametrize("a, b, result", add_test_data)
def test_add(calculator, a, b, result):
    assert calculator.add(a, b) == result
```


---

## 8. 설명문 추가 - id

각 테스트 케이스별로 id를 작성하여 해당 케이스의 의미를 작성할 수 있다.

```python
import pytest

add_test_data = [
    pytest.param(1, 2, 3, id="1 add 2 is 3"),
    pytest.param(2, 2, 4, id="2 add 2 is 4"),
    pytest.param(1, 2, 4, marks=pytest.mark.xfail, id="1 add 2 is not 4"),
    pytest.param(2, 2, 6, marks=pytest.mark.xfail, id="2 add 2 is not 6"),
]

@pytest.mark.parametrize("a, b, result", add_test_data)
def test_add(calculator, a, b, result):
    assert calculator.add(a, b) == result

```


## 9. skip

테스트 함수를 사용하지 않을 때 `@pytest.mark.skip` 데코레이터를 사용함으로 테스트를 생략할 수 있다.

다른 사용법으로 테스트가 조건을 만족할 경우 `pytest.skip`함수로 skip 처리가 가능하다.

tests/test_skip.py

```python
import pytest

@pytest.mark.skip(reason="no way of currently testing this")
def test_skip_v1():
    assert 1 == 1

def test_skip_v2():
    if True:
        pytest.skip(reason="no way of currently testing this")
    assert 1 == 1
```

실행 결과

```
> pytest tests\test_skip.py
======================= test session starts =======================
platform win32 -- Python 3.8.10, pytest-7.0.0, pluggy-1.0.0
rootdir: D:\workspace_go\blog\python\python-pytest
collected 2 items

tests\test_skip.py ss                                        [100%] 

======================= 2 skipped in 0.02s ======================== 
```

- `skipped` : 생략된 테스트 개수

### 9.1. skipif

플랫폼이나 라이브러리 설치 유무, 버전등에 따라서 skip이 필요한 경우가 있다. 

`@pytest.mark.skipif(조건문)` 데코레이터로 조건에 부합될 경우에만 테스트 함수를 생략할 수 있다.


tests/test_skipif.py

```python
import pytest
import sys

@pytest.mark.skipif(sys.version_info < (3, 7), reason="requires python 3.7 or higher")
def test_skipif_v1():
    assert 1 == 1

try:
    import numpy as np
except ImportError:
    pass

@pytest.mark.skipif('numpy' not in sys.modules, reason="requires the Numpy library")
def test_skipif_v2():
    assert 1 == 1
```

실행 결과

- test_skipif_v1 함수 : 현재 python 버전이 3.8이므로 생략되지 않고 테스트 실행됨
- test_skipif_v2 함수 : numpy 라이브러리 설치가 되지 않아 ImportError가 발생하고 테스트 생략됨

```
> pytest tests\test_skipif.py
======================= test session starts =======================
platform win32 -- Python 3.8.10, pytest-7.0.0, pluggy-1.0.0
rootdir: D:\workspace_go\blog\python\python-pytest
collected 2 items

tests\test_skipif.py .s                                      [100%] 

================== 1 passed, 1 skipped in 0.02s ===================
```


---

## 10. TestClass

테스트 함수를 클래스로 그룹화 할 수 있다.

```python
class TestCalcultor:
    value = 0

    def test_add(self):
        self.value += 1
        assert self.value == 1

    def test_result(self):
        assert self.value == 0
```

실행 결과

클래스 내에 각 테스트는 각각의 고유한 클래스 인스턴스를 가지고 있어 self 변수값이 공유되지 않는다.

```
> pytest tests\test_class.py
======================= test session starts =======================
platform win32 -- Python 3.8.10, pytest-7.0.0, pluggy-1.0.0
rootdir: D:\workspace_go\blog\python\python-pytest
collected 2 items

tests\test_class.py ..                                       [100%] 

======================== 2 passed in 0.03s ======================== 
```


---

## 11. 예시코드 Git

[python-pytest](https://github.com/SangjunCha-dev/blog/tree/main/python/python-pytest)


---

## 참고(Reference)

- [pytest docs](https://docs.pytest.org/en/latest/)
- [pytest-xdist docs](https://pytest-xdist.readthedocs.io/en/latest/)
- [unittest vs pytest](https://www.bangseongbeom.com/unittest-vs-pytest.html)
- [PyCon-KR-2019 - pytest](https://github.com/yeongseon/PyCon-KR-2019)
- [How do I disable a test using pytest?](https://stackoverflow.com/questions/38442897/how-do-i-disable-a-test-using-pytest)