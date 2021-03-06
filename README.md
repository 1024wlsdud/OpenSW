# 오픈소스SW과제

### getopt & getopts

getop와 getopts는 어디에 사용될까? -> 사용자나 개발자가 편의를 위해 옵션을 생성하거나 추가할 때 사용
**getopts**명령을 이용하지 않고 옵션을 해석해 처리하면, 옵션 처리에 스크립트가 많이 복잡해질 수 있음


![쉘 옵션](https://user-images.githubusercontent.com/43934522/142739531-d9073ba1-0317-4e5b-8029-b5ac481fcbd4.png)


쉘에서 스크립트나 함수가 파라미터를 받을 땐 $@ (모든 옵션) 또는 $1, $2,,(첫 번째 입력 값, 두 번째 입력 값) 등을 기본 방식으로 저장된다.

 ls 명령어에서 사용되는 -a 또는 -all 등을 명령 행 옵션이라고 함
 
 
![옵션 기본 값](https://user-images.githubusercontent.com/43934522/142739539-71699add-4e08-4150-8ad8-cc14260248b3.png)
![예시](https://user-images.githubusercontent.com/43934522/142739547-a6147a34-f81f-4cbf-b852-812710485754.png)


---     


## 각 옵션의 특징                                      
   
   
|옵션의 종류|예시|처리가능한 명령어|
|:---:|:---:|:---:|
|short|`command -a` or `-adc`|getopt, getopts|
|long|`command --apple`|getopt|


**short 옵션의 특징**
+	옵션을 붙여서 사용가능
+	옵션의 순서에 상관 없음
+	옵션에 인자를 가질 수 있다

**long 옵션의 특징**
+	--command 형태로 사용됨 
+	short옵션과 달리 붙여 쓸 수 없어 사용방법이 간단함
	
getopt가 어떤 명령어인지 확인하기 위해 man getopt를 한 모습
 ![image](https://user-images.githubusercontent.com/43934522/142739574-ae7cda8d-d20c-44b7-bc8a-8bf6a42ee845.png)
 

해석하면 getopt는 손쉬운 구문 분석을 위해, 명령행의 옵션을 분할하고 유용한 옵션을 위해 사용한다는 말인데,
사용자와 개발자의 편의성을 위해 ‘:’를 사용하여 명령어 옵션을 받는다는데 한번 알아보자.

getopts 명령에선 OPTIND이 빠지지 않고 같이 사용된다.
OPTIND: 다음 번 처리될 옵션의 인덱스, getopt()함수는 한 번 호출 될 때 마다 이 값을 업데이트함

![image](https://user-images.githubusercontent.com/43934522/142739578-7b99dbb0-5e3a-4ee4-a777-6eb83079c3cb.png)

 ***   
옵션은 옵션 인수를 가질 수 있는데, 이 때 옵션 스트링에서 해당 옵션 문자 뒤에 ‘:’를 붙이고 getopts명령은 옵션인수 값을 OPTARG 변수에 설정하지만,   
옵션이 처리되지 않으면 계속 그 위치에 있기 때문에 수동으로 위치를 옮겨주거나, while문과 case문을 이용한다.


 ![image](https://user-images.githubusercontent.com/43934522/142739589-15d85ac1-2772-4dcc-9dba-985020b90658.png "수동으로 처리하는 모습")


```shell
#!bin/bash

while getopts "a:bc" opt; do
	case $opt in
	a)
		echo >&2 "-a was triggered!, OPTARG: $OPTARG"
		;;
	b)
		echo >&2 "-b was triggered!, OPTARG: $OPTARG"
		;;
	c)
		echo >&2 "-c was triggered!, OPTARG: $OPTARG"
		;;
	esac
```
위에 보이는 case안의 a), b) c)는 각각 옵션을 뜻하고 a는 argument를 요구한다는 의미이다.
입맛대로 옵션 설정을 할 수 있기 때문에 코드를 더욱 간편하게 만들 수 있다.


**옵션에 없는 값을 넣게 된다면 어떻게 될까?**

getopts 명령은 **error  repotting**과 관련해, 두 개의 모드를 제공한다.
 |Verbose Mode| | 
 |:---|:---:|
 |invalid 옵션 사용|opt 값을 `?`문자로 설정하고 OPTARG같은 unset 오류 메시지를 출력.|
 |옵션인수 값을 제공하지 않음|opt값을 `?` 문자로 설정하고 OPTARG같은 unset. 오류 메시지를 출력.|
 
 |Silent Mode| | 
 |:---|:---:|
 |invalid 옵션 사용|opt 값을 `?`문자로 설정하고 OPTARG같은 unset 오류 메시지를 출력.|
 |옵션인수 값을 제공하지 않음|opt값을 **`:`** 문자로 설정하고 OPTARG같은 unset. 오류 메시지를 출력.|
 
 
default값은 verbose모드, 기본적으로 옵션과 관련된 오류 메시지가 표시되므로 스크립트를 배포할 때는 silent mode를 이용한다.    
silent mode는 옵션 스트링의 맨 앞부분에 ‘:’ 문자를 추가

`$set -a aaa –posix –ling 123 -…`
short옵션은 a -abcd 이런 명령이 있다면, 붙여쓰기한 옵션 명으로 인식함
또한 –long 옵션과 같이 인수를 사용하게 되면, 그 이후의 옵션은 getopts에 의해 인식이 되지 않게 됨    


따라서 long옵션을 따로 처리하고 나머지를 처리해주는 과정이 필요함
+	long옵션을 삭제하고 short 옵션만 getopts 명령에 전달


## long 옵션을 처리하기 위한 getopt사용 
***getopt는 /usr/bin/getopt에 위치한 외부 명령 (getopts 명령은 쉘 내장 명령)***

기본적으로 shor, long 옵션을 모두 지원함, 옵션 인수를 가질 경우 : 문자를 사용하는 것은 getopts 명령과 동일함
|옵션 지정|명령어|
|:---:|:---:|
|short 옵션 지정|`getopt -o a:bc` |
|long 옵션 지정|`getopt -l help,path:`|

long 옵션은 `,` 로 구분한다.

**설정하지 않은 옵션**이 사용되거나 **옵션 인수가 빠질 경우** 오류 메시지를 출력해줌 
```shell
#!/bin/bash

options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
echo "$options"
-----------------------------------------------------

$ ./test.sh -x
getopt: invalid option -- 'x'

$ ./test.sh --xxx
getopt: unrecognized option '--xxx'

$ ./test.sh -a
getopt: option requires an argument -- 'a'

$ ./test.sh --name
getopt: option '--name' requires an argument
```
getopt 특징 ex)
1.	옵션들을 case문에서 깔끔하게 정렬해줌 
2.	내부에서 자체적으로 옵션을 나누기 때문에 getopts가 못하는 부분을 해결함(short, long)


이렇게 명령어에 대한 short옵션들과 long옵션을 편하게 관리하고    
생성하고, 사용하기 위해 getopt, getopts를 사용한다.



---
<br/></br>
# 리눅스 sed & awk 명령어 

## sed Command

sed: Stream Editor, 원본 텍스트 파일을 편집하는 명령어   
vi 편집기와 다르게, 실시간 저장하는 편집이 불가능(ed명령어와 grep 명령어 기능의 일부를 합친 것)   


sed 명령어는 **원본을 건드리지 않고** 편집하기 때문에 원본에 전혀 영향이 없다. (옵션을 지정해주면 원본 변경 가능)

내부적으로 특수한 저장 공간인 **패턴 버퍼**와 **홀드 버퍼**를 사용한다.

![image](https://user-images.githubusercontent.com/43934522/142743520-cc5f9533-7b11-48e4-8af8-8bda7ed9d2b6.png)
[출처](https://reakwon.tistory.com/164)
+	sed는 inputStream으로 파일의 내용을 가져옴
+	패턴버퍼에 내용을 담고, 데이터의 변형과 추가를 위해 임시 버퍼를 사용하는데, 이걸 **홀드 버퍼**라고 한다.
+	작업이 끝난 후, 버퍼에 담긴 내용을 **OutStream**으로 보내주게 되면 콘솔창에 output으로 나타남 
+ 	기억장치 안의 버퍼를 사용하지 않는다는 것이 장점이다. (버퍼를 사용하지 않으면 파일의 크기 제한 없이 작업이 가능)
+ 


기본적인 sed 사용 형태   
`sed -n -e 'command' [input file]`
|옵션|설명|
|:---:|:---:|
|-n|자동 출력 off|
|-e|편집을 가능하게 함|    


sed 명령의 기본 형태는 sed -n 으로 생각해야 함(-n을 붙여주지 않으면 매우 더럽기 때문)


### sed의 특징
+ 정규표현식이 사용가능
+ 정규식을 사용하기 때문에 특수문자 앞에 역슬레시를 붙여줘야함(\)
+ vi처럼 파일 내용을 고치는게 아닌, 커맨드나 스크립트에서 동작하여 원하는 부분만 변경해줌 


편집할 때 사용하는 명령어는 여러개가 있지만, 대표적으로 **출력, 삭제, 치환, 문자열 추가**가 있다.
**옵션**
### 1. 특정 행 출력 - p(print)   
1-1. 전체 내용 출력
`sed -n -e '1,$p' sed_test_file.txt`     
`sed -n -e '/$/p' sed_test_file.txt`      


1-2. 첫번째 줄 출력
`sed -n -e '1p' ./sed_test_file.txt`
![image](https://user-images.githubusercontent.com/43934522/142744107-5f98d68c-b6cc-4ebe-b3eb-a722cda5ff98.png) 


1-3. 범위 출력 (n부터 끝까지 print)
`sed -n -e 'n,$p' sed_test_file.txt`
** 파일 속 내용을 일괄 변경하고 싶을 때 사용 **
`sed -i 's/기존 내용/변경할 내용/g' *.php`

1-4. 다중 command 사용 // 여러 command를 사용하려면 -e를 한번 더 사용하면 가능
`sed -n -e '1p' -e '8,$p'  sed_test_file.txt`


1-5. 특정 문자열이 있는 줄 출력
`/포함된 문자열/p`     
ex) `sed -n -e '/F/p'  sed_test_file.txt`

### 2. 특정 행 삭제 - d(delete)
행삭제 명령어는 **d**를 사용하면 됨
`sed -n -e 'n,nd' -e '1,$p'  sed_test.txt`    
![image](https://user-images.githubusercontent.com/43934522/142744195-339b5305-4983-421d-989e-1151fce46408.png)    
### 3. 단어 치환 - s(substitute)
3.1 기본적인 단어 치환   
`s/old/new/g`   
`s/old/new/gi`   
old는 단어를 치환할 문자열, new는 새롭게 치환한 문자열 **비어있으면 그 문자열을 삭제한 효과**

+부가옵션 i(ignore case)로 단어를 검색할 때 대소문자 구분 x 

3-2 특정 단어로 시작, 끝나는 단어를 포함하는 라인의 문자열 치환
+ 앞에 '^'문자를 사용
+ **특정 단어로 시작하거나 끝나는 문자열이 아닌, 줄임 **
+ 끝나는 문자열은 끝에 $를 붙여서 검색, Chosun.으로 끝나는 줄의 Chosun을 대문자로 바꾸기
+ ***ex)***`sed -n -e 's/Chosun.$/CHOSUN/gi' -e '1,$p' 파일명.txt`
### 4. 문자열 추가 - a, i(append, insert)
|문자열을 추가하는 방법|명령어|
|:---:|:---:|
|Append|`/찾을 문자열/a\다음 출에 추가할 문자열`|
|Insert|`/찾을 문자열/i\위에 삽입할 문자열`|   

***EX)*** `sed -n -e '/Jinyeong/a\Smart' -e '1,$p' 파일명.txt`
+ Jinyeong으로 끝나는 줄 다음에 Smart라는 줄을 넣음 
+ ![image](https://user-images.githubusercontent.com/43934522/142752749-68a39101-58bd-4953-98eb-050af090f1e2.png "코드 실행 사진")


+ 하지만, 원본 파일의 내용은 변하지 않은걸 확인할 수 있다.
+ ![image](https://user-images.githubusercontent.com/43934522/142752781-e31c287d-7464-48c7-832a-d43b6f1aff96.png)



**sed 명령어 번외** 
+ 공백줄 제거하기
	+ `sed '/^$/d' 파일명`
+ 라인마다 공백 추가 
	+ `sed '/^$/d' 파일명`
+ 모든 주석 라인 삭제
	+ `sed '/^#/d' 파일명`


## awk Command
awk 명령어는 이름에 기능을 의미하는 단어가 포함되지 않는 명령어(만든 사람의 이니셜이 들어감: **A**ho+**W**einberger+**K**ernighan)

awk는 파일로부터 레코드를 선택하고, 레코드에 포함된 값을 조작하거나 데이터화하는 것을 목적으로 사용


![image](https://user-images.githubusercontent.com/43934522/142753247-b469afb8-8237-4c50-8a6a-00d73602efcf.png "awk가 하는 일")


awk명령으로 할 수 있는 일
+  텍스트 파일 내용 출력
+  파일의 특정 필드 출력
+  특정 필드에 문자열을 추가해서 출력
+  패턴이 포함된 레코드출력
+  특정 필드에 연산 수행 결과 출력
+  필드 값 비교에 따라 레코드 출력
+  사칙연산도 가능

### awk 기본 구조
**`    awk [OPTION...] 'pattern { action }' [ARGUMENT...]`**
pattern과 action은 모두 생략이 가능, pattern을 생략할 시 모든 레코드가 적용,   
action을 생략하면 print가 적용    


`awk 'pattern' filename`   
`awk '{action}' filename`   
`awk 'pattern {action}' filename`    
+ ***EX)*** 
```
$ awk '{print $0}' awkfile
>
홍 길동 3324    5/11/96 50354  
임 꺽정 5246    15/9/66 287650  
이 성계 7654    6/20/58 60000  
정 약용 8683    9/40/48 365000 
```

 ```sehll
$ awk '{print $1}' awkfile
> 
홍
임
이
정
```

### awk 동작 원리
1. awk는 파일 또는 파이프를 통해 입력 라인을 얻어와 $0 내부 변부에 라인을 입력,   
각 라인을 레코드라 부르고 new line에 의해 구분
2. 라인은 공백을 기준으로 각각의 필드나 단어로 나뉨. 필드는 $1부터 시작.
3. 내장 변수인 `FS`라고 불리는 필드 분리자가 공백을 할당받고, 필드가 콜론이나 대시와 같은   
   문자에 의해 분리되면 새로운 필드 분리자로 `FS`의 값을 변경할 수 있다.
4. awk는 화면에 풀력할 때 print 함수 사용
5. 콤마는 OFS와 매핑되어 있으며 공백을 할당받음+ 

### 레코드와 필드
+ 레코드  
    + awk는 입력 데이터를 볼 수 없지만 포맷 또는 구조를 볼 순 있음. 레코드라고 불리는 각 라인은 newline으로 분리
    + NR 변수: 각 레코드 번호는 awk의 빌트인 변수 NR에 저장된다.
+ 필드
    + 각 레코드는 디폴트로 공백이나 탭으로 분리된 필드라는 워드로 구성, NF에 필드의 수를 유지하며 라인당 100개의 필드를 가짐

**옵션**
### 출력 print
+ awk 필드값 출력 예시
+ ![image](https://user-images.githubusercontent.com/43934522/142753849-32c338db-c8ce-459f-8776-8abd4026aa16.png)
+ `awk '{print "no:"$1, "user:"$2}' ./file.txt` 로 문자열을 같이 출력할 수 있음 
+ ![image](https://user-images.githubusercontent.com/43934522/142753903-9f7114ce-9111-4aa3-be53-08a1e39340c3.png)
+ `awk '$3 > 70 { print $0 }' ./file.txt` 
+ 특정 필드 값 비교를 통해 세 번째 필드가 70보다 큰 레코드를 출력한다. 
+ 이 이외에도 합, 평균값 등 연산을 구해서 출력할 수 있고, 레코드의 길이도 검사할 수 있다. 
### 필드 구분 문자 지정 -F
`awk -F ':' '{ print $1 }' ./file.txt         # 필드 구분 문자를 : 로 변경.`
`awk -F ',' '{ print $1 }' ./file.txt         # 필드 구분 문자를 , 로 변경.`
### awk 프로그램 파일 경로 지정 -f
+ awk액션과 명령이 파일에 작성되어 있을 때 -f 사용
+ `awk -f [awk 명령파일] [awk 명령을 적용할 텍스트 파일]`
+ `awk -f awkp.script ./file.txt           # awkp.script에 저장된 awk program 실행`
### 특정 레코드만 출력하기.
+ exit 키워드를 사용해 awk 실행을 중지시킬 수 있다.
+ ![image](https://user-images.githubusercontent.com/43934522/142754775-fc19a1bb-6fa1-496c-8542-0ea61a6519a1.png)

이 외에도 필드 중 최대값, 출력 피드 너비 지정 등 출력에 관련된 옵션들이 있다.

+ awk 표현식은 C언어 표현식과 유사한 형태로 제공되어 새로운 변수를 선언이 가능
+ 값을 할당하거나 참조할 수 있다
+ c언어처럼 함수를 사용할 수 있고, 변수를 참조할 수 있다. 
