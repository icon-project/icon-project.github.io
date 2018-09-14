# How to write SCORE
This document explains ICON audit creteria and suggests secure SCORE implementation practices. 
Audit checklist consists of 2 severity levels of items, Critical and Warning. 
Audit results will come as Pass/Fail/NA for each Critical items, and Pass/Warning/NA for each Warning items.  
If any Critical item is determined to be Fail, the SCORE deployement will be rejected. 

Listed below are the checklist grouped by severity. 
## Severity level
### Critical
- [Timeout](#timeout)
- [Unfinished loop](#unfinished-loop)
- [Package import](#package-import)
- [System call](#system-call)
- [Randomness](#randomness)
- [Outbound network call](#outbound-network-call)
- [IRC2 Token Standard compliance](#irc2-token-standard에-compliance)
- [IRC2 Token Standard parameter name](#irc2-token-standard-parameter-name)
- [Eventlog on Token Transfer](#eventlog-on-token-transfer)
- [Eventlog without Token Transfer](#eventlog-without-token-transfer)
- [ICXTransfer Eventlog](#icxtransfer-eventlog)

### Warning
- [External Function Parameter Check](#external-function-parameter-check)
- [Internal Function Parameter Check](#internal-function-parameter-check)
- [Predictable random function](#predictable-random-function)
- [Unchecked Low Level Calls](#unchecked-low-level-calls)
- [Fallback](#fallback)
- [\_\_init__ Function](#\_\_init__-function)
- [Underflow/Overflow](#underflowoverflow)
- [Vault](#vault)
- [Reentrancy](#reentrancy)
- [Time Manipulation](#time-manipulation)

## Timeout
SCORE function must return fairly immediately. Blockchain is not for any long-running operation. 
For example, if you implement token airdrop to many users, do not iterate over all users in a single function, handle each airdop separately instead..  
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

## Unfinished loop
Use `for` and `while` statement carefully. Make it sure that the code always reaches the exit condition. 
If the operation inside the loop consumes `step`, the program will halt at some point. 
However,if the code block inside the loop does not consume `step`, 
i.e., Python built-in functions, then the program may hang there forever. 
ICON network will force-kill the hanging task, but, still it may significantly degrade ICON network. 
    
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

## Package import
SCORE must run in a sandboxed environment. 
Package import is prohibited, except `iconservice` and the files in the same or sub-directory of the deplopying SCORE. 

```python
# Bad
import os

# Good
from iconservice import *
from .myclass import *
```

## System call
System call is prohibited. SCORE can not access any system resources.
```python
# Bad
import os
os.uname()
```

## Randomness
Execution result of SCORE must be deterministic. Unlesss, nodes can not reach a consensus. 
If nodes fail to reach a consensus, it will cancle every transactions in the block.
So, besides random function, 
any attempt to prevent block generation by undeterministic operation is strictly prohibited.   

```python
# Bad
# each node may have different outcome
won = datetime.datetime.now() % 2 == 0
```

## Outboung network call
Outbound network call is prohibited. Outcome of network call from each node can be different. 
```python
# Bad
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(host, port)
```

## IRC2 Token Standard compliance
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

## Predictable random funtion
블록체인 네트워크에서 예측 가능한 랜덤함수를 사용하는 것은 매우 위험합니다. 일반적으로 사용되는 random 함수는 정확하게는 pseudo random number generator(PRNG)로 seed 값이 공유되면 난수를 예측할 수 있게 됩니다. 이는 결과를 예측할 수 있게 합니다.
```python
# Bad
# block height 값을 예측할 수 있기 때문에 결과를 예측 할 수 있다.
won = block.height % 2 == 0
```

## Unchecked Low Level Calls
## Fallback
## \_\_init__ Function
## Underflow/Overflow

## Vault
## Reentrancy
## Time Manipulation


