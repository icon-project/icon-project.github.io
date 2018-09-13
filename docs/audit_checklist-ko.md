# How to write SCORE
이 문서는 ICON SCORE audit을 통과하기 위해 권장하는 SCORE 작성 방법에 대해서 설명합니다.
audit 항목은 중요도에 따라 Critical과 Warning 두 단계로 나뉘어 지며 Critical은 Pass/Fail/NA, Warning은 Pass/Warning/NA로 감사를 실시합니다. Critical 단계에서 Fail을 받은 항목이 하나라도 있으면 해당 SCORE의 audit은 거절됩니다.

아래는 중요도에 따른 audit 항목입니다.
## Severity level
### Critical
- [Timeout](#timeout)
- [무한루프 방지](#무한루프-방지)
- [import 금지](#import-금지)
- [시스템 콜 사용금지](#시스템-콜-사용금지)
- [합의에 도달할 수 없는 랜덤 함수 사용 금지](#합의에-도달할-수-없는-랜덤-함수-사용-금지)
- [외부 네트워크 접근 불가](#외부-네트워크-접근-불가)
- [IRC2 Token Standard 에 명시된 모든 함수 구현](#irc2-token-standard에-명시된-모든-함수-구현)
- [IRC2 Token Standard에 명시된 parameter 이름 사용](#irc2-token-standard에-명시된-parameter-이름-사용)
- [Token Transfer 시 Eventlog 필수 사용](#token-transfer-시-eventlog-필수-사용)
- [Token Transfer 없이 Eventlog 사용 금지](#token-transfer-없이-eventlog-사용-금지)
- [ICXTransfer Eventlog 사용 금지](#icxtransfer-eventlog-사용-금지)

### Warning
- [External Function Parameter Check](#external-function-parameter-check)
- [Internal Function Parameter Check](#internal-function-parameter-check)
- [예측 가능한 랜덤 함수 사용 금지](#예측-가능한-랜덤-함수-사용-금지)
- [Unchecked Low Level Calls](#unchecked-low-level-calls)
- [Fallback](#fallback)
- [\_\_init__ Function](#\_\_init__-function)
- [Underflow/Overflow](#underflowoverflow)
- [Vault](#vault)
- [Reentrancy](#reentrancy)
- [Time Manipulation](#time-manipulation)

## Timeout
시간이 너무 많이 소요되도록 SCORE 함수를 구현하지 마십시오. ICON 네트워크는 SCORE 함수 작성시 가급적 빠르게 리턴하도록 작성하는 것을 권장합니다. 예를 들어 모든 사용자에게 Airdrop을 할 경우, Transaction 한번으로 모든 것을 처리하지 않고 사용자별로 Transaction을 분리하여 처리하는 것을 권장합니다.
```python

# Bad
@external
def airDropToken(self, _value:int, _data:bytes = None) -> bool:
  for target in self._very_large_targets:
    self._transfer(self.msg.sender, target, _value, _data)

# Good
@external
def airDropToken(self, _to: Address, _value: int, _data: bytes = None) -> bool:
  if self._airdrop_sent_address[_to]:
     self.revert(f"Token was dropped already: {_to}")

  self._airdrop_sent_address[_to] = True
  self._transfer(self.msg.sender, _to, _value, _data)
```

## 무한루프 방지
for와  while 문의 사용은 신중하게 고려 되어야 합니다. 개발자의 실수로 의도치 않게 for나 while을 빠져 나올 조건에 도달하지 못하는 경우가 있을 수 있고, for나 while문 내에서 step이 소모되지 않는 오퍼레이션만 존재하는 경우 그 단계에 계속 머무르게 되어 ICON 네트워크에 치명적인 영향을 줄 수 있습니다. 반드시 for/while 조건을 빠져 나올 수 있도록 구현하십시오.
```python
# Bad
while True:
  // do something

# Good
i = 0
while i < 10:
  // do something
  i+= 1
```

## import 금지
ICON 네트워크에서 SCORE는 외부 환경에 영향을 받지 않고 완전히 독립된 상태로 동작되어야 합니다. 이를 보장하기 위해 Deploy하는 SCORE의 하위 모듈과 iconservice의 import만을 허용합니다.

```python
# Bad
import os

# Good
from iconservice import *
from .myclass import *
```

## 시스템 콜 사용금지
ICON 네트워크에서 SCORE는 외부 환경에 영향을 받지 않고 완전히 독립된 상태로 동작되어야 합니다. 따라서 모든 종류의 시스템 콜을 허용하지 않습니다.
```python
# Bad
import os
os.uname()
```

## 합의에 도달할 수 없는 랜덤 함수 사용 금지
SCORE에서 무작위성이 필요한 경우 랜덤 함수를 사용하는데, 무작위성을 높이다 보면 노드간 합의에 이루지 못할수 있습니다. 합의에 이르지 못하면 해당 블록에 포함된 모든 트랜잭션은 취소됩니다. 이는 ICON 네트워크의 지속성에 문제를 일으키게 됩니다. 따라서 합의에 이를 수 없는 랜덤 함수 사용을 금지 합니다.

```python
# Bad
# node 마다 동일한 결과 값을 얻기가 매우 어렵다.
won = datetime.datetime.now() % 2 == 0
```

## 외부 네트워크 접근 불가
SCORE의 독립성을 해치므로, 외부 네트워크 관련 함수를 사용하는 것은 권장하지 않습니다.
```python
# Bad
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(host, port)
```

## IRC2 Token Standard에 명시된 모든 함수 구현
Token을 정의하는 SCORE을 제작 할 경우 [IRC2 ICON Token Standard](https://github.com/icon-project/IIPs/blob/master/IIPS/iip-2.md)에 명시된 함수를 모두 구현해야 합니다.
```python
# IRC2 에 명시된 함수 리스트
@external(readonly=True)
def name(self) -> str:

@external(readonly=True)
def symbol(self) -> str:

@external(readonly=True)
def decimals(self) -> int:

@external(readonly=True)
def totalSupply(self) -> int:

@external(readonly=True)
def balanceOf(self, _owner: Address) -> int:

@external
def transfer(self, _to: Address, _value: int, _data: bytes=None):      
```

## IRC2 Token Standard에 명시된 parameter 이름 사용
[IRC2 ICON Token Standard](https://github.com/icon-project/IIPs/blob/master/IIPS/iip-2.md)에 명시된 함수를 구현할 경우 인자의 이름을 반드시 동일하게 사용하여야 합니다.
```python
# Bad
def balanceOf(self, owner: Address) -> int:

# Good
def balanceOf(self, _owner: Address) -> int:
```

## Token Transfer 시 Eventlog 필수 사용
Token transfer 시 이력 확인을 위해서 Eventlog를 반드시 남겨야 합니다.
```python
# Good
@eventlog(indexed=3)
def Transfer(self, _from: Address, _to: Address, _value: int, _data: bytes):
    pass

@external
def transfer(self, _to: Address, _value: int, _data: bytes = None) -> bool:
    self._balances[self.msg.sender] -= value
    self._balances[to_] += value
    self.Transfer(self.msg.sender, _to, value, data)
    return True
```

## Token Transfer 없이 Eventlog 사용 금지
Token을 전송하지 않고 Eventlog를 남기는 것을 금지합니다.
```python
# Bad
@eventlog(indexed=3)
def Transfer(self, _from: Address, _to: Address, _value: int, _data: bytes):
    pass

@external
def transfer(self, _to: Address, _value: int, _data: bytes = None) -> bool:
    // token transfer 하지 않음
    self.Transfer(self.msg.sender, _to, value, data)
    return True
```    

## ICXTransfer Eventlog 사용 금지
ICXTransfer는 ICX 전송할때 ICON 네트워크에서 자동으로 남기는 Eventlog입니다. 동일한 이름의 Eventlog를 SCORE 구현에서 사용할 수 없습니다.
```python
# Bad
@eventlog(indexed=3)
def ICXTransfer(self, _from: Address, _to: Address, _value: int):
```

## External Function Parameter Check
SCORE 함수 호출할 때, 선언된 인자와 타입이 다르게 호출하거나 필수 인자(디폴트 값이 정의되지 않은 인자)를 누락하여 호출한 경우에는 ICON 네트워크에서 error 처리를 합니다. 따라서 SCORE 작성 시에 별도의 타입 체크는 고려하지 않아도 됩니다.

## Internal Function Parameter Check
SCORE 내에서 다른 함수를 호출할 때, 또는 외부 SCORE 함수를 호출할 때, 인자의 타입과 필수 인자(디폴트 값이 설정되지 않은 인자)를 올바르게 사용하여야 합니다. 예를 들면 int로 선언하고 string 타입으로 호출하거나, 그 반대의 경우를 주의 하십시오. address 타입도 마찬가지입니다. 또한 필수 인자의 경우에는 반드시 유효한 값을 사용하여 함수를 호출해야 합니다.
string이나 int의 경우 인자 값의 길이에는 제한이 없으나, ICON 네트워크 상의 트랙잭션 메시지의 길이에는 제한(512KB)이 있습니다. 이를 넘어서지 않도록 주의하여야 합니다.
```python
# Bad
def myTransfer( _value: int) -> bool:
    ...
myTransfer("1000")

# Good
def myTransfer( _value: int) -> bool:
    ...
myTransfer(1000)

# Bad
def myTransfer( _value: int, _extra: str) -> bool:
    ...
myTransfer(1000)

# Good
def myTransfer( _value: int, _extra: str) -> bool:
    ...
myTransfer(1000,'abc')
```

## 예측 가능한 랜덤 함수 사용 금지
블록체인 네트워크에서 예측 가능한 랜덤함수를 사용하는 것은 매우 위험합니다. 일반적으로 사용되는 random 함수는 정확하게는 pseudo random number generator(PRNG)로 seed 값이 공유되면 난수를 예측할 수 있게 됩니다. 이는 결과를 예측할 수 있게 합니다.
```python
# Bad
# block height 값을 예측할 수 있기 때문에 결과를 예측 할 수 있다.
won = block.height % 2 == 0
```

## Unchecked Low Level Calls
@ TBD

## Fallback
@ TBD

## \_\_init__ Function
@ TBD

## Underflow/Overflow
@ TBD

## Vault
@ TBD
## Reentrancy
@ TBD
## Time Manipulation
@ TBD


