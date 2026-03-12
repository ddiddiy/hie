# BKC

## Prac 1
### 1. A simple client class that generates the public & private key by using the built-in python RSA algorithm and test it.
```python
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_OAEP

key = RSA.generate(2048)
private_key = key
public_key = key.publickey()

encryptor = PKCS1_OAEP.new(public_key)
decryptor = PKCS1_OAEP.new(private_key)

msg = b'Hello, World!'
ciphertext = encryptor.encrypt(msg)
print("Ciphertext:", ciphertext)

decrypted_msg = decryptor.decrypt(ciphertext)
print("Decrypted message:", decrypted_msg.decode())
```

### 2. A simple client class that generates the public & private key, ledger by using the built-in python RSA algorithm and test it.
```python
import binascii
import Crypto
from Crypto.PublicKey import RSA
from Crypto.Cipher import PKCS1_v1_5

class Client:
  def __init__(self):
    random = Crypto.Random.new().read
    self._private_key = RSA.generate(1024, random)
    self._public_key = self._private_key.publickey()
    self._signer = PKCS1_v1_5.new(self._private_key)

  @property
  def identity(self):
    return binascii.hexlify(self._public_key.exportKey(format='DER')).decode('ascii')

client = Client()
print("Public Key:", client.identity)
```

## Prac 3
### Find the no. of bitcoins
```python 
investment = float(input("Enter the initial investment amount: "))
btc_price = float(input("Enter the current price of Bitcoin: "))

btc_amt = investment / btc_price
print("Amount of Bitcoin purchased:", f"{btc_amt:8.8f}")
```

## Prac 4
### 1. Create a transaction class to send and receive money and test it 
```python
class Bank:
  def __init__(self):
    self.balance = 0
    print(f"The Account is created with balance: {self.balance}")

  def  deposit(self):
    amount = float(input("Enter the amount to deposit: "))
    self.balance += amount
    print(f"Amount deposited: {amount}, New balance: {self.balance}")

  def withdraw(self):
    amount = float(input("Enter the amount to withdraw: "))
    if amount <= self.balance:
      self.balance -= amount
      print(f"Amount withdrawn: {amount}, New balance: {self.balance}")
    else:
      print("Insufficient balance")

  def enquire(self):
    print(f"Current balance: {self.balance}")

account = Bank()
account.deposit()
account.withdraw()
account.enquire()
```
### 2. Setting up ethereum dev environment


## prac 5
### Ethereum Basic, Ethereum Wallet, Ethereum Transaction
- click on networks in metamask
- add custom network
- copy  the rpc url from the ganache and paste it in the custom network
- chain id is 1337 for ganache
- currency symbol is ETH
- network name is Localhost 8545
- then go to acciunts tab in metamask and click on import account
- copy the private key from ganache and paste it in the import account section in metamask
- now you can see the account in metamask with the balance of 100 ETH
- now click on send in metamask and enter the recipient address which we can get from another account in ganache
- enter the amount of ETH to send and click on next
- then click on confirm to send the transaction
- you can see the transaction in ganache and also in metamask

## Prac 6
### Smartcontract, solidity basics, writing and deploying smart contracts

- create a new woerkspace in remix
- cerate a blank project
- create a new file and name it tyToken.sol
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract tyToken {
    string public name = "tyToken";
    string public symbol = "TYIT";
    uint public totalsupply;
    mapping (address => uint256) public balanceOf;
    

    constructor(uint256 initialSupply) {
        totalsupply = initialSupply;
        balanceOf[msg.sender] = initialSupply;
    }

    function transfer(address toWhom, uint256 value)   public returns (bool sucess) {
        require(balanceOf[msg.sender] >= value, "Insuffient balance");
        balanceOf[msg.sender] -= value;
        balanceOf[toWhom] += value;
        return true;
    }
}
```

- compile the contract
- then go to deploy and run transactions
- select the contract and enter the initial supply in the deploy section
- click on deploy
- then we can eneter any account address and check the balance of that account by calling the balanceOf function
- then we can test the transfer function by entering the recipient address and the amount to transfer and click on transfer
- can verify the transfer by checking the balance of the sender and recipient accounts

## Prac 7
### Create a basic DEFI (decentraised Financial System)
- make a new file called erc.sol
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MyToken is ERC20 {
    constructor() ERC20("MyToken", "MTK") {
        _mint(msg.sender, 1000000 ether);
    }
}
```
- compile the contract
- deploy the contract
- then we can check the balance of the deployer account by calling the balanceOf function
* - Transferfrom *
- make a new file called stakes.sol
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract SimpleStaking {
    IERC20 public token;
    mapping(address => uint256) public stakes;
    constructor(address _token) {
        token = IERC20(_token);
    }

    function stake(uint256 amt) external  {
        require(amt > 0, "Zero Amt");
        token.transferFrom(msg.sender, address(this), amt);
        stakes[msg.sender] += amt;
    }

    function unstake(uint256 amt ) external {
        require(amt > 0, "Zero Amt");
        require(stakes[msg.sender] >= amt, "Not enough Stake");
        stakes[msg.sender] -= amt;
        token.transfer(msg.sender, amt);
    }
}
```

- compile the contract
- deploy the contract by passing the address of the previously deployed MyToken contract
- then we can stake some tokens by calling the stake function and passing the amount to stake

##  Prac 8
### 2.Write a solidity program for creating a smart contract for attendance.
- create a new file called attendance.sol
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

contract Attendance {
    mapping(uint256 => bool) public isPresent;

    constructor(uint256[] memory rollno) {
        for (uint256 i = 0; i < rollno.length; i++) {
            isPresent[rollno[i]] = true;
        }
    }

    function attend(uint256 roll) public view returns (bool) {
        return isPresent[roll];
    }
}
```
- compile the contract
- deploy the contract by passing an array of roll numbers of present students
- test the attend function by passing a roll number to check if the student is present or not
- it will return true if the student is present and false if the student is absent


### 6. Write a solidity program for creating a smart contract for donation.
- create a new file called donation.sol
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

contract Donation {
    address public owner;
    mapping ( address => uint) public balance;
    event Donated (address indexed from, uint256 amount);

    constructor() {
        owner = msg.sender;
    }

    function donate() public payable  {
        require(msg.value > 0, "The amt must be greater than zero");
        balance[msg.sender] += msg.value;
        emit Donated(msg.sender, msg.value);
    }

    function getDonationBalance() public view returns (uint256) {
        return address(this).balance;
    }
}
```
- compile the contract
- deploy the contract
- then we can donate some ether by calling the donate function and passing some ether along with the function call
- we can check the total donations received by calling the getDonationBalance function which will
return the balance of the contract which is the total donations received

## Prac 9
### 1. Interaction with smart contract using truffle and node.js
- setup  ganache and metmask as explained in prac 5
- then in cmd run `npm init -y` to initialize a new node.js project
- then run `npm install truffle -g` to install truffle
- then tun `npm i truffle lite-server` to install lite-server
- then run `npx truffle init` to initialize a new truffle project

- inside the contracts folder create a new file called add.sol
```solidity
pragma solidity ^0.8.0;

contract Add{
    function add(uint a, uint b)
    public pure returns(uint){
        return a + b;
    }
}
```
- inside the migrations folder create a new file called 1_deploy.js
```javascript
const Add = artifacts.require("Add");

module.exports = function (deployer) {
  deployer.deploy(Add);
};
```
- also in truffle-config.js file add the development network configuration as shown below
```javascript
     development: {
      host: "127.0.0.1",     // Localhost (default: none)
      port: 7545,            // Standard Ethereum port (default: none)
      network_id: "*",       // Any network (default: none)
     },
```
- and the change complier version to `0.8.0`
- then run `npx truffle compile --all` to compile the contract 
- then run `npx truffle migrate --reset` to deploy the contract to the local ganache network

## Prac 10
### 1. DApp

- create a new file called index.html
```html
<!doctype html>
<html>
  <head>
    <title>DApp</title>
    <script src="https://cdn.jsdelivr.net/npm/web3/dist/web3.min.js"></script>
  </head>

  <body>
    <h2>Add Two Numbers</h2>

    <input id="num1" placeholder="Number 1" />
    <input id="num2" placeholder="Number 2" />

    <button onclick="add()">Add</button>

    <h3 id="result"></h3>

    <script>
      let web3;
      let contract;

      const contractAddress = "0x5BD9fFE1844d33bba0C77CdA517AC758D0D6dc22";

      const abi = [
        {
          inputs: [
            { internalType: "uint256", name: "a", type: "uint256" },
            { internalType: "uint256", name: "b", type: "uint256" },
          ],
          name: "add",
          outputs: [{ internalType: "uint256", name: "", type: "uint256" }],
          stateMutability: "pure",
          type: "function",
        },
      ];

      window.onload = async () => {
        if (window.ethereum) {
          web3 = new Web3(window.ethereum);

          await window.ethereum.request({ method: "eth_requestAccounts" });

          contract = new web3.eth.Contract(abi, contractAddress);
        }
      };

      async function add() {
        let a = document.getElementById("num1").value;

        let b = document.getElementById("num2").value;

        let result = await contract.methods.add(a, b).call();

        document.getElementById("result").innerHTML = "Result = " + result;
      }
    </script>
  </body>
</html>
```
- after this compile and migrate the contract to get the contract address and then replace the contract address in the above code
- then run `npx lite-server` to start the server and open the index.html in the browser
- then you can enter two numbers and click on add to get the result of addition of those
