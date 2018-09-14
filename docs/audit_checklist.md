# How to write SCORE
This document explains ICON audit creteria and suggests secure SCORE implementation practices. 
Audit checklist consists of 2 severity levels of items, Critical and Warning. 
Audit results will come as Pass/Fail/NA for each Critical items, and Pass/Warning/NA for each Warning items.  
If any Critical item is determined to be Fail, the SCORE deployement will be rejected. 

Listed below are the checklist grouped by severity. 
## Severity level
### Critical
- [Timeout](#timeout)
- [Unfinishing loop](#unfinishing-loop)
- [Package import](#package-import)
- [System call](#system-call)
- [Randomness](#randomness)
- [Outbound network call](#outbound-network-call)
- [IRC2 Token Standard compliance](#irc2-token-standard-compliance)
- [IRC2 Token parameter name](#irc2-token-parameter-name)
- [Eventlog on Token Transfer](#eventlog-on-token-transfer)
- [Eventlog without Token Transfer](#eventlog-without-token-transfer)
- [ICXTransfer Eventlog](#icxtransfer-eventlog)

### Warning
- [External Function Parameter Check](#external-function-parameter-check)
- [Internal Function Parameter Check](#internal-function-parameter-check)
- [Predictable arbitrarity](#predictable-arbitrarity)


## Timeout
SCORE function must return fairly immediately. Blockchain is not for any long-running operation. 
For example, if you implement token airdrop to many users, do not iterate over all users in a single function. Handle each or partial airdrop(s) one by one instead.

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

## Unfinishing loop
Use `for` and `while` statement carefully. Make sure that the code always reaches the exit condition. 
If the operation inside the loop consumes `step`, the program will halt at some point. 
However, if the code block inside the loop does not consume `step`, 
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
Package import is prohibited except `iconservice` and the files in the same or sub-directory of the deplopying SCORE. 

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
If nodes fail to reach a consensus, every transactions in the block will be lost.
Therefore, not only random function, but 
any attempt to prevent block generation by undeterministic operation is strictly prohibited.   

```python
# Bad
# each node may have different outcome
won = datetime.datetime.now() % 2 == 0
```

## Outbound network call
Outbound network call is prohibited. Outcome of network call from each node can be different. 

```python
# Bad
import socket
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(host, port)
```

## IRC2 Token Standard compliance
IRC2 compliant token must implement every functions in the specification. [IRC2 ICON Token Standard](https://github.com/icon-project/IIPs/blob/master/IIPS/iip-2.md)

```python
# IRC2 functions
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

## IRC2 Token parameter name
When implementing IRC2 compliant token, make the parameter names in the function remain the same 
as defined in [IRC2 ICON Token Standard](https://github.com/icon-project/IIPs/blob/master/IIPS/iip-2.md).
```python
# Bad
def balanceOf(self, owner: Address) -> int:

# Good
def balanceOf(self, _owner: Address) -> int:
```

## Eventlog on Token Transfer
Token transfer must trigger Eventlog.
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

## Eventlog without Token Transfer
Do not trigger Transfer Eventlog without token transfer.   
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

## ICXTransfer Eventlog
ICXTransfer Eventlog is reserved for ICX transfer. Do not implement the Eventlog with the same name. 
```python
# Bad
@eventlog(indexed=3)
def ICXTransfer(self, _from: Address, _to: Address, _value: int):
```

## External Function Parameter Check
If a SCORE function is called from EOA with wrong parameter types or without required parameters, ICON service will return an error. 
Developers do not need to deliberately verify the input parameter types inside a function.   

## Internal Function Parameter Check
If a SCORE calls other functions of own or of other SCORE, always make sure that parameter types are correct and required parameters are not omitted. 
Values of parameters must be in a valid range. 
There is no size limit in `str` or `int` type in Python, however, transaction message shoud not exceed 512KB.    
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

## Predictable arbitrarity
Some applications such as lottery require arbitrarity. Due to the nature of blockchain, implementation of such business logic must be done with great care. 
Output of pseudo random number generator can be predictable if random seed is revealed.    
```python
# Bad
# block height is predictable.
won = block.height % 2 == 0
```


