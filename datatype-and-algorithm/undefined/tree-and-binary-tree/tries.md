---
description: 트라이
---

# Tries

* 트라이는 문자열에서 검색을 빠르게 해주는 트리구조를 말한다.&#x20;
* 보통 정수형 이진트리에서 숫자를 검색할 경우, O(logn)의 시간복잡도를 가진다.&#x20;
* 하지만 문자열의 경우는 다르다. 노드 내부에 문자열 각각을 다시 순회해야하므로, 문자열의 길이가 M이라고 했을 때, 최대 O(MlogN)의 시간복잡도를 갖게 된다.&#x20;
* 이러한 문자열 검색을 좀 더 빠르고 효율적으로 할 방법이 없을까? 하던 고민에서 나온 것이 바로 `트라이`이다.&#x20;

<figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (8).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

* 트라이에서 문자열 검색을 하게 되면, 최대 문자열의 길이 M정도의 시간복잡도밖에 걸리지 않는다. -> O(M)&#x20;
