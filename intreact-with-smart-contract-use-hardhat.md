# التفاعل مع العقد الذكي باستخدام Hardhat

في الدرس السابق تمكنا من بناء عقد ذكي ونشره على شبكة sepolia. ولكن اثناء بناء عقود ذكية فنحن بالحاجة بشكل مستمر بتجربة العقد الذكي الذي انتهينا من كتابته فلذلك في هذا الدرس سوف تعرف كيف تقوم بإختبار عقدك الذكي بشكل مستمر على شبكة التطوير بإستخدام Hardhat.

## المتطلبات الاساسية للبدء في هذا الدرس:

1. "انتهيت من قراءة درس "إنشاء عقد ذكي لمشروع Todo-list.
2. يمكنك التعامل مع لغة البرمجة JavaScript.

> سنقوم بإستخدام نفس المشروع السابق وفي نفس تطبيق hardhat الذي قمنا ببناء العقد الذكي من خلاله.

قم بفتح مجلد scripts وانشئ ملف باسم todo-list.js

```js
const hre = require("hardhat")

// وظيفة تعمل على استدعاء المهامات من العقد الذكي
function get_tasks(contract, user) {
  return contract.connect(user).getTasks()
}

async function main() {
  // احصل على بعض الحسابات التي سنعمل معها
  const [user1] = await hre.ethers.getSigners()

  // نحصل على العقد للنشر
  const TodolistContract = await hre.ethers.getContractFactory("Todolist")
  const todolistContract = await TodolistContract.deploy()

  // نشر العقد
  await todolistContract.deployed()
  console.log("Todolist contract deployed to:", todolistContract.address)

  // إضافة مهمة للعقد
  await todolistContract.connect(user1).addTask("Learn Web3")
  await todolistContract.connect(user1).addTask("Travel")
  await todolistContract.connect(user1).addTask("Write")
  console.log("Added tasks!")

  // الحصول على المهمات من العقد المتعلقة بهذا المستخدم
  console.log("Your tasks: ", await get_tasks(todolistContract, user1))

  // تحديث حالة بعض المهام
  console.log("Updateing your tasks...")
  await todolistContract.connect(user1).updateStatus(0)
  await todolistContract.connect(user1).updateStatus(2)
  console.log("Updated! your tasks: ", await get_tasks(todolistContract, user1))

  // حذف بعض المهام
  console.log("Deleting your tasks...")
  await todolistContract.connect(user1).deleteTask(0)
  await todolistContract.connect(user1).deleteTask(2)
  console.log("Deleted! your tasks: ", await get_tasks(todolistContract, user1))
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  })
```

يقوم الكود السابق بتجربة العقد الخاص بنا بشكل كامل, دعنا نفهم ماتم كتابته...

```js
// احصل على بعض الحسابات التي سنعمل معها
const [user1] = await hre.ethers.getSigners()

// نحصل على العقد للنشر
const TodolistContract = await hre.ethers.getContractFactory("Todolist")
const todolistContract = await TodolistContract.deploy()

// نشر العقد
await todolistContract.deployed()
console.log("Todolist contract deployed to:", todolistContract.address)
```

لقد قمنا أولاً بإستدعاء الدالة "()getSigners" من "ethers" للحصول على مجموعة من المُوقِّعين من مثيل "Hyperledger Ressource Environment" وتخزينها في متغيرات ويمكننا جلب اكثر من توقيع عن طريق عمل "[user1, user2, user3]" ومن ثم قمنا بجلب العقد الذكي "Todolist" وفي الاخير قمنا بنشر هذا العقد الذكي.

```js
// إضافة مهمة للعقد
await todolistContract.connect(user1).addTask("Learn Web3")
await todolistContract.connect(user1).addTask("Travel")
await todolistContract.connect(user1).addTask("Write")
console.log("Added tasks!")
```

لقد قمنا باستدعاء العقد الذكي الخاص بنا الذي قمنا بنشره سابقاً وربطنا العقد الذكي بالموقع "user1" ومن ثم استدعينا احد الوظائف التي قمنا بإنشأها في العقد الذكي "(name_)addTask" والتي تعمل على إضافة المهام وادخلنا اسم المهمة الى هذه الوظيفة.

```js
// وظيفة تعمل على استدعاء المهامات من العقد الذكي
function get_tasks(contract, user) {
  return contract.connect(user).getTasks()
}
```

لقد قمنا بإنشاء وظيفة لكي تقوم بجلب المهام من العقد الذكي لكي نتمكن من تكرارها اكثر من مرة ورؤية التحديثات في كل مرة نجري بها تعديلات على العقد الذكي.

```js
// تحديث حالة بعض المهام
console.log("Updateing your tasks...")
await todolistContract.connect(user1).updateStatus(0)
await todolistContract.connect(user1).updateStatus(2)
console.log("Updated! your tasks: ", await get_tasks(todolistContract, user1))

// حذف بعض المهام
console.log("Deleting your tasks...")
await todolistContract.connect(user1).deleteTask(0)
await todolistContract.connect(user1).deleteTask(2)
console.log("Deleted! your tasks: ", await get_tasks(todolistContract, user1))
```

لقد اجرينا أولاً تحديث للمهمة التي تحمل الموقع 0 والمهمة التي تحمل الموقع 2 والتي سيجعل المهمة من مهمة لم يتم تنفيذها "FALSE" الى مهمة تم تنفيذها "TRUE" ومن ثم نقوم بجلب المهام وطباعتها لكي نتمكن من رؤية ما حدث.

ثم قمنا بحذف المهمة التي تحمل الموقع 0 والمهمة التي تحمل الموقع 2 والذي سيقوم بإزالة هذه المهام ومن ثم قمن بجلب المهام مجدداً وطباعتها.

بعد ان قمنا بإستدعاء جميع الوظائف التي قمنا بإنشأها في العقد الذكي دعنا نرئ ما الذي سيحدث عند تجربة هذا

```bash
npx hardhat run scripts/todo-list.js
```

<img src="https://www.web3arabs.com/courses/interactiong-use-hardhat-results.png"/>

إنه يعمل بشكل جيد! 🥳

يمكنك الان الانتقال الى الدرس التالي وإجراء العديد من الاختبارات على عقدك الذكي باستخدام Hardhat 🚀
