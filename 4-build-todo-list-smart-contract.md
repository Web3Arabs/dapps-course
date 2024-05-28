# إنشاء عقد ذكي لمشروع Todo-list

انت تقوم ببناء عقد ذكي يقوم بإدارة المهام والتعامل معها على Blockchain.

## المتطلبات الاساسية للبدء في هذا الدرس:

1. انتهيت من قرأة درس اساسيات لغة Solidity.
2. لقد قمت بإعداد محفظتك على Metamask.

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
2-script: والذي سنقوم من خلاله بالتعامل مع العقود الذكية او رفعها على الشبكات.
3-src: الذي سنقوم من خلاله بكتابة العقود الذكية.
4-test: والذي سنقوم من خلاله بإجراء اختبارات لعقدنا الذكي.

يمكنك الان البدء في إنشاء عقدك الذكي. قم بإنشاء ملف بإسم Todolist.sol في مجلد src:


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

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
    function addTask(string memory _name) public {
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
        taskIds -= 1;
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

يمكنك الان رفع عقدك الذكي بكل سهولة. سنقوم باستخدام شبكة الاختبارات وهي sepolia.


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


قم بإضافة كل ما قمت بنسخه في ملف (env.)

```js
QUICKNODE_HTTP_PROVIDER="add-quicknode-http-url-here"

```
<img src="/courses/dapps/todolist/env-rpc.png">

ستقوم الان بنسخ Private Key الخاص بمحفظتك المشفرة عن طريق:

1-فتح المحفظة الخاصة بك ومن ثم النقر على الثلاث النقاط التي في الاعلى على اليمين.
2-النقر على زر Account details ومن ثم النقر على Show private key.
3-قم بكتابة كلمة السر الخاصة بك ومن ثم سيتم عرض لك private key المرتبطة بحسابك.

<img src="/courses/private-key.png">

سنقوم الان بإضافة معلومات محفظتك في جهازك عن طريق المفتاح الخاص (Private Key) الذي قمت بنسخه عن طريق تشغيل هذا الأمر:

ستقوم أولاً بإضافة العنوان الخاص ومن ثم كلمة سر خاصه بك وتذكرها جيداً، لأن سيتم مطالبتك بها في كل مره تريد استخدام محفظتك في نشر عقد ذكي.

```
cast wallet import deployer --interactive
```
<img src="/courses/dapps/todolist/add-deployer.png">

ستقوم بالتأكيد عن طريق تشغيل هذا الأمر:

```
cast wallet list
```
قم بتجميع العقد الذكي الخاص بك الان. تأكد من انك في مسار تطبيقك (contract-tutorial) وقم بتشغيل هذا الامر:
```
forge build
```
ملاحظة: يمكنك الحصول على بعض العملات التي تساعدك في اختبار ونشر تطبيقاتك على شبكة Sepolia من صنبور Web3Arabs هنا - قم بتوصيل محفظتك بالصنبور وإنقر على الزر إرسال.

<img src="/courses/faucet-sepolia.png">
الان ستقوم بتفعيل المتغير المتواجد في ملف .env في terminal من أجل إستخدامها في الخطوة القادمة:
```
source .env
```
حان وقت نشر عقدك الذكي :) قم بكتابة هذا الامر

```
forge create ./src/Todolist.sol:Todolist --rpc-url $QUICKNODE_HTTP_PROVIDER --account deployer
```
قم بنسخ عنوان عقدك الذكي من أجل إجتياز الاختبار في الاسفل وحفظه حتى نتمكن من استخدامه اثناء جعله يعمل في الواجهة الامامية.

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على Telegram او Discord وسنكون أكثر من سعداء لمساعدتك!فقم بالإنضمام على <a href="https://t.me/Web3ArabsDAO" target="_blank">Telegram</a> او <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
