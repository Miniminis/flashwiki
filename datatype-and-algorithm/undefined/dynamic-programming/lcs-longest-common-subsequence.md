# LCS, Longest Common Subsequence

##

## LCS란?

* \<bcdb> 는 문자열 \<abcbdab>의 subsequence 이다.&#x20;
* \<bca>는 문자열 \<abcbdab>와 \<dbcaba>의 common subsequence 이다. &#x20;
* **Longest Common Subsequence 는 common sequence 들 중에서 가장 긴 것을 의미한다.**&#x20;
* \<bcba>는 \<abcbdab>와 \<bdcba> 의 LCS 이다.&#x20;



## 순환식

<figure><img src="../../../.gitbook/assets/image (24).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/unit/4129">https://www.inflearn.com/course/알고리즘-강좌/unit/4129</a></p></figcaption></figure>

* X의 맨 마지막 글자와 Y의 맨 마지막 글자가 같다면, 그 문자 i와 j 바로 직전인 i-1, j-1까지의 가장 긴 LCS 값과 맨마지막 값의 길이인 1을 더해준 값이 가장 긴 문자열이 된다.&#x20;
* 만약 X의 맨 마지막 글자와 Y의 맨 마지막 글자가 다르다면, 둘 중 하나는 반드시 버려야 한다. 버리는 기준은 각각 맨 마지막 글자를 버렸을 때의 LCS 를 구한 뒤, 더 긴 값을 선택해주면 된다.&#x20;
* 이 순환식의 base case 는 i 나 j가 0인 경우인데, 각 문자열이 empty 임을 의미한다.&#x20;
* general case 에서 순환식을 돌 때, 점차 검사하는 문자열의 길이가 줄어들기 때문에 base case 에 수렴하게 된다는 것을 알 수 있다. &#x20;

## 계산순서

* 위의 순환식에 따르면, (i, j) 값을 알기 위해서는 (i-1, j-1), (i-1, j), (i, j-1) 의 값을 알아야 한다.&#x20;
* 이를 알기 위한 계산 순서는 가장 간단하게 행 우선 계산방법이다.&#x20;

<figure><img src="../../../.gitbook/assets/image (33).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/unit/4129">https://www.inflearn.com/course/알고리즘-강좌/unit/4129</a></p></figcaption></figure>

## 코드

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EA%B0%95%EC%A2%8C/unit/4129">https://www.inflearn.com/course/알고리즘-강좌/unit/4129</a></p></figcaption></figure>

* i와 j가 각각 0일 때는 LCS 가 존재하지 않으므로 0이 된다.&#x20;

