# Text Manipulation

## 1. head&#x20;

* 문서 내용의 앞부분만 출력
* head /etc/passwd : 앞의 10줄만 출력&#x20;
* head -n 1 /etc/passwd : 앞의 1줄만 출력&#x20;
* head -c 10 /etc/passwd : 앞의 10바이트만 출력
  * 앞에서 10글자만 출력됨&#x20;
* cat /etc/passwd | head -n 15 : cat으로 출력한 결과에서 상위 15줄만 출력&#x20;
* cat /etc/passwd | head -n -5 : cat으로 출력한 결과에서 하위 5줄은 제외하고 출력&#x20;

## 2. tail

* tail /etc/passwd -n 1 : 아래에서 1줄만 출력&#x20;
* tail /etc/passwd -n +5 : 위에서 5번째줄부터 마지막까지 출력&#x20;
* cat /etc/passwd | tail -n 15 : cat 출력 결과로부터 아래에서부터 15줄 출력&#x20;
* cat /etc/passwd | tail -n +5 : cat 출력 결과로부터 위에서 5줄부터 마지막까지 출력&#x20;
* tail -f : 파일에 추가되는 내용을 대기한다. 추가되는 내용은 append하여 출력한다.&#x20;
  * 로그를 실시간으로 모니터링할 때 자주 쓰인다.&#x20;
  * 파일이 중간에 사라지면, 출력되지 않고 멈춘다.&#x20;
* tail -F : 파일이 중간에 사라지더라도, 다시 생성하여 내용을 append 하며 출력한다.&#x20;

## 3. wc&#x20;

* 파일의 line/word/byte 를 count 값을 출력한다.
* wc /etc/passwd
  * 19 24 922 /etc/passwd
  * 19줄, 24단어, 922 바이트의 파일임을 나타낸다.&#x20;
* wc -l /etc/passwd : 라인수만 출력된다.&#x20;
  * 19 /etc/passwd
* cat /etc/passwd | wc -l : cat 해서 출력된 결과의 라인수만 출력한다.&#x20;
  * 19&#x20;
* wc -l /etc/passwd | cut -d ' ' -f 1
  * wc 해서 나온 결과 : 19 /etc/passwd
  * 공백문자열을 delimiter 로 하여 cut 한 결과에서 1번째 문자를 출력 : 19&#x20;
* wc -l /etc/passwd | awk '{ print $1 }'
  * wc 해서 나온 결과 : 19 /etc/passwd
  * awk 를 이용하여 첫번째 변수만 출력 : 19&#x20;
* wc \*.c : 확장자가 .c인 파일들의 합계 출력&#x20;

## 4. nl&#x20;

* 파일 내용을 라인 넘버와 함께 출력한다.&#x20;
* nl /etc/passwd : 파일 내용을 라인넘버와 함께 출력한다.&#x20;
* nl -ba /etc/passwd : 모든 라인에 대해서 라인 넘버링을 하여 출력한다.&#x20;
* nl -v 5 /etc/passwd : 시작할 라인번호를 -v 뒤의 숫자로 지정한다.&#x20;
  * 5부터 넘버링 시작
* nl -ba -s "===>" /etc/passwd
  * \-ba : 모든 라인에 대해서 출력하고,&#x20;
  * \-s "===>" : 라인넘버 뒤에 붙여줄 seperator 를 지정한다.&#x20;
* nl -ba -s "===>" /etc/passwd | tail
  * 위의 결과에서 밑에서 10번째 줄만 출력

## 5. sort&#x20;

* 말그대로 정렬하여 출력한다.&#x20;
* sort /etc/passwd : 알파벳 순서로 정렬&#x20;
* sort /etc/passwd -t: -k 3&#x20;
  * ":" 를 구분자로, 3번째 키값에 대해서 오름차순으로 출력&#x20;
* sort /etc/passwd -n -t: -k 3
  * ":" 를 구분자로, 3번째 키값을 숫자로 간주하여 오름차순으로 출력
* sort /etc/passwd -t: -k 3,3
  * ":" 를 구분자로 하되, 3번째부터 3번째 키를 기준으로 오름차순 출력
* sort /etc/passwd -t: -k 4,4 -k 3,3
  * ":" 를 구분자로 하되, 4번째 키를 첫번째 기준으로, 3번째 키를 두번째 기준으로 삼아 오름차순 출력
* ls -al | sort : ls -al 출력 결과를 정렬하여 출력&#x20;
* ls -al | sort -k 5n : ls -al 출력 결과를 5번째 키워드를 숫자로 간주하여 정렬하여 출력&#x20;
* ls -al | sort -k 5 -r : ls -al 출력 결과를 5번째 키워드를 숫자로 간주하여 내림차순으로 정렬하여 출력
* \-u : unique 중복된 결과에 대해서는 1개만 노출&#x20;



## 6. uniq

* 중복된 내용을 제거하고 출력한다.&#x20;
* uniq \[파일명] | nl : 파일 내에 있는 중복값을 제거하고 라인번호를 붙여 출력&#x20;
* sort \[파일명] | uniq | nl
  * 파일 내부를 이름순으로 정렬한 뒤
  * 중복을 제거하고&#x20;
  * 라인번호를 붙여 출력&#x20;
* sort \[파일명] | uniq -i | nl&#x20;
  * 파일 내부를 이름순으로 정렬한 뒤
  * 대소문자를 무시하고 중복을 제거한 뒤&#x20;
  * 라인번호를 붙여 출력한다.&#x20;
* sort \[파일명] | uniq -d | nl
  * 파일 내부를 이름순으로 정렬한 뒤
  * 중복된 항목만&#x20;
  * 라인번호를 붙여 출력한다. &#x20;
* sort \[파일명] | uniq -u | nl&#x20;
  * 파일 내부를 이름순으로 정렬한 뒤
  * 중복되지 않은 내용만&#x20;
  * 라인번호를 붙여 출력한다.&#x20;
* grep "\[keyword]" \*.c | awk -F: '{ print $1 }' | uniq&#x20;
  * \[keyword] 를 포함한 확장자 c 파일들 목록에서&#x20;
  * 확장자를 제외하고 파일명만 추려서&#x20;
  * 중복을 제거한 뒤 출력한다.  &#x20;

## 7. cut&#x20;



## 8. tr



## 9. sed



## 10. awk&#x20;





