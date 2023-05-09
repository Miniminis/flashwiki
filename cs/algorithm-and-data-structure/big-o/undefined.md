---
description: 아래 코드들의 시간복잡도를 구한다.
---

# 빅오표기법의 문제풀이

<figure><img src="../../../.gitbook/assets/image (20) (1).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

* 결국 a가 될때까지 b를 몇번 더할 것인지 묻는 문제이다. 아래와 같은 식이 성립한다.&#x20;
  * b\*n = a&#x20;
  * n = a/b
* 따라서 시간복잡도는 O(a/b) 이 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (21) (3).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

* g가 루트 n이 될 때까지 반복문을 수행하고 있다.&#x20;
* 따라서 시간복잡도는 O(sqrt(n)) 가 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (1) (7).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

* 만약 이진탐색트리가 균형이 맞지 않는다면, 최악의 경우, 얼마만큼의 시간복잡도를 갖게 되는가?&#x20;
* 이진탐색트리의 경우, 최대 2개의 자식을 가진 노드가 층을 이루는 구조이다. 그런데 이때, 각 노드가 각각 하나의 자식을 가진채로 길게 한쪽으로만 늘어서 있다면, 결국 시간복잡도는 노드의 개수만큼 늘어나게 된다.&#x20;
* 따라서 시간복잡도는 O(n) 이 된다. &#x20;

<figure><img src="../../../.gitbook/assets/image (10) (2).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

* 이진탐색트리가 아닐 경우, 트리 내의 특정 요소를 찾는데 걸리는 시간은 얼마일까?&#x20;
* 이진탐색트리가 아닌 경우, 최악의 경우, 특정 요소를 찾기 위해서는 모든 노드를 스캔해야할 수 있다.&#x20;
* 따라서 시간복잡도는 O(n)이 된다. &#x20;

<figure><img src="../../../.gitbook/assets/image (44) (2).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (17) (1) (2).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

* 배열을 복사하는 함수가 있다. 복사할 배열이 매개변수로 전달되면, 길이가 0인 배열을 초기화하고, 복사할 배열을 순회하며 매번 기준 배열에 길이를 1씩 증가한 배열을 새롭게 정의하여 요소를 추가해준다. 이 경우, 시간복잡도는 얼마이겠는가 &#x20;
* array 길이만큼 순회를 한다 -> n&#x20;
* 매번 길이가 n+1인 배열을 새롭게 정의하여 옮겨담는 과정을 거친다 -> n&#x20;
* 따라서 이 알고리즘의 시간복잡도는 O(n^2)이 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (30) (1) (1).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

* 매 로직의 실행마다 n의 값이 10분의 1씩 줄어든다.&#x20;
* 따라서 이 로직의 시간복잡도는 O(logn)이 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (3) (6).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

* mergesort, quicksort는 O(nlogn)의 시간복잡도를 가지고, binarySearch() 는 O(logn) 의 시간복잡도를 가진다.&#x20;
* 위의 경우, mergesort 에서 이미 O(blogb)의 시간이 걸리고,&#x20;
* binarySearch() 에서 O(logb) 만큼의 시간이 소요되고, 이를 a번 반복하므로, 결론적으로는 아래와 같은 시간복잡도를 가지게 된다.&#x20;
* O(blogb) + O(alogb)&#x20;

