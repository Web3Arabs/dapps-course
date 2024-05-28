# التفاعل مع العقد الذكي وإجراء إختبار عليه باستخدام Foundry

## التفاعل مع العقد الذكي باستخدام Foundry

في الدرس السابق تمكنا من بناء عقد ذكي ونشره على شبكة sepolia. ولكن اثناء بناء عقود ذكية فنحن بالحاجة بشكل مستمر بتجربة العقد الذكي الذي انتهينا من كتابته فلذلك في هذا الدرس سوف تعرف كيف تقوم بإختبار عقدك الذكي بشكل مستمر على شبكة التطوير بإستخدام Foundry.

### المتطلبات الاساسية للبدء في هذا الدرس:

1. انتهيت من قراءة درس "<a href="https://www.web3arabs.com/courses/d64bee08-2e38-4ad5-958e-5ab6c42ebb41/lessons/f426338c-23db-463f-9f6a-74a4d9c02b91" target="_blank">إنشاء عقد ذكي لمشروع Todo-list</a>"
2. يمكنك التعامل مع لغة البرمجة solidity.

> سنقوم بإستخدام نفس المشروع السابق وفي نفس تطبيق foundry الذي قمنا ببناء ونشر العقد الذكي من خلاله.
.

قم بفتح مجلد script وانشئ ملف باسم Todolist.s.sol ومن ثم قم بنسخ الكود التالي:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

import "forge-std/Script.sol";
import "../src/Todolist.sol";

contract TodolistScript is Script {
  Todolist todolist;

  function setUp() public {
    uint deployerPrivateKey = vm.envUint("PRIVATE_KEY");
    vm.startBroadcast(deployerPrivateKey);

    todolist = new Todolist();
    vm.stopBroadcast();
  }

  function run() public {
    console.log("Total tasks:", todolist.taskIds());
    todolist.addTask("Work");
    todolist.addTask("Work");
    todolist.addTask("Play");
    console.log("Total tasks:", todolist.taskIds());
    
    console.log("Task 0 status:", todolist.getTasks()[0].status);
    todolist.updateStatus(0);
    console.log("Task 0 status:", todolist.getTasks()[0].status);

    todolist.deleteTask(1);
    console.log("Total tasks:", todolist.taskIds());
  }
}

```

<img src="/courses/dapps/todolist/interact-contract.png">
يقوم الكود السابق بتجربة العقد الخاص بنا بشكل كامل, دعنا نفهم ماتم كتابته...

```
Todolist todolist;

function setUp() public {
  uint deployerPrivateKey = vm.envUint("PRIVATE_KEY");
  vm.startBroadcast(deployerPrivateKey);

  todolist = new Todolist();
  vm.stopBroadcast();
}
```

قمنا بإنشاء المتغير todolist من العقد Todolist، ومن ثم قمنا بإعداد البيئة وإنشاء مُسجل ونشر العقد الذكي في الدالة setUp().


```
function run() public {
  console.log("Total tasks:", todolist.taskIds());
  todolist.addTask("Work");
  todolist.addTask("Work");
  todolist.addTask("Play");
  console.log("Total tasks:", todolist.taskIds());
  
  console.log("Task 0 status:", todolist.getTasks()[0].status);
  todolist.updateStatus(0);
  console.log("Task 0 status:", todolist.getTasks()[0].status);

  todolist.deleteTask(1);
  console.log("Total tasks:", todolist.taskIds());
}

```

في الدالة run() قمنا بالتفاعل مع العقد بالكامل بحيث قمنا بطباعة عدد المهام وإضافة بعض المهام وتحديث حالتها وحذفها.

بعد ان قمنا بإستدعاء جميع الدوال التي قمنا بإنشأها في العقد الذكي دعنا نرئ ما الذي سيحدث عند تجربة هذا.

سنقوم الان بتشغيل شبكة محلية لتجربة العقد الذكي والتفاعل معه مجاناً.

ستقوم بتشغيل شبكة محلية عن طريق إدخال هذا الأمر في terminal:



```
anvil
```

سنقوم بإختيار احد المفاتيح الخاصه التي سيتم تقديمها ومن ثم إضافتها في ملف .env

<img src="/courses/dapps/todolist/env-pk.png">
```
forge script script/Todolist.s.sol:TodolistScript --fork-url http://localhost:8545 --broadcast
```
<img src="/courses/dapps/todolist/forge-script.png">

إنه يعمل بشكل جيد! 🥳


## إجراء عملية اختبار للعقد الذكي باستخدام Foundry
تعد كتابة الاختبارات الآلية عند إنشاء العقود الذكية أمرًا ضروريًا للتأكد من أن الكود آمن ويعمل بشكل صحيح هذا مهم بشكل خاص عندما يتعامل المستخدمون مع الأموال، حيث يمكن أن تؤدي أي ثغرات أو ثغرات أمنية إلى تعرض أموالهم للخطر. يمكن أن تساعد الاختبارات الآلية في اكتشاف مثل هذه المشكلات قبل نشرها، مما يساعد على حماية أموال المستخدمين.

قم بفتح مجلد test وأنشئ ملف بإسم Todolist.t.js وقم بنسخ الكود التالي:



```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;

import "forge-std/Test.sol";
import "../src/Todolist.sol";

contract TodolistTest is Test {
  Todolist todolist;

  // todolist نشر العقد الذكي في المتغير
  function setUp() public {
    todolist = new Todolist();
  }

  // إختبار إضافة مهام
  function test_addTask(string memory _name) public {
    todolist.addTask(_name);
    // التحقق بأن عدد المهام يساوي واحد
    assertEq(todolist.taskIds(), 1);        
  }

  // إختبار تحديث حالة مهام
  function test_updateStatus(string memory _name, uint96 _index) public {
    todolist.addTask(_name);
    todolist.addTask(_name);
    todolist.addTask(_name);
    // التحقق بأن عدد المهام يساوي ثلاثة
    assertEq(todolist.taskIds(), 3);

    // يساوي واحد _index نفرض بأن
    vm.assume(_index == 1);
    // تحديث حالة المهمة التي في الخانة واحد
    todolist.updateStatus(_index);
    // true التحقق بأن حالة المهمة أصبحت
    assertEq(todolist.getTasks()[1].status, true);
  }

  // إختبار حذف المهام
  function test_deleteTask(string memory _name, uint96 _index) public {
    todolist.addTask(_name);
    todolist.addTask(_name);
    todolist.addTask(_name);
    // التحقق بأن عدد المهام يساوي 3
    assertEq(todolist.taskIds(), 3);

    // يساوي واحد _index نفرض بأن
    vm.assume(_index == 1);
    // تحديث حالة المهمة التي في الخانة واحد
    todolist.deleteTask(_index);
    // التحقق بأن عدد المهام اصبحت 2
    assertEq(todolist.taskIds(), 2);
  }
}
```

<img src="/courses/dapps/todolist/test-contract.png">


قم بفتح المشروع في terminal وقم بكتابة هذا الأمر لإختبار العقد الذكي:
```
forge test
```
<img src="/courses/dapps/todolist/forge-test.png">

إنه يعمل بشكل جيد! 🥳

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على Telegram او Discord وسنكون أكثر من سعداء لمساعدتك!

