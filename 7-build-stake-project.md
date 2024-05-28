# بناء عقد ذكي لمشروع Stake

انت تقوم ببناء عقد ذكي يقوم بعملية يمكن من خلالها لمستخدمي شبكة blockchain بالمشاركة في آلية إجماع الشبكة وكسب مكافآت للقيام بذلك.

## المتطلبات الاساسية للبدء في هذا الدرس:

1. نتهيت من قرأة درس اساسيات لغة Solidity.
2. لقد قمت بإعداد محفظتك على Metamask.

## إعداد المشروع

خلال هذا الدرس ستقوم بإنشاء العقد الذكي من اجل تخزين بيانات الفيديوهات التي سيقوم المستخدمين بنشرها ومن ثم ربط العقد الذكي بالموقع الذي سنقوم ببنائه.

ستقوم بفتح terminal وستدخل هذه الاوامر:

```bash
mkdir Staking-Dapp & cd Staking-Dapp
mkdir contract-tutorial & mkdir my-app
```

<div dir="ltr" className="flex justify-center items-center">
<img src="https://www.web3arabs.com/courses/dapps/staking/settingup-project.png"/>
</div>

- في المجلد **contract-tutorial** سنقوم بإعداد ونشر العقد الذكي.
- في المجلد **my-app** سنقوم ببناء الواجهة الامامية للمشروع وربطها بالعقد الذكي في الدروس القادمة.

## العقد الذكي

سنستخدم أحد أقوى وأسرع الادوات التي ستساعدنا في التعامل مع العقود الذكية وهي Foundry. يعتبر Foundry هي بيئة وإطار تطوير شبكة Ethereum مصمم للتعامل بشكل كامل مع لغة Solidity.

يمكنك إعداد وتثبيت أداة Foundry في جهازك بواسطة أحد المقالات لدينا من هنا.

سنقوم بفتح المجلد contract-tutorial على terminal ونقوم بإنشاء مشروع Foundry بواسطة هذا الأمر:


```bash
cd contract-tutorial
forge init

```

سنقوم بحذف الملفات التلقائية بالمشروع التي لا نحتاجها عن طريق تشغيل هذا الأمر:


```bash
rm src/Counter.sol script/Counter.s.sol test/Counter.t.sol
```


#### سنلاحظ ان التطبيق يحتوي على 4 مجلدات رئيسية وهي:

1-lib: يحتوي على المُشغل الاساسي للمشروع بالكامل ولن نقوم بالتعديل عليه على الإطلاق.
2. contracts: الذي سنقوم من خلاله بكتابة العقود الذكية.
3. scripts: والذي سنقوم من خلاله بالتعامل مع العقود الذكية او رفعها على الشبكات.
4. test: والذي سنقوم من خلاله بإجراء اختبارات لعقدنا الذكي.

يمكنك الان البدء في إنشاء عقدك الذكي. قم بإنشاء ملف بإسم Staking.sol في مجلد src:


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

contract Staking {
  uint256 private totalStaked;
  // user -> eth
  mapping(address => uint256) public stakedAmounts;
  // user -> time
  mapping(address => uint256) public lastStakeTime;
  
  event Staked(address indexed account, uint256 amount);
  event UnStaked(address indexed account, uint256 amount);
    
  /**
    تعمل الدالة على إرسال عملات الى العقد الذكي وإضافة الوقت الذي تم فيه إرسال العملات

    مما إذا كان المبلغ أكبر من الصفر require تعمل عملية التحقق    

    stakedAmounts بحيث تقوم بإضافة المبلغ المرسل كما يحدث أيضاً المتغير الذي يسمى totalStaked تحدث الدالة بعد ذلك تحديثًا لمتغير
  */
  function stake() public payable {
    require(msg.value > 0, "The value is zero.");

    if (stakedAmounts[msg.sender] == 0) {
      lastStakeTime[msg.sender] = block.timestamp;
    }

    totalStaked += msg.value;
    stakedAmounts[msg.sender] += msg.value;
    emit Staked(msg.sender, msg.value);
  }
    
  /**
    تعمل الدالة على إعادة جميع العملات التي أرسلها المستخدم إليه

    بفرض أن لدى المستخدم الرموز المرهونة الكافية ستقوم الدالة بتحديث إجمالي المبلغ المرهون ومعقدار الرموز المرهونة للمستخدم

    ثم تقوم بالنهايه إرسال العملات إلى عنوان المستخدم
  */
  function unstake() public {
    require(stakedAmounts[msg.sender] > 0, "Not enough staked amount to unstake.");

    uint256 amount = stakedAmounts[msg.sender];
    totalStaked -= amount;
    stakedAmounts[msg.sender] -= amount;
    emit UnStaked(msg.sender, amount);

    (bool sent, ) = msg.sender.call{value: amount}("");
    require(sent, "Failed");
  }

  /**
    تعمل الدالة على إجراء عملية حسابيه بالمكافئة التي يجب ان يحصل عليه المستخدم
  */
  function getReward() public view returns (uint256) {
    uint256 timeElapsed = block.timestamp - lastStakeTime[msg.sender];
    uint256 stakedAmountUser = stakedAmounts[msg.sender];
    
    if (stakedAmountUser == 0) {
      return 0;
    }
    uint256 reward = (stakedAmountUser * timeElapsed) / (10**12);
    return reward;
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

<img src="https://www.web3arabs.com/courses/dapps/staking/staking-contract.png"/>

> كل ما تحتاجه الان لاكمال عقدك الذكي هو كتابة الوظائف التي تريد تشغليها في داخل العقد الذكي.

يمكنك الان رفع عقدك الذكي بكل سهولة. سنقوم باستخدام شبكة الاختبارات وهي sepolia.
الان ستحتاج الى مزود عقدة يتيح لك الاتصال بالعديد من سلاسل الكتل المختلفة. يمكنك استخدام QuickNode كمزود للعقد الخاصة بك بكل سهولة.

قم بإنشاء حساب على QuickNode من خلال النقر على Create account وإذا كان لديك حساب بالفعل قم بتسجيل الدخول مباشرة من خلال النقر على Sign in.
<img src="https://www.web3arabs.com/courses/quicknode-home.png"/>

بمجرد تسجيل الدخول سيتقم نقلك إلى لوحة التحكم هذه:
<img src="/courses/quicknode-dashboard.png">


سنقوم بالذهاب الى قسم Endpoints من خلال القسم الايسر ومن ثم النقر على الزر Create Endpoint من اجل إنشاء مزود عُقدة
<img src="/courses/quicknode-endpoints.png">
## سنقوم بإنشاء Endpoints بهذه الطريقة:

1- سنقوم بإستخدام سلسلة Ethereum فلذلك سنقوم بتحديدها هكذا والنقر على الزر Continue:

<img src="/courses/quicknode-select-chain.png">

```javascript
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

<img src="https://www.web3arabs.com/courses/dapps/staking/deploy-contract.png"/>

الان ستحتاج الى مزود عقدة يتيح لك الاتصال بالعديد من سلاسل الكتل المختلفة. يمكنك استخدام <a href="https://www.quicknode.com/?utm_source=web3-arabs" target="_blank">**QuickNode**</a> كمزود للعقد الخاصة بك بكل سهولة.

قم <a href="https://www.quicknode.com/?utm_source=web3-arabs" target="_blank">**بإنشاء حساب على QuickNode**</a> من خلال النقر على **Create account** وإذا كان لديك حساب بالفعل قم بتسجيل الدخول مباشرة  من خلال النقر على **Sign in**.

<img src="https://www.web3arabs.com/courses/quicknode-home.png"/>

بمجرد تسجيل الدخول سيتقم نقلك إلى لوحة التحكم هذه:

<img src="https://www.web3arabs.com/courses/quicknode-dashboard.png"/>

سنقوم بالذهاب الى قسم **Endpoints** من خلال القسم الايسر ومن ثم النقر على الزر **Create Endpoint** من اجل إنشاء مزود عُقدة

<img src="https://www.web3arabs.com/courses/quicknode-endpoints.png"/>

**سنقوم بإنشاء **Endpoints** بهذه الطريقة:**

1- سنقوم بإستخدام سلسلة **Ethereum** فلذلك سنقوم بتحديدها هكذا والنقر على الزر **Continue**:

<img src="https://www.web3arabs.com/courses/quicknode-select-chain.png"/>

2- بما ان الغرض من الدرس هو التعلم فلذلك سنقوم بتحديد شبكة الإختبار Sepolia والنقر على الزر Continue:
<img src="/courses/quicknode-select-network.png">

3- سنقوم بالنقر على الزر Create Endpoint لإنشاء المزود:

<img src="/courses/quicknode-create.png">
4- أخيراً - ستقوم بنسخ HTTP Provider لأننا سنحتاجه لاحقاً من اجل نشر عقدنا الذكي على شبكة Sepolia
<img src="/courses/quicknode-keys.png">

قم بإضافة HTTP Provider في ملف (.env)

```
QUICKNODE_HTTP_PROVIDER="add-quicknode-http-url-here"
```


ستقوم الان بنسخ Private Key الخاص بمحفظتك المشفرة عن طريق:

1-فتح المحفظة الخاصة بك ومن ثم النقر على الثلاث النقاط التي في الاعلى على اليمين.
2-النقر على زر Account details ومن ثم النقر على Show private key.
3-قم بكتابة كلمة السر الخاصة بك ومن ثم سيتم عرض لك private key المرتبطة بحسابك.
<img src="/courses/private-key.png">
في حال لم تقوم بإضافة المحفظة من قبل، ستقوم بإضافة معلومات محفظتك في جهازك عن طريق المفتاح الخاص (Private Key) الذي قمت بنسخه عن طريق تشغيل هذا الأمر:

ستقوم أولاً بإضافة العنوان الخاص ومن ثم كلمة سر خاصه بك وتذكرها جيداً، لأن سيتم مطالبتك بها في كل مره تريد استخدام محفظتك في نشر عقد ذكي.

``` cast wallet import deployer --interactive ```
<img src="/courses/dapps/todolist/add-deployer.png">


ستقوم بالتأكيد عن طريق تشغيل هذا الأمر:

``` cast wallet list ```

قم بتجميع العقد الذكي الخاص بك الان. تأكد من انك في مسار تطبيقك (contract-tutorial) وقم بتشغيل هذا الامر:

``` forge build ```

## ملاحظة:
يمكنك الحصول على بعض العملات التي تساعدك في اختبار ونشر تطبيقاتك على شبكة Sepolia من صنبور Web3Arabs هنا - قم بتوصيل محفظتك بالصنبور وإنقر على الزر إرسال.
<img src="/courses/faucet-sepolia.png">

الان ستقوم بتفعيل المتغير المتواجد في ملف .env في terminal من أجل إستخدامها في الخطوة القادمة:

``` source .env ```

حان وقت نشر عقدك الذكي :) قم بكتابة هذا الامر

``` forge create ./src/Staking.sol:Staking --rpc-url $QUICKNODE_HTTP_PROVIDER --account deployer ```

قم بنسخ عنوان عقدك الذكي من أجل إجتياز الاختبار في الاسفل وحفظه حتى نتمكن من استخدامه اثناء جعله يعمل في الواجهة الامامية.

## ربط المشروع بالواجهة الأمامية بإستخدام Next.js ومكتبة Web3.js
في هذا الدرس سنقوم بإستخدام إطار العمل Next.js و TailwindCSS من اجل بناء واجهة الموقع.

1- قم بتشغيل هذا الامر في المجلد my-app من اجل إنشاء مشروع nextjs

``` npx create-next-app . ```
<img src="/courses/dapps/staking/create-nextjs.png">


2- تثبيت مكتبة Web3.js

``` npm install web3@1.10.0 ```

ستذهب الى مجلد app وستقوم بفتح الملف globals.css وستبقي هذه الاوامر في الملف

```
@tailwind base;
@tailwind components;
@tailwind utilities;
```
<img src="/courses/dapps/staking/globals-css.png">

3- عليك الان الوصول الى العقد الذكي من خلال إضافة address العقد الذكي و ABI. ستقوم بإنشاء ملف بإسم info.js في مشروع next.js بعد إنشاء ملف info.js ستقوم بنسخ هذا وإضافة ما يتعلق بعقد الذكي مثل address و ABI

```
// قم بإضافة عنوان عقدك الذكي
export const contractAddress = "0x123456789abcdef0123456xxxxxxxxxxxxxxx"

// foundry في مشروع out المتعلقة بعقدك الذكي من مجلد ABI قم بنسخ
export const contractABI = [
  {
    "type": "fallback",
    "stateMutability": "payable"
  },
  {
    "type": "receive",
    "stateMutability": "payable"
  },
  {
    "type": "function",
    "name": "getReward",
    "inputs": [],
    "outputs": [
      {
        "name": "",
        "type": "uint256",
        "internalType": "uint256"
      }
    ],
    "stateMutability": "view"
  },
  {
    "type": "function",
    "name": "getTotalStaked",
    "inputs": [],
    "outputs": [
      {
        "name": "",
        "type": "uint256",
        "internalType": "uint256"
      }
    ],
    "stateMutability": "view"
  },
  {
    "type": "function",
    "name": "lastStakeTime",
    "inputs": [
      {
        "name": "",
        "type": "address",
        "internalType": "address"
      }
    ],
    "outputs": [
      {
        "name": "",
        "type": "uint256",
        "internalType": "uint256"
      }
    ],
    "stateMutability": "view"
  },
  {
    "type": "function",
    "name": "stake",
    "inputs": [],
    "outputs": [],
    "stateMutability": "payable"
  },
  {
    "type": "function",
    "name": "stakedAmounts",
    "inputs": [
      {
        "name": "",
        "type": "address",
        "internalType": "address"
      }
    ],
    "outputs": [
      {
        "name": "",
        "type": "uint256",
        "internalType": "uint256"
      }
    ],
    "stateMutability": "view"
  },
  {
    "type": "function",
    "name": "unstake",
    "inputs": [],
    "outputs": [],
    "stateMutability": "nonpayable"
  },
  {
    "type": "event",
    "name": "Staked",
    "inputs": [
      {
        "name": "account",
        "type": "address",
        "indexed": true,
        "internalType": "address"
      },
      {
        "name": "amount",
        "type": "uint256",
        "indexed": false,
        "internalType": "uint256"
      }
    ],
    "anonymous": false
  },
  {
    "type": "event",
    "name": "UnStaked",
    "inputs": [
      {
        "name": "account",
        "type": "address",
        "indexed": true,
        "internalType": "address"
      },
      {
        "name": "amount",
        "type": "uint256",
        "indexed": false,
        "internalType": "uint256"
      }
    ],
    "anonymous": false
  }
]
```
<img src="/courses/dapps/staking/infojs.png">

4- الان اذهب الى الملف page.js في المجلد app وقم بلصق هذا الكود ومتابعة الشرح من التعليقات المتواجدة اعلى كل سطر.

```
"use client"
import { useEffect, useState } from 'react';
import Web3 from 'web3';
import { contractAddress, contractABI } from '../info';

export default function Home() {
  // Ethereum الاتصال بشبكة
  const web3 = new Web3(Web3.givenProvider);
  const [reward, setReward] = useState(0)

  // يعمل هذا على اخذ مثيل للعقد بحيث نتمكن من التفاعل مع البلوكتشين
  const contract = new web3.eth.Contract(contractABI, contractAddress)

  // stake تعمل هذه الدالة بعمل
  async function deposit() {
    // Ethereum استدعاء الحساب المتصل بشبكة
    const accounts = await web3.eth.getAccounts();
    // (يمكنك تحديدها كما تريد) ETH التي تم كتابتها في العقد الذكي وإرسال مبلغ معين من stake التفاعل مع الدالة
    const result = await contract.methods.stake().send({ from: accounts[0], value: web3.utils.toWei("0.005", "ether") });
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

  // تعمل هذه الدالة بإظهار المكافئات
  async function getReward() {
    // Ethereum استدعاء الحساب المتصل بشبكة
    const accounts = await web3.eth.getAccounts();
    // التي تم كتابتها في العقد الذكي getReward التفاعل مع الدالة
    const result = await contract.methods.getReward().call({ from: accounts[0] });
    setReward(result)
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
    getReward()
  }, [])

  return (
    <div className='m-2'>
      <h1>Deposit 0.005 ETH</h1>
      <button
        className='bg-black text-white rounded-md p-1 mt-2'
        onClick={deposit}
      >Deposit</button>

      <h1 className='mt-2'>Withdraw all my Deposit</h1>
      <button
        className='bg-black text-white rounded-md p-1 mt-2'
        onClick={withdraw}
      >Withdraw</button>

      <h1 className='mt-4 text-lg'>Your rewards: ({reward}) TOKEN</h1> 
    </div>
  )
}
```
<img src="/courses/dapps/staking/indexjs.png">

يمكنك تجربة تطبيقك الان

``` npm run dev ```

إنه يعمل, لقد انتهيت من بناء تطبيق DApps بنجاح 🥳🥳


يمكنك الوصول الى المشروع بشكل مباشر على GitHub من هنا

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على Telegram او Discord وسنكون أكثر من سعداء لمساعدتك!
