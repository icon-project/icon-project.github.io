ICON Dev Guide
==============
아이콘은 아이콘 재단의 블록체인입니다.
본 문서에서는 아이콘의 개발자용 툴인 T-Bears 와, 아이콘에서 제공하는 파이썬 SDK, 자바 SDK를 활용하여 기초적인 개발을 도와주는 Guide입니다.
따라하기 쉬운 Sample 코드를 활용하여 주어진 상황별로 튜토리얼을 진행해 봅니다. 본 가이드는 아래의 아이콘 github의 내용을 기반으로 작성되었습니다.

<br></br>
아이콘 홈페이지 : <https://icon.foundation/?lang=ko>. 

아이콘 T-Bears : <https://github.com/icon-project/t-bears>. 

아이콘 파이썬 SDK : <https://github.com/icon-project/icon-sdk-python>. 

아이콘 자바 SDK : <https://github.com/icon-project/icon-sdk-java>

<br></br>


## 순서

1. [T-Bears 설치하기](#1-t-bears-설치하기)
    * [공통](#공통)
		* MacOS
		* Ubuntu Linux

    * [Git의 코드를 다운로드하여 설치하기](#1-git의-코드를-다운로드하여-설치하기)
    
    * [PyPI 통해 설치하기](#2-pypI-통해-설치하기)

	* [Docker 활용하기](#3-docker-활용하기)

2. [T-Bears 활용하기](#2-t-bears-활용하기)
    * [서비스](#1-서비스)
        * 서비스 시작 
        * 서비스 중단
        * 서비스에 배포된 SCORE 삭제

    * [SCORE 배포하기](#2-score-배포하기)
        * init으로 생성한 SCORE 프로젝트 배포하기
        * samples로 생성한 SCORE 배포하기

    * [배포한 SCORE의 메소드 호출하기](#3-배포한-score의-메소드-호출하기)
        * init을 통해 생성한 SCORE의 'hello' 메소드 호출
        * samples를 통해 생성한 SCORE("standard_token.py")의 'name' 메소드 호출

    * [트랜잭션](#4-트랜잭션)
        * 트랜잭션 요청
        * 트랜잭션 결과 확인

    * [ICX](#5-icx)
        * ICX 보내기
        * ICX 잔고 확인하기

3. [SDK 개발환경 구축하기](#3-sdk-개발환경-구축하기)
	* [파이썬](#파이썬-개발-환경-구축하기) 
		* 파이썬이 설치되어있지 않다면?  
		* Git이 설치되어있지 않다면?
		* 파이썬SDK 설치하기
		* 1. Git의 코드를 다운로드하여 설치하는 방법
		* 2. pip install 을 활용한 방법

	* [자바](#자바-개발-환경-구축하기)
		* 자바가 설치되어있지 않다면?
		* IDE가 설치되어있지 않다면?(이클립스)  
		* Git이 설치되어있지 않다면?
		* 자바SDK 설치하기
		* 1. Git의 코드를 다운로드하여 설치하는 방법
		* 2. Maven dependency setting
			* Maven dependency 적용 확인
		* 3. Gradle dependency setting
			* Gradle dependency 적용 확인
			
3. [SDK 활용하기](#4-sdk-활용하기)
	* [지갑사용하기](#지갑사용하기)
		* wallet create (지갑생성)
		* wallet load (지갑로드)
		* wallet store (지갑저장)
		
	
	* [네트워크와 연결하기](#네트워크와-연결하기)
	
	
	* [트랜젝션 만들어보기](#트랜젝션-만들어보기)
		* ICX를 전송하는 트랜젝션
		* 토큰을 전송하는 트랜젝션
		* 메세지를 전송하는 트랜젝션
		* SCORE를 Deploy 하는 트랜젝션


	* [Block에서 정보 가지고오기](#block에서-정보-가지고-오기)
	
	* [간단한 조회 해 보기](#간단한-조회-해-보기)
	 
	* [불러온 Block에서-정보-불러오기](#block에서-정보-불러오기)
		* 1.특정 주소의 Balance 호출하기
		* 2.현재 발행된 코인의 총 수 호출하기
		* 3.score주소를 통해서 score api를 모두 호출하기
		
	* [Block을 불러오는 세 가지 방법](#block을-불러오는-세-가지-방법)
		* 1.가장 마지막에 만들어진 Block 불러오기
		* 2.특정높이의 Block 불러오기
		* 3.Block의 hash 값을 통하여 불러오기
		
	* [불러들인 Block에서 정보 불러오기](#불러들인-block에서-정보-불러오기)
	
	* [내가 보낸 트랜젝션의 결과 확인해 보기](#내가-보낸-트랜젝션-결과-확인하기)

<br>

---
# T-Bears 소개
*T-Bears*는 *ICON*의 개발자용 CLI 로컬 테스트 개발툴입니다.
*ICON* 네트워크는 *JSON-RPC* 방식으로 통신하며, *T-Bears*는 로컬에서 *localhostJSON-RPC* 응답을 지원합니다.
현재, [GitHub](https://github.com/icon-project/t-bears.git)에 소스가 공개 되어있으며, 사용을 원하는 개발자는 언제든 다운로드 받아 사용할 수 있습니다.

---
# 1. T\-Bears 설치하기
T-Bears를 설치하는 방법에는 세가지가 있으며 다음과 같습니다.
```
1. Git의 코드를 다운로드하여 설치하기
2. PyPI 통해 설치하기
3. Docker 활용하기
```
>세가지 방법을 진행하기에 앞서 1, 2번 방법의 경우 공통적으로 구성해야할 환경은 아래와 같습니다.


#### 공통

#### *MacOS*
* Home Brew(macOS용 패키지 관리자)를 설치합니다.

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
* LevelDB, autoconf, automake, libtool, pkg-config 를 설치합니다.
```
brew install leveldb
brew install autoconf automake libtool pkg-config
```

* RabbitMQ를 설치합니다.
```
brew install rabbitmq
brew services start rabbitmq
```

* wget, git을 설치합니다.
```
brew install wget // wget 설치
brew install git // git 설치
```

* Anaconda를 설치합니다.
```
wget https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh 
bash ./Anaconda3-5.2.0-Linux-x86_64.sh
```

* Virtualenv (python3 venv 대체 가능)를 설치합니다.
```
pip3 install virtualenv
```
---
#### *Ubuntu Linux*
* apt-get 패키지 리스트를 업데이트 및 업그레이드 합니다.
```
sudo apt-get update
sudo apt-get upgrade
```

* LevelDB, libsecp256k1, autoconf, automake, libtool, pkg-config 를 설치합니다.
```
sudo apt-get install libleveldb1 libleveldb-dev
sudo apt-get install autoconf automake libtool pkg-config
sudo apt-get install libsecp256k1-dev
```

* RabbitMQ를 설치합니다.
```
sudo apt-get install rabbitmq-server
sudo service rabbitmq-server start
```

* wget, git을 설치합니다.
```
sudo apt-get install wget
sudo apt-get install git
```

* Anaconda를 설치합니다.
```
wget https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh
bash ./Anaconda3-5.2.0-Linux-x86_64.sh
```

* Virtualenv (python3 venv 대체 가능)를 설치합니다.
```
pip3 install virtualenv
```
---
#### 1. Git의 코드를 다운로드하여 설치하기
* git 설치가 완료되면, tbears Git repository에 존재하는 코드를 `t-bears` 디렉토리로 clone 합니다.

```
git clone https://github.com/icon-project/t-bears.git t-bears
cd t-bears
```
* 생성된 t-bears 디렉토리로 이동하여 가상환경을 조성하여 진입합니다.
```
virtualenv -p python3 venv  
source venv/bin/activate
```
* `build.sh` 를 실행합니다. (*script* 실행 시 `dist` 디렉토리가 생성되며 디렉토리 안에 `.whl` 파일과 `.tar.gz` 두가지 파일이 함께 생성됩니다.)
```
(venv) ./build.sh
```
* 생성된 `dist` 디렉토리의 `.whl` 파일과 `pip` 를 이용하여 T-Bears를 설치합니다.
```
(venv) pip install ./dist/tbears-1.0.5-py3-none-any.whl
```
---
#### 2. PyPI 통해 설치하기
> 보다 간단한 방법으로 가급적 두번째 방법을 따라 설치하기를 권장합니다. 

* work 디렉토리 생성
```
mkdir work
cd work
```
* work 디렉토리에 가상환경을 조성하여 진입합니다.
```
virtualenv -p python3 .
source ./bin/activate
```
* `pip` 를 이용하여 T-Bears를 설치합니다.
```
(work) pip install tbears
```
---
#### 3. Docker 활용하기
* Docker 설치하기
```
curl -fsSL https://get.docker.com/ | sudo sh
```
* Docker image 가져오기
```
docker pull iconloop/tbears
```
* 새로운 Docker 컨테이너 생성하기
```
docker create --name local-tbears -p 9000:9000 -it iconloop/tbears
```

* 생성한 Docker 컨테이너 실행하기
```
docker start -i local-tbears
```

* 앞서 설명한 3단계의 과정을 `run` 명령을 통해 한번에 실행할 수 있습니다.
```
docker run -it --name local-tbears -p 9000:9000 iconloop/tbears
```

---
# 2. T\-Bears 활용하기

###  *1. 서비스*

*1.1 서비스 시작*

```
tbears start
```
출력되는 메시지를 통해 서비스가 시작되는 것을 확인할 수 있습니다.
```
Started tbears service successfully
```
<br></br>
*1.2 서비스 중단*

```
tbears stop          
```
출력되는 메시지를 통해 서비스가 중단되는 것을 확인할 수 있습니다.
```
Stopped tbears service successfully
```
<br></br>
*1.3 서비스에 배포된 SCORE 삭제*

```
tbears clear         
```
출력되는 메시지를 통해 서비스에 배포된 SCORE가 삭제되는 것을 확인할 수 있습니다. (`tbears clear`의 경우 `tbears stop`을 통해 T-Bears 서비스가 중단된 상태에서만 가능합니다.)
```
Cleared SCORE deployed on tbears successfully
```
> `tbears stop` 실행 후 `tbears clear`를 실행하지 않고 `tbears start`를 통해 서비스를 다시 시작하였을 경우 이전에 배포하였던 *SCORE* 가 유지됩니다.
---
### *2. SCORE 배포하기*

*2.1 init으로 생성한 SCORE 프로젝트 배포하기*

init 명령어를 통해 SCORE 프로젝트를 생성합니다.(현재 디렉토리에 `1.keystore_test1 2.tbears_cli_config.json 3.tbears_server_config.json`를 생성합니다. 직접 `tbears genconf`를 통해 생성할 수도 있습니다.)

> `keystore_test1` : 테스트 계정을 위한 keystore 파일을 의미합니다.

> `tbears_cli_config.json` : start, stop, samples, clear, init, keystore를 제외한 모든 T-Bears CLI 명령어를 수행할 때 디폴트 파라미터와 초기 설정을 위해 사용됩니다..

> `tbears_server_config.json` : T-Bears 서비스를 시작할때 초기 설정과 파라미터를 "tbears_server_config.json" 통해 설정합니다.

```
tbears genconf (optional : 미실행시 init단계에서 설정파일을 생성합니다.)
tbears init myproject ABCToken
```
예제와 같이 명령어를 실행 시 `myproject` 디렉토리가 생성되어있어야 하며, `myproject` 디렉토리 안에 있는 파일 목록은 다음과 같습니다.
```
__init__.py  myproject.py package.json
```

> `__init__py` : 프로젝트 디렉토리가 파이썬 패키지로 인식되도록 하는 파일을 의미합니다.

> `myproject.py` : SCORE 파일을 의미하며, 예제의 명령어와 같이 실행 시 ABCToken 클래스가 정의되어 있습니다. ABCToken 클래스의 경우 "hello" 메소드를 예시로 가지고 있습니다.

> `package.json` : SCORE가 로드 되었을때 필요한 정보를 가지고 있으며, "main_file" 와 "main_class" 를 필요로 합니다.

생성된 프로젝트를 T-Bears 서비스에 배포합니다. (`-u` 옵션을 적용하지 않을 경우, 디폴트 값으로 `http://127.0.0.1:9000/api/v3` 가지며, 따라서 예제의 tbears deploy의 경우 tbears start를 통해 T-Bears 서비스가 가동 중인 상태에서만 가능합니다.)
```
tbears deploy myproject
```
출력 
```
Send deploy request successfully.
If you want to check SCORE deployed successfully, execute txresult command
transaction hash: 0x6fb79dec3a8f16478a4c1fb759b3df6defef86cdebe402b5a61e69ef4b3a9080
```
`tbears txresult` 에 앞서 확인한 `transaction hash` 값을 입력으로 하여 실행할 경우 해당 트랜잭션의 결과를 확인할 수 있습니다.
```
Transaction result: {
    "jsonrpc": "2.0",
    "result": {
        "txHash": "0xb6c94d6b05999bedcdbe45a162ea27d52e71797293f158dd0ef4ea5197317acc",
        "blockHeight": "0xe",
        "blockHash": "0x7778e2dba93f82d330759b40a130e46bd5b5a02a49ef3281ce973c9161fe2bc5",
        "txIndex": "0x0",
        "to": "cx0000000000000000000000000000000000000000",
        "scoreAddress": "cxd3bc5f3e59ba351350baa77f197aa49694e09536",
        "stepUsed": "0x251f070",
        "stepPrice": "0x0",
        "cumulativeStepUsed": "0x251f070",
        "eventLogs": [],
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "status": "0x1"
    },
    "id": 1
}
```
> `txHash` : 트랜잭션의 해시값을 의미합니다.

> `blockHeight` : 해당 트랜잭션을 포함하는 블록의 높이를 의미합니다.

> `blockHash` : 해당 트랜잭션을 포함하는 블록의 해시값을 의미합니다.

> `txIndex` : 트랜잭션이 포함된 블록에서의 트랜잭션 인덱스를 의미합니다.

> `to` : 트랜잭션을 받는 수신자의 주소를 의미합니다.

> `scoreAddress` : 새로운 SCORE를 생성한 경우 SCORE의 주소를 의미합니다. (optional)

> `stepUsed` : 해당 트랜잭션에서 소비된 "step"의 양을 의미합니다.

> `stepPrice` : 해당 트랜잭션에서 소비된 "step" 당 지불할 가격을 의미합니다.

> `cumulativeStepUsed` : 해당 트랜잭션과 해당 트랜잭션을 포함하는 블록 내 모든 진행 중인 트랜잭션의 "stepUsed"을 합친 양을 의미합니다.

> `eventLogs` : 해당 트랜잭션이 생성한 eventlogs를 모은 배열을 의미합니다.

> `logsBloom` : 관련된 eventlogs를 빠르게 수집하기 위한 Bloom 필터를 의미합니다.

> `id` : `Request Object`의 `id`와 동일한 값을 가지고 있어야 합니다. `Request Object`의 `id`는 반드시 String, Number, Null 중 한가지 값을 가지며, 사용자에 의해 정의됩니다. 값으로 Null 사용을 권장하지 않으며, 숫자의 경우 소수값을 사용할 수 없습니다.

> `status` : 트랜잭션 성공 시 1의 값을 가지며, 실패시 0의 값을 가집니다.

<br></br>
*2.2 samples로 생성한 SCORE 배포하기*

`tbears samples` 명령을 통해 샘플을 생성합니다.
```
tbears samples
```
`standard_token` 과 `standard_crowd_sale` 디렉토리가 생성되어 있어야 하며, 각각의 디렉토리 안에 있는 파일 목록은 다음과 같습니다. (예제는 `standard_token` 을 배포하는 것으로 진행하였습니다.)
```
standard_crowd_sale:
__init__.py            package.json           standard_crowd_sale.py

standard_token:
__init__.py       package.json      standard_token.py
```

> `standard_crowd_sale.py` : 
SCORE 파일을 의미하며, 예제의 명령어와 같이 실행 시 StandardCrowdSale 클래스가 정의되어 있습니다. StandardCrowdSale 클래스의 경우 호출 가능한 다양한 메소드(`total_joiner_count`, `check_goal_reached`, `safe_withdrawal` 등)를 가지고 있습니다.

> `standard_token.py` : SCORE 파일을 의미하며, 예제의 명령어와 같이 실행 시 StandardToken 클래스가 정의되어 있습니다. StandardToken 클래스의 경우 호출 가능한 다양한 메소드(`name`, `symbol`, `decimals` 등)를 가지고 있습니다.

생성된 standard_token 디렉토리로 이동하여 설정파일을 생성합니다.
```
cd standard_token
tbears genconf (명령어 실행 후 생성된 설정 파일 중 tbears_cli_config.json을 수정합니다.)
```

수정한 `tbears_cli_config.json`의 내용은 다음과 같습니다.
```json
{
    "uri": "http://127.0.0.1:9000/api/v3",
    "nid": "0x3",
    "keyStore": null,
    "from": "hxe7af5fcfd8dfc67530a01a0e403882687528dfcb",
    "to": "cx0000000000000000000000000000000000000000",
    "stepLimit": "0x3000000",
    "deploy": {
        "mode": "install",
        "scoreParams": {
            "initialSupply":"1000",
            "decimals":"10"
        }
    },
    "txresult": {},
    "transfer": {}
}
```

> `uri` : Request를 보낼 URI를 의미합니다.

> `nid` : 네트워크 ID를 의미합니다. T-Bears의 경우 0x3 을 사용합니다.

> `keyStore` : Keystore 파일의 위치를 의미합니다.

> `from` : "From" 주소를 의미합니다. keyStore가 설정되어 있을 경우 keyStore의 설정을 따릅니다.

> `to` : "To" 주소를 의미합니다. `deploy.mode`가 `install`일 경우 생략 가능하며, `deploy.mode`가 `install`이나 생략하지 않을 경우 scoreAddress를 `cx0000~` 으로 설정하여야 합니다.

> `stepLimit` : 디폴트 값으로 0x3000000를 가지며, stepLimit 값을 의미합니다. (optional)

> `deploy` : deploy 명령을 위한 설정을 의미합니다.

> `deploy.mode` : 배포 모드를 의미하며 "install" 과 "update" 중 선택할 수 있습니다.
>+ "install" : 새로운 SCORE를 배포합니다. `to` 의 값이 `cx0000~`로 설정되어야 합니다.
>+ "update" : 기존에 배포된 SCORE를 업데이트 합니다.

> `deploy.scoreParams` : SCORE를 배포하는 경우에 install 또는 update에 필요한 파라미터를 의미합니다. 필요한 파라미터의 경우 SCORE파일의 on_install() 또는 on_update() 메소드를 통해 확인할 수 있습니다.

> `txresult` : `txresult` 명령을 위한 설정을 의미합니다. 

> `transfer` : `transfer` 명령을 위한 설정을 의미합니다.

생성한 프로젝트를 T-Bears 서비스에 배포합니다.('-c' 옵션을 통해 수정한 설정파일을 적용합니다.)
```
tbears deploy -c ./tbears_cli_config.json .
```
출력:
```
If you want to check SCORE deployed successfully, execute txresult command
transaction hash: 0xb6c94d6b05999bedcdbe45a162ea27d52e71797293f158dd0ef4ea5197317acc
```
`tbears txresult` 에 앞서 확인한 `transaction hash` 값을 입력으로 하여 실행할 경우 해당 트랜잭션의 결과를 확인할 수 있습니다.

---
### *3. 배포한 SCORE의 메소드 호출하기*

*3.1 init을 통해 생성한 SCORE의 'hello' 메소드 호출*

`tbears call`의 필수 요소인 json파일(call.json)을 다음과 같이 작성합니다
```json
{
  "jsonrpc": "2.0",
  "method": "icx_call",
  "params": {
    "from": "hxef73db5d0ad02eb1fadb37d0041be96bfa56d4e6",
    "to": "cx53d5080a7d8a805bb10eb9bc64637809dc910832",
    "dataType": "call",
    "data": {
      "method": "hello"
    }
  },
  "id": 1
}
```
> `icx_call` : SCORE의 메소드(external function)을 호출합니다.

> `from` : 메시지를 보내는 사람의 주소를 의미합니다.

> `to` : 메시지를 처리할 SCORE 주소를 의미합니다.

> `data.method` : `tbears scoreapi` 명령을 통해 `scoreAddress` 를 입력으로 하여 `method` 에 들어갈 메소드를 확인할 수 있습니다.

> `data.params` : 호출하는 메소드가 필요로 하는 파라미터를 의미합니다. (optional)

`tbears scoreapi` 를 통해 해당 SCORE의 호출 가능한 메소드를 확인한 결과는 다음과 같습니다.
```json
SCORE API: [
    {
        "type": "fallback",
        "name": "fallback",
        "inputs": []
    },
    {
        "type": "function",
        "name": "hello",
        "inputs": [],
        "outputs": [
            {
                "type": "str"
            }
        ],
        "readonly": "0x1"
    }
]
```
SCORE의 `hello` 메소드를 호출합니다.
```
tbears call call.json
```
출력
```
response : {
        "jsonrpc": "2.0",
        "result": "hello",
        "id": 1
    }
```
<br></br>
*3.2 samples를 통해 생성한 SCORE("standard_token.py")의 'name' 메소드 호출*

`tbears call`의 필수 요소인 json파일(call.json)을 다음과 같이 작성합니다
```json
{
  "jsonrpc": "2.0",
  "method": "icx_call",
  "params": {
    "from": "hxef73db5d0ad02eb1fadb37d0041be96bfa56d4e6",
    "to": "cx02b13428a8aef265fbaeeb37394d3ae8727f7a19",
    "dataType": "call",
    "data": {
      "method": "name"
    }
  },
  "id": 1
}
```

SCORE의 `name` 메소드를 호출합니다.

```
tbears call call.json
```
출력
```
response : {
    "jsonrpc": "2.0",
    "result": "SampleToken",
    "id": 1
}
```
---
### *4. 트랜잭션*

*4.1 트랜잭션 요청*

`tbears sendtx` 의 필수 요소인 json파일(send.json)을 다음과 같이 작성합니다
```json
{
  "jsonrpc": "2.0",
  "method": "icx_sendTransaction",
  "params": {
    "version": "0x3",
    "from": "hxe7af5fcfd8dfc67530a01a0e403882687528dfcb",
    "value": "0x0",
    "stepLimit": "0x3000000",
    "timestamp": "0x573117f1d6568",
    "nid": "0x3",
    "nonce": "0x1",
    "to": "hxaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa",
    "dataType": "call",
    "data": {
      "method": "method to invoke",
      "params": {
        "key": "value"
      }
    }
  },
  "id": 1
}
```
> `version` : 프로토콜의 버전의 의미합니다. (V3를 사용하는 경우 "0x3"로 설정합니다.)

> `from` : 트랜잭션을 생성한 지갑의 주소를 의미합니다.

> `to` : 코인을 받을 지갑의 주소, 또는 트랜잭션을 실행할 SCORE의 주소를 의미합니다.

> `value` : 디폴트 값으로 0을 가지며, 송금할 ICX의 양을 의미합니다. (단위 : loop, 1 icx = 10 ^ 18 loop)

> `stepLimit` : 트랜잭션에 의해 소비될 수 있는 step의 최대치를 의미합니다. 

> `timestamp` : 트랜잭션이 생성된 시간을 의미합니다. (μs 단위로 표시합니다.)

> `nid` : 네트워크 ID를 의미합니다. ("0x1" : 메인넷, "0x2" : 테스트넷, etc)

> `nonce` : 트랜잭션 해시값의 충돌을 막기 위한 임의의 수를 의미합니다.

> `dataType` : 데이터의 타입을 의미합니다. (call, deploy, message)

> `data` : 데이터의 내용은 데이터타입에 따라 다양하게 나타나며, [Parameters - data](https://github.com/icon-project/icon-rpc-server/blob/master/docs/icon-json-rpc-v3.md#sendtxparameterdata) 를 통해 데이터타입에 따른 데이터의 내용을 확인할 수 있습니다. 


트랜잭션 서명에 필요한 keystore 파일을 생성합니다.
```
tbears keystore key
```
출력
```
Input your keystore password:
Retype your keystore password:
Made keystore file successfully
```
트랜잭션을 요청합니다.('-k' 옵션을 통해 생성한 keystore 파일을 적용합니다.)

```
tbears sendtx -k key send.json
```
출력
```
input your keystore password:
Send transaction request successfully.
transaction hash: 0xd8801c6b0f8c63642859fe3dbd2d4ee030149d76258abc1764335b397cc073e3
```
<br></br>
*4.2 트랜잭션 결과 확인*

`txresult`를 통해 요청한 트랜잭션의 결과를 확인합니다.
```
tbears txresult 0xd8801c6b0f8c63642859fe3dbd2d4ee030149d76258abc1764335b397cc073e3
```
출력 
```
Transaction result: {
    "jsonrpc": "2.0",
    "result": {
        "txHash": "0xd8801c6b0f8c63642859fe3dbd2d4ee030149d76258abc1764335b397cc073e3",
        "blockHeight": "0x12e",
        "blockHash": "0x291d3f995475811402e2db9cf44f577d2571ef0d2c225d49829f522f6d0ef28b",
        "txIndex": "0x0",
        "to": "hxaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa",
        "stepUsed": "0xf4a10",
        "stepPrice": "0x0",
        "cumulativeStepUsed": "0xf4a10",
        "eventLogs": [],
        "logsBloom": "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
        "status": "0x1"
    },
    "id": 1
}
```
---
### *5. ICX*

*5.1 ICX 보내기*

제네시스 블록과 Test Account를 제외한 다른 address에는 ICX가 없으므로, Test Account에서 나의 지갑으로 ICX를 보내는 예제를 작성하였습니다.

ICX를 보내기 위해 먼저 keystore 파일을 통해 지갑의 주소를 확인합니다. (예제의 경우 `keystore` 파일의 이름이 `key` 이므로 `cat key`를 통해 확인하였습니다.)
```
{"address": "hx408a524b9400991184b4130bdf98a6a7aa5af3e6", ...}
```

Test Account에서 나의 지갑으로 1 icx를 보냅니다. (`tbears transfer`의 전송 단위는 1 loop이며 1 icx = 10 ^ 18 loop 입니다.)
```
tbears transfer -k keystore_test1 -f hxe7af5fcfd8dfc67530a01a0e403882687528dfcb hx408a524b9400991184b4130bdf98a6a7aa5af3e6 1e18
```
> `keystore_test1` : Test Account의 정보를 가지는 keystore 파일입니다. (*비밀번호* : *'test1_Account'*)

출력
```
Input your keystore password:
Send transfer request successfully.
transaction hash: 0x33f4c69eabf45ab62f216e7498e01b3c684324543fa92b8b9a3c288d340813c8
```
> 이전에 `tbears start` 를 통해 *T-Bears* 서비스를 실행했다면, `-k` 옵션을 '생략' 또는 '부적절한 입력' 하더라도 *T-Bears* 자체가 에뮬레이터를 돌리는 것이므로 트랜잭션이 성공합니다.

<br></br>
*5.2 ICX 잔고 확인하기* 

`tbears balance`를 통해 해당 주소의 잔고를 확인할 수 있습니다.
```
tbears balance hx408a524b9400991184b4130bdf98a6a7aa5af3e6
```
출력

```
balance in hex: 0xde0b6b3a7640000
balance in decimal: 1000000000000000000
```
---
# 3. SDK 개발환경 구축하기
현재 아이콘의 개발자 배포용 SDK는 파이썬, 자바 두 가지 종류가 있습니다. 개발자들을 위해서 Git을 통해 배포되고 있으며, 각각 언어들을 활용해 개발자들은 지갑을 만들거나, Dapp을 만들어볼 수 있습니다. 

## 파이썬 개발 환경 구축하기

작업환경

----------------------

	OS X
	
	Python 3.6.x
	
	Git
	
<br></br>



>파이썬이 설치되어있지 않다면?

#### 파이썬 설치 
파이썬이 설치되어있지 않다면, Home Brew를 통하여 설치해 줍니다. 
Home Brew[<https://brew.sh>] 를 통하여 설치를 진행합니다.
	
	brew install python3


<br></br>

>Git이 설치되어있지 않다면?

#### Git 설치 
Git이 설치되어있지 않다면, Home Brew를 통하여 설치해 줍니다. 
Home Brew[<https://brew.sh>] 를 통하여 설치를 진행합니다.

	brew install git

<br></br>

### 파이썬 SDK 설치하기
파이썬을 활용하여 개발할 수 있도록 아이콘재단에서 공개한 파이썬 SDK를 다운로드 받는 방법은 아래와 같이 두 가지 입니다.  

	1. Git의 코드를 다운로드하여 설치하는 방법
	2. pip install 을 활용한 방법




<br></br>

#### 1. Git의 코드를 다운로드하여 설치하는 방법

Git의 코드를 다운로드하여 설치하는 방법은 아이콘이 지원하는 SDK의 가장 최신버전을 사용할 수 있는 방법입니다. 


* git을 통하여 원격 레포지토리에 있는 SDK를 원하는 위치에 clone 합니다.

		git clone https://github.com/icon-project/icon-sdk-python

* SDK 파일이 clone 된 위치에서 아래와같이 입력합니다.

		python setup.py install 


<br></br>


#### 2. pip install 을 활용한 방법
제일 간단한 방법입니다. pip는 파이썬을 설치하게 되면 같이 설치되는 파이썬으로 작성된 패키지 라이브러리 관리 시스템으로서, 사용자에게 간단한 명령어로 pypl(파이썬 패키지 인덱스)에 등록되어있는 라이브러리를 다운로드받도록 지원해 줍니다.

* pip명령어를 통하여 iconSDK를 설치합니다. 

		pip install iconsdk
	

<br></br>



## 자바 개발 환경 구축하기

작업환경

---------------------

	OS X
	
	Java(TM) SE Runtime Environment 18.3
	
	Eclipse Version: 2.2.200.v20180611-0500
	
	Maven 	OR	Gradle
	
<br></br>


> 자바가 설치되어있지 않다면?

#### Java 설치 
아래의 자바 홈페이지 설치 경로에서 자바를 다운로드 받습니다. 

* 홈페이지 : <https://java.com/ko/download/>


<br></br>

>IDE가 없다면?

#### Eclipse 설치 
아래의 이클립스 홈페이지 주소에서 이클립스를 다운로드 받습니다. 

* 홈페이지 : <https://www.eclipse.org/downloads/>

<br></br>


>Git이 설치되어있지 않다면?

#### Git 설치 
Git이 설치되어있지 않다면, Home Brew를 통하여 설치해 줍니다. 
Home Brew[<https://brew.sh>] 를 통하여 설치를 진행합니다.
	
	brew install git

<br></br>

### 자바 SDK 설치하기
자바를 활용하여 개발할 수 있도록 아이콘재단에서 공개한 자바SDK를 다운로드 받는 방법은 아래와 같이 세 가지 입니다.  

	1. Git의 코드를 다운로드하여 설치하는 방법
	2. Maven dependency 설정을 통한 다운로드 방법 (권장)
	3. Gradle dependency 설정을 통한 다운로드 방법 (권장)

<br></br>


#### 1. Git의 코드를 다운로드하여 설치하는 방법

Git의 코드를 다운로드하여 설치하는 방법은 아이콘이 지원하는 SDK의 가장 최신버전을 사용할 수 있는 방법입니다. 


* git을 통하여 원격 레포지토리에 있는 SDK를 원하는 위치에 clone 합니다.

		git clone https://github.com/icon-project/icon-sdk-java

* 다운로드받은 SDK 라이브러리를 import 하여 사용합니다. 

<br></br>


#### 2. Maven dependency setting
* 만들어진 Maven 패키지 안의 POM.xml 파일에 아래와 같이 삽입합니다. 

1. icon-sdk를 위한 Dependencies 설정

	+ Maven을 사용할 경우

```
<dependencies>
	<!-- icon-sdk를 위한 dependency -->
  	<dependency>
		<groupId>foundation.icon</groupId>
		<artifactId>icon-sdk</artifactId>
		<version>0.9.1</version>
  	</dependency>
  
	<!-- okhttp3를 위한 dependency -->
 	<dependency>
  		<groupId>com.squareup.okhttp3</groupId>
  		<artifactId>logging-interceptor</artifactId>
  		<version>3.11.0</version>
	</dependency>
</dependencies>
```

 ``` <dependencies> ``` 부터 ``` </dependencies> ``` 까지 내용을 붙여 넣습니다. 
 
* 본 Sample Code에서는 
logging-interceptor를 활용한 Log를 위하여 dependencies에 logging-interceptor도 추가하였습니다.(옵션입니다.)

* 예시 (POM.xml) // 현재 버전 0.9.4

```   
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>java_icon</groupId>
	<artifactId>java_icon</artifactId>
	<version>0.0.1-SNAPSHOT</version>


	<dependencies>
		<!-- icon-sdk를 위한 dependency -->
  		<dependency>
  			<groupId>foundation.icon</groupId>
  			<artifactId>icon-sdk</artifactId>
  			<version>0.9.4</version>
  		</dependency>
  
		<!-- logging-interceptor를 위한 dependency -->
 		<dependency>
  			<groupId>com.squareup.okhttp3</groupId>
  			<artifactId>logging-interceptor</artifactId>
  			<version>3.11.0</version>
		</dependency>
  	</dependencies>
  
	<build>
		<sourceDirectory>src</sourceDirectory>
		<plugins>
		<plugin>
			<artifactId>maven-compiler-plugin</artifactId>
			<version>3.7.0</version>
			<configuration>
			<release>10</release>
			</configuration>
		</plugin>
		</plugins>
	</build>
</project>

```

위와 같이 dependency 설정을 마치면,  refreash를 해 주어야 원격 아카이브에서 jar 파일을 다운받습니다. 

```[생성된 패키지 우클릭 --> Maven --> UpdateProject]```

#### Maven dependency 적용 확인


정상적으로 다운로드가 완료 되면, 프로젝트 내에 있는 
```[Maven Dependencies]```에서 
```icon-sdk-버전.jar``` 
```logging-interceptor-버전.jar``` 가 다운로드 되어있는것을 확인 할 수 있습니다. 


#### 3. Gradle dependency setting

	
	만들어진 Gradle 패키지 안의 build.Gradle 파일에 아래와 같이 삽입합니다.
	

```
    implementation 'foundation.icon:icon-sdk:0.9.5'
```


* 예시 (build.Gradle) // 현재 버전 0.9.5
	

```
/*
 * This build file was generated by the Gradle 'init' task.
 *
 * This generated file contains a sample Java Library project to get you started.
 * For more details take a look at the Java Libraries chapter in the Gradle
 * user guide available at https://docs.gradle.org/4.3/userguide/java_library_plugin.html
 */

// Apply the java-library plugin to add support for Java Library
apply plugin: 'java-library'

// In this section you declare where to find the dependencies of your project
repositories {
    // Use jcenter for resolving your dependencies.
    // You can declare any Maven/Ivy/file repository here.
    jcenter()
}

dependencies {
    // This dependency is exported to consumers, that is to say found on their compile classpath.
    api 'org.apache.commons:commons-math3:3.6.1'

    // This dependency is used internally, and not exposed to consumers on their own compile classpath.
    implementation 'com.google.guava:guava:23.0'

    // Use JUnit test framework
    testImplementation 'junit:junit:4.12'
    
    implementation 'foundation.icon:icon-sdk:0.9.5'
    
}
```

위와 같이 dependency 설정을 마치면,  Refreash를 해주어야 원격 아카이브에서 jar 파일을 다운받습니다. 

```[생성된 패키지 우클릭 --> Gradle --> Refreash Gradle Project]```

#### Gradle dependency 적용 확인

정상적으로 다운로드가 완료 되면, 프로젝트 내에 있는 
```[Project and External Dependencies]```에서 
```icon-sdk-버전.jar```  가 다운로드 되어있는 것을 확인 할 수 있습니다. 



<br></br>


# 4. SDK 활용하기
현재 아이콘의 개발자 배포용 SDK는 파이썬, 자바 두 가지 종류가 있습니다. 개발자들을 위해서 Git을 통해 배포되고 있으며, 각각 언어들을 활용해 개발자들은 지갑을 만들거나, 내 지갑의 토큰 balance 를 조회할 수 있습니다.


## 지갑사용하기
아이콘의 지갑은 여러 방법을 통하여 만들 수 있습니다. KeyWallet은 Keystore 파일을 통하여 백업할 수 있고, Keystore 파일을 통하여 다시 불러들일 수 있습니다. 가장 간단하게 아이콘의 지갑을 만나볼 수 있는 방법중 하나는 크롬의 [확장프로그램](<https://chrome.google.com/webstore/detail/iconex/flpiciilemghbmfalicajoolhkkenfel>)을 다운로드받아 사용하는 것입니다.

지갑에 관련된 작업은 크게 3가지로 분류됩니다.

#### wallet create (지갑생성)
>지갑을 임의로 생성.


#### wallet store (지갑저장)
> Keystore 파일을 경로와 비밀번호를 지정하여 저장.


#### wallet load (지갑로드)
> Private Key를 통하여 지갑을 로드.

> 비밀번호와 Keystore 파일을 통하여 로드.

Keystore 파일을 저장할 경우, 비밀번호를 통해서 Keystore 파일 내의 정보를 암호화합니다. 따라서, Keystore 파일을 불러올 경우에는 저장할 때 입력했던 비밀번호를 사용하여 복호화 하게 됨으로, 저장시 사용하였던 비밀번호가 필수적으로 필요합니다. 지갑을 만들경우 생성되는 개인키는 토큰 또는 ICX를 전송할 때 사용됨으로, 반드시 본인이 별도로 보안이 유지되는 곳에 기록해 두어야 하며, 유출을 주의하여야 합니다. 각 언어별 예제는 아래 순서대로 안내되어 있습니다.

	1. 자바 SDK로 지갑 만들기
	2. 파이썬 SDK로 지갑 만들기

<br></br>


**wallet create (지갑생성)**

 SDK에 내장된 함수를 통하여 지갑 인스턴스를 생성합니다.

* ##### 자바 SDK로 실행


		KeyWallet KeyWallet = foundation.icon.icx.KeyWallet.create();
		System.out.println("지갑 주소 : " +  KeyWallet.getAddress());


	실행결과

		지갑 주소 : hx159afb011f442cefc9cd07ef15748877aeeedb32
		// 지갑 주소를 출력합니다
    
*위 출력값은 예시입니다. 지갑마다 출력값은 달라집니다.*

* ##### 파이썬 SDK로 실행

		from iconsdk.wallet.wallet import KeyWallet

		wallet = KeyWallet.create()
		wallet.get_address()

	실행결과

		Out[6]: 'hxfb87932482914ff8ecc750767242e1cbe8b8c41b'
		## 지갑 주소를 출력합니다.
		
*위 출력값은 예시입니다. 지갑마다 출력값은 달라집니다.*

<br></br>

**wallet store (지갑저장)**	
##### keyStore 파일이 저장될 경로를 지정하고, 불러들여진 지갑 인스턴스를 입력한 password로 암호화하여 저장합니다. 
keyStore 파일을 생성할 때는 비밀번호가 필요합니다. 현재 아이콘 크롬 확장프로그램 [지갑](<https://chrome.google.com/webstore/detail/iconex/flpiciilemghbmfalicajoolhkkenfel>)에서는, 숫자, 문자, 특수문자를 모두 포함하여 9자 이상입니다. 현재 SDK상에서는 규약이 강제되어있지 않습니다. 보안성을 위해서는 아이콘 크롬 확장 프로그램에서와 같은 강제성이 부여되는것이 좋습니다.
 
지갑을 저장하기 위해서는 지갑 인스턴스를 불러들여야 합니다. 
저장된 지갑은 T-Bears, aws, SDK, [크롬확장프로그램](<https://chrome.google.com/webstore/detail/iconex/flpiciilemghbmfalicajoolhkkenfel>)을 통해서 사용할 수 있습니다.

* ##### 자바 SDK로 실행

		//keyStore 파일 저장할 경로를 지정합니다. 
		File destinationDirectory = new File("./"); 

		// keysotre 파일의 password
		String password = "qwer1234%";  
	
		// KeyWallet.create() 를 사용하여, 새로 만들어낸 지갑을 저장하였습니다.
		// 불러들인 지갑도 저장할 수 있습니다. 
		String store = KeyWallet.store(KeyWallet.create(), password, destinationDirectory);	
		//parameter : 로드한 지갑객체, 비밀번호 , 저장할 경로 
		//성공시 저장된 key store file 의 파일명이 리턴됩니다. 
	
		System.out.println("Located in = " + destinationDirectory + "/" + store);
		
	실행결과

		Located in = ./UTC--2018-09-13T03-12-16.955572000Z--hxa7e9954ab1d6a2b2faacf5af8b8bbec17ce5c8c8.json


		
* ##### 파이썬 SDK로 실행



		made_wallet=wallet.KeyWallet.create()
		made_wallet.store("/Users/nc0201020/Desktop/makekeystore","User_password123")




<br></br>

**wallet load (지갑로드)**

##### 1. private key 변수를 이용해 지갑을 로드합니다. 

> privateKey를 통해 지갑을 불러들여 보고, 불러들인 지갑의 privateKey를 출력해서 확인해 봅니다. iconex 크롬 익스텐션 또는 모바일 앱에서도 프라이빗 키를 확인할 수 있습니다. 

* ##### 자바 SDK로 실행
		
		//개인키를 사용하여 지갑을 불러옵니다. 
		Bytes Private_Key = new Bytes("625de46fb951054330a58ab6f66c18849afc94797f0d37df6ff18cf8ed573981");
		Wallet Localwallet = KeyWallet.load(Private_Key);

		//불러온 지갑의 주소를 확인합니다.
		System.out.println("주소 = "+Localwallet.getAddress());

		//불러온 지갑의 개인키를 확인하여, 제대로 불러왔는지 확인합니다.
		System.out.println("개인키 = "+KeyWallet.load(Private_Key).getPrivateKey());


	실행결과 

		주소 = hxcc61e31ed6080926d6d6f7d0ac6e1b8b2ee5a9fa
		개인키 = 0x625de46fb951054330a58ab6f66c18849afc94797f0d37df6ff18cf8ed573981



* ##### 파이썬 SDK로 실행
	
		key=bytes.fromhex("625de46fb951054330a58ab6f66c18849afc94797f0d37df6ff18cf8ed573981")
		##개인키를 통해서 지갑 불러오기
		wallet = wallet.load(key)
		##불러온 지갑에서 개인키와 송금주소를 출력
		print("PK : "+wallet.get_private_key());
		print("address : "+wallet.get_address());

	실행결과 

		PK : 625de46fb951054330a58ab6f66c18849afc94797f0d37df6ff18cf8ed573981
		address : hxd4b792110d4be458e74fcab3cab1d820b04bc696

		
<br></br>


##### 2. 기존에 저장되어있는 keystore 파일로 지갑을 로드합니다. 

* ##### 자바 SDK로 실행

		String destinationDirectory = "./Key/";
		String password = "qwer1234%";
		File file = new File(destinationDirectory);
		String Firstfile = file.list()[0];
		System.out.println("첫번째 파일명 : "+Firstfile);
		
		//Key경로 내의 첫 번째 지갑 파일을 불러들인 후, 로드합니다. 
		KeyWallet keyStoreLoad = KeyWallet.load(password, new File(destinationDirectory+Firstfile));
		
		
		//위에서 불러들인 키스토어의 주소를 출력합니다. 
		System.out.println("keyStoreLoad address : " + 
		keyStoreLoad.getAddress());
		
		//위에서 불러들인 키스토어의 개안키를 출력합니다. 
		System.out.println("keyStoreLoad PrivateKey : " + 
		keyStoreLoad.getPrivateKey());


		
	실행결과 

		첫번째 파일명 : UTC--2018-09-13T03-12-16.955572000Z--hxa7e9954ab1d6a2b2faacf5af8b8bbec17ce5c8c8.json
		keyStoreLoad address : hxa7e9954ab1d6a2b2faacf5af8b8bbec17ce5c8c8
		keyStoreLoad address : 0x00fae633e794bdb2b6a3f7e8dad200326fc3f6d097722455ebc936bed7fb4c0130


		
*위 출력값은 예시입니다. 지갑마다 출력값은 달라집니다.*

* ##### 파이썬 SDK로 실행
		##password와 keystore의 위치를 통해 KeyWallet.load을 불러들입니다. 
		wallet = KeyWallet.load("./keystore", "password")
		print(wallet.get_address())
		
	실행결과 
	
		hx4873b94352c8c1f3b2f09aaeccea31ce9e90bd31
		## 지갑주소를 출력합니다.
				
*위 출력값은 예시입니다. 지갑마다 출력값은 달라집니다.*

<br></br>

### 네트워크와 연결하기
지갑은 로컬에서 네트워크와의 연결이 없어도 만들 수 있습니다. 하지만 트랜젝션을 보내는 작업은 T-Bears와 같은 ICON네트워크와 연결되어 있어야 가능합니다. 네트워크는 아래 4가지가 존재합니다.

		1. 메인넷
		2. 테스트넷
		3. 로컬넷
		4. aws 네트워크

 실제 트랜젝션을 발생시키고, 거래를 블록에 기입하기 위해서는 네트워크에 연결이 되어 있어야 합니다. 위의 4가지 네트워크의 유형 중 메인넷은 아이콘블록체인 네트워크의 메인 네트워크를 말하며, 테스트넷은 개발자들을 위해 오픈된 테스트 넷을 말합니다. 둘 다 현재는 오픈되어있지 않습니다. (2018/09/07기준) SDK 테스트는 앞선 1번에서 T-Bears 를 활용하여 구축한 로컬 네트워크와 연결하여 진행하겠습니다. 네트워크와의 연결은 트랜젝션을 작성하고 노드에 보낼때 반드시 필요한 부분 이기 때문에, 아래의 [트랜젝션 만들어보기](#트랜젝션-만들어보기)에 포함하겠습니다. 

<br></br>


### 트랜젝션 만들어보기

트랜젝션은 4가지 종류가 있습니다. 

		1. ICX를 전송하는 트랜젝션
		2. SCORE를 실행하는 트랜젝션
		3. 메세지를 전송하는 트랜젝션
		4. SCORE를 Deploy 하는 트랜젝션

각기 트랜젝션은 서버에 json 형식으로 전송이 되며, 서버는 해당 트랜젝션을 받아 블록에 트랜젝션 해시값을 기입함 으로서 사용자가 전송한 트랜젝션이 블록에 기입됩니다. 


* ### 실습 해 볼 내용

	1. 지갑만들기에서 만든 지갑을 불러와서 다른 지갑으로 ICX를 보내보고 결과 확인하기
	2. SCORE의 함수를 호출하고, 결과를 받습니다.
	3. 메세지를 트랜젝션에 넣고 전송해 보기
	4. 만들어진 SCORE를 Deploy 해 보기

위의 4가지 트랜젝션은 **Transaction 메세지 생성** 부분만이 다릅니다. 아래의 **1. 지갑만들기에서 만든 지갑을 불러와서 다른 지갑으로 ICX를 보내보고 결과 확인하기** 의 실습 코드에서 전체적인 내용을 살펴 보고, **Transaction 메세지 생성** 부분만을 변경함 으로서, 다른 트랜젝션을 보내는 내용을 기록하겠습니다. 

<br></br>

### 1. 지갑만들기에서 만든 지갑을 불러와서 다른 지갑으로 ICX를 보내보고 결과 확인하기

아이콘 rpc 서버의 노드 URL은 아래와 같이 규정됩니다. 

	ip_address:9000/api/v3

* ##### 자바 SDK로 실행

	    public final String URL = "http://[node ip]/api/v3"; 
    
		// icx를 전송할때 필요한 객체를 선언합니다.
		IconService iconService;
		KeyWallet keyStoreLoad;


지갑을 로드할때 필요한 변수를 선언하고, 개인키를 사용해서 지갑을 로드합니다. 

	Bytes Private_Key = new Bytes("625de46fb951054330a58ab6f66c18849afc94797f0d37df6ff18cf8ed573981");
    keyStoreLoad = KeyWallet.load(Private_Key);
    Address fromAddress = keyStoreLoad.getAddress(); 
    
    
노드와의 연결 및 상세한 로그를 표출하기 위한 logger를 선언합니다. logger는 옵션이기 때문에 생략하셔도 됩니다.**[option]이라고 주석처리된 코드는 생략하셔도 됩니다.**

		HttpLoggingInterceptor logging = new HttpLoggingInterceptor();  //[option]
		//httpProvider 의 url의 정보를 가로채기 위함입니다.
	    logging.setLevel(HttpLoggingInterceptor.Level.BODY); //[option]
   		OkHttpClient httpClient = new OkHttpClient.Builder() //로그 출력 
    			.addInterceptor(logging) // [option]
    			.build();


		// iconService를 쓰기위해 객체를 선언합니다. 
     	iconService = new IconService(new HttpProvider(httpClient, URL)); 
 
    

ICX 송금 하기
	
	
접속하려는 노드의 networkId값은 해당 네트워크의 제네시스 블록에 기록된 값을 기입해 줍니다. networkId는 네트워크를 구분 해 주는 확인자 중 하나이기 때문에 네트워크마다 다르지만, 1은 메인넷, 2는 테스트넷, 3번부터는 Custom Network 가 할당할 수 있습니다. [T-Bears의 networkId는 기본 3번으로 할당되어 있습니다. ]
	
		BigInteger networkId = new BigInteger("3"); 
		
		Address fromAddress = keyStoreLoad.getAddress(); 
		//ICX를 보낼 주소 (keystore 파일로 불러들인 지갑의 주소를 말합니다.)
		
		Address toAddress = new Address("hx4644c1e45240d0aea7e6f457049c088419957ffe"); 
		//ICX를 받을 주소(입력된 지갑 주소는 임의값입니다.)


송금할 금액(ICX)을 정하고, stepLimit 과 timestamp, nonce 를 정해줍니다. 아이콘 네트워크의 전송은 내부적으로 Loop단위로 전송이 되게 됩니다. 따라서 전송될 때 에는 ICX를 Loop와의 비율로 계산하여 보내게 됩니다. ICX가 아닌 다른 토큰들 또한, Loop와의 비율을 생성자가 결정합니다. ICX와 Loop의 비율은 아래와 같습니다. 
	
			1 ICX = 1*10^(18) Loop 
					
보낼 ICX의 양은 "1"이며, ICX를 Loop로 계산해 주는 코드입니다. 

			BigInteger value = IconAmount.of("1", 18).toLoop(); 
	   	
stepLimit은 수수료의 한계치 입니다. 잘못된 스마트컨트렉트의 결과로 지갑내의 모든 잔고가 소진되는것을 막아주기 위함 입니다. 
ICX 전송시, 권장하는 stepLimit은 10,000입니다. 또한 IRC 2 기반 토큰을 전송할 경우, 권장 step limit 은 20,000 입니다. (20180910기준입니다.) 수수료는 ICON의 정책에 따라서 달라질 수 있습니다. 
	
		BigInteger stepLimit = new BigInteger("7500000"); 
	
동일 트랜잭션을 막기 위해 타임스탬프 값을 사용합니다. timestamp는 꼭 현재 시간을 넣어야 합니다. 현재시간과 큰 격차가 있을 경우, 서버에서 트랜젝션을 거부합니다. 
	
		long timestamp = System.currentTimeMillis() * 1000L;
	

**Transaction 메세지 생성**
	
nonce 값은 입력하지 않아도 됩니다.  
    
    Transaction transaction = TransactionBuilder.newBuilder() 
            .from(fromAddress)    //보낼 지갑 주소 
            .to(toAddress)        //받을 지갑 주소 
            .value(value)         //보낼 금액
            .stepLimit(stepLimit) //수수료 한계
            .timestamp(new BigInteger(Long.toString(timestamp)))
            .build();


Transaction 결과 확인
트랜잭션 정보를 보내는 사람의 주소로 Sign하여 SignedTransaction을 생성하고, 트랜잭션의 결과로 hash값을 확인합니다. 
    
    //빌드된 트랜젝션을 보내는사람 주소로 sign 합니다. 
    SignedTransaction signedTransaction =new SignedTransaction(transaction, keyStoreLoad ); 
    
    // 트랜젝션을 보낸 후, 결과인 TX hash 값을 받아옵니다. 
    Bytes hash = iconService.sendTransaction(signedTransaction).execute();
    
    
    // 결과 종합 출력 
    System.out.println( "from Addres : "+fromAddress);
    System.out.println( "to Addres : "+toAddress);
    System.out.println( "Amount : "+value);
    System.out.println( "stepLimit : "+stepLimit);
    //TX hash 값을 출력합니다. 
    System.out.println("TX hash : "+hash);

실행결과

~~~
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: --> POST http://127.0.0.1:9000/api/v3
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: Content-Type: application/json
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: 
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: {"jsonrpc":"2.0","id":1536817241316,"method":"icx_sendTransaction","params":{"stepLimit":"0x7270e0","signature":"sQGdawCxGxbLXvEHbk9pO/pQNiRMYtQBbXpES+aAAVVlc3MuLe+HdLtLkN48L7UFV6bEZJsbPumStVRZh/4nvAA=","nid":"0x3","from":"hxcc61e31ed6080926d6d6f7d0ac6e1b8b2ee5a9fa","to":"hx4644c1e45240d0aea7e6f457049c088419957ffe","version":"0x3","value":"0xde0b6b3a7640000","timestamp":"0x575ba25c04248"}}
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: --> END POST (-1-byte body)
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: <-- 200 OK http://127.0.0.1:9000/api/v3 (45ms)
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: Connection: close
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: access-control-allow-origin: *
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: Content-Length: 119
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: Content-Type: application/json
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: 
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: {"jsonrpc": "2.0", "result": "0x270f1cdc20d60f656868bb969c677b8f2948693dbaefe5250f1e047d072a62eb", "id": 1536817241316}
Sep 13, 2018 2:40:41 PM okhttp3.internal.platform.Platform log
정보: <-- END HTTP (119-byte body)
from Addres : hxcc61e31ed6080926d6d6f7d0ac6e1b8b2ee5a9fa
to Addres : hx4644c1e45240d0aea7e6f457049c088419957ffe
Amount : 1000000000000000000
stepLimit : 7500000
TX hash : 0x270f1cdc20d60f656868bb969c677b8f2948693dbaefe5250f1e047d072a62eb
~~~
	   
* ##### 파이썬 SDK로 실행

	필요 모듈을 import 해 줍니다. 

		from iconsdk.icon_service import IconService
		from iconsdk.providers.http_provider import HTTPProvider
		from iconsdk.wallet.wallet import KeyWallet
		from iconsdk.builder.transaction_builder import TransactionBuilder
		from iconsdk.signed_transaction import SignedTransaction
	
	아이콘 네트워크 주소를 지정하여 아이콘 서비스 인스턴스를 생성합니다. 
	
		icon_service = IconService(HTTPProvider("http://localhost:9000/api/v3"))

	ICX를 송금할 지갑의 프라이빗 키를 통해 지갑을 로드할 것이므로, 지갑의 프라이빗 키를 선언하고, 지갑을 로드합니다. 
	
		key=UserPrivateKey
		Loaded_wallet = KeyWallet.load(key)
	
	**Transaction 메세지 생성**
	
	TransactionBuilder를 통해 트랜젝션을 빌드합니다. 
	
		transaction = TransactionBuilder()\
			.from_(Loaded_wallet.get_address())\ 	## 보내는 지갑 주소
		   	.to("")\ 								## 받는 지갑 주소
		   	.value(150000000)\						##보내는 ICX
		   	.step_limit(1000000)\					## steplimit 설정
		   	.nid(3)\								## NetworkID
		   	.nonce(100)\							## 옵션값입니다. 
		   	.build()	 	
		   	
	빌드된 transaction 객체를 사인하여 전송합니다. 
		
			signed_transaction = SignedTransaction(transaction, Loaded_wallet)
			
	전송된 트랜젝션의 결과 확인 		
	
			tx_hash = icon_service.send_transaction(signed_transaction)
			
			
### [트랜젝션의 결과 확인](#내가-보낸-트랜젝션-결과-확인하기)

## 2. SCORE의 함수를 호출하고, 결과를 받습니다.  


* ##### 자바 SDK로 실행
		
	Transaction 메세지를 만들기 전에, 삽입될 파라미터를 정의합니다. 

        Address address = new Address("hxcc61e31ed6080926d6d6f7d0ac6e1b8b2ee5a9fa");
        Param params = new Param();
        params._owner = address;
        
        class Param {
        public Address _owner;}

	**Transaction 메세지 생성**
	파라미터를 삽입하여 SCORE api의 함수를 실행하고, 결과를 불러옵니다. 
	
        Call<RpcItem> call = new Call.Builder()
                .from(address)
                .to(scoreAddress)
					//method가  balanceOf이면, 잔액조회만을 진행합니다. 
                .method("balanceOf")
                .params(params)
                .build();

	
	SCORE를 향하여 트랜젝션을 보내고, 결과를 받아 출력합니다. 
	
        RpcItem result = iconService.call(call).execute();
        System.out.println("balance:"+result);
		
		
	실행결과
	 
~~~
Sep 13, 2018 3:39:02 PM okhttp3.internal.platform.Platform log
정보: --> POST http://localhost:9000/api/v3
Sep 13, 2018 3:39:02 PM okhttp3.internal.platform.Platform log
정보: Content-Type: application/json
Sep 13, 2018 3:39:03 PM okhttp3.internal.platform.Platform log
정보: 
Sep 13, 2018 3:39:03 PM okhttp3.internal.platform.Platform log
정보: {"jsonrpc":"2.0","id":1536820742630,"method":"icx_call","params":{"data":{"method":"balanceOf","params":{"_owner":"hxcc61e31ed6080926d6d6f7d0ac6e1b8b2ee5a9fa"}},"dataType":"call","from":"hxcc61e31ed6080926d6d6f7d0ac6e1b8b2ee5a9fa","to":"cx92dc41a0997aa367df301174485bd6cd579a5ce9"}}
Sep 13, 2018 3:39:03 PM okhttp3.internal.platform.Platform log
정보: --> END POST (-1-byte body)
Sep 13, 2018 3:39:03 PM okhttp3.internal.platform.Platform log
정보: <-- 200 OK http://localhost:9000/api/v3 (60ms)
Sep 13, 2018 3:39:03 PM okhttp3.internal.platform.Platform log
정보: Connection: close
Sep 13, 2018 3:39:03 PM okhttp3.internal.platform.Platform log
정보: access-control-allow-origin: *
Sep 13, 2018 3:39:03 PM okhttp3.internal.platform.Platform log
정보: Content-Length: 56
Sep 13, 2018 3:39:03 PM okhttp3.internal.platform.Platform log
정보: Content-Type: application/json
Sep 13, 2018 3:39:03 PM okhttp3.internal.platform.Platform log
정보: 
Sep 13, 2018 3:39:03 PM okhttp3.internal.platform.Platform log
정보: {"jsonrpc": "2.0", "result": "0x0", "id": 1536820742630}
Sep 13, 2018 3:39:03 PM okhttp3.internal.platform.Platform log
정보: <-- END HTTP (56-byte body)
balance:0x0
~~~

* ##### 파이썬 SDK로 실행
	**Transaction 메세지 생성**
	자바와 동일하게 transaction을 빌드합니다.
	SCORE을 실행하고, 결과를 리턴받습니다.  
	
		transaction = CallTransactionBuilder()\
		    .from_(Loaded_wallet.get_address())\
		    .to(SCORE_주소)\
		    .step_limit(1000000)\
		    .nid(3)\
		    .nonce(100)\
		    .method("balance_of")\
		    .params(params)\
		    .build()
		    

##	3. 메세지를 트랜젝션에 넣고 전송해 보기
* ##### 자바 SDK로 실행
	**Transaction 메세지 생성**
	메세지를 transaction에 넣어 전송할 수 있습니다. 
	
		String message = "Hello World";
	
   			Transaction transaction = TransactionBuilder.newBuilder() 
    			.from(fromAddress)
    			.to(toAddress)
    			.stepLimit(stepLimit)
    			.timestamp(new BigInteger(Long.toString(timestamp)))        
    			//timestamp는 unix time을 삽입합니다. 시간은 1/1000000초 까지 기록됩니다. 
    			.message(message)
    			.build();
실행결과 

~~~
Sep 13, 2018 3:52:02 PM okhttp3.internal.platform.Platform log
정보: --> POST http://127.0.0.1:9000/api/v3
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: Content-Type: application/json
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: 
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: {"jsonrpc":"2.0","id":1536821522958,"method":"icx_sendTransaction","params":{"data":"0x48656c6c6f20576f726c64","stepLimit":"0x7270e0","signature":"puooYE2nJc2coB2en+cgxrqUN6qa0EBk16e7AAnjBPUmkatCIffL8QySp69MBS1s87ilr5aNoE8lhaufYul8owA=","dataType":"message","nid":"0x1","from":"hxcc61e31ed6080926d6d6f7d0ac6e1b8b2ee5a9fa","to":"hx4644c1e45240d0aea7e6f457049c088419957ffe","version":"0x3","timestamp":"0x575bb24f4e688"}}
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: --> END POST (-1-byte body)
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: <-- 200 OK http://127.0.0.1:9000/api/v3 (47ms)
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: Connection: close
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: access-control-allow-origin: *
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: Content-Length: 119
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: Content-Type: application/json
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: 
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: {"jsonrpc": "2.0", "result": "0x8b7f9c28028abc4777114a0f0941e84710cbe46185f4fa8060dc85976e5cf373", "id": 1536821522958}
Sep 13, 2018 3:52:03 PM okhttp3.internal.platform.Platform log
정보: <-- END HTTP (119-byte body)
from Addres : hxcc61e31ed6080926d6d6f7d0ac6e1b8b2ee5a9fa
to Addres : hx4644c1e45240d0aea7e6f457049c088419957ffe
Amount : 1000000000000000000
stepLimit : 7500000
TX hash : 0x8b7f9c28028abc4777114a0f0941e84710cbe46185f4fa8060dc85976e5cf373
~~~
	
* ##### 파이썬 SDK로 실행
	**Transaction 메세지 생성**

			transaction = MessageTransactionBuilder()\
			    .from_(Loaded_wallet.get_address())\
			    .to(SCORE_주소)\
			    .step_limit(1000000)\
			    .nid(3)\
			    .nonce(100)\
			    .message("test")\
			    .build()

<br></br>

##	4. 만들어진 SCORE를 Deploy 해 보기

로컬에서 사용자가 만들어낸 SCORE 실행파일(파이썬)을 아이콘 네트워크에 Deploy(배포) 합니다. 
content 파라미터에는 파일을 로드하여 삽입해 줍니다. 트랜젝션의 용량제한이 있으므로(2018/09/06기준 현재 최대 512kb) 용량을 잘 지키고, 스마트 컨트렉트 코드 작성 규칙을 지켜 작성하여야 합니다. 
규칙을 지키지 않은 스마트컨트렉트 코드는 아이콘 네트워크에 치명적인 영향을 끼치므로, 규칙에 따라서 작성하여야 합니다. 

* ##### 자바 SDK로 실행
	**Transaction 메세지 생성**
	
		Transaction transaction = TransactionBuilder.of(networkId)
		    .from(wallet.getAddress())
		    .to(scoreAddress)
		    .stepLimit(new BigInteger("5000000"))
		    .nonce(new BigInteger("1000000"))
		    .deploy("application/zip", content) 
		    // (20180910기준) zip만 지원합니다. 
		    
		    [변경]
		    .deploy("zip", content)  
		    
		    .params(params)
		    .build();

* ##### 파이썬 SDK로 실행
	**Transaction 메세지 생성**
	
		transaction = DeployTransactionBuilder()\
		    .from_(Loaded_wallet.get_address())\
		    .to(SCORE_주소)\
		    .step_limit(1000000)\
		    .nid(3)\
		    .nonce(100)\
		    .content_type("zip")\
		    .content(content)\
		    .params(params)\
		    .build()


<br></br>


# Block에서 정보 가지고 오기

아이콘의 C-Rep 노드가 만들어내는 Block은 아래와 같은 정보를 지니고 있습니다. 블록은 각각의 TX를 지님과 동시에 블록을 만들어낸 피어의 ID, 이전 블록의 해시값인 PrevBlockHash와 같이, 많은 정보를 내포하고 있습니다. ICON에서 제공하는 자바, 파이썬 SDK를 활용하면, 특정 번호의 블럭을 혹은 가장 최근의 블록을 불러들여서 정보를 출력해 볼 수 있습니다. 

 Block의 정보는 크게 두 가지로 나뉩니다. **Block을 불러들이지 않고 조회할 수 있는 데이터**와 **불러들인 Block에서 얻을 수 있는 데이터**입니다. 

<br></br>

**Block을 불러들이지 않고 조회할 수 있는 데이터**

		특정 주소의 Balance
		현재 발행된 코인의 총 수 
		현재 블록의 높이
		SCOREAddress를 통해서 SCORE API를 호출합니다.

 <br></br>

**불러들인 Block에서 얻을 수 있는 데이터**
   
      불러들인 블록의 해시 
      BlockHash
      
      블록에 기입되어있는루트머클트리의 해시 (제네시스 블록과 같은 값)
      MerkleTreeRootHash
      
      peer의 ID Block을 만들어낸 Node의 ID
      PeerId
      
      이전 블록의 해시 
      PrevBlockHash
      
      불러들인 블럭의 해시 
      hashCode
      
      블럭이 만들어진 시간 
      Timestamp
      
      블럭에 포함되어있는 Transaction들 
      Transactions
      
      해싱되어 서명된 데이터
      Signature

<br></br>

> Block을 불러오지 않아도, 네트워크와 연결되었다면 조회할 수 있는 정보들
## 간단한 조회 해 보기.

ICON네트워크와 정상적으로 연결되었다면, 조회 해 볼 수 있는 정보들은 아래와 같습니다. 

	1. 특정 주소의 Balance 호출하기.
	2. 현재 발행된 코인의 총 수 호출하기.
	3. SCORE주소를 통해서 SCORE API를 모두 호출하기. 

파이썬과 자바 SDK 모두, **iconService 객체는 RPC를 위한 연결을 수립합니다.** 부분의 내용이 서로 같습니다. 따라서, 처음 이후엔 모두 생략하겠습니다. 

##1. 특정 주소의 Balance 호출하기.

주어진 주소의 Balance를 호출하여 출력합니다.

*  자바 SDK로 실행

api 서버와 연결되는 URL 주소에 httpclient 를 활용하여, OkHttpClient 접속을 만들어내는 클래스입니다. 연결을 위해 선언되는 
**iconService 객체는 RPC를 위한 연결을 수립합니다.**  여기서, URL은 연결코자 하는 ICON네트워크의 주소를 입력해 줍니다. 

검색할 주소와 아이콘서비스를 선언합니다. 

		public final static String URL = "http://127.0.0.1:9000/api/v3";    
		private static IconService iconService;
		public static String walletaddress = "hx4644c1e45240d0aea7e6f457049c088419957ffe";

네트워크와 연결을 수립하고, 조회하여 지정된 Address 주소의 잔액을 출력합니다.

    OkHttpClient httpClient = new OkHttpClient.Builder()
            .build();
    iconService = new IconService(new HttpProvider(httpClient, URL));
    Address address = new Address(walletaddress);
    BigInteger balance = iconService.getBalance(address).execute();
    System.out.println("The Wallet address "+walletaddress+"'s balance : " + balance);
    

* 파이썬 SDK로 실행

사용할 라이브러리를 import 합니다.

	from iconsdk.icon_service import IconService
	from iconsdk.providers.http_provider import HTTPProvider

선언되는 **icon_service 객체는 RPC를 위한 연결을 수립합니다.**  여기서, URL은 연결코자 하는 ICON네트워크의 주소를 입력해 줍니다.

	icon_service = IconService(HTTPProvider("http://[node ip]/api/v3"))

Balance를 조회할 Adress를 입력합니다. 

	balance = icon_service.get_balance(Adress)

<br></br>

## 2. 현재 발행된 코인의 총 수 호출하기.

발행된 코인의 총 수를 구합니다. 처음부터 **icon_service 객체는 RPC를 위한 연결을 수립합니다.** 까지의 내용은 생략되었습니다. 


*  자바 SDK로 실행

TotalSupply(현재 발행된 ICX의 총 수)를 출력합니다. 

	    OkHttpClient httpClient = new OkHttpClient.Builder()
	            .build();
	    iconService = new IconService(new HttpProvider(httpClient, URL));
        BigInteger gettotalSupply = iconService.getTotalSupply().execute();
        System.out.println("totalSupply : "+gettotalSupply);

실행결과 

	totalSupply:1600920000000000000000000000



*  파이썬 SDK로 실행

TotalSupply(현재 발행된 ICX의 총 수)를 출력합니다. 
	
	print(icon_service.get_total_supply());


<br></br>

## 	3. SCORE주소를 통해서 SCORE API를 모두 호출하기. 

SCORE의 주소를 입력하여 SCORE의 api 목록을 호출합니다. 

#####  자바 SDK로 실행

~~~
private static final String URL = "http://127.0.0.1:9000/api/v3";    
private static final String SCOREaddress = "cx92dc41a0997aa367df301174485bd6cd579a5ce9";

	public static void main(String[] args) throws IOException{
	OkHttpClient httpClient = new OkHttpClient.Builder()
											  .build();
	IconService iconService = new IconService(new HttpProvider(httpClient, URL));
	
	Address scoreAddress = new Address(SCOREaddress);
	List<ScoreApi> apis = iconService.getScoreApi(scoreAddress).execute();
	System.out.println("apis:" + apis);

~~~

 실행결과
 
 ~~~
 apis:[ScoreApi{properties=RpcObject(items={outputs=RpcArray(items=[RpcObject(items={type=int})]), readonly=0x1, inputs=RpcArray(items=[RpcObject(items={name=_owner, type=Address})]), name=balanceOf, type=function})}, ScoreApi{properties=RpcObject(items={outputs=RpcArray(items=[RpcObject(items={type=int})]), readonly=0x1, name=decimals, type=function})}, ScoreApi{properties=RpcObject(items={name=fallback, type=fallback})}, ScoreApi{properties=RpcObject(items={outputs=RpcArray(items=[RpcObject(items={type=str})]), readonly=0x1, name=name, type=function})}, ScoreApi{properties=RpcObject(items={outputs=RpcArray(items=[RpcObject(items={type=str})]), readonly=0x1, name=symbol, type=function})}, ScoreApi{properties=RpcObject(items={outputs=RpcArray(items=[RpcObject(items={type=int})]), readonly=0x1, name=totalSupply, type=function})}, ScoreApi{properties=RpcObject(items={inputs=RpcArray(items=[RpcObject(items={name=_to, type=Address}), RpcObject(items={name=_value, type=int}), RpcObject(items={default=null, name=_data, type=bytes})]), name=transfer, type=function})}, ScoreApi{properties=RpcObject(items={inputs=RpcArray(items=[RpcObject(items={indexed=0x1, name=_from, type=Address}), RpcObject(items={indexed=0x1, name=_to, type=Address}), RpcObject(items={indexed=0x1, name=_value, type=int}), RpcObject(items={name=_data, type=bytes})]), name=Transfer, type=eventlog})}]

 ~~~


#####  파이썬 SDK로 실행

	
	print(score_apis = icon_service.get_score_api(SCOREAddress));


 

<br></br>

> Block에서 정보를 가지고 오기 전에...
## Block을 불러오는 세 가지 방법

블록에서 정보를 읽기 위해서는 ICON 네트워크에서 특정 Block을 불러와야 합니다. SDK를 활용하여 블록을 불러들이는 방법은 크게 세 가지가 있습니다.

	1. 가장 마지막에 만들어진 Block 불러오기
	2. 특정 높이의 Block 불러오기
	3. Block의 Hash 값을 통하여 불러오기

<br></br>

### 1. 가장 마지막에 만들어진 Block 불러오기
가장 마지막에 만들어진 Block을 불러오는 메서드 입니다.
Block을 불러들이기 위해서 작성되는 코드는 자바와 파이썬에서 모두 **iconService 객체는 RPC를 위한 연결을 수립합니다.**  부분이 동일합니다. 1. 가장 마지막에 만들어진 Block 불러오기를 제외한 2, 3번에서는 **iconService 객체는 RPC를 위한 연결을 수립합니다.**  부분을 생략하겠습니다. 

* ##### 자바 SDK로 실행

api 서버와 연결되는 URL 주소에 httpclient 를 활용하여, OkHttpClient 접속을 만들어내는 클래스입니다. 
**iconService 객체는 RPC를 위한 연결을 수립합니다.**  여기서, URL은 연결코자 하는 ICON네트워크의 주소를 입력해 줍니다. 

	public final String URL = "http://[node ip]/api/v3";    
	private IconService iconService;
    public BasicGetMethods() {
        OkHttpClient httpClient = new OkHttpClient.Builder().build();
        iconService = new IconService(new HttpProvider(httpClient, URL));
    }

가장 마지막 블럭을 불러옵니다. 

    public Block getLastBlock() throws IOException {
        Block block = iconService.getLastBlock().execute();
        System.out.println("block:" + block);
        return block;
    }

실행코드 

        BasicGetMethods LastBlock = new BasicGetMethods();
        LastBlock.getLastBlock();

* 파이썬 SDK로 실행

사용할 라이브러리를 import 합니다.

	from iconsdk.icon_service import IconService
	from iconsdk.providers.http_provider import HTTPProvider

**iconService 객체는 RPC를 위한 연결을 수립합니다.**  여기서, URL은 **자바 SDK로 실행** 과 동일하게, 연결코자 하는 ICON네트워크의 주소를 입력해 줍니다.

	icon_service = IconService(HTTPProvider("http://[node ip]/api/v3"))

마지막 블록(가장 최신의 블록)을 불러옵니다.

	block = icon_service.get_block("latest")    	
<br></br>


### 2. 특정높이의 Block 불러오기

**1. 가장 마지막에 만들어진 Block 불러오기**와 동일하게 네트워크와의 연결을 수립한 후, 
해당 네트워크에서 특정 높이의 Block을 불러옵니다. 

* 자바 SDK로 실행

        Block block = iconService.getBlock(height).execute();
   

* 파이썬  SDK로 실행

		block = icon_service.get_block(height)
		
		
<br></br>

### 3. Block의 Hash 값을 통하여 불러오기

##### 자바 SDK로 실행
블록의 Hash값은 Bytes 로 삽입되어야 합니다.

    public void getBlockByHash(String Hash) throws IOException {
        Bytes hash = new Bytes(Hash);
        Block block = iconService.getBlock(hash).execute();
    }
   

##### 파이썬  SDK로 실행

		block = icon_service.get_block(Hash)
		

<br></br>


>위에서 언급한 3가지 방법으로 불러들인 Block 의 정보를 불러볼 수 있습니다.
## 불러온 Block에서 정보 불러오기.

**Block을 불러오는 세 가지 방법**에서 Block을 불러오게 되면, 해당 Block에서 아래와 같은 정보를 출력 해 볼 수 있습니다. 
<br></br>


**불러들인 Block에서 얻을 수 있는 데이터**
   
* 불러들인 블록의 해시 
> BlockHash
      
* 블록에 기입되어있는루트머클트리의 해시 (제네시스 블록과 같은 값)
> MerkleTreeRootHash
      
* peer의 ID Block을 만들어낸 Node의 ID
> PeerId
      
* 이전 블록의 해시 
> PrevBlockHash
      
* 불러들인 블럭의 해시 
> hashCode
      
* 블럭이 만들어진 시간 
> Timestamp
      
* 블럭에 포함되어있는 Transaction들 
> Transactions
      
* 해싱되어 서명된  데이터
> Signature

<br></br>


블럭에 포함되어있는 Transaction들의 경우에는 트랜젝션의 실패 유무와 상관 없이 기록됩니다. 따라서 get transaction result와 같은 함수를 통하여 Transaction이 실패했는지, 성공했는지, 어떤 트랜젝션인지 확인할 수 있습니다. 이러한 결과는 ICON에서 제공하는 [ICONTracker](https://tracker.icon.foundation)에서 제공되고 있습니다.

<br></br>

**아래 수행되는 예제 코드는 Block을 불러들인 상태에서, 불러들여진 Block 인스턴스를 통하여 로드하는 내용을 수록하고 있습니다. 따라서, Block을 불러들이기 이전까지의 내용은 생략되었습니다. Block을 불러들이는 내용은 1. 가장 마지막에 만들어진 Block 불러오기의 내용을 참조하시면 됩니다.**


##### 자바 SDK로 실행

높이를 통해서 불러들인 블럭에서 출력할 수 있는 내용을 모두 출력해 봅니다. 이 작업은 **Block을 불러오는 세 가지 방법**중 한 가지를 사용자의 선택에 따라 불러들인 후, 활용 가능합니다. 

~~~    
    public void getWholeBlockget(int Height) throws IOException {
        BigInteger height = BigInteger.valueOf(Height);
        Block block = iconService.getBlock(height).execute();
        System.out.println("block BlockHash:" + block.getBlockHash());
        System.out.println("block MerkleTreeRootHash:" + block.getMerkleTreeRootHash());
        System.out.println("block PeerId:" + block.getPeerId());
        System.out.println("block PrevBlockHash:" + block.getPrevBlockHash());
        System.out.println("block hashCode:" + block.hashCode());
        System.out.println("block BlockHash:" + block.getBlockHash());
        System.out.println("block Timestamp:" + block.getTimestamp());
        System.out.println("block Transactions:" + block.getTransactions());
        System.out.println("block Signature:" + block.getSignature());
    }
~~~
결과

~~~
block BlockHash:0x933d5018323cfca4951cc8f16ee407b5356d129fbe7ea6878a9bc4b715636cdb
block MerkleTreeRootHash:0xc27c3b0ecb8c3c689f134273b82a2af6f84ce76c0ebfa63542f7a0ae65b58336
block PeerId:hx86aba2210918a9b116973f3c4b27c41a54d5dafe
block PrevBlockHash:0x5b0c817130ea2b32d40938542153b214634fd25cf19b169b0efa9631b2854acb
block hashCode:1293203138
block BlockHash:0x933d5018323cfca4951cc8f16ee407b5356d129fbe7ea6878a9bc4b715636cdb
block Timestamp:1536313982794810
block Transactions:[foundation.icon.icx.data.ConfirmedTransaction@25df00a0]
block Signature:56znSEOorKAZG/1XHNW24C+1a9ZsGY1Cf1P/YZ2NN0UsXa+W7r5puWvG5grr8wbkwWz6K02b/eo4BUdbxWox5QE=
~~~


##### 파이썬 SDK로 실행

~~~
icon_service.get_block(1)

~~~

결과

~~~
{'version': '0.1a',
 'prev_block_hash': '5b0c817130ea2b32d40938542153b214634fd25cf19b169b0efa9631b2854acb',
 'merkle_tree_root_hash': 'c27c3b0ecb8c3c689f134273b82a2af6f84ce76c0ebfa63542f7a0ae65b58336',
 'time_stamp': 1536313982794810,
 'confirmed_transaction_list': [{'from': 'hxebf3a409845cd09dcb5af31ed5be5e34e2af9433',
   'to': 'hx4873b94352c8c1f3b2f09aaeccea31ce9e90bd31',
   'value': '0x21e19e0c9bab2400000',
   'version': '0x3',
   'nid': '0x3',
   'stepLimit': '0xf4240',
   'timestamp': '0x57544f90d8cd0',
   'signature': '6p/W20c9LKNA2HwtFFHU6vmGA3S/t7iHL63znZwOlzoH5gPxpfV8du2veUR5SdYxxCgDIgfuVWDMNF6AdxinhAA=',
   'txHash': '0xc27c3b0ecb8c3c689f134273b82a2af6f84ce76c0ebfa63542f7a0ae65b58336'}],
 'block_hash': '933d5018323cfca4951cc8f16ee407b5356d129fbe7ea6878a9bc4b715636cdb',
 'height': 1,
 'peer_id': 'hx86aba2210918a9b116973f3c4b27c41a54d5dafe',
 'signature': '56znSEOorKAZG/1XHNW24C+1a9ZsGY1Cf1P/YZ2NN0UsXa+W7r5puWvG5grr8wbkwWz6K02b/eo4BUdbxWox5QE='}

~~~

<br></br>

#### 내가 보낸 트랜젝션 결과 확인하기

트랜젝션의 발생은 누구나 할 수 있지만, 수수료부족 등의 이유로 거절되기도 합니다. 때문에 트랜젝션을 발생시킨 주체는 항상 트랜젝션의 결과를 확인해야 합니다. 

Transaction의 결과 또한, json 형식으로 출력됩니다. 
**Transaction이 문제없이 완료되었을 경우에는 Status가 "1", "0x1"으로 출력이 됩니다.**


##### 자바 SDK로 실행

~~~
    // Transaction Hash 값을 통해서 Transaction 을 호출한다. 
    public void getTransaction(String Tx_Hash) throws IOException {
        Bytes txHash = new Bytes(Tx_Hash);
        ConfirmedTransaction tx = iconService.getTransaction(txHash).execute();
        System.out.println("transaction:" + tx);
    }
    // Transaction Hash 값을 통하여 Transaction의 결과를 호출한다. 
    public void getTransactionResult(String TX_Hash) throws IOException {
        Bytes txHash = new Bytes(TX_Hash);
        TransactionResult tx = iconService.getTransactionResult(txHash).execute();
        System.out.println("transaction: " + tx.getLogsBloom());
        System.out.println("TransactionResult Status: " + tx.getStatus());
    }

~~~

결과 

~~~
transaction: 0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
TransactionResult Status: 1

~~~

##### 파이썬 SDK로 실행


~~~
tx_hash="0xc27c3b0ecb8c3c689f134273b82a2af6f84ce76c0ebfa63542f7a0ae65b58336"
icon_service.get_transaction_result(tx_hash)
~~~

결과 

~~~
{'txHash': '0xc27c3b0ecb8c3c689f134273b82a2af6f84ce76c0ebfa63542f7a0ae65b58336',
 'blockHeight': '0x1',
 'blockHash': '0x933d5018323cfca4951cc8f16ee407b5356d129fbe7ea6878a9bc4b715636cdb',
 'txIndex': '0x0',
 'to': 'hx4873b94352c8c1f3b2f09aaeccea31ce9e90bd31',
 'stepUsed': '0xf4240',
 'stepPrice': '0x2540be400',
 'cumulativeStepUsed': '0xf4240',
 'eventLogs': [],
 'logsBloom': '0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
 'status': '0x1'}
~~~



