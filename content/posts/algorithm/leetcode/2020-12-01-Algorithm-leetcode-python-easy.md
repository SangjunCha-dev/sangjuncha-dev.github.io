---
title: "LeetCode Python (Easy)"
date: 2020-12-01T17:30:00+09:00
description: LeetCode Python(Easy) 문제풀이 
menu:
  sidebar:
    name: LeetCode Python
    identifier: leetcode-python
    parent: leetcode
    weight: 30
tags: ["algorithm", "leetcode", "python"]
categories: ["Algorithm", "LeetCode"]
---



# [1. Two Sum](https://leetcode.com/problems/two-sum/)

1. `nums` 리스트 속성값 중 `두개의 값`이 `target` 값과 동일할때 해당 `속성 값`의 `index` 반환

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        for i, num in enumerate(nums):
            for j, num2 in enumerate(nums[i+1:]):
                if i != j+i+1:
                    if target == num+num2:
                        return [i, j+i+1]
```

**2020-11-27**

> Runtime: `40 ms`, faster than `97.02%` of Python3 online submissions for Two Sum. <br>
> Memory Usage: `14.5 MB`, less than `89.48%` of Python3 online submisstions for Two Sum. <br>



# [7. Reverse Integer](https://leetcode.com/problems/reverse-integer/)

1. `x`값을 list형으로 변환하여 `testCase` 변수에 저장
2. `testCase[0]`값에 `-`부호가 있다면 `sign` 변수에 저장하고 `testCase[0]`에서 부호 삭제
3. for문을 사용하여 `testCase` 역순으로 `result` 변수에 저장
4. `sign`변수에 값이 있다면 `sign + result`값을 `result`에 저장
5. 결과값이 int 32비트범위를 넘지 않으면 `result`을, 넘으면 `0` 반환
    - return : `x` 숫자를 역순으로 나열하여 반환

```python
class Solution:
    def reverse(self, x: int) -> int:
        testCase = list(str(x))
        sign = None
        result = ''
        
        if testCase[0] == '-':
            sign = testCase.pop(0)
            
        testCaseLen = len(testCase)
        for i in range(testCaseLen):
            result += testCase[testCaseLen-1-i]
        
        if sign:
            result = sign + result
        
        if -2147483648 <= int(result) <= 2147483647:
            return int(result)
        else:
            return 0
```

**2020-11-27** 

> Runtime: `28 ms`, faster than `83.14%` of Python3 online submissions for Reverse Integer. <br>
> Memory Usage: `14.2 MB`, less than `41.05%` of Python3 online submisstions for Reverse Integer. <br>



# [9. Palindrome Number](https://leetcode.com/problems/palindrome-number/)

1. `x`값을 `num`변수에 대입
2. `res`변수에 나머지연산으로 `num` 1의 자리값을 더하고 `num` 나누기 연산하여 1의 자리 삭제
3. `res`변수를 10곱하여 자릿수 증가
4. 위의 과정을 `num`변수가 0이 될때까지 반복
5. 초기 `x`값과 `res`변수를 비교한 결과 반환

시간복잡성 : O(n)
공간복잡성 : O(1)

```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if x < 0: return False

        num, res = x, 0
        while num != 0:
            res = res*10 + num%10
            num //= 10
        return x==res
```

**2020-12-15** 

> Runtime: `52 ms`, faster than `88.59%` of Python3 online submissions for Palindrome Number. <br>
> Memory Usage: `14.1 MB`, less than `59.38%` of Python3 online submissions for Palindrome Number. <br>



# [14. Longest Common Prefix](https://leetcode.com/problems/longest-common-prefix/)

1. 문자열 길이로 정렬된 `strs` 리스트에서 가장 짧은 `strs[0]`문자열로 각 문자열의 `i`번째 값과 비교
2. 모든 문자열에서 동일한 문자가 있다면 `compareText`변수에 추가하고 마지막에 `compareText` 반환
    - return : `strs` 리스트의 모든 문자열에서 공통된 문자열 반환

```python
class Solution:
    def longestCommonPrefix(self, strs: List[str]) -> str:
        if not strs:
            return ''    
        strs.sort(key=len)
        compareText = ''
        for i in range(len(strs[0])):
            for text in strs[1:]:
                if text[i] != strs[0][i]:
                    return compareText
            compareText += strs[0][i]
        return compareText
```

**2020-12-01**

> Runtime: `28 ms`, faster than `90.77%` of Python3 online submissions for Longest Common Prefix. <br>
> Memory Usage: `14.3 MB`, less than `47.57%` of Python3 online submisstions for Longest Common Prefix. <br>



# [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)

1. 열린 괄호 `(`, `{`, `[` 는 list자료형에 `스택에 쌓고` 
2. 닫는 괄호 `)`, `}`, `]` 는 스택의 마지막값과 비교해서 동일한 종류면 `스택에서 값 제거`
3. 최종적으로 list 길이가 0일때 `True` 반환 아니면 `False` 반환
 - True : 모든 괄호가 쌍일 경우
 - False : 쌍을 이루지 못한 괄호가 있을 경우

```python
class Solution:
    def isValid(self, s: str) -> bool:
        textLen = len(s)
        if (not str) or ((textLen%2) == 1):
            return False
        stackList = []
        
        for i in range(textLen):
            if s[i] == '(' or s[i] == '{' or s[i] == '[':
                stackList.append(s[i])
            elif not stackList:
                return False
            elif s[i] == ')' and stackList[-1] == '(':
                stackList.pop()
            elif s[i] == '}' and stackList[-1] == '{':
                stackList.pop()
            elif s[i] == ']' and stackList[-1] == '[':
                stackList.pop()
            else:
                return False
        if stackList:
            return False
        else:
            return True
```

**2020-12-03**  

> Runtime: `16 ms`, faster than `99.83%` of Python3 online submissions for Valid Parentheses. <br>
> Memory Usage: `14.3 MB`, less than `24.08%` of Python3 online submisstions for Valid Parentheses. <br>



# [35. Search Insert Position](https://leetcode.com/problems/search-insert-position/)

1. `nums` 리스트 첫 번째 index를 `low`변수에 저장
2. `nums` 리스트 마지막 index를 `high`변수에 저장
3. while문을 통해 low 값이 high 값보다 작을 때 실행
    - `nums` 리스트의 중간위치인 (low+high)//2 값을 `index` 변수에 저장
    - `nums[index]`값이 `target`보다 클 때 `index+1`값을 `low`변수에 저장
    - 그 외에는 `index`값을 `high`변수에 저장
4. while문 종료 후 `low` 변수 반환
    - return : `target`값이 들어갈 index 반환

```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        if target <= nums[0]:
            return 0
        
        low, high = 0, len(nums)

        while low < high:
            index = (low+high)//2

            if (nums[index] < target):
                low = index+1
            else:
                high = index
        return low
```

**2020-12-02**  

> Runtime: `40 ms`, faster than `96.18%` of Python3 online submissions for Search Insert Position. <br> 
> Memory Usage: `15.1 MB`, less than `9.31%` of Python3 online submisstions for Search Insert Position. <br>



# [38. Count and Say](https://leetcode.com/problems/count-and-say/)

1. 비교 문자열 `1` 선언
2. 아래의 알고리즘을 입력 받은 `n`번 반복 실행
    - 문자 하나씩 비교하여 같으면 `count` 1증가
    - 문자가 다르면 `resultText` 문자열에 `count`값과 리스트 `[i-1]`번째 문자를 저장
3. 안쪽 for문 종료 후 `numText` 마지막 [-1]문자열 값 대입하여 반환
    - return : 입력받은 각 숫자의 갯수와 숫자를 반환

```python
class Solution:
    def countAndSay(self, n: int) -> str:
        numText = '1'
        for _ in range(1, n):
            count = 1
            resultText = ''
            for i in range(1,len(numText)):
                if numText[i-1] != numText[i]:
                    resultText += (str(count) + numText[i-1])
                    count = 1
                else:
                    count += 1
            numText = resultText + str(count) + numText[-1]

        return numText
```

**2020-12-07**

> Runtime: `40 ms`, faster than `70.73%` of Python3 online submissions for Count and Say. <br>
> Memory Usage: `14.3 MB`, less than `48.22%` of Python3 online submisstions for Count and Say. <br>



# [53. Maximum Subarray](https://leetcode.com/problems/maximum-subarray/)

1. 동적 계획법(Dynamic Programming, DP) 사용할 것
2. 아래는 잘못푼 문제

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        if len(nums) == 1:
            return nums[0]
        
        numMax = max(nums)
        if numMax < 1:
            return numMax
        
        start, end = 0, 0
        for i in range(len(nums)-1):
            if 0 < nums[start]:
                end = i+2
                numSum = sum(nums[start:end])
                if numSum < 0:
                    start = end
                elif numMax <= numSum:
                    numMax = numSum
            else:
                start = i+1
            
        return numMax
```

**2020-12-08**

> Runtime: `1580 ms,`, faster than `5.26%` of Python3 online submissions for Maximum Subarray. <br>
> Memory Usage: `15.2 MB`, less than `5.01%` of Python3 online submisstions for Maximum Subarray. <br>



# [58. Length of Last Word](https://leetcode.com/problems/length-of-last-word/)

1. 문자열 `앞뒤 공백 제거`
2. 공백` `으로 `분할`하고 리스트 `마지막 문자열` 반환
    - return : 마지막 문자열 반환

```python
class Solution:
    def lengthOfLastWord(self, s: str) -> int:
        return len(s.strip().split(' ')[-1])
```

**2020-12-09**

> Runtime: `16 ms`, faster than `99.80%` of Python3 online submissions for Length of Last Word. <br>
> Memory Usage: `14.2 MB`, less than `38.86%` of Python3 online submissions for Length of Last Word. <br>



# [66. Plus One](https://leetcode.com/problems/plus-one/)

1. `digits` 리스트 마지막 값 `1증가`
2. 반복문을 통해 리스트 `역순`으로 값 `한자릿수` 검증
3. 모든요소가 단일숫자 구성시 `digits` 반환
4. `digits[0]` 값이 `10`으로 나눠질경우 digits[0]위치에 `[1]값 속성 추가`하여 반환
    - return : `digits` 리스트 반환

```python
class Solution:
    def plusOne(self, digits: List[int]) -> List[int]:
        digits[-1] += 1
        for i in range(len(digits)-1, -1, -1):
            if digits[i]%10 != 0:
                return digits
            elif i != 0:
                digits[i] = 0
                digits[i-1] += 1
            else:
                digits[i] = 0
                return [1] + digits
```

**2020-12-04**  

> Runtime: `24 ms`, faster than `95.84%` of Python3 online submissions for Plus One. <br>
> Memory Usage: `14.2 MB`, less than `38.47%` of Python3 online submisstions for Plus One. <br>



# [136. Single Number](https://leetcode.com/problems/single-number/)

1. nums 리스트 정렬
2. index(0,2,4,...) 2씩 증가하는 for문 실행
3. nums 리스트 `index의 값`과 `index+1의 값`을 `비교`하여 다를경우 list `index` 값 반환
4. 리스트 마지막까지 도달할경우 `nums[-1]` 반환
    - return : nums 리스트에서 같은 수가 없는 값 반환

시간복잡성 O(n)

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        nums.sort()
        numsLen = len(nums)
        for i in range(0, len(nums), 2):
            if numsLen <= i+1:
                return nums[-1]
            elif nums[i] != nums[i+1]:
                return nums[i]
```

**2020-12-04**

> Runtime: `124 ms`, faster than `85.60%` of Python3 online submissions for Single Number. <br>
> Memory Usage: `16.6 MB`, less than `60.41%` of Python3 online submisstions for Single Number. <br>



# [167. Two Sum II - Input array is sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

1. 리스트 첫번째 인덱스 = `low`, 마지막 인덱스 = `high` 선언
2. `while`문 `low`값이 `high`값보다 작을때 반복실행
    - `리스트[low]` + `리스트[high]` 값이 `target`보다 작으면 `low` 1 증가
    - `리스트[low]` + `리스트[high]` 값이 `target`보다 크면 `high` 1 감소
    - `리스트[low]` + `리스트[high]` 값이 `target` 같은 값이면 `low`, `high` return 

- return : `numbers`리스트 두수의 합이 `target`값과 같을 때 두수의 `index`값 반환

```python
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        low, high = 0, len(numbers)-1
        
        while low < high:
            val = numbers[high] + numbers[low]
            
            if target < val:
                high -= 1
            elif val < target:
                low += 1
            else:
                return [low+1, high+1]
```

**2020-12-11**  

> Runtime: `56 ms`, faster than `92.55%` of Python3 online submissions for Two Sum II - Input array is sorted. <br>
> Memory Usage: `14.8 MB`, less than `11.31%` of Python3 online submissions for Two Sum II - Input array is sorted. <br>



# [169. Majority Element](https://leetcode.com/problems/majority-element/)

## 방법 1

1. `nums`리스트에서 `중복제거`
2. `nums`리스트 for문을 통해 반복
    - 해당 속성 값의 `cnt` 값이 `max`값보다 클때
    - `max`변수에 `cnt`값 저장
    - `textMax`변수에 해당 속성값 저장
3. for문이 끝난 마지막에 `textMax` 반환
    - return : 입력받은 `nums`리스트의 문자 중 갯수가 많은 문자 반환


```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        max, textMax = 0, None
        for num in list(set(nums)):
            cnt = nums.count(num)
            if max < cnt:
                max = cnt
                textMax = num
        return textMax
```

**2020-12-09**

> Runtime: `136 ms`, faster than `99.99%` of Python3 online submissions for Majority Element. <br>
> Memory Usage: `15.4 MB`, less than `35.47%` of Python3 online submissions for Majority Element. <br>

## 방법 2

1. `nums`리스트 정렬하여 `중간 위치값` 반환
    - 입력받는 문자는 두 종류

```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        nums.sort()
        return nums[len(nums)//2]
```

**2020-12-09**

> Runtime: `148 ms`, faster than `98.15%` of Python3 online submissions for Majority Element. <br>
> Memory Usage: `15.4 MB`, less than `35.47%` of Python3 online submissions for Majority Element. <br>



# [204. Count Primes](https://leetcode.com/problems/count-primes/)

1. `아리스토텔레스의 체` 이용하여 소수가 아닌값을 거르는 알고리즘

```python
class Solution:
    def countPrimes(self, n: int) -> int:
        if n < 3: return 0

        numList = [True] * n
        sqrt = int(n**0.5)+1
            
        for i in range(2, sqrt):
            if numList[i]:
                for j in range(i+i, n, i):
                    numList[j] = False
        return numList.count(True)-2
```

**2020-12-09**

> Runtime: `392 ms`, faster than `80.27%` of Python3 online submissions for Count Primes. <br>
> Memory Usage: `25.7 MB`, less than `55.70%` of Python3 online submissions for Count Primes. <br>



# [217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)

1. `nums`리스트 중복제거한 길이와 `nums` 리스트 길이와 비교
2. 길이가 같다면 `False` 길이가 다르면 중복제거하여 `True` 반환
    - True : nums 리스트에 중복값이 포함
    - False : nums 리스트에 중복값이 없음

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        return True if len(nums) != len(set(nums)) else False
```

**2020-12-11**

> Runtime: `108 ms`, faster than `93.87%` of Python3 online submissions for Count Primes. <br>
> Memory Usage: `20.2 MB`, less than `44.91%` of Python3 online submissions for Count Primes. <br>

1. 리스트 값을 dict 키값으로 저장
2. 리스트 반복문 실행 중에 해당 키값이 있다면 `True` 반환
3. 모든 리스트가 if 조건에 걸리지 않으면 `False` 반환
    - True : 중복된 값이 존재할 경우
    - False : 중복된 값이 없을 경우

```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        dict = {}
        for num in nums:
            if dict.get(num):
                return True
            dict[num] = True
        return False
```

**2020-12-11**

> Runtime: `116 ms`, faster than `65.53%` of Python3 online submissions for Contains Duplicate. <br>
> Memory Usage: `21.5 MB`, less than `8.09%` of Python3 online submissions for Contains Duplicate. <br>



# [231. Power of Two](https://leetcode.com/problems/power-of-two/)

## 방법 1

1. `n`이 양수일때 이진수로 변환
2. 이진수 리스트에서 앞의 두자리를 제외한 리스트를 저장 (`b0~`형식)
3. `0번째` 값을 제외하고 for 반복문 실행
4. 리스트 `0번째` 값과 비교하여 같은 1이 나오면 `False` 반환
5. 반복문 종료까지 if 조건문 안걸리면 `True` 반환
    - True : 입력받은 값이 2의 제곱수일 경우
    - False : 입력받은 값이 2의 제곱수가 아닐 경우

```python
class Solution:
    def isPowerOfTwo(self, n: int) -> bool:
        if n < 1: return False
        nums = list(bin(n))[2:]
        for num in nums[1:]:
            if nums[0] == num: return False
        return True
```

**2020-12-11**

> Runtime: `24 ms`, faster than `94.77%` of Python3 online submissions for Power of Two. <br>
> Memory Usage: `14.3 MB`, less than `15.35%` of Python3 online submissions for Power of Two. <br>

## 방법 2

1. 입력값 `n`이 양수이고
2. 2의 거듭제곱수 판별은 `n`값과 `n-1`값을 비트 `AND`연산하면 `0`으로 나옴
3. 위의 2가지 조건이 만족하면 `True` 만족하지 않는다면 `False` 반환

```python
class Solution:
    def isPowerOfTwo(self, n: int) -> bool:
        return (0<n) and ((n&(n-1)) == 0)
```

**2020-12-11**

> Runtime: `24 ms`, faster than `94.77%` of Python3 online submissions for Power of Two. <br>
> Memory Usage: `14.1 MB`, less than `80.64%` of Python3 online submissions for Power of Two. <br>



# [242. Valid Anagram](https://leetcode.com/problems/valid-anagram/)

1. `s`, `t` 문자열을 `sorted`함수로 정렬된 리스트 반환받아 비교하여 결과값 반환
    - True : 입력받은 두 문자열이 동일할 경우
    - False : 입력받은 두 문자열이 다를 경우

```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        return sorted(s) == sorted(t)
```

**2020-12-11**

> Runtime: `40 ms`, faster than `84.57%` of Python3 online submissions for Valid Anagram. <br>
> Memory Usage: `15 MB`, less than `6.58%` of Python3 online submissions for Valid Anagram. <br>



# [258. Add Digits](https://leetcode.com/problems/add-digits)

## 방법 1

1. `num` 값을 `10`으로 나눈 `몫`과 `나머지`를 `합산`하고
2. `num` 값이 `10 이하` 일때 반환
    - return : 자릿수근 반환

시간복잡성 : O(n)

```python
class Solution:
    def addDigits(self, num: int) -> int:
        while 9 < num:
            num = num//10 + num%10
        return num
```

**2020-12-14**

> Runtime: `20 ms`, faster than `98.98%` of Python3 online submissions for Add Digits. <br>
> Memory Usage: `14.3 MB`, less than `20.30%` of Python3 online submissions for Add Digits. <br>

## 방법 2

1. `num` 값을 `(num-1) % 9 + 1`자릿수근(digital root) 공식을 이용하여 결과값 반환
    - return : 자릿수근 반환

시간복잡성 : O(1)

```python
class Solution:
    def addDigits(self, num: int) -> int:
        return 0 if num == 0 else (num-1)%9 + 1
```

**2020-12-14**

> Runtime: `24 ms`, faster than `95.45%` of Python3 online submissions for Add Digits. <br>
> Memory Usage: `14.3 MB`, less than `20.30%` of Python3 online submissions for Add Digits. <br>



# [263. Ugly Number](https://leetcode.com/problems/ugly-number/)

1. while 반복문으로 `num`값이 `5`로 나눈 `나머지`가 `0`일때 `몫`을 `num`값에 저장
2. 동일한 방식으로 `3`, `2`로 나눈다.
3. 결과값이 `1`일때만 `True`, 그외의 결과는 `False` 반환
    - True : 입력받은 값이 `2`, `3`, `5`로 나누어질 경우
    - False : 그외의 경우

```python
class Solution:
    def isUgly(self, num: int) -> bool:
        if num == 0: return False
        while num % 5 == 0:
            num //=  5
        while num % 3 == 0:
            num //= 3
        while num % 2 == 0:
            num //= 2
        return num == 1
```

**2020-12-14**

> Runtime: `28 ms`, faster than `82.19%` of Python3 online submissions for Ugly Number. <br>
> Memory Usage: `14.3 MB`, less than `19.35%` of Python3 online submissions for Ugly Number. <br>



# [268. Missing Number](https://leetcode.com/problems/missing-number/)

## 풀이 방법 1

1. `nums`리스트 정렬
2. `nums`리스트 길이만큼 for문 실행
3. index 위치값에 해당되는 값이 없으면 index 반환
4. for문 마지막까지 if문 해당되지 않을경우 `nums`리스트 길이 반환
    - return : `nums`리스트에 없는 값 반환

공간복잡성 : O(1)  
시간복잡성 : O(n)

```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        nums.sort()
        numsLen = len(nums)
        for i in range(numsLen):
            if nums[i] != i: 
                return i
        return numsLen
```

**2020-12-14**

> Runtime: `136 ms`, faster than `38.61%` of Python3 online submissions for Missing Number. <br>
> Memory Usage: `15.5 MB`, less than `27.11%` of Python3 online submissions for Missing Number. <br>

## 풀이 방법 2

1. `nums`리스트의 길이를 `n`변수에 대입
2. 1부터 n까지 수의 합(가우스 공식)을 계산하여 `total`변수에 대입
    - 가우스 공식 : n*(n-1)/2 + n
3. `total` - (`nums`리스트 합)을 값을 반환
    - return : `nums`리스트에 없는 값 반환

공간복잡성 : O(1)  
시간복잡성 : O(n)

```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        n = len(nums)
        total = int(n*(n-1)/2)+n
        return total - sum(nums)
```

**2020-12-14**

> Runtime: `120 ms`, faster than `93.14%` of Python3 online submissions for Missing Number. <br>
> Memory Usage: `15.4 MB`, less than `38.54%` of Python3 online submissions for Missing Number. <br>



# [278. First Bad Version](https://leetcode.com/problems/first-bad-version)

1. 이진탐색으로 풀이
2. `low`변수에 0, `high`변수에 `n` 대입
3. while문으로 low변수가 high보다 작을때 반복실행
    - `low`와 `high`의 중간값을 `middle`변수에 저장
    - isBadVersion(middle)의 반환값이 `True`이면 `high`변수에 `middle` 대입
    - isBadVersion(middle)의 반환값이 `False`이면 `low`변수에 `middle + 1` 대입
4. `low`변수 반환

- return : isBadVersion(n) 반환값중 `False`결과가 나오는 가장 큰 `n+1` 반환

시간복잡성 : O(log n)

```python
class Solution:
    def firstBadVersion(self, n):
        """
        :type n: int
        :rtype: int
        """
        low, middle, high = 0, 1, n
        
        while low < high:
            middle = (low+high)//2
            
            if isBadVersion(middle):
                high = middle
            else:
                low = middle + 1
        return low
```

**2020-12-14**

> Runtime: `24 ms`, faster than `90.25%` of Python3 online submissions for First Bad Version. <br>
> Memory Usage: `14.1 MB`, less than `45.50%` of Python3 online submissions for First Bad Version. <br>



# [283. Move Zeroes](https://leetcode.com/problems/move-zeroes/)

1. `nums`리스트 요소값이 `0`이 아닐때 index인 `i`값 1증가시키고
2. `nums`리스트 요소값이 `0`일때 해당 요소삭제 및 리스트 마지막에 `[0]`추가
3. 위의 과정들을 for문으로 `nums`리스트의 길이만큼 반복 실행

- return : 앞에는 0을 제외한 `nums`리스트, 뒤에는 제외된 `[0]`이 리스트에 붙어서 반환

시간복잡성 : O(log n)  
공간복잡성 : O(1)

```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        zero_cnt = 0
        for i in range(len(nums)-1, -1, -1):
            if nums[i] == 0:
                del nums[i]
                zero_cnt += 1
        nums += ([0] * zero_cnt)
```

**2020-12-15**

> Runtime: `44 ms`, faster than `88.45%` of Python3 online submissions for Move Zeroes. <br>
> Memory Usage: `15.2 MB`, less than `63.17%` of Python3 online submissions for Move Zeroes. <br>



# [292. Nim Game](https://leetcode.com/problems/nim-game/)

1. `n`값을 4로 나누었을때 0일경우만 `False`, 그외에 `True`

시간복잡성 : O(1)  
공간복잡성 : O(1)

```python
class Solution:
    def canWinNim(self, n: int) -> bool:
        return n%4
```

**2020-12-15**

> Runtime: `28 ms`, faster than `68.93%` of Python3 online submissions for Nim Game. <br>
> Memory Usage: `14.2 MB`, less than `38.05%` of Python3 online submissions for Nim Game. <br>



# [342. Power of Four](https://leetcode.com/problems/power-of-four/)

## 풀이 방법 1

1. 양수인 `n`을 이진수로 변환
2. 각 이진수를 int형변환하고 `num`리스트에 저장
3. 이진수가 짝수개이면 4의 제곱이 아니므로 return 
4. `num`리스트에서 0번째를 제외한 모든 값을 or연산
    - 모든값이 0 일때만 4의 제곱수
5. 연산한 결과 `result` not 연산하여 반환

시간복잡성 : O(log n)  
공간복잡성 : O(1)

```python
class Solution:
    def isPowerOfFour(self, n: int) -> bool:
        if n < 1: return False
        
        nums = list(map(int, bin(n)[2:]))
        if len(nums)%2==0: return False
        
        result = False
        for num in nums[1:]:
            result |= num
        return not result
```

**2020-12-15**

> Runtime: `16 ms`, faster than `99.80%` of Python3 online submissions for Power of Four. <br>
> Memory Usage: `14.3 MB`, less than `17.90%` of Python3 online submissions for Power of Four. <br>


## 풀이 방법 2

1. 양수인 `n`을 2비트 우쉬프트 연산결과 곱하기 4일때 `n`과 동일하면서 `n`비트 갯수가 홀수 일때 `res`변수에 `True` 대입
2. 그 외 경우 `res` 반환
3. `n`을 2비트 우쉬프트 연산
4. 위의 과정을 `n` 1 이하일때 까지 반복하고 `res` 반환

시간복잡성 : O(log n)  
공간복잡성 : O(1)

```python
class Solution:
    def isPowerOfFour(self, n: int) -> bool:
        if n == 1: return True
        res = False
        while 1 < n:
            res = False
            if (n>>2)*4 == n and len(bin(n))%2==1:
                res = True
            else:
                return res
            n >>= 2
        return res
```

**2020-12-15**

> Runtime: `28 ms`, faster than `82.04%` of Python3 online submissions for Power of Four. <br>
> Memory Usage: `14 MB`, less than `82.10%` of Python3 online submissions for Power of Four. <br>



# [1678. Goal Parser Interpretation](https://leetcode.com/problems/goal-parser-interpretation/)

1. `command`문자열에서 `()` -> `o`, `(al)` -> `al` 문자열 변환하여 반환

```python
class Solution:
    def interpret(self, command: str) -> str:
        return command.replace('()', 'o').replace('(al)', 'al')
```

**2020-12-11**

> Runtime: `28 ms`, faster than `89.89%` of Python3 online submissions for Goal Parser Interpretation. <br>
> Memory Usage: `13.9 MB`, less than `100.00%` of Python3 online submissions for Goal Parser Interpretation. <br>


