---
description: 정렬
---

# Sort

정렬은 컴퓨터가 가장 많이 하는 행위 중 하나로 꼽힐 정도로, 매우 자주 발생한다. 그리고 그만큼 다양한 종류의 정렬 알고리즘도 존재한다.&#x20;

* 간단하지만 느린 정렬&#x20;
  * bubble sort
  * insertion sort&#x20;
  * selection sort&#x20;
* 복잡하지만 빠른 정렬&#x20;
  * quick sort
  * merge sort
  * heap sort&#x20;
* O(n)
  * radix sort&#x20;



## Selection Sort

* 제일 큰 값을 선택하여 맨 뒤의 요소와 자리를 바꾸면서 큰 값부터, 뒤에서부터 차례대로 정렬하는 것&#x20;

<img src="../../../.gitbook/assets/image (22) (1).png" alt="[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님" data-size="original">

<figure><img src="../../../.gitbook/assets/image (18) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 시간복잡도&#x20;
  * 우선 for 문이 n-1 번 반복되고&#x20;
  * 매번 가장 큰수를 찾느라 n-1, n-2, n-3 ... 번 탐색시간이 소요된다.&#x20;
  * 가장 큰 값과 맨 마지막 요소를 교환하는 것 자체는 상수시간이 소요된다.&#x20;
  * 결국, (n-1) + (n-2) + ... + (2) + 1 = `O(n^2)`
  * 최악의 경우나, 최선의 경우나 모두 같은 시간 복잡도를 가진다.&#x20;



## Bubble Sort&#x20;

* selection sort 와 비슷하게 가장 큰 값을 찾아, 제일 마지막 값으로 옮긴 뒤, 잊어버리고 나머지 값들을 대상으로 반복 작업을 하는 방식이지만,&#x20;
* 가장 큰 값을 맨 마지막 요소와 바꾸는 방법에서 차이가 존재한다.&#x20;
* 마치 그물로 고기를 몰아 잡는 것과 비슷하다고 한다. 잔챙이들은 그물을 빠져나가지만, 큰 물고기들은 빠져나갈 수 없다!&#x20;

<figure><img src="../../../.gitbook/assets/image (4) (1) (6) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (28) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 시간복잡도&#x20;
  * for 루프는 n-1 번 반복되고&#x20;
  * 각각 n-1, n-2, n-3, ... 1번 반복된다.&#x20;
  * 교환 자체는 역시 상수시간이다.&#x20;
  * 따라서 n(n-1)/2 -> O(n^2) 가 된다.&#x20;



## Insertion Sort

* 제일 최초의 데이터는 이미 정렬된 상태로 본다.&#x20;
* 두번째 데이터를 넣어줄 때부터 정렬을 신경쓰면서 삽입한다.&#x20;
* 삽입 위치를 찾는 방법&#x20;
  * 뒤에서부터 검사한다면, 앞에 정렬된 데이터를 모두 검사하지 않아도 된다. 따라서 뒤에서부터 검사한다면, 보다 효율적으로 삽입 위치를 찾을 수 있다.&#x20;
  * 내가 insert 할 값을 임시 변수인 temp 에 저장한다.&#x20;
  * 이 temp 값을 정렬된 리스트의 맨 마지막 값부터 차례대로 비교한다. 값이 temp보다 크면 뒤로 shift, temp보다 작다면, 해당 index 바로 뒤로 삽입한다.&#x20;

<figure><img src="../../../.gitbook/assets/image (6) (1) (4) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (16) (1).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 시간복잡도&#x20;
  * for 반복 : n-1&#x20;
  * insert 를 위해 필요한 비교 연산 : 최선의 경우 1번, 최악의 경우 n(n-1)/2 가 소요된다.&#x20;
  * 결국 시간복잡도는 O(n^2) 가 된다. 비록 다른 selection sort, bubble sort 와 시간 복잡도가 같아보이지만, insertion 정렬의 경우는 이 시간복잡도가 최악의 경우를 의미한다. 따라서 실제로는 이것의 거의 절반정도로 보면 되고, 확실하게 selection, bubble sort 보다는 빠르다는 것을 보장한다.&#x20;

