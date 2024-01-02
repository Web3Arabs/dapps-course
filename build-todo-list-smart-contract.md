# إنشاء عقد ذكي لمشروع Todo-list

انت تقوم ببناء عقد ذكي يقوم بإدارة المهام والتعامل معها على Blockchain.

## المتطلبات الاساسية للبدء في هذا الدرس:

1. يمكنك التعامل مع لغة البرمجة JavaScript.
2. انتهيت من قرأة درس اساسيات لغة Solidity.
3. لقد قمت بإعداد محفظتك على Metamask.
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

- في المجلد **contract-tutorial** سنقوم بإعداد ونشر العقد الذكي.
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

1. contracts: الذي سنقوم من خلاله بكتابة العقود الذكية.
2. scripts: والذي سنقوم من خلاله بالتعامل مع العقود الذكية او رفعها على الشبكات.
3. test: والذي سنقوم من خلاله بإجراء اختبارات لعقدنا الذكي.

يمكنك الان البدء في إنشاء عقدك الذكي. قم بإنشاء ملف في مجلد **contracts** بإسم **Todolist.sol**

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

1. addTask: والتي تقوم بإضافة مهمة جديدة.
2. getTasks: والتي تقوم بجلب المهام المرتبطة بالمسخدم.
3. updateStatus: والتي تقوم بتحديث حالة المهمة إن كان قد تم إنجازها او لا.
4. deleteTask: والتي تقوم بإزالة المهمة التي ترغب في إزالتها.

يمكنك الان رفع عقدك الذكي بكل سهولة. سنقوم باستخدام شبكة الاختبارات وهي **sepolia**.
اذهب الى المجلد **scripts** وقم بإنشاء ملف بإسم **deploy.js** (في حال هناك ملف بنفس الاسم قم بإزالة الاكواد التي فيه وإجعله بهذا الشكل):

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

2- بما ان الغرض من الدرس هو التعلم فلذلك سنقوم بتحديد شبكة الإختبار **Sepolia** والنقر على الزر **Continue**:

<img src="https://www.web3arabs.com/courses/quicknode-select-network.png"/>

3- سنقوم بالنقر على الزر **Create Endpoint** لإنشاء المزود:

<img src="https://www.web3arabs.com/courses/quicknode-create.png"/>

4- أخيراً - ستقوم بنسخ **HTTP Provider** لأننا سنحتاجه لاحقاً من اجل نشر عقدنا الذكي على شبكة **Sepolia**

<img src="https://www.web3arabs.com/courses/quicknode-keys.png"/>

ستقوم الان بنسخ **Private Key** الخاص بمحفظتك المشفرة عن طريق:

1. فتح المحفظة الخاصة بك ومن ثم النقر على الثلاث النقاط التي في الاعلى على اليمين.
2. النقر على زر **Account details** ومن ثم النقر على **Show private key**.
3. قم بكتابة كلمة السر الخاصة بك ومن ثم سيتم عرض لك **private key** المرتبطة بحسابك.

<img src="https://www.web3arabs.com/courses/private-key.png"/>

قم بإضافة كل ما قمت بنسخه في ملف (env.)

```js
QUICKNODE_HTTP_PROVIDER="add-quicknode-http-url-here"

PRIVATE_KEY="add-private-key-here"
```

قم بفتح ملف **hardhat.config.js** وقم باستيراد المفاتيح المتواجدة في ملف <span dir="ltr">**.env**</span> وقم بإختيار الشبكة التي تريد استخدامها لرفع العقد الذكي الخاص بك ولكننا هنا سنستخدم شبكة **sepolia** فلذلك سنقوم بتحديدها

```javascript
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config({ path: ".env" });

const QUICKNODE_HTTP_PROVIDER = process.env.QUICKNODE_HTTP_PROVIDER;
const PRIVATE_KEY = process.env.PRIVATE_KEY;

module.exports = {
  solidity: "0.8.19",
  networks: {
    sepolia: {
      url: QUICKNODE_HTTP_PROVIDER,
      accounts: [PRIVATE_KEY],
    },
  },
}
```

قم بتجميع العقد الذكي الخاص بك الان. تأكد من انك في مسار تطبيقك (contract-tutorial) وقم بتشغيل هذا الامر

```bash
npx hardhat compile
```

**ملاحظة**: يمكن الحصول على بعض العملات التي تساعدك في اختبار ونشر تطبيقاتك على شبكة **Sepolia** من <a href="https://www.infura.io/faucet/sepolia" target="_blank">**Infura Faucet**</a> - ستقوم بإنشاء حساب على **Infura** ستقوم بوضع عنوان **address** الخاص بك وستحصل على بعض من العملات.

حان وقت نشر عقدك الذكي :) قم بكتابة هذا الامر

```bash
npx hardhat run scripts/deploy.js --network sepolia
```

<img src="https://www.web3arabs.com/courses/dapps/todolist/deployed-contract.png"/>

<br/>

قم بحفظ عنوان عقدك الذكي حتى نتمكن من استخدامه اثناء جعله يعمل في الواجهة الامامية.

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://t.me/Web3ArabsDAO" target="_blank">Telegram</a> او <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
