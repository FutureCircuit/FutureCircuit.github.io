---
layout: post
title: 703. Kth Largest Element in a Stream
subtitle: 스트림에서 K번째로 큰 원소
categories: [LeetCode Problems]
tags: [LeetCode, C++]
---

## 문제 설명
<span style="color:green"><b>난이도: 쉬움</b></span> <b>[(문제 링크)](https://leetcode.com/problems/kth-largest-element-in-a-stream/)</b>

스트림에서 <code>k<sup>th</sup></code>로 큰 원소를 찾는 클래스를 설계하라. 정렬된 순서에서 <code>k<sup>th</sup></code>로 큰 원소이며, <code>k<sup>th</sup></code>로 멀리 떨어진 원소가 아님에 주의하시오.

`KthLargest` 클래스 구현:
  * `KthLargest(int k, int[] nums)`는 정수 `k`와 정수 스트림 `nums`를 가진 객체를 초기화한다.
  * `int add(int val)`는 스트림에 정수 `val`을 추가하고 스트림에서 <code>k<sup>th</sup></code>로 큰 원소를 리턴한다.

**예시 1:**<br>
**입력**<br>
`["KthLargest", "add", "add", "add", "add", "add"]`<br>
`[[3, [4, 5, 8, 2]], [3], [5], [10], [9], [4]]`<br>
**출력**<br>
`[null, 4, 5, 5, 8, 8]`

**해설**
```
KthLargest kthLargest = new KthLargest(3, [4, 5, 8, 2]);
kthLargest.add(3);      // return 4
kthLargest.add(5);      // return 5
kthLargest.add(10);     // return 5
kthLargest.add(9);      // return 8
kthLargest.add(4);      // return 8
```

**제약조건:**
  * <code>1 <= k <= 10<sup>4</sup></code>
  * <code>0 <= nums.length <= 10<sup>4</sup></code>
  * <code>-10<sup>4</sup> <= nums[i] <= 10<sup>4</sup></code>
  * <code>-10<sup>4</sup> <= val <= 10<sup>4</sup></code>
  * 최대 <code>10<sup>4</sup></code>번의 `add` 호출이 일어날 것이다.
  * <code>k<sup>th</sup></code> 원소를 찾을 때, 배열에 최소 `k`개의 원소가 있음을 보장한다.

## 풀이
배열에서 가장 큰 원소를 찾은 적은 많지만, k번째로 큰 원소를 찾는다는 걸 생각해 본 적은 이번이 처음인 것 같다. 배열의 최댓값은 `<algorithm>` 헤더에 선언되어 있는 `std::max` 함수를 사용하면 되지만, k번째로 큰 원소는 어떻게 찾을 수 있을까?

일단 생각해 볼 수 있는 방법은 배열을 오름차순(또는 내림차순)으로 정렬하는 것이 있겠다. 그러면 원소들이 크기 순서대로 정렬되므로 k번째로 큰 원소를 찾기 용이해진다.

그러면 이를 바탕으로 코드를 작성해 보자. `k`와 벡터 `nums`를 저장할 클래스 변수를 선언하고, 생성자에서 이들을 저장하도록 한다. 그리고 `int add(int val)` 함수에서는 `val`을 벡터에 넣은 뒤, `std::sort` 함수로 정렬하고 `k`번째로 큰 원소를 리턴하도록 한다.

```C++
class KthLargest {
    int k;
    vector<int> v;
public:
    KthLargest(int k, vector<int>& nums) {
        this->k = k;
        for(int i : nums) { v.push_back(i); }
    }
    
    int add(int val) {
        v.push_back(val);
        sort(v.begin(), v.end());

        return v[v.size() - k];
    }
};
```

코드는 오류 없이 잘 작동한다. 하지만 이대로 제출하면 시간이 초과됐다는 메시지가 출력될 것이다. 즉, 이 코드는 속도가 느리다.

코드를 분석해 보자면, 이 코드에 쓰인 `std::sort`는 intro sort 알고리즘을 사용하며, 시간 복잡도는 \\(O(n\log{}{n})\\)이다. 앞서 제약조건을 보면 최대 10<sup>4</sup> = 10000회의 `add` 함수 호출이 있을 것이라 했는데, 아무래도 \\(O(n\log{}{n})\\)의 시간 복잡도로는 이러한 퍼포먼스를 감당할 수 없는 듯하다. 더 빠른 시간 복잡도를 가진 알고리즘으로 다시 짜야 할 필요가 있다.

하지만 아직 자료구조 활용이 미숙한 나로서는 도저히 생각해도 적당한 방법을 찾을 수 없어 다른 사람의 풀이에서 힌트를 얻을 수밖에 없었다. 그렇게 찾아낸 힌트는 우선순위 큐(priority queue)를 활용하는 것이었다. 우선순위 큐는 힙(heap)을 사용한 자료구조로, 따라서 시간 복잡도는 \\(O(log{}{n})\\)이다. 그리고 우선순위 큐는 우선순위가 높은 데이터(C++의 경우 가장 큰 데이터)가 먼저 나가는 특징이 있으므로, 이 문제를 해결하기에도 매우 적절해 보였다.

하지만 조금 생각해 보니 문제가 있었다. `k`번째로 큰 데이터를 리턴한다고 치면, 우선순위 큐에서 `k - 1`번 데이터를 빼낸 다음 그 우선순위 큐의 top 값을 리턴해야 한다. 그런데 `add` 함수를 한번 쓰고 끝이면 모를까, 여러 번 호출될 것이므로 빼냈던 데이터는 다시 원래대로 넣어놔야 한다. 그러면 매번 `add` 함수가 호출될 때마다 큐에 많은 데이터를 넣다 뺐다 하는 연산이 반복될 텐데, 이건 이것대로 퍼포먼스에 영향을 끼치는 문제가 생긴다.

그러면 어떻게 해야 이 '넣다 뺐다'를 덜 할 수 있을까. 아까 다른 사람의 풀이를 확인했을 때, 우선순위 큐의 선언문이 조금 특이했다.

`priority_queue<int, vector<int>, greater<int>> pq`

선언문이 긴 편인데, 찾아보니 세 번째 인자의 경우 `greater<int>`를 추가하면 오름차순으로 정렬되어, 데이터를 빼낼 때 가장 작은 데이터부터 나온다고 한다. 생략해서 `priority_queue<int>`라고 쓰거나 `less<int>`를 넣으면 내림차순으로 정렬되어 가장 큰 데이터부터 나온다.

왜 가장 작은 데이터부터 나오도록 선언했을까, 기존의 내 관점에서는 의도를 잘 알아차릴 수 없었다. 그런데 머리를 비우고 다시 생각해 보니 의도를 알아차릴 수 있었다.

큐에서 가장 큰 데이터부터 나오도록 하면, k번째로 큰 데이터를 리턴하기 위해 필연적으로 그보다 큰 데이터를 먼저 빼내야 한다. 하지만 우선순위 큐의 크기를 k로 제한하면서 가장 작은 데이터부터 나오도록 하면 반드시 k번째로 큰 데이터부터 먼저 나오게 되므로, 이러한 '넣다 뺐다' 과정을 없앨 수 있다. k번째로 큰 데이터를 구하는 데 있어서 그보다 큰 데이터와의 대소관계가 중요하지, 더 작은 데이터와의 관계는 배제해도 상관없으므로 우선순위 큐의 크기를 제한하는 것은 유효하다.

이러한 사고를 바탕으로 앞에서 작성한 코드를 우선순위 큐로 재작성해 보자.

```C++
class KthLargest {
    int k;
    priority_queue<int, vector<int>, greater<int>> pq;
public:
    KthLargest(int k, vector<int>& nums) {
        this->k = k;
        for(int i : nums) { add(i); }
    }
    
    int add(int val) {
        pq.push(val);

        if(pq.size() > k) {
            pq.pop();
        }

        return pq.top();
    }
};
```

`add` 함수를 통해 우선순위 큐에 데이터를 넣는다. 일단 데이터를 push하면 우선순위 큐의 데이터는 오름차순으로 정렬되어 저장된다. 그리고 여기서 크기가 `k`를 초과하지 않도록 데이터를 빼낸다. 그러면 `return pq.top();`을 실행할 경우 자연스럽게 `k`번째로 큰 데이터를 리턴하게 된다.

그나저나 이번 문제는 우선순위 큐의 이해도가 높았다면 손쉽게 풀 수 있었던 것 같았는데, 아직 이런 문제에 쩔쩔매는 걸 보면 앞으로도 많은 공부가 필요할 것 같다.