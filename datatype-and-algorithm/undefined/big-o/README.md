---
description: 빅오표기법
---

# Big-O

## 표기법

* 알고리즘의 성능을 수학적으로 표현해주는 표기법이다.&#x20;
*  알고리즘의 시간과 공간 복잡도를 표현할 수 있다.&#x20;
* 알고리즘의 실제 러닝타임을 표시하는 것이라기보다는 데이터나 사용자의 증가율에 따른 알고리즘의 성능을 예측하는 것이 목표&#x20;
  * 상수와 같은 숫자들은 모두 1로 간주된다.&#x20;

## O(1)&#x20;

* 입력 데이터의 크기에 상관없이 언제나 일정한 시간이 걸리는 알고리즘을 말한다.&#x20;
  * 예) 입력값에 상관없이 특정 수를 반환하는 알고리즘&#x20;

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

## O(n)

* 입력데이터의 크기에 비례하여 수행되는 알고리즘&#x20;
  * 예를 들면, 배열의 길이만큼 순회하는 로직 &#x20;

<figure><img src="../../../.gitbook/assets/image (46).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

## O(n^2)&#x20;

* n을 중복해서 돌리는 것&#x20;
* 데이터가 커질수록 처리 시간의 부담도 늘어나게 된다.&#x20;

<figure><img src="../../../.gitbook/assets/image (43).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>



## O(nm)

* n번 반복하면서 매번 m번 반복하는 구조

<figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>



## O(n^3)&#x20;

<figure><img src="../../../.gitbook/assets/image (36).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (28).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

## O(2^n), O(m^n)&#x20;

* n^3보다도 많은 시간복잡도를 자랑한다.&#x20;
* 대표적인 예 : 피보나치

<figure><img src="../../../.gitbook/assets/image (24).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (42).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (45).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>



## O(logn)&#x20;

* 한번 연산할 때마다 연산해야할 데이터가 절반씩 줄어드는 알고리즘의 시간복잡도&#x20;
  * 대표적인 예) binary search
* 시간이 지날수록, 데이터가 늘어날수록 속도에 큰 변화가 없다.&#x20;

<figure><img src="../../../.gitbook/assets/image (13).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (47).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>



## O(sqrt(n))

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption><p><a href="https://www.youtube.com/user/damazzang/videos">[유튜브] 엔지니어 대한민국</a></p></figcaption></figure>

## 기억할 것! 상수는 과감히 버린다

* 증가하지 않는 숫자는 신경쓰지 않겠다. &#x20;
