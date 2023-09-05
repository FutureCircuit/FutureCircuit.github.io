---
layout: post
title: 2035. First Letter to Appear Twice
subtitle: 처음으로 두 번 나오는 문자
categories: [LeetCode Problems]
tags: [LeetCode, C++]
---

## 문제 설명
<span style="color:green"><b>난이도: 쉬움</b></span> <b>[(문제 링크)](https://leetcode.com/problems/first-letter-to-appear-twice/)</b>

영어 소문자로 이루어진 문자열 `s`에서, *처음으로 **두 번** 나오는 문자*를 리턴하라.

**주의**:
  * 만약 문자 `a`의 **두 번째** 등장이 다른 문자 `b`의 **두 번째** 등장 이전이라면, 문자 `a`가 문자 `b`보다 먼저 두 번 나오는 것이다.
  * `s`는 두 번 나오는 문자를 최소 하나 포함할 것이다.

**예시 1:**<br>
**입력:** `s = "abccbaacz"`<br>
**출력:** `"c"`<br>
**해설:**<br>
문자 'a'는 0, 5, 6번 인덱스에 있다.<br>
문자 'b'는 1, 4번 인덱스에 있다.<br>
문자 'c'는 2, 3, 7번 인덱스에 있다.<br>
문자 'z'는 8번 인덱스에 있다.<br>
문자 'c'의 두 번째로 등장하는 인덱스가 모든 문자 중에서 가장 작으므로 처음으로 두 번 나오는 문자이다.

**예시 2:**<br>
**입력:** `s = "abcdd"`<br>
**출력:** `"d"`<br>
**해설:**<br>
두 번 나오는 문자는 'd' 뿐이므로 'd'를 리턴한다.

**제약조건:**
  * `2 <= s.length <= 100`
  * `s`는 영어 소문자로 이루어져 있다.
  * `s`는 반복되는 문자를 최소 하나는 가진다.

## 풀이
이번 문제는 풀이법을 떠올리는 것이 그리 어렵지 않다. 문자열 앞에서부터 나오는 문자를 하나씩 저장해 뒀다가, 특정 문자가 2개 저장되면 그 문자를 바로 리턴하면 된다.

나는 처음에 이 문제를 푸는 데 정적 배열을 냅다 선언해서 풀었었다. 주어진 문자열은 영어 소문자 26자로 구성되어 있으므로, `arr[26]`을 선언한 다음에 거기다 문자별 등장횟수를 저장해서 푸는 식이었다.

```C++
class Solution {
public:
    char repeatedCharacter(string s) {
        int arr[26];

        for(char c : s) {
            arr[c - 'a']++;
            if(arr[c - 'a'] == 2) {
                return c;
            }
        }

        return 0;
    }
};
```

뭐 이렇게 풀어도 퍼포먼스는 그럭저럭 잘 나왔다. 하지만 이건 문자 종류가 좀만 많아져도 보기 지저분해진다. 만약 문자열이 유니코드로 이루어져 있었다면, `arr[65536]`이라고 선언된 배열의 모습이 결코 깔끔해 보이진 않을 것이다. 거기다 C++은 STL이 있는 만큼, 가능하면 배열보단 STL의 사용이 생산성 측면에서 권장된다.

그렇게 난 배열로 한번 푼 다음 다른 사람의 풀이를 보다 C++ STL에 unordered_map이 있다는 것을 알아차렸다. 이름을 보면 binary tree로 구현한 map 자료구조와 비슷하지만, unordered_map은 hash table로 구현한 자료구조이며, 중복 데이터를 허용하지 않는다. map의 시간 복잡도는 \\(O(log n)\\) 이고, unordered_map의 시간 복잡도는 \\(O(1)\\) 로, unordered_map이 데이터가 많을 때 월등히 성능이 좋음을 알 수 있다.

```C++
class Solution {
public:
    char repeatedCharacter(string s) {
        unordered_map<char, int> um;

        for(char c : s) {
            um[c]++;
            if(um[c] == 2) {
                return c;
            }
        }

        return 0;
    }
};
```

그리하여 unordered_map을 활용하여 코드를 다시 작성하였다. 배열로 작성된 코드에 비해 확실히 깔끔한 게 느껴진다.