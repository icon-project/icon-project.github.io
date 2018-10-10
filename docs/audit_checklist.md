# How to write SCORE
This document explains ICON audit creteria and suggests secure SCORE implementation practices.
Audit checklist consists of 2 severity levels of items, Critical and Warning.
Audit results will come as Pass/Fail/NA for each Critical items, and Pass/Warning/NA for each Warning items.
If any Critical item is determined to be Fail, the SCORE deployement will be rejected.

Listed below are the checklist grouped by severity.
We assume that you have read [this](https://github.com/icon-project/icon-service/blob/master/docs/dapp_guide.md), and understand the basics of SCORE development.
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
- [Unchecked Low Level Calls](#unchecked-low-level-calls)
- [Super Class](#super-class)
- [Underflow/Overflow](#underflowoverflow)
- [Vault](#vault)
- [Reentrancy](#reentrancy)

# Critical
## Timeout
SCORE function must return fairly immediately. Blockchain is not for any long-running operation.
For example, if you implement token airdrop to many users, do not iterate over all users in a single function. Handle each or partial airdrop(s) one by one instead.

```python
# Bad
@external
def airDropToken(self, _value: int, _data: bytes = None):
    for target in self._very_large_targets:
        self._transfer(self.msg.sender, target, _value, _data)

# Good
@external
def airDropToken(self, _to: Address, _value: int, _data: bytes = None):
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
ICON network will force-kill the hanging task, but it may still degrade significantly the ICON network.

```python
# Bad
while True:
    // do something without consuming 'step' or proper exit condition

# Good
i = 0
while i < 10:
    // do something
    i += 1
```

## Package import
SCORE must run in a sandboxed environment.
Package import is prohibited except `iconservice` and the files in your deployed SCORE folder tree.

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
Execution result of SCORE must be deterministic. Unless, nodes can not reach a consensus.
If nodes fail to reach a consensus due to the undeterministic outcomes, every transactions in the block will be lost.
Therefore, not only random function, but any attempt to prevent block generation by undeterministic operation is strictly prohibited.

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
def transfer(self, _to: Address, _value: int, _data: bytes = None):
    self._balances[self.msg.sender] -= _value
    self._balances[_to] += _value
    self.Transfer(self.msg.sender, _to, _value, _data)
```

## Eventlog without Token Transfer
Do not trigger Transfer Eventlog without token transfer.
```python
# Bad
@eventlog(indexed=3)
def Transfer(self, _from: Address, _to: Address, _value: int, _data: bytes):
    pass

@external
def doSomething(self, _to: Address, _value: int):
    // no token transfer occurred
    self.Transfer(self.msg.sender, _to, _value, None)
```

## ICXTransfer Eventlog
ICXTransfer Eventlog is reserved for ICX transfer. Do not implement the Eventlog with the same name.
```python
# Bad
@eventlog(indexed=3)
def ICXTransfer(self, _from: Address, _to: Address, _value: int):
```

# Warning
## External Function Parameter Check
If a SCORE function is called from EOA with wrong parameter types or without required parameters, ICON service will return an error.
Developers do not need to deliberately verify the input parameter types inside a function.

## Internal Function Parameter Check
If a SCORE calls other functions of own or of other SCORE, always make sure that parameter types are correct and required parameters are not omitted.
Values of parameters must be in a valid range.
There is no size limit in `str` or `int` type in Python, however, transaction message should not exceed 512 KB.
```python
# Function declarations
def myTransfer(_value: int) -> bool:
def myTransfer1(_value: int, _extra: str) -> bool:

# Bad
myTransfer("1000")
myTransfer1(1000)

# Good
myTransfer(1000)
myTransfer1(1000, 'abc')
```

## Predictable arbitrarity
Some applications such as lottery require arbitrarity. Due to the nature of blockchain, implementation of such business logic must be done with great care.
Output of pseudo random number generator can be predictable if random seed is revealed.
```python
# Bad
# block height is predictable.
won = block.height % 2 == 0
```
## Unchecked Low Level Calls
In case of sending ICX by calling a low level function such as 'icx.send', you should check the execution result of 'icx.send' and handle the failure properly. 'icx.send' returns boolean result of its execution, and does not raise an exception on failure. On the other hand, 'icx.transfer' raises an exception if transaction fails. If the SCORE does not catch the exception, the transaction will be reverted. [Reference: An object used to transfer icx coin]( https://github.com/icon-project/icon-service/blob/master/docs/dapp_guide.md#icx--an-object-used-to-transfer-icx-coin)

```python

# Bad
self._refund_icx_amount[_to] += amount
self.icx.send(_to)

# Good
self._refund_icx_amount[_to] += amount
if not self.icx.send(_to, amount):
    self._refund_icx_amount[_to] -= amount

# Good
self._refund_icx_amount[_to] += amount
self.icx.transfer(_to, amount)
```

## Super Class
In your SCORE main class that inherits IconScoreBase, you must call super().\_\_init\_\_() in the \_\_init\_\_() function to initialize the state DB. Likewise, super().on_install() must be called in on_install() function and super().on_update() must be called in on_update() function.
```python
# Bad
class MyClass(IconScoreBase):
    def __init__(self, db: IconScoreDatabase) -> None:
        self._context__name = VarDB('context.name', db, str)
        self._context__cap = VarDB('context.cap', db, int)

    def on_install(self, name: str, cap: str) -> None:
        # doSomething

    def on_update(self) -> None:
        # doSomething

# Good
class MyClass(IconScoreBase):
    def __init__(self, db: IconScoreDatabase) -> None:
        super().__init__(db)
        self._context__name = VarDB('context.name', db, str)
        self._context__cap = VarDB('context.cap', db, int)

    def on_install(self, name: str, cap: str) -> None:
        super().on_install()
        # doSomething

    def on_update(self) -> None:
        super().on_update()
        # doSomething
```

## Underflow/Overflow
When you do arithmetic, It is really important to validate that operands and results are in the designed range.
```python
# Bad
@external
def mintToken(self, _amount: int):
    if not msg.sender == self.owner:
        self.revert('Only owner can mint token')

    # if _amount is below zero, self._balances[self.owner] and self._total_supply can be minus potentially
    self._balances[self.owner] = self._balances[self.owner] + _amount
    self._total_supply.set(self._total_supply.get() + _amount)

    self.Transfer(EOA_ZERO, self.owner, _amount, b'mint')

# Good  
@external
def mintToken(self, _amount: int):
    if not msg.sender == self.owner:
        self.revert('Only owner can mint token')
    if _amount <= 0:
        self.revert('_amount should be greater than 0')

    self._balances[self.owner] = self._balances[self.owner] + _amount
    self._total_supply.set(self._total_supply.get() + _amount)

    self.Transfer(EOA_ZERO, self.owner, _amount, b'mint')
```

## Vault
Anybody can view the data stored in public blockchain network. It is strongly recommended to save personal data such as password off the blockchain network even if it is encrypted.
```python
# Bad
def changePassword(self, _account: Account, _passwd: str):
    if msg.sender != _account:
        self.revert('Only owner of the account can change password')

    self.passwords[_account] = _passwd
```

## Reentrancy
When you send ICX or token, keep it mind that the target could be a SCORE. If the SCORE's fallback or tokenFallback function is implemented maliciously, it could reenter original SCORE. Then there could be unintended loop between two SCOREs.
```python
# Bad
# refund function in SCORE1. (assume ICX:token ratio is 1:1)
def refund(self, _to:Address, _amount:int):
    if msg.sender != _to:
        self.revert('Only owner of the account can request refund')
    if token_balances[_to] < _amount:
        self.revert('Not enough balance')

    self.icx.transfer(_to, _amount)
    self.token_balances[_to] -= _amount

# malicious fallback function in SCORE2
@payable
def fallback(self):
    is msg.sender == SCORE1_ADDRESS:
        # call refund of SCORE1 Again
        score1 = self.create_interface_score(SCORE1_ADDRESS, Score1Interface)
            score1.refund(self.msg.sender, bigAmountOfICX)

# Good
# refund function in SCORE1
def refund(self, _to:Address, _amount:int):
    if msg.sender != _to:
        self.revert('Only owner of the account can request refund')
    if token_balances[_to] < _amount:
        self.revert('Not enough balance')

    # decrease balance first
    self.balances[_to] -= _amount
    self.icx.transfer(_to, _amount)

# Good
# refund function in SCORE1
def refund(self, _to:Address, _amount:int):
    if msg.sender != _to:
        self.revert('Only owner of the account can request refund')
    if token_balances[_to] < _amount:
        self.revert('Not enough balance')

    # block if _to is smart contract
    if _to.is_contract:
        self.revert('ICX can not be transferred to SCORE')

    self.icx.transfer(_to, _amount)
    self.balances[_to] -= _amount
```
