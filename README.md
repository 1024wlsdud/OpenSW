# 오픈소스SW과제

### getopt & getopts

getop와 getopts는 어디에 사용할까?

오픈소스 과제

 

쉘에서 스크립트나 함수가 파라미터를 받을 땐 $@ (모든 옵션) 또는 $1, $2,,(첫 번째 입력 값, 두 번째 입력 값) 등을 기본 방식으로 저장된다.

  ![쉘 옵션](https://user-images.githubusercontent.com/43934522/142739531-d9073ba1-0317-4e5b-8029-b5ac481fcbd4.png)

 ls 명령어에서 사용되는 -a 또는 -all 등을 명령 행 옵션이라고 함
![옵션 기본 값](https://user-images.githubusercontent.com/43934522/142739539-71699add-4e08-4150-8ad8-cc14260248b3.png)
![예시](https://user-images.githubusercontent.com/43934522/142739547-a6147a34-f81f-4cbf-b852-812710485754.png)

옵션은 short옵션과 long옵션이 있는데, getopts는 short옵션을, getopt는 long옵션을 처리한다
short 옵션의 특징
+옵션을 붙여서 사용할 수 있으며, 순서가 바뀌어도 된다
--	대표적인 명령어 ex) ls 명령어
long 옵션의 특징
+	--command 형태로 사용됨 
+	short옵션과 달리 붙여 쓸 수 없어 사용방법이 간단함
	
getopt가 어떤 명령어인지 확인하기 위해 man getopt를 한 모습
 ![image](https://user-images.githubusercontent.com/43934522/142739574-ae7cda8d-d20c-44b7-bc8a-8bf6a42ee845.png)
 

해석하면 getopt는 손쉬운 구문 분석을 위해, 명령행의 옵션을 분할하고 유용한 옵션을 위해 사용한다는 말인데,
쉽게 말해서, 사용자와 개발자의 편의성을 위해 ‘:’를 사용하여 명령어 옵션을 받는데 유용한 함수다. 
getopts 명령에선 OPTIND이 빠지지 않고 같이 사용된다.
OPTIND: 다음 번 처리될 옵션의 인덱스, getopt()함수는 한 번 호출 될 때 마다 이 값을 업데이트함
![image](https://user-images.githubusercontent.com/43934522/142739578-7b99dbb0-5e3a-4ee4-a777-6eb83079c3cb.png)

 
옵션은 옵션 인수를 가질 수 있는데, 이 때 옵션 스트링에서 해당 옵션 문자 뒤에 ‘:’를 붙이고, getopts명령은 옵션인수 값을 OPTARG 변수에 설정한다
하지만, 옵션이 처리되지 않으면 계속 그 위치에 있기 때문에
수동으로 위치를 옮겨주거나, while문과 case문을 이용한다.
 ![image](https://user-images.githubusercontent.com/43934522/142739589-15d85ac1-2772-4dcc-9dba-985020b90658.png "수동으로 처리하는 모습")



 ![image](https://user-images.githubusercontent.com/43934522/142739593-69735ac8-da82-481a-9c3d-d869ea4f314b.png)

위에 보이는 case안의 a), b) c)는 각각 옵션을 뜻하고 a는 argument를 요구한다는 의미이다.
입맛대로 옵션 설정을 할 수 있기 때문에 코드를 더욱 간편하게 만들 수 있다.


