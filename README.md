# eth-todo-list
智能合約教學

事前安裝:
1.Ganache(自己本地端的區塊鍊)
https://trufflesuite.com/ganache/

2.Node.JS(為了安裝npm而安裝)
https://nodejs.org/en/

3.Truffle(管理智能合約專案套件)
npm install -g truffle@5.0.2

4.Metamask(虛擬貨幣錢包)
https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn?hl=zh-TW

專案開始:
1. $ mkdir eth-todo-list(創建一個專案)
2. $ cd eth-todo-list(進入該專案)
3. $ truffle init(初始化智能合約專案管理套件)
4. $ touch package.json(建立package.json檔案)
5. package.json貼上 
{
  "name": "eth-todo-list",
  "version": "1.0.0",
  "description": "Blockchain Todo List Powered By Ethereum",
  "main": "truffle-config.js",
  "directories": {
    "test": "test"
  },
  "scripts": {
    "dev": "lite-server",
    "test": "echo \"Error: no test specified\" && sexit 1"
  },
  "author": "gregory@dappuniversity.com",
  "license": "ISC",
  "devDependencies": {
    "bootstrap": "4.1.3",
    "chai": "^4.1.2",
    "chai-as-promised": "^7.1.1",
    "chai-bignumber": "^2.0.2",
    "lite-server": "^2.3.0",
    "nodemon": "^1.17.3",
    "truffle": "5.0.2",
    "truffle-contract": "3.0.6"
  }
}
6.npm install(安裝上述devDependencies的套件)
7.$ touch ./contracts/TodoList.sol(在contracts下建立一個.sol智能合約檔案)
8.TodoList.so貼上
pragma solidity ^0.5.0;

contract TodoList {
  uint taskCount = 0;
}
9.$ truffle compile
10.開啟ganache記得去看port
11.truffle-config.js改成
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8454,
      network_id: "*" // Match any network id
    }
  },
  solc: {
    optimizer: {
      enabled: true,
      runs: 200
    }
  }
}

重要! port:8454要跟ganache server port一樣才能對上

12.$ touch migrations/2_deploy_contracts.js
13.貼上
var TodoList = artifacts.require("./TodoList.sol");

module.exports = function(deployer) {
  deployer.deploy(TodoList);
};

14.$ truffle migrate
15.$ truffle console

出現佈署到ganache的log就代表成功了

16.truffle console內打 todoList = await TodoList.deployed()

todoList.address
// => '0xABC123...'

17.將TodoList.so改成
pragma solidity ^0.5.0;

contract TodoList {
  uint public taskCount = 0;

  struct Task {
    uint id;
    string content;
    bool completed;
  }

  mapping(uint => Task) public tasks;
  
  constructor() public {
        createTask("Check out dappuniversity.com");
  }

  function createTask(string memory _content) public {
    taskCount ++;
    tasks[taskCount] = Task(taskCount, _content, false);
  }

}

記得存檔

18.重新truffle compile=>truffle migrate=>truffle console

19.todoList = await TodoList.deployed()
todoList就會多了createTask function



參考來源:https://www.dappuniversity.com/articles/blockchain-app-tutorial#dependencies(發生多次錯誤或者版本不適用的情況)
