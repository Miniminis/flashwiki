---
description: 전 세계 개발자가 가장 많이 쓰는 에디터 중 하나
---

# vim

## 1. 설명

* VI iMproved&#x20;
* 원래 vi -> vim 으로 변화됨&#x20;
* 자주 사용되는 옵션&#x20;
  * \-t : tag : tag 로 jump 하여 에디터를 시작한다.&#x20;
  * \-o files : 여러 파일을 split 모드로 열기&#x20;
  * \-R files : read-only mode 로 열기&#x20;
  * \-d files : diff mode&#x20;

## 2. 이동

* j, k, h, l : 상하좌우&#x20;
* w : 단어 시작점을 기준으로 앞으로 이동&#x20;
* e : 단어 끝점을 기준으로 앞으로 이동&#x20;
* b : 단어 시작점을 기준으로 뒤로 이동&#x20;
* 0 : 문장 맨 앞으로 이동&#x20;
* $ : 문장 맨 끝으로 이동&#x20;
* f(CHAR) : 문장 내에서 현재 포인터에서 가장 가까운 CHAR를 찾아 앞으로 이동  &#x20;
* F(CHAR): 문장 내에서 현재 포인터에서 가장 가까운 CHAR를 찾아 뒤로 이동&#x20;
* } : 단락(빈줄) 단위로 아래로 이동한다.&#x20;
* { : 단락(빈줄) 단위로 위로 이동한다.&#x20;
* G : 파일의 맨 끝으로 한번에 이동&#x20;
* gg : 파일의 맨 앞으로 한번에 이동
* :\[number]
  * number 라인으로 이동한다.&#x20;
  * :10 - 10번째 라인으로 이동한다.&#x20;
* :set number / :set nonumber&#x20;
  * 파일의 라인 넘버를 보여준다/안보여준다. &#x20;
* H, M, L : 현재 페이지의 최상단, 중간, 최하단으로 이동한다.&#x20;
* ctrl + f / ctrl + b
  * 페이지 단위로 뒤로 / 앞으로 이동&#x20;
* ctrl + d / ctrl + u
  * 반페이지 단위로 뒤로 / 앞으로 이동&#x20;
* ctrl + e / ctrl + y
  * 한줄씩 밑으로 스크롤 / 한줄씩 위로 스크롤&#x20;
  * 커서는 이동하지 않는다.&#x20;

## 3. 편집 1

* 입력모드로 전환&#x20;
  * i : 커서 위치부터 편집 시작&#x20;
  * I : 커서가 있던 라인의 가장 앞으로 이동하여 편집 시작&#x20;
  * a : 현재 커서에서 한칸 왼쪽으로 이동하여 편집 시작&#x20;
  * A : 커서가 있던 라인의 가장 마지막으로 이동하여 편집 시작&#x20;
  * o : 현재 커서에서 맡으로 한줄을 추가하면서 편집 시작&#x20;
  * O : 현재 커서에서 위로 한줄을 추가하면서 편집 시작 &#x20;
  * cc : 현재 라인을 한줄 삭제하면서 편집 시작&#x20;
  * C : 현재 커서 단어를 지우면서 편집 시작&#x20;
* 복사&#x20;
  * yy : 현재 줄 복사&#x20;
  * yw : 현재 단어 복사&#x20;
* 붙여넣기&#x20;
  * P : 현재 위치에 바로 붙여넣기&#x20;
  * p : 현재 커서 다음줄에 붙여넣기&#x20;
* 잘라내기
  * x : 현재 캐릭터 잘라내기&#x20;
  * dd : 현재 라인 잘라내기&#x20;
  * dw : 현재 word 의 끝까지 잘라내기&#x20;
  * D : 현재 커서부터 라인 끝까지 지우기 -> 클립보드에 보관 X
* 덮어쓰기 - replace
  * r : 현재 캐릭터만 대체한다. 끝나자마자 replace 됨&#x20;
  * R : 끝날때까지 덮어쓰기 모드가 유지됨&#x20;
  * s :&#x20;
* 되돌리기/반복&#x20;
  * u : undo&#x20;
  * ctrl-r : redo&#x20;
  * . : 마지막 행동 반복&#x20;

## 4. 편집 2

* :q : 문서 편집화면에서 나가기
* :q! : 현재 변경사항을 저장하지 않고 나가기&#x20;
* :w \[filename]: 파일 저장하기
* :wq / :x : 저장하고 나가기  &#x20;
* :qa : 전체 저장하고 나가기&#x20;
* :e : 현재 창에서 다른 파일 열기&#x20;
  * 현재 파일 내용 위에 다른 파일이 열린다.&#x20;
  * 현재 수정 내용을 저장한 뒤에야 이동할 수 있다.&#x20;

## 5. 검색&#x20;

* vim file 내에서 내가 원하는 검색어를 찾기 위함

### 5-1. 주요 사용법&#x20;

* /keyword
* ?keyword
* /\\\<keyword\\>
* n : 정방향 검색 반복&#x20;
* N : 역방향 검색 반복&#x20;
* \* : 커서 위치의 키워드 아래쪽으로 검색&#x20;
* \# : 커서 위치의 키워드 위쪽으로 검색&#x20;
* /abc\\|xyz : abc or xyz 검색&#x20;

### 5-2. 유용한 설정모음&#x20;

* :set ic : 대소문자 구분없이 - ignore case&#x20;
* :set noic : 대소문자 구분있게&#x20;
* :set hlsearch : search highlight On&#x20;
* :set nohlsearch : search highlight OFF &#x20;
* :set incsearch : 타이밍시 화면에 표시 O&#x20;
* :set noincsearch : 타이밍시 화면에 표시 X&#x20;

## 6. 스트링 치환

* 문서 전체 스트링 치환 : %
  * :%s/str/replace : 전체 문서에 대해서 str -> replace 로 치환 / 라인별로 가장 최초의 한번만 적용된다. &#x20;
  * :%s/str/replace/g : 다중 검색시에도 적용한다. / 검색되는 케이스 모두에 적용된다.&#x20;
  * :%s/str/replace/gc : 치환하기 전에 사용자에게 묻는다. 하나하나 물어본다.&#x20;
  * :%s/\\\<str\\>/replace/gc : "str" 단어 단위로 검색하여 치환한다.&#x20;
* 라인 지정 의미 기호&#x20;
  * . : 현재라인&#x20;
  * 1 : 라인 1
  * $ : 문서의 마지막 라인&#x20;
* 라인 지정 스트링 치환
  * :1,10s/str/replace/gc : 라인 1-10 에 대해서만 치환을 적용한다.&#x20;
  * :1,.s/str/replace/gc : 라인 1 - 현재 라인까지 치환 적용한다.&#x20;
  * :1,$s/str/replace/gc : 라인 1 - 마지막 라인까지 치환 적용한다.&#x20;

## 7. RegEx

* 주로 vim 에서 찾을 때 많이 사용된다.&#x20;
* 주요 의미 기호
  * . : newline 제외한 모든 문자&#x20;
  * ^ : 라인의 시작 &#x20;
  * $ : 라인의 끝&#x20;
    * /{$
  * \* : 0이거나 여러 개
    * /String.\*r &#x20;
  * \\+ : 1이거나 여러 개 = 1개는 반드시 있다.&#x20;
  * \\= : 0개이거나 1개 = 없거나 1개가 있거나&#x20;
  * \\{n} : n회 반복한다.&#x20;
  * \\{n,m} : n\~m회 반복한다.&#x20;
  * \\{,m} : 최대 m회까지 반복&#x20;
  * \\{n,} : 최소 n회까지 반복&#x20;
  * \[0-9] : 0\~9 사이의 캐릭터&#x20;
  * \[a-z] : a\~z 사이의 캐릭터&#x20;
  * \[abc] : a, b, c 중 하나와 매치되는 캐릭터&#x20;
  * \[^0-9] : 0\~9를 제외한 캐릭터&#x20;
* 예제 &#x20;
  * /sem\_.\*(.\*) : sem\_ 으로 시작하여 함수를 호출/선언하는 경우&#x20;
    * 예) sem\_dnskdksldhk() : sem 시리즈를 호출하는 api 를 일괄적으로 검색하겠다.
  * /public.\*_(.\*_)&#x20;
  * /^#include : 라인의  시작에 #include 가 나오는 경우&#x20;
  * /\\\<inotify\_ : 단어가 inotify\_ 로 시작하는 경우&#x20;
  * :%s/\\\<variable\\>/var/g : 문서 전체에서 variable 단어와 정확히 일치하는 단어를 찾아 var 로 변경한다.&#x20;

## 8. 멀티 윈도우&#x20;

* vim 실행시 창 분할
  * 가로분할
    * vim -o test.c test.h
    * vim \*.sh -o&#x20;
  * 세로분할 &#x20;
    * vim -O test.c test.h&#x20;
* vim 실행 후 창 분할&#x20;
  * 가로분할&#x20;
    * :split s / ctrl+w s
    * :split n / ctrl+w n&#x20;
  * 세로분할&#x20;
    * :vsplit v
    * ctrl+w v&#x20;
* vim 실행 후 새 창 분할
  * 가로 새 창&#x20;
    * :new (n)
    * ctrl+w (n)&#x20;
  * 세로 새 창&#x20;
    * :vnew
    * ctrl+w
* 창 닫기&#x20;
  * 현재창&#x20;
    * :quit
    * ctrl+w q
  * 현재창만 남기기&#x20;
    * :only&#x20;
    * ctrl+w o
* 창 간 커서 이동 &#x20;
  * ctrl+w w : 다음 창으로 이동&#x20;
  * ctrl+w k : 윗 창으로 이동&#x20;
  * ctrl+w j : 아래 창으로 이동&#x20;
  * ctrl+w h : 왼쪽 창으로 이동&#x20;
  * ctrl+w l : 오른쪽 창으로 이동&#x20;
  * ctrl+w t : 최상단 창으로 이동&#x20;
  * ctrl+w b : 최하단 창으로 이동&#x20;
* 창 이동&#x20;
  * ctrl+w K : 윗 창으로 이동&#x20;
  * ctrl+w J : 아래 창으로 이동&#x20;
  * ctrl+w H : 왼쪽 창으로 이동&#x20;
  * ctrl+w L : 오른쪽 창으로 이동&#x20;
* 창 크기 조절&#x20;
  * ctrl+w + // ctrl+w - : 높이 증가 // 감소&#x20;
  * ctrl+w \_ OR / : 높이 최대화&#x20;
  * ctrl+w > // ctrl+w < : 폭 증가 // 감소&#x20;
  * ctrl+w | : 폭 최대화&#x20;
  * ctrl+w = : 높이/폭 모두 같게&#x20;
* 기타 기능&#x20;
  * ctrl+w ] : 새창에서 tag jump&#x20;
  * ctrl+w f : 커서 위치의 파일 이름을 새창에서 열기&#x20;

## 9. 단축키 (사용자 정의)

* 모드마다 다른 단축키를 설정할 수 있다.&#x20;
* 기본문법 : CMD LHS RHS
  * CMD : map, nmap, imap...&#x20;
  * LHS : 입력할 키&#x20;
  * RHS : 입력시 동작 &#x20;
* \<C-key> : ctrl + key&#x20;
* \<S-key> : shift + key&#x20;
* \<A-key> : alt + key&#x20;
* \<C-S-key> : ctrl + shift + key
* \<BS>, \<Tab>, ... &#x20;
* example
  * :nmap \<F8> oprintf("%s %d line : \n", \_\_func\_\_, \_\_LINE\_\_);\<ESC>
    * normal mode 에서 F8 누르면 아래 줄에 printf() 출력한다.&#x20;
  * :imap \<F8> printf("%s %d line: \n", \_\_func\_\_, \_\_line\_\_);\<CR>
    * insert mode 에서 F8누르면 print() 출력한다.&#x20;
  * :nmap \<F2> :cs find s \<C-R>=expand("\<cword>")\<CR>\<CR>
    * F2 누르면 커서위의 단어를 cscope find symbol 로 매핑해서 실행한다.&#x20;
    * 그 후 엔터를 두번 누른다.&#x20;
  * :map \<F8> oprintf("hello\n");\<ESC>
    * F8을 누르면 hello 를 츨력하고 개행한다.&#x20;

## 10. 유용한 기능들&#x20;

<figure><img src="../../.gitbook/assets/image (50) (3).png" alt=""><figcaption><p>vim [file1] [file2] -d</p></figcaption></figure>

* 자동완성 : insert mode 에서 ctrl + p&#x20;
  * 미리 정의된 변수 등 프로그래밍 파일 내에서 유용하게 사용&#x20;
* diff mode&#x20;
  * 서로 비슷하지만 살짝 다른 두 파일의 내용을 비교할 때 유용하게 사용
  * vim \[file1] \[file2] -d&#x20;
  * or vimdiff \[file1] \[file2]&#x20;
  * diff 설정 - :diffthis&#x20;
  * diff 해제 - :diffoff
  * do : 왼쪽에 있는 내용 오른쪽으로 복사
* fold&#x20;
  * 생성 : \[visual block 모드에서] zf&#x20;
  * 삭제 : zd OR space &#x20;
  * 열기 : zo
  * 닫기 : zc&#x20;
* 괄호 쌍 이동 : \[괄호에 커서를 놓고] %
* 마킹
  * 위치마킹 : m\[a-z]
  * 마킹으로 이동 : '\[a-z]
* man page 열기 : 단어위에 커서 놓고 k
  * section 3 에 대한 man 페이지 열 때 : 3k
  * :!man -a : 모든 섹션으로 보기&#x20;
* indentation&#x20;
  * 현재중 auto indent, ==
  * 블록에 적용시, \[비주얼모드] =
  * 파일 전체에 적용시, gg=G
  * :set expandtab&#x20;

## 11. vim 플러그인

* vim 의 기능을 확장할 수 있는 방법&#x20;
* 운영체제에 소프트웨어 패키지를 설치해서 사용하듯, vim 도 plugin 을 설치할 수 있다. -> vim 기능의 확장!&#x20;
* 설치방법&#x20;
  * \~/.vim/plugin 디렉터리에 plugin 파일(.vim) 복사
  * 플러그인 관리자&#x20;
    * vundle&#x20;
    * pathogen&#x20;
    * vim-plug &#x20;
* 플러그인은 어디에서 찾는가?&#x20;
  * [https://www.vim.org/scripts/index.php](https://www.vim.org/scripts/index.php)
  * [https://vimawesome.com/](https://vimawesome.com/)

## 12. man&#x20;

* man page 조회&#x20;

### sections&#x20;

섹션에 따라서 내용이 나누어져 설명되어있다.&#x20;

1. general commands&#x20;
2. system call&#x20;
3. library functions : C standard library&#x20;
4. special files&#x20;
5. file formats and conventions&#x20;
6. games and screensavers
7. miscellanea&#x20;
8. system administration commands and daemons&#x20;

### example

* man 3 pringf
* man -a stat&#x20;
* apropos pthread
  * 검색에서 포함된 모든 페이지를 보여준다.&#x20;
* whatis pthread&#x20;

### command (less)&#x20;

* 종료 : q&#x20;
* 검색
  * /pattern
  * ?pattern&#x20;
* 화면이동
  * 한 페이지 앞으로 : f, ctrl-f, space&#x20;
  * 한페이지 뒤로 : b, ctrl-b&#x20;
  * 한 줄 앞으로 : j, e, ctrl-e
  * 한줄 뒤로 : k, y, ctrl-y

## 13. diff&#x20;

* 두 파일의 차이점을 비교하고, stdout 으로 출력한다.&#x20;
* diff 결과를 patch command 로 적용하는 것이 가능하다.&#x20;

### 자주 사용되는 옵션&#x20;

* \-r, --recursive&#x20;
  * diff 뜰 때, 디렉토리 단위로하면, 모든 파일에 대해서 반복적으로 설정해주어야 한다.&#x20;
* \-q, --brief
  * 변경 여부만 출력 &#x20;
* \-u, -U NUM, --unified\[=NUM]
  * unified context 방식으로 출력한다.
  * 기본값은 3dnl
* \-N, --new file
  * 없는 파일을 빈 파일로 인식한다.&#x20;
* \-p, --show-c-function&#x20;
  * 변화된 블럭 함수의 이름을 출력&#x20;

### example

* diff -uN before.c after.c
* diff <mark style="background-color:yellow;">-urNp</mark> dir\_a dir\_b
* diff -q dir\_a dir\_b&#x20;
  * echo $?
    * 변경사항 없다면 0
    * 변경되었다면, 1

```shell
 diff -urNp diff_a diff_b
--- diff_a	2023-01-17 16:24:35.000000000 +0900
+++ diff_b	2023-01-17 16:27:40.000000000 +0900
@@ -6,4 +6,4 @@ hello, vim
 hello, vim
 hello, vim
 hello, vim
-hello, vim
+hello, vim...!!!!
```



## 14. patch&#x20;

* patch file (diff 의 결과물)을 파일에 적용한다.&#x20;

### 자주 사용되는 옵션&#x20;

* \-pNum
  * \--strip=NUM
* \-i patchfile&#x20;
  * \--input-patchfile&#x20;
* \--dry-run
  * 가상으로 실행(적용되지 않음)
  * 실행 결과만 출력한다.&#x20;
* \--merge&#x20;
  * conflict 발생시 merge&#x20;

### example

* patch -p0 --dry-run < test.patch&#x20;
* patch -p3 -i test.patch&#x20;
* patch -p0 --merge -i test.patch&#x20;

## 15. ctags

* 소스코드를 분석하여 tag 를 파일로 저장
  * function 이나 variable 등을 tag 로 저장하여 vim 이 탐색하기 쉽도록 해준다.&#x20;
  * ctags 가 없다면 vim 은 주석인지, 함수인지, 변수인지 구분해내지 못한다.&#x20;
* vim 등의 에디터에서 소스코드 탐색이 쉽도록 한다.&#x20;
* ctags \[options] \[files...]&#x20;

### 자주 사용되는 옵션&#x20;

* \-R : recurse&#x20;
* \--exclude=\[pattern]

### example

* ctags -R
* ctags -R --exclude="drivers"

### vim 연동 예제&#x20;

* 특정 tag 파일 지정&#x20;
  * :set tags=../tags
* 상위로 올라가며 tag 파일 지정&#x20;
  * :set tags=./tags;/
* tag 찾기&#x20;
  * :tag rte\_eal\_init
* 여러가지 tag 중 선택
  * :tselect rte\_eal\_init
  * :tselect main&#x20;
* 여러가지 tag 중 선택 + 검색&#x20;
  * :ts /rte\_eal\_
* 커서 위의 단어로 tag jump 단축키 : ctrl+]
  * :tj dump\_info&#x20;
  * 수정하기 전에 다른 키워드로 jump 하고 싶을 때&#x20;
  * ctrl + w + ]
    * 창을 새로 열면서 태그 점프하면 수정된 내용을 저장하지 않아도 다른 키워드 있는 파일을 볼 수 있다.&#x20;
* tag jump 에서 되돌아오는 단축키 : ctrl+t&#x20;
* ctags --list-languages
  * ctags 에서 지원되는 언어 목록&#x20;

<figure><img src="../../.gitbook/assets/image (51) (3).png" alt=""><figcaption><p><a href="https://www.inflearn.com/course/%EB%A6%AC%EB%88%85%EC%8A%A4-%EC%BB%A4%EB%A7%A8%EB%93%9C%EB%9D%BC%EC%9D%B8-%ED%88%B4">인프런 강의 - 리눅스 커맨드라인 툴 (Full ver.)</a></p></figcaption></figure>



## 16. cscope

* 소스 파일을 분석하여 파일로 저장한다.&#x20;
  * ctags 보다 파일이름, 함수 호출하는 곳 - 호출되는 곳 등 다양한 방법으로 검색이 가능하다.&#x20;
* 자체 사용자 인터페이스 사용이 가능하다.&#x20;
* vim 등의 에디터에서도 연동이 가능하다.&#x20;

### options

* \-b : 분석 파일만 빌드한다. vim 에서 연동하기 위해서 필요하다.&#x20;
* \-R : recursive&#x20;

### example

* find \`pwd\` -type f -name ".\[ch]" > csope.files&#x20;
  * 현재 디렉토리에서 이름이 .ch 로 끝나는 타입의 파일들에 대해서 검색한다.&#x20;
* cscope -b&#x20;
* cscope -R

### interactive mode&#x20;

* cscope -R&#x20;
* find this C symbol : symbol 검색. 원형, 함수 호출하는 부분을 포함한다.&#x20;
* find this global definition  : 함수 선언부&#x20;
* find functions called by this function : 입력한 함수가 호출하는 함수를 검색
* find functions calling this function : 입력한 함수를 호출하는 부분을 검색
* find this text string : text 검색&#x20;
* change this text string : 텍스트 대체&#x20;
* find this egrep pattern : extended regex pattern 으로 검색
* find this file : 파일 이름 검색&#x20;
* find files #including this file : 특정 파일을 #include 하는 부분 검색&#x20;
* find assignments to this symbol : 심볼에 해당하는 부분을 검색

### vim 연동 예제&#x20;

* : set cscopetag
  * ctag 파일 대신에 ctrl+] 로 tag jump 한다.&#x20;
* :cscope (add, find, help, kill, reset, show)&#x20;
  * a  : 심볼에 해당하는 부분 검색&#x20;
  * c : 이 함수를 호출하는 부분 검색&#x20;
  * d : 이 함수가 호출하는 함수 검색&#x20;
  * e : extended regex pattern 으로 검색&#x20;
  * f : 파일 이름 검색&#x20;
  * g : 함수 선언부&#x20;
  * i : 특정 파일을 $include 하는 부분 검색&#x20;
  * s : symbol 검색 (원형, 함수 호출하는 부분 포함)&#x20;
  * t : text 검색&#x20;

## 17. strace&#x20;

* system call 과 signal 을 trace 한다.&#x20;
* 어플리케이션을 개발하다보면, 알게모르게 app - linux kernal 사이를 자주 왔다갔다 한다. -> systeml call&#x20;

### option&#x20;

* \-p : pid 지정. 동작 중인 프로세스에 attach 한다.&#x20;
* \-f : 생성되는 thread 도 trace 한다.&#x20;
* \-tt : timestamp 를 같이 출력한다.&#x20;
* \-o : stdout 대신 입력받는 파일에 저장한다.&#x20;

### example

* strace ls&#x20;
* strace -tt ls&#x20;
* strace -tt -o output ls&#x20;
* strace -p 1234&#x20;
  * strace -p \`pidof \[실행중인 파일 이름]\`
