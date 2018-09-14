Wallet
==============

## Wallet
계정은 ICON에 중요한 역할을합니다. 계정에는 두 가지 종류의 계정이 있습니다. 일반적인 사용자가 소유한 Externally Owned Account (EOA)와 Smart Contract Account (CA)입니다
여기에서는 EOA에 초점을 맞추어 설명을 하겠습니다. EOA는 간단히 지갑이라고 생각할 수 있습니다.
지갑에는 잔액 및 지갑정보가 들어있습니다. 지갑 상태는 블록에 저장되며 지갑은 ICON 이용에 필수적인 기능입니다.
지갑은 암호화를 사용하여 안전한 보안을 보장합니다.

## keystore (keyfiles)
Keystore는 private key(개인키), public key(공개키)로 정의되어있습니다. keystore는 JSON형식으로 만들어져 있어 텍스트 편집기를 이용하여 확인할 수 있습니다. keystore는 암호화 되어 저장되며, 파일을 읽기 위해서는 파일 생성시 등록한 암호가 필요합니다. keystore를 만드는것은 지갑을 (EOA) 만드는 것과 같습니다.<br>정기적으로 keystore를 백업하는 행동이 크게 도움이 될 것입니다.<br>
본인을 확인하는 방법으로 keystore 를 사용합니다. keystore 생성할 때 입력했던 비밀번호를 통해 본인 확인을 하고 지갑에 접근할 수 있게 됩니다. 
keystore 를 decrypt 하여 Private Key를 알 수 있습니다. 지갑은 keysotre 파일 또는 private key를 통해 로드할 수 있습니다. 만약 privatekey가 해킹당했을 경우, 당신의 지갑의 모든권한을 상대방이 얻을 수 있어 매우 위험합니다. 따라서 지갑을 로드할 때 privatekey 사용보단 keystore 사용을 추천합니다.
<br>

아래에서는 지갑을 생성하는 다양한 방법을 설명합니다.

## T-bears 사용하기


#### keystore 생성
``` 
tbears keystore 
```
Example
``` 
tbears keystore [keystore name]

Input your keystore password : 패스워드 입력
Retype your keystore password : 패스워드 확인
```
result
```
Made keystore file successfully
```

## Python 사용하기


#### 지갑 생성하기

Example
``` 
wallet = KeyWallet.create()
```

#### 지갑 가져오기

Example
``` 
# PrivateKey 지갑 가져오기
key = bytes.fromhex(UserPrivateKey)
wallet = KeyWallet.load(key)

# Keystore 파일로 지갑 가져오기
wallet = KeyWallet.load("./keystore", "password")
```

#### 지갑(keystore파일) 내보내기(저장)

Example
```
wallet.store("경로","비밀번호")
```



## Java 사용하기


#### 지갑 생성하기

Example
``` 
wallet = KeyWallet.create()
```
#### 지갑가져오기

Example
``` 
// PrivateKey 지갑 가져오기
Bytes key = new Bytes(UserPrivateKey)
Wallet wallet = KeyWallet.load(key);

// Keystore 파일로 지갑 가져오기
// 불러올 Keystore 파일 경로 
File file = new File(destinationDirectory, store);
KeyWallet keyStoreLoad = KeyWallet.load(password, file);
```


#### 지갑(keystore파일) 내보내기(저장)

Example
```
// keyStore 파일 저장할 경로를 지정합니다.
File destinationDirectory = new File("./"); 

// keysotre 파일의 password 
String password = ""; 
   
store = KeyWallet.store(loadedKey, password, destinationDirectory);
```



## ICONex 사용하기
GUI 기반의 구글 크롬 확장 프로그램입니다.


[ICONex 설치](<https://chrome.google.com/webstore/detail/iconex/flpiciilemghbmfalicajoolhkkenfel>)


![img001](./img/iconex001.png)

<br><br>

### Create Wallet

Create Wallet 클릭 지갑생성
<br>

1. 코인 선택
![img002](./img/iconex002.png)
<br><br>
2. 지갑 이름 및 비밀번호 설정
![img003](./img/iconex003.png)
<br><br>
3. 지갑 백업(keystore) 다운<br>지갑을 백업 합니다파일은 지갑을 불러 올 때 쓰입니다.
![img004](./img/iconex004.png)
<br><br>
4. 지갑 privatekey 확인<br>privatekey 는 지갑을 불러 올 때 쓰입니다.
![img005](./img/iconex005.png)


### Load Wallet

Load Wallet 클릭 지갑 불러오기
<br><br>

1. keystore 파일을 이용하는 Select wallet file 방식과<br>privatekey를 이용하는 Enter Private Key 방식 중 선택할수있습니다.
![img006](./img/iconex006.png)


## 지갑 백업 및 복원

### keyfiles 이용
keyfiles이 있으면 백업과 복원이 자유롭습니다. keyfiles을 소유하고있으면 언제라도 백업과 복원이 가능합니다<br>
keyfiles이용하여 지갑을 불러올시 사용자가 지정한 비밀번호가 필요합니다.

### Privatekey 이용
PrivateKey는 16진수로 인코딩 된 바이트로 암호화 되지않은 Public key 를 포함하고 있습니다.<br>
PrivateKey를 이용하여 지갑을 불러올 수 있습니다.