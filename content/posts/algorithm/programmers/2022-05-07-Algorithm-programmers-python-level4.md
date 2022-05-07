---
title: "Programmers Python (level 4)"
date: 2022-05-07T22:45:11+09:00
description: Programmers Python (level 4) 문제풀이 
tags: ["algorithm", "programmers", "python"]
categories: ["Algorithm", "Programmers"]
---


---

## [3차] 자동완성

분류 : 2018 KAKAO BLIND RECRUITMENT

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/17685)

1. 아래와 같은 문자열 값이 주어졌을때 convert_words 함수로 문자의 위치에 따른 문자 갯수와 다음순서의 문자의 딕셔너리 자료형을 저장한다.
    - 입력값
        ```
        go
        gone
        guild
        ```
    - 변환값
        ```json
        {
        "g": {
            "count": 3,
            "o": {
            "count": 2,
            "n": {
                "count": 1,
                "e": {
                "count": 1
                }
            }
            },
            "u": {
            "count": 1,
            "i": {
                "count": 1,
                "l": {
                "count": 1,
                "d": {
                    "count": 1
                }
                }
            }
            }
        }
        }
        ```

2. 변환된 자료형에서 count_word 함수로 count값이 모두 더한다. 이때 count값이 1인 경우 다음 문자를 탐색하지 않고 반환한다.

```python
def convert_words(words):
    dict_words = {}

    for word in words:
        dict_word = dict_words
        for w in word:
            if w in dict_word:
                dict_word[w]['count'] += 1
            else:
                dict_word[w] = {'count': 1}
            dict_word = dict_word[w]

    return dict_words

def count_word(dict_words):
    count = 0
    if 'count' in dict_words:
        count = dict_words['count']
        del dict_words['count']
        if count == 1:
            return count

    for words in dict_words.values():
        count += count_word(words)

    return count

def solution(words):
    dict_words = convert_words(words)
    answer = count_word(dict_words)
    return answer
```

풀이시간 : 37분

**2022-05-07**

> min TaseCase : 0.01ms, 10MB  
> max TaseCase : 751.77ms, 183MB  


---

<!--
## 

분류 : 

[문제 링크]()

1. 

```python

```

풀이시간 : 분

**2022**

> min TaseCase :   
> max TaseCase :   
-->
