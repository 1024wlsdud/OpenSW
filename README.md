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


