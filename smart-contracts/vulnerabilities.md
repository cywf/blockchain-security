# Smart Contract Vulnerabilities

1. **Reentrancy**

_A reentrancy vulnerability occurs when a function can be called multiple times before its execution is completed, potentially leading to unintended behavior_

```solidity
pragma solidity ^0.8.0;

contract VulnerableBank {
    mapping(address => uint256) public balances;

    function withdraw(uint256 _amount) public {
        require(balances[msg.sender] >= _amount, "Insufficient balance");

        (bool success, ) = msg.sender.call{value: _amount}("");
        require(success, "Transfer failed");

        balances[msg.sender] -= _amount;
    }
}
```

2. **Arithmetic overflows and underflows**

_Overflow and underflow issues occur when a variable reaches its maximum or minimum value and wraps around._

Example:

```solidity
pragma solidity ^0.8.0;

contract OverflowUnderflow {
    uint256 public counter;

    function increment() public {
        counter += 1; // potential overflow
    }

    function decrement() public {
        counter -= 1; // potential underflow
    }
}
```

3. **Timestamp dependence**

_Smart contracts that rely on timestamps can be manipulated by miners or may be affected by network latency._

Example:

```solidity
pragma solidity ^0.8.0;

contract TimestampDependence {
    uint256 public endTime;

    constructor(uint256 _duration) {
        endTime = block.timestamp + _duration;
    }

    function timeDependentFunction() public {
        require(block.timestamp < endTime, "Time has expired");
        // Perform some action
    }
}
```

4. **Unsafe delegate calls**

_Delegate calls allow a contract to execute code from another contract while maintaining the context of the calling contract._
Example:

```solidity
pragma solidity ^0.8.0;

contract DelegateCallVulnerable {
    function executeDelegateCall(address _target, bytes memory _data) public {
        (bool success, ) = _target.delegatecall(_data);
        require(success, "Delegate call failed");
    }
}
```

5. **Short address attack**

_A short address attack can occur when an exchange or service doesn't properly validate the length of an address, allowing an attacker to craft a transaction with a shorter address._

Example: This vulnerability is not directly in the smart contract but rather in the external service interacting with it.

6. **Denial of service (DoS) attacks**

_DoS attacks can occur when a contract becomes unusable due to an attacker's actions, such as blocking other users from performing actions._

Example:

```solidity
pragma solidity ^0.8.0;

contract DOSVulnerable {
    address payable public owner;

    constructor() {
        owner = payable(msg.sender);
    }

    function withdraw() public {
        require(msg.sender == owner, "Not the owner");
        owner.transfer(address(this).balance);
    }

    receive() external payable {
        // An attacker can make this contract receive Ether
    }
}
```