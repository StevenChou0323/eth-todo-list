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

9.開啟ganache記得去看port
10.truffle-config.js改成
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
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

重要! port:8545要跟ganache server port一樣才能對上

11.$ touch migrations/2_deploy_contracts.js
12.貼上
var TodoList = artifacts.require("./TodoList.sol");

module.exports = function(deployer) {
  deployer.deploy(TodoList);
};

13.$ truffle compile(有新增或是異動檔案要先compile 再migrate)
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

20.手動新增此三個檔案
bs-config.json
src/index.html
src/app.js

21.bs-config.json貼上
{
  "server": {
    "baseDir": [
      "./src",
      "./build/contracts"
    ],
    "routes": {
      "/vendor": "./node_modules"
    }
  }
}

/vendor可以把./node_modules路徑替換掉

22.index.html內貼上
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <!-- The above 3 meta tags *must* come first in the head; any other head content must come *after* these tags -->
    <title>Dapp University | Todo List</title>

    <!-- Bootstrap -->
    <link href="vendor/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" />

    <!-- HTML5 shim and Respond.js for IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
      <script src="https://oss.maxcdn.com/html5shiv/3.7.3/html5shiv.min.js"></script>
      <script src="https://oss.maxcdn.com/respond/1.4.2/respond.min.js"></script>
    <![endif]-->

    <style>
      main {
        margin-top: 60px;
      }

      #content {
        display: none;
      }

      form {
        width: 350px;
        margin-bottom: 10px;
      }

      ul {
        margin-bottom: 0px;
      }

      #completedTaskList .content {
        color: grey;
        text-decoration: line-through;
      }
    </style>
  </head>
  <body>
    <nav class="navbar navbar-dark fixed-top bg-dark flex-md-nowrap p-0 shadow">
      <a
        class="navbar-brand col-sm-3 col-md-2 mr-0"
        href="http://www.dappuniversity.com/free-download"
        target="_blank"
        >Dapp University | Todo List</a
      >
      <ul class="navbar-nav px-3">
        <li class="nav-item text-nowrap d-none d-sm-none d-sm-block">
          <small
            ><a class="nav-link" href="#"><span id="account"></span></a
          ></small>
        </li>
      </ul>
    </nav>
    <div class="container-fluid">
      <div class="row">
        <main role="main" class="col-lg-12 d-flex justify-content-center">
          <div id="loader" class="text-center">
            <p class="text-center">Loading...</p>
          </div>
          <div id="content">
            <!-- <form onSubmit="App.createTask(); return false;">
              <input id="newTask" type="text" class="form-control" placeholder="Add task..." required>
              <input type="submit" hidden="">
            </form> -->
            <ul id="taskList" class="list-unstyled">
              <div class="taskTemplate" class="checkbox" style="display: none">
                <label>
                  <input type="checkbox" />
                  <span class="content">Task content goes here...</span>
                </label>
              </div>
            </ul>
            <ul id="completedTaskList" class="list-unstyled"></ul>
          </div>
        </main>
      </div>
    </div>

    <!-- jQuery (necessary for Bootstrap's JavaScript plugins) -->
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.4/jquery.min.js"></script>
    <!-- Include all compiled plugins (below), or include individual files as needed -->
    <script src="vendor/bootstrap/dist/js/bootstrap.min.js"></script>
    <script src="vendor/truffle-contract/dist/truffle-contract.js"></script>
    <script src="app.js"></script>
    <script type="text/javascript" src="vendor/web3/dist/web3.js"></script>
    <script
      type="text/javascript"
      src="vendor/truffle-contract/dist/truffle-contract.js"
    ></script>
  </body>
</html>

這兩個路徑是為了解決找不到Web3跟TruffleContract(可能是版本問題)
目前手動解決路徑問題
<script src="vendor/truffle-contract/dist/truffle-contract.js"></script>
<script type="text/javascript" src="vendor/web3/dist/web3.js"></script>

23.app.js內貼上
App = {
  loading: false,
  contracts: {},

  load: async () => {
    await App.loadWeb3()
    await App.loadAccount()
    await App.loadContract()
    await App.render()
  },

  // https://medium.com/metamask/https-medium-com-metamask-breaking-change-injecting-web3-7722797916a8
  loadWeb3: async () => {
    if (typeof web3 !== 'undefined') {
      App.web3Provider = web3.currentProvider
      web3 = new Web3(web3.currentProvider)
    } else {
      window.alert("Please connect to Metamask.")
    }
    // Modern dapp browsers...
    if (window.ethereum) {
      window.web3 = new Web3(ethereum)
      try {
        // Request account access if needed
        await ethereum.enable()
        // Acccounts now exposed
        web3.eth.sendTransaction({/* ... */})
      } catch (error) {
        // User denied account access...
      }
    }
    // Legacy dapp browsers...
    else if (window.web3) {
      App.web3Provider = web3.currentProvider
      window.web3 = new Web3(web3.currentProvider)
      // Acccounts always exposed
      web3.eth.sendTransaction({/* ... */})
    }
    // Non-dapp browsers...
    else {
      console.log('Non-Ethereum browser detected. You should consider trying MetaMask!')
    }
  },

  loadAccount: async () => {
    // Set the current blockchain account
    App.account = web3.eth.accounts[0]
  },

  loadContract: async () => {
    // Create a JavaScript version of the smart contract
    const todoList = await $.getJSON('TodoList.json')
    App.contracts.TodoList = TruffleContract(todoList)
    App.contracts.TodoList.setProvider(App.web3Provider)

    // Hydrate the smart contract with values from the blockchain
    App.todoList = await App.contracts.TodoList.deployed()
  },

  render: async () => {
    // Prevent double render
    if (App.loading) {
      return
    }

    // Update app loading state
    App.setLoading(true)

    // Render Account
    $('#account').html(App.account)

    // Render Tasks
    await App.renderTasks()

    // Update loading state
    App.setLoading(false)
  },

  renderTasks: async () => {
    // Load the total task count from the blockchain
    const taskCount = await App.todoList.taskCount()
    const $taskTemplate = $('.taskTemplate')

    // Render out each task with a new task template
    for (var i = 1; i <= taskCount; i++) {
      // Fetch the task data from the blockchain
      const task = await App.todoList.tasks(i)
      const taskId = task[0].toNumber()
      const taskContent = task[1]
      const taskCompleted = task[2]

      // Create the html for the task
      const $newTaskTemplate = $taskTemplate.clone()
      $newTaskTemplate.find('.content').html(taskContent)
      $newTaskTemplate.find('input')
                      .prop('name', taskId)
                      .prop('checked', taskCompleted)
                      // .on('click', App.toggleCompleted)

      // Put the task in the correct list
      if (taskCompleted) {
        $('#completedTaskList').append($newTaskTemplate)
      } else {
        $('#taskList').append($newTaskTemplate)
      }

      // Show the task
      $newTaskTemplate.show()
    }
  },

  setLoading: (boolean) => {
    App.loading = boolean
    const loader = $('#loader')
    const content = $('#content')
    if (boolean) {
      loader.show()
      content.hide()
    } else {
      loader.hide()
      content.show()
    }
  }
}

$(() => {
  $(window).load(() => {
    App.load()
  })
})

24.npm run dev(將此專案開啟)
有改code記得要先truffle compile跟truffle migrate
npm run dev才會是最新的code

目前會長這樣(卡loading 按F12看看有沒有錯誤log)
![image](https://user-images.githubusercontent.com/17786940/170486460-f3b0550d-0364-4238-bd32-6c3b55c9384c.png)

但是metamask還沒有跟專案連動
教學參考
https://www.youtube.com/watch?t=43m55s&v=rzvk2kdjr2I&feature=youtu.be

24.先登入metamask
25.小狐狸錢包網路選localhost
![image](https://user-images.githubusercontent.com/17786940/170486785-aa5803bb-a605-4b5c-bf03-69403a891c43.png)

26.接著到ganache去找帳號最後面的鑰匙
點開後會有個private key複製後
![image](https://user-images.githubusercontent.com/17786940/170486975-291b6c3d-0d0f-4fa8-98da-b92618f2c2a8.png)

27.到小狐狸錢包"匯入錢包"把private key貼上
![image](https://user-images.githubusercontent.com/17786940/170486904-780b5ce2-1fa1-452a-b996-5264df282df5.png)

28.確認後小狐狸錢包就可以跟ganache連動了

補充:
如何讓remix ide連動到localhost
先安裝npm install -g @remix-project/remixd
再透過remixd -s C:\Users\Steven\Desktop\eth-todo-list
再到remix ide選localhost即可
![image](https://user-images.githubusercontent.com/17786940/170493654-3ec31738-eb80-47d7-86c8-ad6ad4d13b3c.png)


參考來源:https://www.dappuniversity.com/articles/blockchain-app-tutorial#dependencies(發生多次錯誤或者版本不適用的情況)
