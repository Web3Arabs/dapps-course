# إنشاء عقد ذكي لمشروع Todo-list

انت تقوم ببناء عقد ذكي يقوم بإدارة المهام والتعامل معها على Blockchain.

## المتطلبات الاساسية للبدء في هذا الدرس:

1. يمكنك التعامل مع لغة البرمجة JavaScript.
2. انتهيت من قرأة درس اساسيات لغة Solidity.
3. لقد قمت بإعداد محفظتك على Metamask.
4. لقد قمت بتثبيت Node.js على حهاز الكمبيوتر الخاص بك.

## العقد الذكي

سنستخدم احد الادوات التي ستساعدنا في التعامل مع العقود الذكية وهي Hardhat. يعتبر Hardhat هي بيئة وإطار تطوير شبكة Ethereum مصمم للتعامل بشكل كامل مع لغة Solidity.

سنقوم بإنشاء مشروع بإسم Todolist-Dapp والذي سيحتوي على تطبيق Hardhat وعقدنا الذكي.

```bash
mkdir Todolist-Dapp
cd Todolist-Dapp/
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

يمكنك الان البدء في إنشاء عقدك الذكي. قم بإنشاء ملف في مجلد contracts بإسم Todolist.sol

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.9;

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
        Task[] memory listTask = new Task[](Tasks[msg.sender].length);

        uint i = 0;
        for (uint taskId = 0; taskId < Tasks[msg.sender].length; taskId++) {
            listTask[i] = Tasks[msg.sender][taskId];
            i++;
        }

        return listTask;
    }
}

```

> كل ما تحتاجه الان لاكمال عقدك الذكي هو كتابة الوظائف التي تريد تشغليها في داخل العقد الذكي.

#### يمتلك عقدك الذكي الان أربعة وظائف وهم:

1. addTask: والتي تقوم بإضافة مهمة جديدة.
2. getTasks: والتي تقوم بجلب المهام المرتبطة بالمسخدم.
3. updateStatus: والتي تقوم بتحديث حالة المهمة إن كان قد تم إنجازها او لا.
4. deleteTask: والتي تقوم بإزالة المهمة التي ترغب في إزالتها.

يمكنك الان رفع عقدك الذكي بكل سهولة. سنقوم باستخدام شبكة الاختبارات وهي sepolia.
اذهب الى المجلد scripts وقم بإنشاء ملف بإسم deploy.js

```js
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

الان ستحتاج الى مزود عقدة يتيح لك الاتصال بالعديد من سلاسل الكتل المختلفة. يمكنك استخدام <a href="https://alchemy.com/" target="_blank">Alchemy</a> كمزود للعقد الخاصة بك بكل سهولة.

قم بإنشاء حساب في منصة <a href="https://alchemy.com/" target="_blank">Alchemy</a> وإذا كان لديك حساب بالفعل قم بتسجيل الدخول وقم بإنشاء تطبيق (CREATE APP) وقم بكتابة اسم لتطبيقك وتحديد شبكة (Sepolia) وقم بالنقر على (CREATE APP) ومن ثم قم بنسخ رابط المفتاح (HTTPS).

**ملاحظة**: يمكن الحصول على بعض العملات التي تساعدك في اختبار ونشر تطبيقاتك على شبكة **Sepolia** من <a href="https://sepoliafaucet.com/" target="_blank">**Alchemy Faucet**</a>

<img src="https://www.web3arabs.com/courses/alchemy-build.png" alt="Alchemy build"/>

يمكنك الحصول على (Private Key) انقر فوق النقاط الثلاث ، وانقر فوق (Account Details) ثم (Export Private Key).

<img src="https://www.web3arabs.com/courses/alchemy-keys.png" alt="Alchemy keys"/>

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
  solidity: "0.8.9",
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

<br/>

> قم بحفظ عنوان عقدك الذكي حتى نتمكن من استخدامه اثناء جعله يعمل في الواجهة الامامية.

يمكنك الانتقال الان الى الدرس التالي والتفاعل مع عقدك الذكي باستخدام Hardhat 🚀

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://t.me/Web3ArabsDAO" target="_blank">Telegram</a> او <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
 