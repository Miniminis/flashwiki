# comparison sort 에서 최상의 시간복잡도

## 시간복잡도&#x20;

지금까지 많은 정렬 기법을 다루었다. 시간복잡도를 각각 비교해보자.&#x20;

* O(n^2)
  * bubble, selection, insertion
  * quick : 평균적으로는 O(nlogn), 최악의 경우, O(n^2)
* O(nlogn)
  * merge
  * heap&#x20;

{% hint style="success" %}
여기서 드는 의문!&#x20;

과연 O(nlogn) 이 최선인가? 더 나은 방법은 없는가?&#x20;

\-> 결론 : O(nlogn)이 최선이다. 이보다 더 나은 시간복잡도를 가지는 정렬 알고리즘은 없다. (단, comparison sort 인 경우에 한해서)&#x20;
{% endhint %}



## Comparison Sort&#x20;

* 데이터들 간의 상대적 크기 관계만을 이용해서 정렬하는 알고리즘을 말한다.&#x20;
* 따라서 데이터들간의 크기 관계가 정의되어있으면, 어떤 데이터에든 적용이 가능하다.&#x20;
  * 문자열(사전순), 알파벳(알파벳순), 사용자 정의 객체 등
* 버블소트, 삽입정렬, 합병정렬, 큇소트, 힙정렬 등&#x20;
* non-comparison sort
  * 정렬할 데이터에 대한 사전지식을 이용한다.&#x20;
    * 적용에 제한이 있다.
  * bucket sort&#x20;
    * 예) 시험 점수 순으로 정렬을 할 때, 100점을 제외하고는 90점대, 80점대, 70점대 ... 순으로 먼저 정렬을 하고&#x20;
    * 이후에 90점대 내에서 높은 순으로 정렬을 할 것이다.&#x20;
    * 즉, 먼저 큰 분류를 기준으로 바구니에 나누어 담고, 그 내부에서 다시 정렬을 하는 방법이 있다.  &#x20;
  * radix sort&#x20;



## 정렬 문제의 하한 (lower bound)

* 아래의 명제를 증명해내고자 한다.&#x20;
* 입력된 데이터를 한번식 다 보기 위해서 최소 O(n)의 시간복잡도는 필요하다.&#x20;
* 합병정렬과 힙정렬 알고리즘의 시간복잡도는 O(nlogn)이다.&#x20;
* 결론적으로 어떤 comparison sort 에 대해서도 그 시간복잡도가 O(nlogn)보다 낮을 수는 없다.

## decision tree

이미 comparison sort 알고리즘이 하나 있다고 가정해보자.&#x20;

<figure><img src="../../../.gitbook/assets/image (30).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C">[인프런] 영리한 프로그래밍을 위한 알고리즘 강좌 by 권오흠 교수님</a></p></figcaption></figure>

* 3개의 값을 정렬하는 insertion sort 알고리즘에 대해서 발생할 수 있는 모든 경우의 수를 나타내는 decision tree 이다.&#x20;
  * insertion sort 뿐만아니라 다른 정렬 알고리즘도 마찬가지로 적용된다.&#x20;
* 위의 트리에서 leaf 노드들은 정렬의 결과이고 그 개수는 3! = 6개가 된다.&#x20;
* 즉, 데이터의 개수 n개에 대하여 정렬의 결과는 n! 개가 나올 수 있다. 왜냐하면 모든 순열에 해당하므로!&#x20;
* 최악의 경우, 정렬의 시간복잡도는 위의 decision tree 의 높이에 비례한다.&#x20;
* 그렇다면 트리의 높이는 얼마인가?&#x20;
  * height >= logn! = o(nlogn)&#x20;
    * starling의 이론에 의해 logn! -> nlogn&#x20;



