# إنشاء عقد ذكي لمشروع Todo-list

انت تقوم ببناء عقد ذكي يقوم بإدارة المهام والتعامل معها على Blockchain.

## المتطلبات الاساسية للبدء في هذا الدرس:

1. يمكنك التعامل مع لغة البرمجة JavaScript. <br/>
2. انتهيت من قرأة درس اساسيات لغة Solidity. <br/>
3. لقد قمت بإعداد محفظتك على Metamask. <br/>
4. لقد قمت بتثبيت Node.js على حهاز الكمبيوتر الخاص بك.

## إعداد المشروع

خلال هذا الدرس ستقوم بإنشاء العقد الذكي من اجل تخزين بيانات الفيديوهات التي سيقوم المستخدمين بنشرها ومن ثم ربط العقد الذكي بالموقع الذي سنقوم ببنائه.

ستقوم بفتح terminal وستدخل هذه الاوامر:

```bash
mkdir Todolist-Dapp & cd Todolist-Dapp
mkdir contract-tutorial & mkdir my-app
```

<div dir="ltr" className="flex justify-center items-center">
<img src="https://www.web3arabs.com/courses/dapps/todolist/settingup-project.png"/>
</div>

- في المجلد **contract-tutorial** سنقوم بإعداد ونشر العقد الذكي. <br/>
- في المجلد **my-app** سنقوم ببناء الواجهة الامامية للمشروع وربطها بالعقد الذكي في الدروس القادمة.

## العقد الذكي

سنستخدم احد الادوات التي ستساعدنا في التعامل مع العقود الذكية وهي Hardhat. يعتبر Hardhat هي بيئة وإطار تطوير شبكة Ethereum مصمم للتعامل بشكل كامل مع لغة Solidity.

سنقوم بفتح المجلد **contract-tutorial** على terminal ونقوم بإضافة هذه الاوامر

```bash
cd contract-tutorial
npm init --yes
npm install --save-dev hardhat @nomicfoundation/hardhat-toolbox@2 dotenv
```

سنقوم الان بتشغيل تطبيق Hardhat

```bash
npx hardhat
```

<img src="https://www.web3arabs.com/courses/dapps/todolist/npx-hardhat.png"/>

#### سنلاحظ ان التطبيق يحتوي على 3 مجلدات رئيسية وهي:

1. contracts: الذي سنقوم من خلاله بكتابة العقود الذكية. <br/>
2. scripts: والذي سنقوم من خلاله بالتعامل مع العقود الذكية او رفعها على الشبكات. <br/>
3. test: والذي سنقوم من خلاله بإجراء اختبارات لعقدنا الذكي.

يمكنك الان البدء في إنشاء عقدك الذكي. قم بإنشاء ملف في مجلد contracts بإسم Todolist.sol

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.19;

contract Todolist {

    // تحديد هيكلية لتخزين مهمة
    struct Task {
        string name;
        bool status;
        uint timestamp;
    }

    mapping (address => Task[]) private Tasks;
    uint256 public taskIds;

    /**
        addTask - تعمل هذه الوظيفة بارسل مهمة جديدة في Tasks
    */
    function addTask(string calldata _name) public {
        // Add the task to storage.
        Tasks[msg.sender].push(Task({
            name: _name,
            status: false,
            timestamp: block.timestamp
        }));
        taskIds += 1;
    }

    /**
        updateStatus - تعمل هذه الوظيفة في تحديث حالة المهمة
    */
    function updateStatus(uint256 _index) public {
        Tasks[msg.sender][_index].status = !Tasks[msg.sender][_index].status;
    }

    /**
        deleteTask - تعمل هذه الوظيفة بحذف مهمة مطلوبة
    */
    function deleteTask(uint256 _index) public {
        delete Tasks[msg.sender][_index];
    }

    /**
        getTasks - تعمل هذه الوظيفة في جلب المهام المتعلقة بالمسخدم
    */
    function getTasks() public view returns (Task[] memory) {
        return Tasks[msg.sender];
    }
}

```

<img src="https://www.web3arabs.com/courses/dapps/todolist/todolist-contract.png"/>

> كل ما تحتاجه الان لاكمال عقدك الذكي هو كتابة الوظائف التي تريد تشغليها في داخل العقد الذكي.

#### يمتلك عقدك الذكي الان أربعة وظائف وهم:

1. addTask: والتي تقوم بإضافة مهمة جديدة. <br/>
2. getTasks: والتي تقوم بجلب المهام المرتبطة بالمسخدم. <br/>
3. updateStatus: والتي تقوم بتحديث حالة المهمة إن كان قد تم إنجازها او لا. <br/>
4. deleteTask: والتي تقوم بإزالة المهمة التي ترغب في إزالتها. <br/>

يمكنك الان رفع عقدك الذكي بكل سهولة. سنقوم باستخدام شبكة الاختبارات وهي sepolia.
اذهب الى المجلد scripts وقم بإنشاء ملف بإسم deploy.js (في حال هناك ملف بنفس الاسم قم بإزالة الاكواد التي فيه وإجعله بهذا الشكل):

```javascript
const hre = require("hardhat");

async function main() {
    /*
      لنشر عقود ذكية جديدة getContractFactory يستخدم
    */
    const todolistContract = await hre.ethers.getContractFactory("Todolist");

    // هنا نقوم برفع العقد
    const todolist = await todolistContract.deploy();

    // انتظر حتى تنتهي عملية الرفع
    await todolist.deployed();

    // طباعة عنوان العقد المنشور
    console.log("Todolist deployed to:", todolist.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

<img src="https://www.web3arabs.com/courses/dapps/todolist/deploy-contract.png"/>

الان ستحتاج الى مزود عقدة يتيح لك الاتصال بالعديد من سلاسل الكتل المختلفة. يمكنك استخدام <a href="https://alchemy.com/" target="_blank">Alchemy</a> كمزود للعقد الخاصة بك بكل سهولة.

قم بإنشاء حساب في منصة <a href="https://auth.alchemy.com/signup?redirectUrl=https%3A%2F%2Fdashboard.alchemy.com%2Fsignup%2F" target="_blank">Alchemy</a> وإذا كان لديك حساب بالفعل قم <a href="https://auth.alchemy.com/?redirectUrl=https%3A%2F%2Fdashboard.alchemy.com%2Fsignup%2F" target="_blank">بتسجيل الدخول مباشرة</a> ومن ثم الإتجاه نحو لوحة التحكم هذه:

<img src="https://www.web3arabs.com/courses/alchemy-dashboard.png"/>

قم بالنقر على (Create new app) وقم بكتابة اسم لتطبيقك وتحديد شبكة (Sepolia) ومن ثم النقر على (Create app)

<img src="https://www.web3arabs.com/courses/alchemy-build.png" alt="Alchemy build"/>

بعد  إنشاء التطبيق سيظهر لك هذه الصفحة قم بالنقر على **VIEW KEY**

<img src="https://www.web3arabs.com/courses/alchemy-view-key.png"/>

يمكنك نسخ رابط المفتاح (HTTPS) الخاص بك:

<img src="https://www.web3arabs.com/courses/alchemy-keys.png" alt="Alchemy keys"/>

ستقوم الان بنسخ **Private Key** الخاص بمحفظتك المشفرة عن طريق:

1. فتح المحفظة الخاصة بك ومن ثم النقر على الثلاث النقاط التي في الاعلى على اليمين. <br/>
2. النقر على زر **Account details** ومن ثم النقر على **Show private key**. <br/>
3. قم بكتابة كلمة السر الخاصة بك ومن ثم سيتم عرض لك **private key** المرتبطة بحسابك.

<img src="https://www.web3arabs.com/courses/private-key.png"/>

قم بإضافة كل ما قمت بنسخه في ملف (env.)

```js
ALCHEMY_HTTPS_URL="add-alchemy-http-url-here"

PRIVATE_KEY="add-private-key-here"
```

قم بفتح ملف hardhat.config.js وقم باستيراد المفاتيح المتواجدة في ملف env. وقم بإختيار الشبكة التي تريد استخدامها لرفع العقد الذكي الخاص بك ولكننا هنا سنستخدم شبكة sepolia فلذلك سنقوم بتحديدها

```javascript
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config({ path: ".env" });

const ALCHEMY_HTTPS_URL = process.env.ALCHEMY_HTTPS_URL;
const PRIVATE_KEY = process.env.PRIVATE_KEY;

module.exports = {
  solidity: "0.8.19",
  networks: {
    sepolia: {
      url: ALCHEMY_HTTPS_URL,
      accounts: [PRIVATE_KEY],
    },
  },
}
```

<img src="https://www.web3arabs.com/courses/dapps/todolist/hardhat-config.png"/>

قم بتجميع العقد الذكي الخاص بك الان. تأكد من انك في مسار تطبيقك (contract-tutorial) وقم بتشغيل هذا الامر

```bash
npx hardhat compile
```

**ملاحظة**: يمكن الحصول على بعض العملات التي تساعدك في اختبار ونشر تطبيقاتك على شبكة **Sepolia** من <a href="https://sepoliafaucet.com/" target="_blank">**Alchemy Faucet**</a>

حان وقت نشر عقدك الذكي :) قم بكتابة هذا الامر

```bash
npx hardhat run scripts/deploy.js --network sepolia
```

<img src="https://www.web3arabs.com/courses/dapps/todolist/deployed-contract.png"/>

<br/>

قم بحفظ عنوان عقدك الذكي حتى نتمكن من استخدامه اثناء جعله يعمل في الواجهة الامامية.

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://t.me/Web3ArabsDAO" target="_blank">Telegram</a> او <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
