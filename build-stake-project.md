# بناء عقد ذكي لمشروع Stake

انت تقوم ببناء عقد ذكي يقوم بعملية يمكن من خلالها لمستخدمي شبكة blockchain بالمشاركة في آلية إجماع الشبكة وكسب مكافآت للقيام بذلك.

## المتطلبات الاساسية للبدء في هذا الدرس:

1. يمكنك التعامل مع لغة البرمجة JavaScript.
2. انتهيت من قرأة درس اساسيات لغة Solidity.
3. لقد قمت بإعداد محفظتك على Metamask.
4. لقد قمت بتثبيت Node.js على حهاز الكمبيوتر الخاص بك.

## العقد الذكي

سنستخدم احد الادوات التي ستساعدنا في التعامل مع العقود الذكية وهي Hardhat. يعتبر Hardhat بيئة وإطار تطوير شبكة Ethereum مصمم للتعامل بشكل كامل مع لغة Solidity.

سنقوم بإنشاء مشروع بإسم Todolist-Dapp والذي سيحتوي على تطبيق Hardhat وعقدنا الذكي.

```bash
mkdir Staking-Dapp
cd Staking-Dapp/
```

سنقوم بإعداد npm في التطبيق وتثبيت Hardhat

```bash
npm init --yes
npm install --save-dev hardhat
npm install --save-dev @nomicfoundation/hardhat-toolbox@2
```

سنقوم الان بتشغيل تطبيق Hardhat

```bash
npx hardhat
```

#### سنلاحظ ان التطبيق يحتوي على 3 مجلدات رئيسية وهي:

1. contracts: الذي سنقوم من خلاله بكتابة العقود الذكية.
2. scripts: والذي سنقوم من خلاله بالتعامل مع العقود الذكية او رفعها على الشبكات.
3. test: والذي سنقوم من خلاله بإجراء اختبارات لعقدنا الذكي.

يمكنك الان البدء في إنشاء عقدك الذكي. قم بإنشاء ملف في مجلد contracts بإسم Staking.sol

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

contract Staking {
  uint256 private totalStaked;
  mapping(address => uint256) public stakedAmounts;

  event Staked(address indexed account, uint256 amount);
  event UnStaked(address indexed account, uint256 amount);
    
  /**
    Stake تمثل مقدار الرموز أو الأثير الذي يريد المستخدم مشاركته في uint256 من النوع amount_  تأخذ الدالة وسيطة واحدة

    أكبر من الصفر مما يضمن عدم قدرة المستخدم على إيداع كمية سلبية _amount مما إذا كان require تحقق البيانات المسبقة الأولى    

    الذي يتم إيداعه بواسطة المستخدم عن طريق ربط عنوانهم بالمبلغ الذي يملكونه _amount باستخدام stakedAmounts إليه كما يحدث الدالة تحديثًا على التناظر الذي يسمى _amount عن طريق إضافة totalStaked تحدث الدالة بعد ذلك تحديثًا لمتغير
  */
  function stake(uint256 _amount) public payable {
    require(_amount > 0);
    require(msg.value >= _amount, "Staked amount must match the value being sent.");

    totalStaked += _amount;
    stakedAmounts[msg.sender] += _amount;
    (bool success, ) = payable(msg.sender).call{value: (msg.value - _amount)}("");
    require(success, "Failed!");
    emit Staked(msg.sender, _amount);
  }
    
  /**
    وتأخذ معامل واحد وهو مقدار الرموز الذي يريد المستخدم إلغاء رهنها في العقد unstake الوظيفة تسمى

    بفرض أن لدى المستخدم الرموز المرهونة الكافية ستقوم الوظيفة بتحديث إجمالي المبلغ المرهون وطرح المبلغ المراد إلغاؤه من تعيين مقدار الرموز المرهونة للمستخدم
  */
  function unstake() public payable {
    require(stakedAmounts[msg.sender] > 0, "Not enough staked amount to unstake.");

    uint256 _amount = stakedAmounts[msg.sender];
    payable(msg.sender).transfer(_amount);
    totalStaked -= _amount;
    stakedAmounts[msg.sender] -= _amount;
    emit UnStaked(msg.sender, _amount);
  }

  /**
    في العقد الذكي Stake تقوم هذه الدالة بإرجاع إجمالي الرموز التي تم عمل لها
  */
  function getTotalStaked() public view returns (uint256) {
    return totalStaked;
  }

  receive() external payable {}

  fallback() external payable {}
}
```

> كل ما تحتاجه الان لاكمال عقدك الذكي هو كتابة الوظائف التي تريد تشغليها في داخل العقد الذكي.

يمكنك الان رفع عقدك الذكي بكل سهولة. سنقوم باستخدام شبكة الاختبارات وهي sepolia.
اذهب الى المجلد scripts وقم بإنشاء ملف بإسم deploy.js

```js
const {ethers} = require("hardhat");

async function main() {
	/*
    لنشر عقود ذكية جديدة getContractFactory يستخدم
  */
  const StackingContract = await ethers.getContractFactory("Staking");

	// هنا نقوم برفع العقد
  const contract = await StackingContract.deploy();

  // انتظر حتى تنتهي عملية الرفع
  await contract.deployed();

  // طباعة عنوان العقد المنشور
  console.log(
    `Contract is deployed to ${contract.address}`
  );
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

الان ستحتاج الى مزود عقدة يتيح لك الاتصال بالعديد من سلاسل الكتل المختلفة. يمكنك استخدام <a href="https://alchemy.com/" target="_blank">Alchemy</a> كمزود للعقد الخاصة بك بكل سهولة.

قم بإنشاء حساب في منصة <a href="https://alchemy.com/" target="_blank">Alchemy</a> وإذا كان لديك حساب بالفعل قم بتسجيل الدخول وقم بإنشاء تطبيق (CREATE APP) وقم بكتابة اسم لتطبيقك وتحديد شبكة (Sepolia) وقم بالنقر على (CREATE APP) ومن ثم قم بنسخ رابط المفتاح (HTTPS).

**ملاحظة**: يمكن الحصول على بعض العملات التي تساعدك في اختبار ونشر تطبيقاتك على شبكة **Sepolia** من <a href="https://sepoliafaucet.com/" target="_blank">**Alchemy Faucet**</a>

<img src="/courses/alchemy-build.png" alt="Alchemy build"/>

يمكنك الحصول على (Private Key) انقر فوق النقاط الثلاث ، وانقر فوق (Account Details) ثم (Export Private Key).

<img src="/courses/alchemy-keys.png" alt="Alchemy keys"/>

قم بإضافة كل ما قمت بنسخه في ملف (.env)

```bash
ALCHEMY_HTTPS_URL="add-alchemy-http-url-here"

PRIVATE_KEY="add-private-key-here"
```

قم بتثبيت حزمة dotenv حتى تتمكن من إستيراد ملف .env

```bash
npm install dotenv
```

قم بفتح ملف hardhat.config.js وقم باستيراد المفاتيح المتواجدة في ملف .env وقم بإختيار الشبكة التي تريد استخدامها لرفع العقد الذكي الخاص بك ولكننا هنا سنستخدم شبكة sepolia فلذلك سنقوم بتحديدها

```js
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config({ path: ".env" });

const ALCHEMY_HTTPS_URL = process.env.ALCHEMY_HTTPS_URL;
const PRIVATE_KEY = process.env.PRIVATE_KEY;

module.exports = {
  solidity: "0.8.18",
  networks: {
    sepolia: {
      url: ALCHEMY_HTTPS_URL,
      accounts: [PRIVATE_KEY],
    },
  },
}
```

قم بتجميع العقد الذكي الخاص بك الان. تأكد من انك في مسار تطبيقك (Todolist-Dapp) وقم بتشغيل هذا الامر

```bash
npx hardhat compile
```

حان وقت نشر عقدك الذكي :) قم بكتابة هذا الامر

```bash
npx hardhat run scripts/deploy.js --network sepolia
```

> قم بحفظ عنوان عقدك الذكي حتى نتمكن من استخدامه اثناء جعله يعمل في الواجهة الامامية.

## ربط المشروع بالواجهة الأمامية بإستخدام Next.js ومكتبة Web3.js

1. إنشاء مشروع Next.js

```bash
npx create-next-app my-app
cd my-app/
```

2. تثبيت مكتبة Web3.js

```bash
npm install web3
```

3. عليك الان الوصول الى العقد الذكي من خلال إضافة address العقد الذكي و ABI. ستقوم بإنشاء ملف بإسم *info.js* في مشروع next.js

بعد إنشاء ملف *info.js* ستقوم بنسخ هذا وإضافة ما يتعلق بعقد الذكي مثل address و ABI

```js
// قم بإضافة عنوان عقدك الذكي
export const contractAddress = "0x123456789abcdef0123456xxxxxxxxxxxxxxx"

// hardhat في مشروع artifacts/contracts المتعلقة بعقدك الذكي من مجلد ABI قم بنسخ
export const contractABI = [
  {
    "anonymous": false,
    "inputs": [
      {
        "indexed": true,
        "internalType": "address",
        "name": "account",
        "type": "address"
      },
      {
        "indexed": false,
        "internalType": "uint256",
        "name": "amount",
        "type": "uint256"
      }
    ],
    "name": "Staked",
    "type": "event"
  },
  {
    "anonymous": false,
    "inputs": [
      {
        "indexed": true,
        "internalType": "address",
        "name": "account",
        "type": "address"
      },
      {
        "indexed": false,
        "internalType": "uint256",
        "name": "amount",
        "type": "uint256"
      }
    ],
    "name": "UnStaked",
    "type": "event"
  },
  {
    "stateMutability": "payable",
    "type": "fallback"
  },
  {
    "inputs": [],
    "name": "getTotalStaked",
    "outputs": [
      {
        "internalType": "uint256",
        "name": "",
        "type": "uint256"
      }
    ],
    "stateMutability": "view",
    "type": "function"
  },
  {
    "inputs": [
      {
        "internalType": "uint256",
        "name": "_amount",
        "type": "uint256"
      }
    ],
    "name": "stake",
    "outputs": [],
    "stateMutability": "payable",
    "type": "function"
  },
  {
    "inputs": [
      {
        "internalType": "address",
        "name": "",
        "type": "address"
      }
    ],
    "name": "stakedAmounts",
    "outputs": [
      {
        "internalType": "uint256",
        "name": "",
        "type": "uint256"
      }
    ],
    "stateMutability": "view",
    "type": "function"
  },
  {
    "inputs": [],
    "name": "unstake",
    "outputs": [],
    "stateMutability": "payable",
    "type": "function"
  },
  {
    "stateMutability": "payable",
    "type": "receive"
  }
]
```

4. الان اذهب الى الملف *index.js* وقم بلصق هذا الكود ومتابعة الشرح من التعليقات المتواجدة اعلى كل سطر.

```jsx
import { useEffect } from 'react';
import Web3 from 'web3';
import { contractAddress, contractABI } from '../info';

export default function Home() {
  // Ethereum الاتصال بشبكة
  const web3 = new Web3(Web3.givenProvider);

  // يعمل هذا على اخذ مثيل للعقد بحيث نتمكن من التفاعل مع البلوكتشين
  const contract = new web3.eth.Contract(contractABI, contractAddress)

  // stake تعمل هذه الدالة بعمل
  async function deposit() {
    // Ethereum استدعاء الحساب المتصل بشبكة
    const accounts = await web3.eth.getAccounts();
    // (يمكنك تحديدها كما تريد) ETH التي تم كتابتها في العقد الذكي وإرسال مبلغ معين من stake التفاعل مع الدالة
    const result = await contract.methods.stake(web3.utils.toWei("0.005", "ether")).send({ from: accounts[0], value: web3.utils.toWei("0.005", "ether") });
    // طباعة النتيجة
    console.log(result);
  }

  // unstake تعمل هذه الدالة بعمل
  async function withdraw() {
    // Ethereum استدعاء الحساب المتصل بشبكة
    const accounts = await web3.eth.getAccounts();
    // التي تم كتابتها في العقد الذكي unstake التفاعل مع الدالة
    const result = await contract.methods.unstake().send({ from: accounts[0] });
    // طباعة النتيجة
    console.log(result);
  }

  // DApp تعمل هذه الدالة على عملية اتصال المحفظة بتطبيق
  const connect = async () => {
    const accounts = await window.ethereum.enable()
    return accounts[0]
  }

  // تمثل المصفوفة في نهاية استدعاء الوظيفة ما هي تغييرات الحالة التي ستؤدي إلى هذا التغيير
  // في هذه الحالة كلما تغيرت قيم الوظيفتين سيتم استدعاء هذا التغيير مباشرة
  useEffect(() => {
    connect()
  }, [])

  return (
    <div>
      <h1>Deposit 0.005 ETH</h1>
      <button onClick={deposit}>Deposit</button>

      <h1>Withdraw all my Deposit</h1>
      <button onClick={withdraw}>Withdraw</button>
    </div>
  )
}
```

يمكنك تجربة تطبيقك الان
```bash
npm run dev
```

إنه يعمل, لقد انتهيت من بناء تطبيق DApps بنجاح 🥳🥳

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
