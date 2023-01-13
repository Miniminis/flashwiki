# linux basic command

## 0. 기본명령어

* ls : list
* / : to root directory
* \~ : to home directory&#x20;
* cd : change directory
* pwd : print working directory&#x20;
* man \[command] : command 쓰는 법에 대한 manual 표시

## 1. 파일시스템

* touch \[filename] : 신규 파일 생성
* cat \[filename] : concatenate. 연결시킨다는 의미를 가지고 있음. 이름과 일치하는 파일 내용을 읽어 화면에 출력
* mv \[기존경로] \[새로운 경로] : 기존 경로에서 새로운 경로로 파일이나 디렉토리를 이동시킨다. 경로에는 옮길 파일의 이름이나 디렉토리까지 포함시켜야한다. &#x20;
* rm \[filename]  : 삭제
  * rm -r : recursive 하게 삭제. 디렉토리를 삭제할 때 사용한다.&#x20;
  * rm -rf : recursive + forcibly 하게 삭제. 디렉토리 내부의 모든 파일을 강제로 삭제한다.&#x20;
* mkdir : 디렉토리 생성
* file !s : 파일의 속성&#x20;



## 2. head

* 문서 내용의 앞부분만 출력
* head /etc/passwd : 앞의 10줄만 출력
* head -n 1 /etc/passwd : 앞의 1줄만 출력
* head -c 10 /etc/passwd : 앞의 10바이트만 출력 / 앞에서 10글자만 출력됨
* cat /etc/passwd | head -n 15 : cat으로 출력한 결과에서 상위 15줄만 출력
* cat /etc/passwd | head -n -5 : cat으로 출력한 결과에서 하위 5줄은 제외하고 출력

## 3. tail&#x20;

* tail /etc/passwd -n 1 : 아래에서 1줄만 출력
* tail /etc/passwd -n +5 : 위에서 5번째줄부터 마지막까지 출력&#x20;
* cat /etc/passwd | tail -n 15 : cat 출력 결과로부터 아래에서부터 15줄 출력
* cat /etc/passwd | tail -n +5 : cat 출력 결과로부터 위에서 5줄부터 마지막까지 출력&#x20;
* tail -f : 파일에 추가되는 내용을 대기한다.&#x20;
  * 추가되는 내용은 append하여 출력한다.
  * 로그를 실시간으로 모니터링할 때 자주 쓰인다.&#x20;
  * 파일이 중간에 사라지면, 출력되지 않고 멈춘다.
* tail -F : 파일이 중간에 사라지더라도, 다시 생성하여 내용을 append 하며 출력한다.



## 4. wc

* 파일의 line/word/byte 를 count 값을 출력한다.
* wc /etc/passwd
  * 19 24 922 /etc/passwd
  * 19줄, 24단어, 922 바이트의 파일임을 나타낸다.&#x20;
* wc -l /etc/passwd
  * 라인수만 출력된다.
  * 19 /etc/passwd
* cat /etc/passwd | wc -l : cat 해서 출력된 결과의 라인수만 출력한다.&#x20;
  * 19
* wc -l /etc/passwd | cut -d ' ' -f 1
  * 공백문자열을 delimiter 로 하여 cut 한 결과에서 1번째 문자를 출력 : 19&#x20;
  * wc 해서 나온 결과 : 19 /etc/passwd
* wc -l /etc/passwd | awk '{ print $1 }'
  * wc 해서 나온 결과 : 19 /etc/passwd
  * awk 를 이용하여 첫번째 변수만 출력 : 19&#x20;
* wc \*.c : 확장자가 .c인 파일들의 합계 출력&#x20;

## 5. nl

* 파일 내용을 라인 넘버와 함께 출력한다.&#x20;
* nl /etc/passwd : 파일 내용을 라인넘버와 함께 출력한다.&#x20;
* nl -ba /etc/passwd : 모든 라인에 대해서 라인 넘버링을 하여 출력한다.&#x20;
* nl -v 5 /etc/passwd : 시작할 라인번호를 -v 뒤의 숫자로 지정한다. 5부터 넘버링 시작
* nl -ba -s "==>" /etc/passwd : -ba
  * 모든 라인에 대해서 출력하고,
  * &#x20;\-s "===>" : 라인넘버 뒤에 붙여줄 seperator 를 지정한다.&#x20;
* nl -ba -s "==>" /etc/passwd | tail
  * 위의 결과에서 밑에서 10번째 줄만 출력

## 6. sort

* 말그대로 정렬하여 출력한다.&#x20;
* sort /etc/passwd : 알파벳 순서로 정렬
* sort /etc/passwd -t: -k 3  : ":" 를 구분자로, 3번째 키값에 대해서 오름차순으로 출력&#x20;
* sort /etc/passwd -n -t: -k 3 : ":" 를 구분자로, 3번째 키값을 숫자로 간주하여 오름차순으로 출력
* sort /etc/passwd -t: -k 3,3 : ":" 를 구분자로 하되, 3번째부터 3번째 키를 기준으로 오름차순 출력
* sort /etc/passwd -t: -k 4,4 -k 3,3 : ":" 를 구분자로 하되, 4번째 키를 첫번째 기준으로, 3번째 키를 두번째 기준으로 삼아 오름차순 출력
* ls -al | sort : ls -al 출력 결과를 정렬하여 출력&#x20;
* ls -al | sort -k 5n : ls -al 출력 결과를 5번째 키워드를 숫자로 간주하여 정렬하여 출력
* ls -al | sort -k 5 -r : ls -al 출력 결과를 5번째 키워드를 숫자로 간주하여 내림차순으로 정렬하여 출력
* sort -u : unique 중복된 결과에 대해서는 1개만 노출&#x20;

## 7. uniq

* 중복된 내용을 제거하고 출력한다.&#x20;
* uniq \[파일명] | nl : 파일 내에 있는 중복값을 제거하고 라인번호를 붙여 출력&#x20;
* sort \[파일명] | uniq | nl
  * 파일 내부를 이름순으로 정렬한 뒤&#x20;
  * 중복을 제거하고&#x20;
  * 라인번호를 붙여 출력
* sort \[파일명] | uniq -i | nl&#x20;
  * 파일 내부를 이름순으로 정렬한 뒤
  * 대소문자를 무시하고 중복을 제거한 뒤&#x20;
  * 라인번호를 붙여 출력한다.
* sort \[파일명] | uniq -d | nl
  * 파일 내부를 이름순으로 정렬한 뒤
  * 중복된 항목만&#x20;
  * 라인번호를 붙여 출력한다.&#x20;
* sort \[파일명] | uniq -u | nl&#x20;
  * 파일 내부를 이름순으로 정렬한 뒤
  * 중복되지 않은 내용만&#x20;
  * 라인번호를 붙여 출력한다.
* grep "\[keyword]" \*.c | awk -F: '{ print $1 }' | uniq
  * \[keyword] 를 포함한 확장자 c 파일들 목록에서&#x20;
  * 확장자를 제외하고 파일명만 추려서&#x20;
  * 중복을 제거한 뒤 출력한다.

## 8. cut

* head /etc/passwd | cut -d ':' -f 1,7
  * \-d : tab 대신 사용할 delimiter 지정&#x20;
  * \-f : 필드(컬럼) 선택
* head /etc/passwd | cut -d ':' -f 1,7 --output-delimiter='==>'
  * \--output-delimiter=STRING : 출력시 사용할 구분자 지정
* ls -al | cut -b -10
  * ls -al 결과를 10바이트까지 출력&#x20;
* ls -al | cut -b -10 --complement
  * 위의 결과와 반대로 출력&#x20;
* ls -al | cut -c 10
  * 10번째 글자만 출력
* ls -al | cut -c 1-15
  * 1부터 15번째 글자까지 출력&#x20;

## 9. tr

* translate. 어떤 내용을 변환한다.&#x20;
* head /etc/passwd | tr ':' '^‘
  * 모든 : 을 ^으로 변환한다.&#x20;
* head /etc/passwd | tr \[:lower:] \[:upper:]
  * 모든 소문자를 대문자로 변환한다.&#x20;
* head /etc/passwd | tr -d ':'
  * 모든 : 을 지운다.&#x20;
* head /etc/passwd | tr ':' '\n'
  * 모든 :을 개행문자로 변환한다.&#x20;
* ls -al | tr -d \[:blank:]
  * 모든 공백문자열을 지운다.&#x20;
* ls -al | tr -d \[:space:]
  * 모든 공백문자열+개행을 지운다.&#x20;
* ls -al | tr -s ' '
  * 반복되는 공백을 지운다.&#x20;

## 10. sed

* stream editor
  * \- {RANGE}p : range 내에서 print&#x20;
  * \- {RANGE}d : range 내에서 삭제 delete&#x20;
  * \- /SEARCHPATTERN/p : print SEARCHPATTERN과 매치되는 라인을 출력
  * \- /SEARCHPATTERN/d : SEARCHPATTERN 과 매치되는 라인을 삭제&#x20;
  * \- s/REGEX/REPLACE/ : REGEX에 매치되는 부분을 REPLACE로 교체(substitute)
* sed -n
  * sed 자체로 원래 출력되는 내용을 출력하지 않음
* head /etc/passwd | sed '1,3p'
  * 1라인부터 3라인까지 출력한다.
  * 기존에 head 명령어로 인해 출력예정이었던 원래 10개의 리스트에 1라인부터 3라인가지 한번씩 더 출력된다.
* head /etc/passwd | sed -n '1,3p'
  * 1라인부터 3라인까지 출력한다.
  * 기존에 head 명령어로 인해 출력예정이었던 원래 10개의 리스트는 출력하지않고 1라인부터 3라인 까지만 출력된다.
* head /etc/passwd | sed -n '/nologin/p'
  * `nologin`  단어를 포함한 라인만 출력한다.&#x20;
* head /etc/passwd | sed -n '/nologin/d'
  * `nologin`  단어를 포함한 라인을 삭제한 뒤 출력한다. &#x20;
* head /etc/passwd | sed -n '/bin:/p'
  * `bin:` 가 포함된 라인만 출력한다.&#x20;
* head /etc/passwd | sed '/bin:/d'
  * `bin:` 단어가 포함된 라인을 삭제하고 출력한다.&#x20;
* head /etc/passwd | sed 's/root/ROOT/'
  * &#x20;제일 처음 등장하는 root에 대하여 ROOT 로 교체한다.&#x20;
* head /etc/passwd | sed 's/root/ROOT/g'
  * &#x20;모든 root에 대하여 ROOT 로 교체한다.&#x20;
* head /etc/passwd | sed '3,5 s/:/==>/g'
  * 3라인부터 5라인까지의 범위 중에서 모든(g) :을 => 로 교체한다.&#x20;
* head /etc/passwd | sed -n '/games/,+2p'
  * \-n : 전체를 출력하지않고, 뒤의 조건에 맞는 경우만 출력&#x20;
  * games 키워드를 가진 라인부터 그 이후의 2개의 라인까지 출력한다.&#x20;

## 11. awk

* 텍스트 처리 script language
  * \- -F : file seperator 지정&#x20;
  * \- $1, $2, $3 .. : Nth field
  * \- NR : number of records
  * \- NF : number of fields
  * \- FS : field seperator (default : white space)&#x20;
  * \- RS : record seperator (default : new line)&#x20;
  * \- OFS : output field seperator&#x20;
  * \- ORS : output record seperator&#x20;
* head /etc/passwd | awk '/nologin/ {print}'
  * "nologin" 단어를 포함하는 라인을 출력한다.&#x20;
* head /etc/passwd | awk -F: '/nologin/ {print $1, $7 }'
  * seperator : 을 기준으로 "nologin" 단어를 포함하는 라인에 대하여 첫번째 필드와 7번째 필드를 각각 출력한다.&#x20;
* head /etc/passwd | awk -F: '/nologin/ {print NR, $1 }'
  * seperator : 을 기준으로 "nologin" 단어를 포함하는 라인에 대하여 라인 번호와 첫번째 필드를 출력
* head /etc/passwd | awk -F: '/nologin/ {print $1, NF }'
  * seperator : 을 기준으로 "nologin" 단어를 포함하는 라인 중에서 1번째 필드와 그 라인의 총 필드 수를 출력&#x20;
* head /etc/passwd | awk -F: '/nologin/ {print $1 "->" $NF }'
  * seperator : 을 기준으로 "nologin" 단어를 포함하는 라인 중에서 "1번째 필드 -> 가장 마지막 필드"를 출력&#x20;

## 12. find

* 검색
* 옵션
  * \-name, -regex, -empty, -size(-N, +N)
  * 타입
    * \-type d : directory
    * \-type p : named pipe&#x20;
    * \-type f : regular file&#x20;
    * \-type l : softlink
    * \-type s : socket
  * 권한
    * \-perm mode : 정확히 일치하는 파일
    * \-perm +mode : 모든 flag가 포함된 파일
    * \-perm /mode : 어떤 flag라도 포함된 파일
  * 명령어&#x20;
    * \-delete : 파일 삭제
    * \-ls : ls –dils 명령 수행
    * \-print : 파일 이름 출력
    * \-printf : 파일 이름을 포맷에 맞게 출력
    * \-exec : 주어진 명령 수행
    * \-execdir : 해당 디렉터리로 이동하여 명령 실행&#x20;
    * \-ok : 사용자에게 확인 후 exec
    * \-okdir : 사용자에게 확인 후 실행 execdir
* find . -name "\*.py“
  * 현재 디렉토리에서  파일이름 중  .py 를 포함한 파일을 모두 찾는다.
* find `pwd` -name “\*.py”
  * 현재 디렉토리에서  파일이름 중  .py 를 포함한 파일을 모두 찾는다.&#x20;
* find . -regextype egrep -regex '.\*_hash.\*_.c$'
  * hash 단어를 포함하고, 확장자가 .c 로 끝나는 파일을 찾는다.&#x20;
* find . -empty
  * 현재 디렉토리에서 빈 파일을 모두 찾는다.&#x20;
* find . -type d
  * 현재 디렉토리에서 모든 디렉토리를 찾는다.&#x20;
* find . -type f
  * 현재 디렉토리에서 모든 파일을 찾는다.
* find . -perm 0644
  * 권한이 644인 항목을 모두 찾는다.
* find . -perm /u+x
  * 권한이 u+x 인 항목을 모두 찾는다.&#x20;
* find -perm /g+w
  * 권한이 g+w 인 항목을 모두 찾는다.&#x20;
* find -type f -perm 644 -printf '%f-%i\n'
* find -type f -name "\*.log" -exec stat {} \\;
  * 파일명에 .log 가 존재하는 파일들에 대해서 stat 을 출력한다.&#x20;
* find . -type f -perm 755 -execdir pwd ; | uniq
* find . -type f -name "README" -ok rm {} ;
  * 파일명이 README 인 파일에 대해서 사용자에게 물어본 뒤, 삭제&#x20;

## 13. grep

* 파일 내용 중 원하는 내용을 찾는다.&#x20;
* grep \[options] PATTERN \[FILE...]
  * \-r : recursive
  * \-i : ignore case
  * \-v : invert match. 매치가 안되는 것을 조회&#x20;
  * \-q : quiet mode. 검색이 성공했을 때, exit status 를 받아올 수 있는데 결과값 말고, 성공과 실패만 알고 싶을 때 이용한다.&#x20;
* grep "run" \*.log | awk -F: '{ print $1 }' | sort -u
  * run 이라는 단어를 포함하고
  * 확장자가 .log 인 파일들 중에서&#x20;
  * : 를 기준으로 나누어 첫번째 변수를 프린트한 결과를 unique 하게 정렬한다. &#x20;
* grep run \*.log -q / grep dkahkdlshksld \*.log
  * run 키워드가 포함되었고 + 확장자가 log 인 파일들을 조회한다.&#x20;
  * \-q : 검색 결과를 프린트하지 않고, 성공과 실패 (0과 1)로만 반환하되 프린트하지 않는다.&#x20;
    * 이를 조회하기 위해서는 echo $? 로 결과를 확인할 수 있다.&#x20;
* grep "\<run>" \*.log
  * run 이라는 단어와 정확히 일치하는 내용만 출력한다.&#x20;
* grep "^run" \*.log
  * run 으로 시작하는 내용만 출력한다.&#x20;
* grep "^static.\*(void)$" \*.c
  * static 으로 시작하고, 임의의 단어가 주어지다가 (void)로 끝나는 내용만 출력한다.&#x20;
* apropos \[keyword]
  * \= man -k \[keyword]&#x20;
  * man page 에 포함되어있는 키워드를 찾는다.&#x20;
* apropos pthread -s 7
  * 탐색할 섹션을 콜론으로 구분하여 입력&#x20;
* apropos '^sem\_'&#x20;
  * sem\_ 로 시작하는 명령어의 사용법을 찾는다.&#x20;
* apropos '.\*'&#x20;
* apropos '.\*' -s 5:6:7
  * 탐색할 섹션을 콜론으로 구분하여 입력&#x20;

## 14. locate

* 파일의 위치를 찾아 보여준다. 시스템 전체를 대상으로.&#x20;
* 파일의 위치를 빠르게 검색해서 보여주기 위해 시스템 시작시에 updatedb 에 미리 파일의 위치정보를 업데이트시켜놓는다.
* locate 명령어를 수행하면, updatedb 가 저장해놓은 db 파일 내에서 검색하므로 누락 파일이 생길수도 있다.&#x20;
* 업데이트 시점은 cron 을 이용해서 OS 가 진행한다.&#x20;
* locate -i&#x20;
  * 대소문자 구분없이 검색&#x20;
* locate -l 10
  * 출력 결과를 LIMIT 개수 만큼만 출력한다.&#x20;
* locate -n 10
  * 출력 결과를 10개 만큼만 출력한다.&#x20;
* locate --regex "/usr/src/.\*\\\<main.c$"
  * PATTERN 에 맞는 파일들을 찾아 위치 출력&#x20;

## 15. which

* 실행 파일의 위치를 보여준다. 일반 텍스트 파일은 검색이 되지 않는다.&#x20;
* which ls / which chmod
  * 명령어에 해당하는 실행파일의 위치를 프린트한다.&#x20;
* which ls strace chmod
  * 두 실행파일의 위치를 동시에 보여준다.&#x20;

## 16. ps

* process status 출력&#x20;
  * \-e : all processes
  * \-f : full-format
  * \-L : print including thread&#x20;
  * \--forest : tree type print&#x20;
  * \-o : output formatting&#x20;
  * \--sort : sorting
* **\<output 컬럼>**
  * TTY : terminal&#x20;
  * c : cpu load - cpu의 몇 %를 차지하고 있는지&#x20;
* ps -ef
  * 모든 프로세스를 full format 으로 출력
* ps -ef | grep java
  * 보통 서버에서 앱이 백그라운드에서 실행되고 있을 때, 모니터링을 위해서 사용한다. &#x20;
* ps --ppid 1033 -f
  * 자식의 프로세스 아이디가 1033 인 프로세스를 full format 으로 출력&#x20;
* ps -ef --forest
  * 모든 프로세스를 트리 형태로 출력&#x20;
* ps -eF --sort=%mem | head
  * memori utilization 을 기준으로 정렬하여 10줄 까지만 출력하라
  * \-F : 좀 더 많은 컬럼 정보를 보여준다.&#x20;
* ps -eF --sort=-%mem
  * \-%mem : 메모리가 가장 높은 것부터 출력 (내림차순으로 출력)
* ps -eo,pid,c,user,args --sort pid
  * \-o : output 을 pid, c, user, args 만 출력하라&#x20;

## 17. top

* 프로세스를 어떤 기준으로 정렬하여 n개의 프로세스를 조회/모니터링&#x20;
* \<COMMANDS>
  * space : update&#x20;
  * q : quit
  * c : full command line search&#x20;
  * 1(number) : 모든 CPU 상태를 조회한다. &#x20;
* \<SORTING>
  * M : memory usage
  * P : CPU usage
  * N : process ID
  * T : running time&#x20;
  * R : reverse order&#x20;

## 18. lsof

* 열려있는 파일 (일반파일, 소켓, 디바이스 등) 목록을 조회한다.  (=ls opened file)
  * \-U : unix domain socket
  * \-i : internet domain socket&#x20;
  * \-p : specific pid&#x20;
  * \-u : user ID&#x20;
* lsof -U : unix domain
* lsof -i 4 : ipv4 only
* lsof -i tcp : tcp only
* lsof -u \`whoami\` : 현재 로그인한 사용자 소유의 열린 파일정보 조회&#x20;
* lsof -c Xorg : Xorg 가 열고있는 파일들&#x20;

## 19. netstat

* 네트워크/소켓 상태를 조회할 수 있는 툴
  * \-a : all types of port
  * \-n : numerical address
  * \-t : TCP socket only
  * \-u : UDP socket only
  * \-x : unix domain socket only
  * \-l : listening socket only
  * \-p : pid&#x20;
  * \-r : routing table&#x20;
  * \-s : network statistics &#x20;
* netstat -an : 전체를 출력하되, 숫자 주소 형태로 출력한다.&#x20;
* netstat -t&#x20;
* netstat -p : 뒤에 pid 및 program name 이 출력됨&#x20;
* netstat -s  : 프로토콜별로 통계정보 출력&#x20;
* netstat -r : 라우팅 테이블을 검색한다.&#x20;

## 20. sysctl

* 여러가지 시스템 설정을 조회, 설정한다. 윈도우의 레지스트리와 비슷한 설정방법이다.&#x20;
  * \-w : set value
  * \-r : variable search pattern&#x20;
* sysctl -a | grep net
  * \-a : all variables
* sysctl -a -r 'net' --names&#x20;
  * \-N : print variable only
* sysctl -a -r 'net' -values
  * \-n : print value only&#x20;
* sysctl -w net.ipv4tcp\_syn\_retries=4
  * 특정 설정값을 4로 변경&#x20;

## 21. df

* disk 의 사용량을 조회한다.&#x20;
* df -a
  * &#x20;\-a : 모든 것을 출력 &#x20;
* df -h
  * \-h : human readable 형식으로 출력&#x20;
* df -t ext4
  * \-t : 지정된 파일시스템에 대해서만 출력&#x20;
* df FILE

## 22. du

* 특정 파일이나 디렉토리가 점유하고 있는 disk usage 를 조회한다.&#x20;
* du -a : all files including directories
* du -b L byte
* du -h : human readable
* du -d --maxt-depth=N : set max depth&#x20;
* du -c : total sum&#x20;
* du -s : summary&#x20;

## 23. dmidecode

* SMBIOS 조회 툴&#x20;
  * DMI (Desktop Management Interface)&#x20;
  * SMBIOS (System Management  BIOS) : BIOS 가 생성한 시스템 하드웨어 정보. (old name : DMIBIOS)&#x20;
* dmidecode -t bios
  * 지정된 타입만 출력한다.&#x20;
* dmidecode -s system-uuid
  * 지정된 키워드만 출력한다.&#x20;

## 24. free

* 메모리 사용량 조회 툴&#x20;
* cat /proc/meminfo 기반으로 조회
* free -w -h
  * \-w :&#x20;
  * \-h " human readable output&#x20;
* free -s 1 -c 10 -h
  * \-s N : --seconds N. N초마다 반복 출력&#x20;
  * \-c N : --count N. N회 출력 후 종료&#x20;

## 25. lscpu

* cpu 구조정보를 출력해준다.&#x20;
* \-e, --extended : extended readable format&#x20;
* \-p, --parse : parsable format&#x20;
* lscpu
* lscpu -e
* lscpu -e=SOCKET,CORE,CPU
* lscpu -p=SOCKET,CORE,CPU

### 참고) 요즘 컴퓨터의 CPU

* 요즘 컴퓨터들은 다음과 같이 구성되어있다.&#x20;
  * processor : 1개&#x20;
  * core : 2개&#x20;
  * thread : 4개 -> 이것을 두고 CPU가 4개이다라고 말한다.&#x20;
* 캐시메모리&#x20;
  * L1 : 코어 내에서만 공유되는 캐시 메모리&#x20;
  * L2 : 코어들간 공유되는 캐시 메모리&#x20;
  * L3&#x20;

```shell
root@e6e99a9b4209:/# lscpu
Architecture:           x86_64
  CPU op-mode(s):       32-bit, 64-bit
  Address sizes:        39 bits physical, 48 bits virtual
  Byte Order:           Little Endian
CPU(s):                 8    //processor or core 가 아닌 logical 한 cpu 를 의미한다. (thread)
  On-line CPU(s) list:  0-7
Vendor ID:              GenuineIntel
  Model name:           Intel(R) Core(TM) i9-9880H CPU @ 2.30GHz
    CPU family:         6
    Model:              158
    Thread(s) per core: 1  //hyper thread 가 적용 안되어있는 core 임! 
    Core(s) per socket: 1
    Socket(s):          8
    Stepping:           13
    BogoMIPS:           4600.00
    Flags:              fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss ht pbe syscall nx pdpe1gb lm co
                        nstant_tsc rep_good nopl xtopology nonstop_tsc cpuid pni pclmulqdq dtes64 ds_cpl ssse3 sdbg fma cx16 xtpr pcid sse4_1 sse4_2 movbe p
                        opcnt aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch fsgsbase bmi1 avx2 bmi2 erms xsaveopt arat
    //flags : intel 에서 정의하고 있는 기능목록                         
Caches (sum of all):
  L1d:                  256 KiB (8 instances)    //d : data
  L1i:                  256 KiB (8 instances)    //i : instruction 
  L2:                   2 MiB (8 instances)
  L3:                   128 MiB (8 instances)
Vulnerabilities:
  Itlb multihit:        KVM: Mitigation: VMX unsupported
  L1tf:                 Mitigation; PTE Inversion
  Mds:                  Vulnerable; SMT Host state unknown
  Meltdown:             Vulnerable
  Mmio stale data:      Vulnerable
  Spec store bypass:    Vulnerable
  Spectre v1:           Vulnerable: __user pointer sanitization and usercopy barriers only; no swapgs barriers
  Spectre v2:           Vulnerable, STIBP: disabled
  Srbds:                Unknown: Dependent on hypervisor status
  Tsx async abort:      Not affected

  
root@e6e99a9b4209:/# lscpu -e
CPU SOCKET CORE L1d:L1i:L2:L3 ONLINE
  0      0    0 0:0:0:0          yes
  1      1    1 1:1:1:1          yes
  2      2    2 2:2:2:2          yes
  3      3    3 3:3:3:3          yes
  4      4    4 4:4:4:4          yes
  5      5    5 5:5:5:5          yes
  6      6    6 6:6:6:6          yes
  7      7    7 7:7:7:7          yes
  
root@e6e99a9b4209:/proc# cat /proc/cpuinfo
processor	: 0
vendor_id	: GenuineIntel
cpu family	: 6
model		: 158
model name	: Intel(R) Core(TM) i9-9880H CPU @ 2.30GHz
stepping	: 13
cpu MHz		: 2300.000
cache size	: 16384 KB
physical id	: 0
siblings	: 1
core id		: 0
cpu cores	: 1
apicid		: 0
initial apicid	: 0
fpu		: yes
fpu_exception	: yes
cpuid level	: 22
wp		: yes
flags		: fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss ht pbe syscall nx pdpe1gb lm constant_tsc rep_good nopl xtopology nonstop_tsc cpuid pni pclmulqdq dtes64 ds_cpl ssse3 sdbg fma cx16 xtpr pcid sse4_1 sse4_2 movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch fsgsbase bmi1 avx2 bmi2 erms xsaveopt arat
bugs		: cpu_meltdown spectre_v1 spectre_v2 spec_store_bypass l1tf mds swapgs itlb_multihit srbds mmio_stale_data
bogomips	: 4600.00
clflush size	: 64
cache_alignment	: 64
address sizes	: 39 bits physical, 48 bits virtual
power management:

processor	: 1
vendor_id	: GenuineIntel
cpu family	: 6
```

