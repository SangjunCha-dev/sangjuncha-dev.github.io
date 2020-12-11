---
title: LeetCode Python (Easy) 풀이
author: Sang Jun
date: 2020-12-01 17:30:00 +0800
categories: [Algorithm, LeetCode]
tags: [algorithm, leetcode]
pin: false
---

# [1. Two Sum](https://leetcode.com/problems/two-sum/)

target 값을 nums 리스트

Given an array of integers `nums` and an integer `target`, return *indices of the two numbers such that they add up to `target`*.
You may assume that each input would have ***exactly* one solution**, and you may not use the *same* element twice.
You can return the answer in any order.



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

> Runtime: `40 ms`, faster than `97.02%` of Python3 online submissions for Reverse Integer. <br>
> Memory Usage: `14.5 MB`, less than `89.48%` of Python3 online submisstions for Reverse Integer. <br>



# [7. Reverse Integer](https://leetcode.com/problems/reverse-integer/)

Given a 32-bit signed integer, reverse digits of an integer.

**Note:**

Assume we are dealing with an environment that could only store integers within the 32-bit signed integer range: [−231,  231 − 1]. 
For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.



```python
class Solution:
    def reverse(self, x: int) -> int:
        testCase = list(str(x))
        sign = None
        result = ''
        
        if testCase[0] == '-':
            sign = testCase.pop(0)
            
        testCaseLen = len(testCase)
        for i, _ in enumerate(testCase):
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



# [14. Longest Common Prefix](https://leetcode.com/problems/longest-common-prefix/)

Write a function to find the longest common prefix string amongst an array of strings.
If there is no common prefix, return an empty string `""`.


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

> Runtime: `28 ms`, faster than `90.77%` of Python3 online submissions for Reverse Integer. <br>
> Memory Usage: `14.3 MB`, less than `47.57%` of Python3 online submisstions for Reverse Integer. <br>



# [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)

문자열 `s` 입력받아 `(`, `)`, `{`, `}`, `[`, `]` 입력 문자열이 유효한 경우

- 열린 괄호는 동일한 유형의 괄호로 닫음
- 반환값은 `True`, `False` 두가지로 반환
<br>



열린 괄호는 list자료형에 스택을 쌓고 닫는 괄호는 스택의 마지막값과 비교해서 올바르면 스택에서 값 제거

최종적으로 list 길이가 0일때 `True` 반환 

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

> Runtime: `16 ms`, faster than `99.83%` of Python3 online submissions for Reverse Integer. <br>
> Memory Usage: `14.3 MB`, less than `24.08%` of Python3 online submisstions for Reverse Integer. <br>



# [35. Search Insert Position](https://leetcode.com/problems/search-insert-position/)

Given a sorted array of distinct integers and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.



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

> Runtime: `40 ms`, faster than `96.18%` of Python3 online submissions for Reverse Integer. <br> 
> Memory Usage: `15.1 MB`, less than `9.31%` of Python3 online submisstions for Reverse Integer. <br>



# [38. Count and Say](https://leetcode.com/problems/count-and-say/)

- 1 <= n <= 30
<br>


1. 비교 문자열 `1` 선언
2. 아래의 알고리즘을 입력 받은 `n`번 반복 실행
3. 문자 하나씩 비교하여 같으면 count 1증가
4. 문자가 다르면 `resultText` 문자열에 `count`값과 리스트`[i-1]`번째 문자를 저장
5. 안쪽 for문 종료 후 `numText` 마지막 [-1]문자열 값 대입


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

> Runtime: `40 ms`, faster than `70.73%` of Python3 online submissions for Reverse Integer. <br>
> Memory Usage: `14.3 MB`, less than `48.22%` of Python3 online submisstions for Reverse Integer. <br>



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

> Runtime: `1580 ms,`, faster than `5.26%` of Python3 online submissions for Reverse Integer. <br>
> Memory Usage: `15.2 MB`, less than `5.01%` of Python3 online submisstions for Reverse Integer. <br>



# [58. Length of Last Word](https://leetcode.com/problems/length-of-last-word/)


1. 

```python
class Solution:
    def lengthOfLastWord(self, s: str) -> int:
        return len(s.strip().split(' ')[-1])
```

**2020-12-09**

> Runtime: `16 ms`, faster than `99.80%` of Python3 online submissions for Length of Last Word. <br>
> Memory Usage: `14.2 MB`, less than `38.86%` of Python3 online submissions for Length of Last Word. <br>



# [66. Plus One](https://leetcode.com/problems/plus-one/)

음이 아닌 정수 0~9까지의 십진수 배열을 입력받아 `정수 1 증가`

- 각 요소는 `단일 숫자`로 반환
<br>


1. list의 마지막값 1증가
2. 반복문을 통해 역순으로 값 검증
3. 모든요소가 단일숫자 구성시 list return
4. list 0번째 값이 10으로 나눠질경우 첫번째 [1] 리스트 추가하여 반환

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

> Runtime: `24 ms`, faster than `95.84%` of Python3 online submissions for Reverse Integer. <br>
> Memory Usage: `14.2 MB`, less than `38.47%` of Python3 online submisstions for Reverse Integer. <br>



# [136. Single Number](https://leetcode.com/problems/single-number/)

비어있지 않은 정수 배열 `nums` 중 하나를 제외하고 두번 포함일때 나머지 하나 찾기
- nums 리스트를 추가할당하지 않은상태에서 시간복잡성 O(n)으로 해결
<br>


1. nums 리스트 정렬
2. index(0,2,4,...) 2씩 증가하는 for 반복문실행
3. list `index의 값과 index+1의 값을 비교`하여 다를경우 list index의 값 리턴
4. 리스트 마지막까지 도달할경우 list[-1] 리턴

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

> Runtime: `124 ms`, faster than `85.60%` of Python3 online submissions for Reverse Integer. <br>
> Memory Usage: `16.6 MB`, less than `60.41%` of Python3 online submisstions for Reverse Integer. <br>



# [167. Two Sum II - Input array is sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

1. 리스트 첫번째 인덱스 = `low`, 마지막 인덱스 = `high` 선언
2. `while`문 `low`값이 `high`값보다 작을때 반복실행
3. `리스트[low]` + `리스트[high]` 값이 `target`보다 작으면 `low` 1 증가
4. `리스트[low]` + `리스트[high]` 값이 `target`보다 크면 `high` 1 감소
5. `리스트[low]` + `리스트[high]` 값이 `target` 같은 값이면 `low`, `high` return 

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

1. 

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

1. 아리스토텔레스의 체를 이용하여 소수가 아닌값을 거르는 알고리즘

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

> Runtime: `392 ms`, faster than `80.27%` of Python3 online submissions for Count Primes.  
> Memory Usage: `25.7 MB`, less than `55.70%` of Python3 online submissions for Count Primes.



