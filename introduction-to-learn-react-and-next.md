# مقدمة لتعلم React و Next.js

نقوم دائماً بإستخدم أطر عمل الويب لتبسيط عملية تطوير الويب. هل هو أبسط مع ذلك؟ يعتمد على منظور الشخص الذي تبحث منه. إذا كنت قد بدأت للتو ولم تفعل ذلك من قبل، فقد يستغرق الأمر بعض الوقت حتى تفهم كل المفاهيم الضرورية. لكن على المدى الطويل، سوف تشكر نفسك وستكون سعيدًا لأنك أخذت الوقت الكافي لتعلمها.

أشهر أطر عمل الويب المستخدمة اليوم هي:

- <a href="https://react.dev/" target="_blank">React</a>
- <a href="https://angular.io/" target="_blank">Angular</a>
- <a href="https://vuejs.org/" target="_blank">Vue</a>

على الرغم من أن لكل منها مزايا وعيوب خاصة به، إلا أن React قد أحدثت ثورة في عالم تطوير الويب. وينطبق الشيء نفسه على مساحة Web3، حيث يعتبر React هو إطار عمل الويب الأكثر استخدامًا لبناء dApps.

## ماهو React؟

React هو إطار عمل ويب يجعل من السهل إنشاء وتوضيح "طريقة عرض" تطبيق الويب الخاص بك. "العرض" هو ما يتم عرضه على الشاشة، وكيف يتغير، وكيف يتم تحديثه، وما إلى ذلك. يمنحك React أساسًا لغة القالب، ويمكنك إنشاء وظائف Javascript التي تقوم بإرجاع HTML.

تعمل وظائف جافا سكريبت العادية على إرجاع أشياء تشبه جافا سكريبت - سلاسل وأرقام ووحدات منطقية وكائنات وما إلى ذلك. تفاعل بشكل أساسي مع جافا سكريبت و HTML لإنتاج لغة يسمونها JSX. في JSX، ترجع الوظائف الشبيهة بجافا سكريبت HTML بدلاً من عناصر جافا سكريبت العادية. هذا هو الأساس.

تسمى مجموعة وظائف Javascript التي تعرض HTML المكونات. المكونات مكتوبة في JSX. على الرغم من أنها تبدو محرجة في البداية، إلا أنه من السهل جدًا التعامل معها بمجرد أن تعتاد عليها.

فيما يلي مثال لمكون بسيط:

```jsx
import "./styles.css";

export default function DirayList() {
  return (
    <div>
      <h1>Diray List</h1>
      <ul>
        <li>Gym</li>
        <li>Work</li>
        <li>Eat</li>
        <li>Sleep</li>
      </ul>
    </div>
  );
}
```

<a href="https://codesandbox.io/s/elastic-bouman-kvfq4o?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
    <button className="py-2 md:px-8 md:mt-0 mt-[3%] md:ml-4 md:w-auto w-full ml-0 text-white font-medium bg-rose-800 hover:bg-rose-900 active:bg-rose-800 rounded-lg duration-150">تجربة الكود على Codesandbox</button>
</a>

لكن هذا لا يختلف كثيرًا عن HTML. ولكن ماذا لو أردت تقديم قائمة بالعناصر بناءً على مصفوفة؟

```jsx
import "./styles.css";

export default function DirayList() {
  const items = ["Gym", "Work", "Eat", "Sleep"];

  return (
    <div>
      <h1>Diray List</h1>
      <ul>
        {items.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
}
```

<a href="https://codesandbox.io/s/vigilant-monad-0ur37d?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
    <button className="py-2 md:px-8 md:mt-0 mt-[3%] md:ml-4 md:w-auto w-full ml-0 text-white font-medium bg-rose-800 hover:bg-rose-900 active:bg-rose-800 rounded-lg duration-150">تجربة الكود على Codesandbox</button>
</a>

لقد استخدمنا للتو Javascript داخل HTML. في JSX، يمكنك كتابة Javascript داخل HTML عن طريق تغليف كود JS بأقواس معقوفة **{ }**. في الواقع، إذا فكرت في هذا الأمر أكثر من ذلك بقليل، فسوف تدرك ما يحدث. **.map()** هي وظيفة Javascript، والتي تدور حول مصفوفة وتعيد شيئًا لكل عنصر. في هذه الحالة، يتم إجراء التكرار الحلقي فوق **items** المصفوفة وإرجاع **li** عنصر له قيمة متغير **item**، وهو HTML بداخله JavaScript.

داخل مكوننا، قمنا بتضمين مكون آخر بشكل أساسي. وظيفة الخريطة هي دالة JS تقوم بإرجاع HTML. إنه مكون (component). على الرغم من أنه لم يتم تعريفها صراحةً على أنها وظيفة من المستوى الأعلى، إلا أنها لا تزال مكونًا.

**إن تضمين المكونات (components) داخل المكونات الأخرى هو قوة React**. نحن نؤلف معًا عدة وظائف جافا سكريبت التي تُرجع HTML، وننشئ مستند HTML واحدًا مدمجًا منه ليتم عرضه على تطبيق الويب.

## مرور البيانات بين المكونات (Components)

لمكونات ليست مفيدة جدًا إذا كانت ثابتة فقط. من المؤكد أن الالتفاف حول المصفوفات والأشياء أمر جيد، لكن معظم تطبيقات الويب اليوم ليست مستندات ثابتة. ستجلب معظم تطبيقات الويب اليوم البيانات ديناميكيًا من نوع ما من الخادم أو قاعدة البيانات أو blockchain. هذا يعني أنه غالبًا ما تكون هناك حاجة إلى نفس المكون لعرض بيانات مختلفة.

حالة الاستخدام الرئيسية لامتلاك المكونات هي القدرة على كتابة كود قابل لإعادة الاستخدام ويمكن أن يحتوي بداخله على معلومات مختلفة دون إعادة كتابة الكود بالكامل مرة أخرى.

دعنا نلقي نظرة على مثال. أي من هذين الرمزين أكثر قابلية للقراءة؟

```jsx
<div class="cards">
  <div class="card">
    <img src="img_avatar.png" alt="Avatar" />
    <div class="container">
      <h4><b>Ali</b></h4>
      <p>Web Developer</p>
    </div>
  </div>

  <div class="card">
    <img src="img_avatar.png" alt="Avatar" />
    <div class="container">
      <h4><b>Alaa</b></h4>
      <p>Web3 Developer</p>
    </div>
  </div>

  <div class="card">
    <img src="img_avatar.png" alt="Avatar" />
    <div class="container">
      <h4><b>Ayesha</b></h4>
      <p>WordPress Developer</p>
    </div>
  </div>
</div>
```


```jsx
function Cards() {
  return (
      <div className="cards">
          {/* Data is passed to children through HTML attributes */}
          <Card name="Ali" job="Web Developer" />
          <Card name="Alaa" job="Web3 Developer" />
          <Card name="Ayesha" job="WordPress Developer" />
      </div>
  )
}

// Card receives an object as an argument
// We can destructure the object to get specific variables
// from inside the object - name and job
function Card({name, job}) {
  return (
      <div className="card">
          <img src="img_avatar.png" />
          <div className="container">
              <h4><b>{name}</b></h4>
              <p>{job}</p>
          </div>
      </div>
  )
}
```

<a href="https://codesandbox.io/s/elegant-perlman-6mmgz0?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
  <button className="py-2 md:px-8 md:mt-0 mt-[3%] md:ml-4 md:w-auto w-full ml-0 text-white font-medium bg-rose-800 hover:bg-rose-900 active:bg-rose-800 rounded-lg duration-150">تجربة الكود على Codesandbox</button>
</a>

أعاد مثال HTML البسيط استخدام نفس الكود ثلاث مرات، على الرغم من أن الشيء الوحيد الذي تغير بالفعل كان مجرد اسم الشخص والمسمى الوظيفي.

في JSX، يمكننا تجريد كل **Card** عنصر كمكون، والذي يأخذ بيانات معينة من المكون الأصلي (**Cards**). يمرر المكون الأصل البيانات إلى child من خلال سمات تشبه HTML (**name="Ali"**) ، ويصل child إلى قيمه مثل وظيفة JS التي تتلقى كائن JS كوسيطة. يمكن للمكون **Card** بعد ذلك إرجاع HTML مع بيانات متغيرة بداخله اعتمادًا على ما تلقاه من الأصل.

هذا الرمز قابل لإعادة الاستخدام وقابل للتمديد بسهولة أكبر. هل تريد تغيير شكل جميع البطاقات قليلاً؟ فقط قم بتعديل مكون واحد! ليس كل HTML الذي تم نسخه ولصقه.

إن تضمين المكونات داخل المكونات الأخرى هو قوة React. هذا يسمى التكوين. نحن نؤلف معًا عدة وظائف جافا سكريبت التي تُرجع HTML، وننشئ مستند HTML واحدًا مدمجًا منه ليتم عرضه على تطبيق الويب.

## المكونات التفاعلية (Interactive Components)
حسنًا، يمكننا الآن تمرير البيانات بين المكونات. كل هذا جيد، لكننا لم نضف التفاعل بعد. أشياء مثل القدرة على تشغيل بعض التعليمات البرمجية عند النقر فوق الزر، أو عند كتابة نص في مربع الإدخال، وما إلى ذلك.

في Javascript، يمكن أن تحتوي الوظائف على وظائف داخلها. على سبيل المثال

```jsx
function someFunc() {
  function otherFunc() {
    console.log("Hello!");
  }

  otherFunc();
}

someFunc(); // "Hello!" سيقوم بطباعة

otherFunc(); // undefined سيظهر لك خطأ غير محدد
```

**otherFunc** متاح فقط للاستخدام داخل **someFunc** نفسه. نادرًا ما تُستخدم هذه الميزة في جافا سكريبت العادية، لكنها تُستخدم بكثرة عند العمل مع React. دعونا نرى لماذا بمثال.

```jsx
function Button() {
  function handleClick() {
    console.log("Hello");
  }

  return (
    <button className="button" onClick={handleClick}>
      Click Here!
    </button>
  );
}
```

<a href="https://codesandbox.io/s/goofy-cherry-n22w4n?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
  <button className="py-2 md:px-8 md:mt-0 mt-[3%] md:ml-4 md:w-auto w-full ml-0 text-white font-medium bg-rose-800 hover:bg-rose-900 active:bg-rose-800 rounded-lg duration-150">تجربة الكود على Codesandbox</button>
</a>

لدينا وظيفة JSX تسمى **Button**. ضمن هذه الوظيفة، لدينا وظيفة أخرى تسمى **handleClick**. في علامة `<button>`، نحدد `onClick={handleClick}` ذلك عند النقر فوق الزر، **handleClick** يتم استدعاء الوظيفة. هذه الوظيفة متاحة فقط داخل المكون **Button**. سيؤدي النقر فوق الزر الموجود في تطبيق الويب إلى الطباعة **Hello** في وحدة تحكم المتصفح. هذه هي الطريقة التي نبني بها مواقع ويب تفاعلية باستخدام React!

هذا المثال لا يزال بسيطًا إلى حد ما، لأنه **handleClick** لا يأخذ أي حجج. ماذا لو أردنا طباعة نص في وحدة التحكم أثناء قيام المستخدم بالكتابة في مربع إدخال؟ كيف نمرر النص إلى الوظيفة؟

إليك الطريقة.

```jsx
function PrintText() {
  function handleOnChange(text) {
    console.log(text);
  }

  return <input type="text" onChange={(e) => handleOnChange(e.target.value)} />;
}
```

<a href="https://codesandbox.io/s/stupefied-rgb-45dsiw?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
  <button className="py-2 md:px-8 md:mt-0 mt-[3%] md:ml-4 md:w-auto w-full ml-0 text-white font-medium bg-rose-800 hover:bg-rose-900 active:bg-rose-800 rounded-lg duration-150">تجربة الكود على Codesandbox</button>
</a>

يوفر عنصر input مستمعًا مفيدًا للأحداث - **onChange** - يتم تشغيله في كل مرة يتغير فيها النص الموجود في مربع الإدخال هذا (كتابة حرف جديد، وحذف حرف، وما إلى ذلك).

إلى جانب تشغيل وظيفة، فإنها تمر أيضًا على طول عنصر HTML الذي تغير (يشار إليه **e** هنا). يمكننا بعد ذلك أخذ عنصر **e** واستخراج النص باستخدام **e.target.value** وتمريره كوسيطة **handleOnChange** يتم من خلالها تسجيل النص في وحدة تحكم المتصفح.

عناصر HTML المختلفة لها معالجات أحداث مختلفة - وهذان **onClick** و **onChange** مثالان، ولكن هناك الكثير! يمكنك العثور على قائمة بجميع أحداث HTML هنا.

من خلال دمج أحداث HTML مع معالجات الوظائف، يمكننا القيام بكل أنواع الأشياء الرائعة! تحميل البيانات من الخادم، وإرسال البيانات إلى الخادم، وتحديث وجهة نظرنا، وما إلى ذلك.

## خطافات React (React Hooks) توضيح useState و useEffect

حسنًا، لقد تجاوزنا التكوين وتمرير البيانات والتفاعل. لكن تطبيقاتنا لا تزال غبية تمامًا. سيسمح لك التفاعل بتشغيل بعض التعليمات البرمجية عند نقرات الأزرار وما إلى ذلك، ولكن ماذا لو كنت تريد تحديث بعض المتغيرات؟

لسوء الحظ، ما يلي لا يعمل

```jsx
function DoesNotWork() {
  let myNumber = 0;

  function increment() {
    myNumber++;
  }

  return (
    <div>
      <p>{myNumber}</p>
      <button onClick={increment}>Increment!</button>
    </div>
  );
}
```

<a href="https://codesandbox.io/s/modest-shamir-e25qtm?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
  <button className="py-2 md:px-8 md:mt-0 mt-[3%] md:ml-4 md:w-auto w-full ml-0 text-white font-medium bg-rose-800 hover:bg-rose-900 active:bg-rose-800 rounded-lg duration-150">تجربة الكود على Codesandbox</button>
</a>

بغض النظر عن عدد مرات النقر فوق **Increment** الزر، سيتم تعليق الرقم المعروض على الشاشة **0**. هذا لأنه عندما تقوم بتحديث متغيرات عادية مثل **myNumber** من داخل مكون React، على الرغم من تحديث القيمة، فإن React لا تقوم في الواقع بإعادة عرض تطبيق الويب. لا يقوم تلقائيًا بتحديث عرض HTML للصفحة.

خطافات React هي وظائف "ربط" بأجزاء مختلفة من مكونات React مما يتيح لك القيام بأشياء مثل تحديث العرض عند تغيير قيمة المتغير أو تشغيل بعض رموز JS تلقائيًا في كل مرة يتم فيها تحميل الصفحة أو تغيير متغير، والعديد من المزيد من الأشياء الرائعة! سنركز بشكل أساسي على ثلاثة خطاطيف React تستخدم 95٪ من الوقت وهي **useState** و **useEffect** و **useRef**.

### useState
هناك الكثير من الأوقات التي تريد فيها تحديث عرض HTML بناءً على تغيير قيمة بعض المتغيرات. يمكننا استخدام **useState** الخطاف للحفاظ على متغير يقوم تلقائيًا بإعادة عرض HTML المعروض على الشاشة في كل مرة يتم تغيير قيمتها. هذا مثال:


```jsx
function ThisWorks() {
  // myNum is the variable itself
  // setMyNum is a function that lets us update the value
  // useState(0) initializes the React Hook
  // with the starting value of 0
  const [myNum, setMyNum] = useState(0);

  function increment() {
    // Sets the new value to the old value + 1
    setMyNum(myNum + 1);
  }

  return (
    <div>
      <p>{myNum}</p>
      <button onClick={increment}>Increment!</button>
    </div>
  );
}
```

<a href="https://codesandbox.io/s/friendly-ully-pugvy8?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
  <button className="py-2 md:px-8 md:mt-0 mt-[3%] md:ml-4 md:w-auto w-full ml-0 text-white font-medium bg-rose-800 hover:bg-rose-900 active:bg-rose-800 rounded-lg duration-150">تجربة الكود على Codesandbox</button>
</a>

إذا حاولت تشغيل الكود أعلاه، فسترى عرض تطبيق الويب يتم تحديثه تلقائيًا ليعكس القيمة الجديدة للمتغير.

المتغيرات التي تم إنشاؤها باستخدام **useState** تسمى متغيرات الحالة. يمكن تحديث متغيرات الحالة وتحديث عرض التطبيق تلقائيًا. فيما يلي مثال آخر لاستخدام متغيرات الحالة مع مربعات الإدخال.

```jsx
function StateWithInput() {
  // myName is the variable
  // setMyName is the updater function
  // Create a state variable with initial value
  // being an empty string ""
  const [myName, setMyName] = useState("");

  function handleOnChange(text) {
    setMyName(text);
  }

  return (
    <div>
      <input type="text" onChange={(e) => handleOnChange(e.target.value)} />
      <p>Hello, {myName}!</p>
    </div>
  );
}
```

<a href="https://codesandbox.io/s/condescending-taussig-z46kpt?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
  <button className="py-2 md:px-8 md:mt-0 mt-[3%] md:ml-4 md:w-auto w-full ml-0 text-white font-medium bg-rose-800 hover:bg-rose-900 active:bg-rose-800 rounded-lg duration-150">تجربة الكود على Codesandbox</button>
</a>

رى النص المعروض على HTML يتغير مع تغير محتوى مربع الإدخال.

آخر شيء أريد أن أقوله عن useState هو أنه يمكنك أيضًا استخدامه لأكثر من مجرد أنواع أساسية مثل السلاسل والأرقام. يمكنك أيضًا استخدامها لتخزين المصفوفات والعناصر.

### useEffect

في كثير من الأحيان ، هناك حاجة إلى تشغيل بعض التعليمات البرمجية تلقائيًا عند تحميل الصفحة لأول مرة - ربما لجلب البيانات من الخادم أو blockchain - وكذلك الحاجة إلى تشغيل بعض التعليمات البرمجية تلقائيًا عندما يتغير متغير حالة معينة.

يسمى هذا النوع من الوظائف بالآثار الجانبية. يعطينا خطاف  useEffect الذي يسمح لنا بكتابة هذه الأنواع من التأثيرات. useEffect يأخذ وسيطتين - دالة ومجموعة تبعية. الوظيفة هي الكود الذي يتم تشغيله عند تشغيل التأثير، وتحدد مصفوفة التبعية متى يتم تشغيل التأثير الجانبي.

ضع في اعتبارك مثالاً حيث عندما يتم تحميل موقع الويب لأول مرة، فإنه يريد تحميل بعض البيانات من الخادم. أثناء القيام بذلك، يريد عرض شاشة تحميل للمستخدم، وبعد ذلك بمجرد تحميل البيانات، قم بإزالة شاشة التحميل وإظهار المحتوى الفعلي. كيف نفعل ذلك؟

```jsx
function LoadDataFromServer() {
  // Create a state variable to hold the data returned from the server
  const [data, setData] = useState("");
  // Create a state variable to maintain loading state
  const [loading, setLoading] = useState(false);

  async function loadData() {
    // Set `loading` to `true` until API call returns a response
    setLoading(true);

    // Imaginary function that performs an API call to load
    // data from a server
    const data = await apiCall();
    setData(data);

    // We have the data, set `loading` to `false`
    setLoading(false);
  }

  // loadData is the function that is run
  // An empty dependency array means this code is run
  // once when the page loads
  useEffect(() => {
    loadData();
  }, []);

  // Display `"Loading..."` while `loading` is `true`,
  // otherwise display `data`
  return <div>{loading ? "Loading..." : data}</div>;
}
```

<a href="https://codesandbox.io/s/frosty-pond-ij6hrb?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
  <button className="py-2 md:px-8 md:mt-0 mt-[3%] md:ml-4 md:w-auto w-full ml-0 text-white font-medium bg-rose-800 hover:bg-rose-900 active:bg-rose-800 rounded-lg duration-150">تجربة الكود على Codesandbox</button>
</a>

إذا قمت بتشغيل الكود أعلاه من الرابط ، فسترى أنه يتم عرضه **Loading...** لمدة 5 ثوانٍ على الشاشة، ثم يتم عرضه **ABCDEF**. لأنها **apiCall** دالة تنتظر 5 ثوان ثم تعيد السلسلة **ABCDEF**.

المكالمات عند تحميل الصفحة لأول مرة - بفضل مصفوفة التبعية الفارغة - ومتغيرات الحالة تجعل HTML يعرض المحتوى المناسب.

هذا مفيد لتشغيل التعليمات البرمجية عند تحميل الصفحة لأول مرة، ولكن ماذا عن تشغيل جزء من التعليمات البرمجية في كل مرة تتغير فيها قيمة متغير الحالة؟ على سبيل المثال، عندما تبحث عن اسم شخص على Facebook، كيف يجلب Facebook التوصيات ويعرضها في كل مرة تضيف فيها/تزيل شخصية؟

يمكنك أيضًا القيام بذلك **useEffect** من خلال توفير متغير الحالة في مصفوفة التبعية. في كل مرة تتغير قيمة هذا المتغير، سيتم تشغيل التأثير.

```jsx
function DependentEffect() {
  const names = ["Alice", "Bob", "Charlie", "David", "Emily"];

  const [recommendations, setRecommendations] = useState([]);
  const [searchText, setSearchText] = useState("");

  useEffect(() => {
    // If user is not searching for anything, don't show any recomendations
    if (searchText.length === 0) {
      setRecommendations([]);
    }
    // Else, find recommendations
    else if (searchText.length > 0) {
      const newRecs = names.filter((name) =>
        name.toLowerCase().includes(searchText.toLowerCase())
      );
      setRecommendations(newRecs);
    }
  }, [searchText]);

  return (
    <div>
      <input type="text" onChange={(e) => setSearchText(e.target.value)} />
      <h2>Recommendations:</h2>
      <ul>
        {recommendations.map((rec, index) => (
          <li key={index}>{rec}</li>
        ))}
      </ul>
    </div>
  );
}
```

<a href="https://codesandbox.io/s/fancy-microservice-3mgd4m?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
  <button className="py-2 md:px-8 md:mt-0 mt-[3%] md:ml-4 md:w-auto w-full ml-0 text-white font-medium bg-rose-800 hover:bg-rose-900 active:bg-rose-800 rounded-lg duration-150">تجربة الكود على Codesandbox</button>
</a>

إذا قمت بتشغيل الكود أعلاه، وحاولت كتابة بعض الأحرف، فسترى قائمة التوصيات يتم تحديثها تلقائيًا عند إضافة/إزالة أحرف جديدة في مربع البحث. searchText هذا لأنه عندما تقوم بتحديث مربع الإدخال ، يتم تحديث قيمة من خلال onChange المعالج، الذي يقوم بتشغيل useEffect، والذي يقوم بتحديث recommendations القائمة، والذي يقوم بتحديث عرض HTML.

يمكنك أيضًا إنشاء تأثيرات جانبية بالمثل تعتمد على متغيرات حالة متعددة، وليس واحدة فقط. إذا تغير أي من المتغيرات التابعة، يتم تشغيل التأثير الجانبي. يمكنك القيام بذلك عن طريق إضافة المزيد من متغيرات الحالة إلى مصفوفة التبعية.

```jsx
useEffect(() => {
  // Some code
}, [stateVar1, stateVar2, stateVar3, andSoOn]);
```

### useRef

useRef هو خطاف React آخر شائع الاستخدام إلى حد ما. إنه مشابه تمامًا لما هو ظاهر useState على السطح، ولكن به بعض الاختلافات الدقيقة التي تعتبر في الواقع مهمة جدًا مما يجعل خطاف React هذا مهمًا للتعلم.

يتم إنشاء متغيرات useRef على النحو التالي:

```jsx
function Component() {
  const myValue = useRef();

  function updateMyValue(newValue) {
    myValue.current = newValue;
  }

  function printMyValue() {
    console.log(myValue.current);
  }
}
```

### ملاحظة 1: No Re-Rendering

بعيداً عن الخطاف useState فإن useRef يسمح لنا أيضًا بتخزين متغير في مكون يمكن تحديثه بمرور الوقت. ولكن على عكس متغيرات الحالة، لا يؤدي تحديث قيمة متغير المرجع إلى إعادة عرض HTML.

لذلك، إذا كان لديك useRef متغير وكنت تعرض قيمته في عرض HTML، فلن يؤدي تحديث المتغير إلى تحديث طريقة عرض HTML.

```jsx
function CounterWithRef() {
  const myNumber = useRef();

  function increment() {
    if (myNumber.current !== undefined) {
      myNumber.current += 1;
    } else {
      myNumber.current = 1;
    }
    console.log(myNumber.current);
  }

  return (
    <div>
      <p>{myNumber}</p>
      <button onClick={increment}>Increment!</button>
    </div>
  );
}
```

إذا قمت بتشغيل الكود أعلاه، فستلاحظ أنه في كل مرة تنقر فيها على الزر، تتم زيادة القيمة وطباعتها في وحدة تحكم المتصفح، لكن عرض HTML لا يتم تحديثه بالفعل. في الواقع، لا تعرض طريقة عرض HTML أي شيء، لأن القيمة الأولية لـ myNumber.current هي undefined ولأن HTML لا يتم تحديثها، فإنها تظل undefined بقدر ما يتعلق الأمر بـ HTML على الرغم من أن القيمة يتم تحديثها بالفعل.

### ملاحظة 2: التحديثات المتزامنة

شيء لم نذكره من قبل useState هو أنه عندما نقوم بتحديث متغير حالة باستخدام الوظائف setXYZ، فإنه في الواقع لا يتم تحديثه على الفور.

يحدث تحديد قيمة جديدة لمتغيرات الحالة بشكل غير متزامن في React، مما يعني أنك إذا حاولت استخدام قيمة متغير الحالة فورًا بعد تعيينها على قيمة جديدة، فقد لا ترى في الواقع تنعكس القيمة الجديدة لأنها تحدث بشكل غير متزامن.

دعنا نلقي نظرة على مثال العداد مرة أخرى عند استخدام useState.

```jsx
function AsyncStateVariables() {
  const [number, setNumber] = useState(0);

  function increment() {
    setNumber(number + 1);
    console.log(number);
  }

  return (
    <div>
      <p>{number}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}
```

<a href="https://codesandbox.io/s/fervent-flower-vdsgld?fontsize=14&hidenavigation=1&theme=dark" target="_blank">
  <button className="py-2 md:px-8 md:mt-0 mt-[3%] md:ml-4 md:w-auto w-full ml-0 text-white font-medium bg-rose-800 hover:bg-rose-900 active:bg-rose-800 rounded-lg duration-150">تجربة الكود على Codesandbox</button>
</a>

عند تشغيل هذا، لاحظ ما يحدث في العرض وما يحدث في وحدة التحكم. عند النقر فوق الزر لأول مرة، يجب تحديث متغير الحالة إلى **1** - وهذا ما يحدث في طريقة العرض ، يتم عرض صفحة الويب **1**. ولكن إذا نظرت إلى وحدة تحكم المتصفح، **0** فستتم طباعة القيمة بدلاً من **1**. يستمر هذا النمط مع استمرار النقر فوق الزر.

هذا لأن **setNumber** المكالمة تعمل بشكل غير متزامن ، وبحلول الوقت الذي نصل فيه إلى السطر **console.log(number)**، لم يتم تحديث القيمة بعد ، لذا فهي تطبع القيمة القديمة **number**. عندما يتم تحديثه في الواقع، تتم إعادة عرض HTML لعرض القيمة الجديدة.

**useRef** من ناحية أخرى، يسمح بالتحديثات المتزامنة. عندما تقوم بتحديث قيمة متغير مرجعي باستخدامه **myVar.current = newValue** يتم تحديثه على الفور، ولا يوجد تأخير. يمكن أن يكون هذا مفيدًا في بعض الأحيان.

### ملاحظة 3: الإشارة إلى عناصر DOM

الشيء الرائع الآخر الذي يقدمه لنا useRef هو أنه يسمح لنا بالإشارة مباشرة إلى عناصر DOM. هذا شيء غير ممكن مع useState.

على سبيل المثال، يمكنك الإشارة إلى input عنصر مباشرةً باستخدام useRef

```jsx
function InputFocus() {
  const inputRef = useRef();

  useEffect(() => {
    inputRef.current.focus();
  }, []);

  return <input ref={inputRef} type="text" />;
}
```

عند تشغيل هذا المثال أعلاه، ستلاحظ أنه بمجرد تحميل الصفحة، input يكون العنصر قيد التركيز بالفعل، أي يمكنك البدء في الكتابة دون النقر فوقه أولاً. هذا لأننا نحتفظ بمرجع للعنصر input، ولدينا مرجع useEffect يتم تشغيله عند تحميل الصفحة بسبب وجود مصفوفة تبعية فارغة، تركز على العنصر input.

آمل أن يكون هذا المقال مفيدًا لك ويمكن أن يكون بمثابة دورة تدريبية مكثفة. لقد ركزت بشكل متعمد على React، حيث أن التعود على جزء الواجهة الأمامية سيكون أكثر ملاءمة لنا من الجزء الخلفي. أيضًا، كود الواجهة الخلفية هو جافا سكريبت عادي بشكل أساسي، في حين أن الواجهة الأمامية هي JSX التي أردت أن تجعلك تشعر براحة أكبر معها.

لقد قمت بإدراج بعض القراءات ومقاطع الفيديو الإضافية التي أوصي بها للحصول على فهم أفضل لهذه المفاهيم. كما هو الحال دائمًا ، إذا كان لديك أي أسئلة ، أرسل رسالة على مجتمعنا في Discord وسنسعد بتقديم المساعدة.

## التعامل مع مشروع React

لقد غطينا المفاهيم الأساسية التي يجب أن تعرفها إذا كنت قد بدأت للتو في استخدام React. لكن حتى الآن، تعاملنا فقط مع أمثلة مكونة معزولة. كيف يبدو مشروع React الفعلي؟

تُنشأ تطبيقات React عادةً باستخدام أداة مثل create-react-app. وهي أداة سطر أوامر تساعدك على إعداد مشاريع React الجديدة وتثبيت جميع التبعيات المطلوبة دون الحاجة إلى إنشاء كل النماذج القياسية يدويًا.

```bash
npx create-react-app my-app
```

عند استخدام CRA، سينتهي بك الأمر ببنية ملف تبدو كالتالي:
<img/>

يجب أن يكون الملف **package.json** قابلاً للتمييز. تعمل CRA من خلال بيئة Node.js، *package.json* حيث يتم إنشاء جميع التبعيات والبيانات الوصفية للمشروع - كما هو الحال مع أي مشروع Node.js آخر.

يحتوي المجلد **/src** على المكونات وتصميم CSS، أي رمز خاص بـ React بشكل أساسي. المكون الرئيسي هنا هو **App.js** المكون المُنشأ تلقائيًا والذي يتم إنشاؤه عند إعداد تطبيق React لأول مرة. **index.js** هي نقطة الدخول الرئيسية لتطبيق React، لكنك عادةً لن تحتاج إلى تعديلها كثيرًا (أو على الإطلاق). إنه يحتوي فقط على بعض كود React المعياري الذي يأخذ مكوناتك ويحولها إلى HTML و JS فعليين يمكن تشغيلهما في متصفح.

يحتوي المجلد **/public** بعد ذلك على ملف واحد فقط بشكل افتراضي - **index.html**. عادة لن تلمس هذا بنفسك. هذا ملف HTML بسيط للغاية. عندما يتم تشغيل تطبيق React، يقوم React ببعض السحر تحت الغطاء الذي يأخذ كل مكوناتك ورمز JSX، ويحوله إلى HTML و JS الفعلي الذي يمكن تشغيله في المتصفح، ويستبدل محتويات كل ذلك **index.html**. ثم المحدث **index.html** هو ما يتم عرضه للمستخدم.

إذا كنت ترغب في إضافة الصور والخطوط والموسيقى وما إلى ذلك إلى موقع الويب الخاص بك، فسوف ينتقلون أيضًا إلى المجلد **/public** يحتوي المجلد **/public** بشكل أساسي على كل ما تريد الوصول إليه مباشرة على موقع الويب الخاص بك.

على سبيل المثال، إذا أضفت صورة تم استدعاؤها **avatar.png** إلى مجلد **/public** فيمكنك حينئذٍ عرض تلك الصورة داخل مكون React على النحو التالي:

```jsx
<img src="/avatar.png" />
```

في حين أن هذا قد يبدو غريبًا لأن المكون الخاص بك موجود داخل المجلد **/src** وليس المجلد **/public** - السبب في أنه يعمل لأن الصورة موجودة في نفس المجلد مثل **index.html** وهو المكان الذي ينتهي فيه رمز React بالفعل. لذلك عند الرجوع إلى صورة باستخدام المسار النسبي **avatar.png/**، فإنها تعلم أنه **avatar.png** يجب أن تكون داخل المجلد **public**.

## شرح Back-End

لقد ناقشنا حتى الآن React، وكل إمكانياتها الأمامية. لكن ماذا عن backend

لا يعد React إطار عمل لل backend، لذا إذا أردت إنشاء واجهة API الخلفية الخاصة بك، فسيتعين عليك إعداد مشروع منفصل باستخدام شيء مثل Node.js و Express. ومع ذلك، يعد هذا مرهقًا، كما لو كان backend و frontend للمشروع نفسه، فمن المحتمل أن يكون لديك الكثير من التعليمات البرمجية التي يمكن إعادة استخدامها ومشاركتها عبر الاثنين. بالإضافة إلى ذلك، فإن صيانة مشروعين هي دائمًا أصعب من صيانة مشروع واحد فقط.

Next.js هو إطار عمل فوقي لـ React. ماذا يعني هذا؟ حسنًا، React نفسها هي إطار عمل لبناء تطبيقات الويب. بينما Next.js في الأعلى، هو إطار عمل لـ React والذي يقدم أيضًا بعض الميزات الإضافية التي لم تكن تمتلكها React.

إذا كنت تعرف React، فإن Next هو 90٪ من نفس الشيء بالضبط ويمكنك البدء في استخدامه بسرعة كبيرة، لكني أريد التحدث عن هذه الميزات الإضافية التي يوفرها Next.

بادئ ذي بدء، نظرًا لأن العنوان والمقدمة قد يكونان قد ألمحا إليك، فإن Next يسمح لك بكتابة كود الواجهة الأمامية والخلفية في مشروع واحد. أنت تبني الواجهة الأمامية الخاصة بك باستخدام React ، وتكتب نقاط نهاية API الخاصة بك في صيغة مشابهة مثل استخدام Express - ولكن جميعها في نفس المشروع.

ثانيًا، يجعل Next إنشاء تطبيقات ويب متعددة الصفحات أمرًا سهلاً للغاية. تم تصميم React في الأصل للمساعدة في إنشاء تطبيقات ذات صفحة واحدة (SPAs)، والمكونات رائعة لذلك! ولكن ماذا لو كان موقع الويب الخاص بك يحتوي على صفحات متعددة؟ على سبيل المثال https://web3arabs.com/ و https://web3arabs.com/about و https://web3arabs.com/courses وما إلى ذلك.

لهذا تم تقديم مكتبات مثل **React Router**، مما جعلها ممكنة ولكنها كانت أيضًا مرهقة بعض الشيء. قام **Next** بتبسيط هذا كثيرًا من خلال السماح بالتوجيه التلقائي للصفحة استنادًا إلى اسم الملف.

أخيرًا، يتميز Next أيضًا بالعرض الجانبي للخادم (SSR) وإنشاء الموقع الثابت (SSG). هذه ليست ميزات سنستخدمها في مساراتنا، لذلك لن أقضي الكثير من الوقت هنا، لكن لا تتردد في مراجعة القراءات الموصى بها لاحقًا إذا كنت ترغب في معرفة المزيد عنها.

## خاتمة

آمل أن يكون هذا المقال مفيدًا لك ويمكن أن يكون بمثابة دورة تدريبية مكثفة. لقد ركزت بشكل متعمد على React أكثر من Next هنا، حيث أن التعود على جزء الواجهة الأمامية سيكون أكثر ملاءمة لنا من الجزء الخلفي. أيضًا، كود الواجهة الخلفية هو جافا سكريبت عادي بشكل أساسي ، في حين أن الواجهة الأمامية هي JSX التي أردت أن تجعلك تشعر براحة أكبر معها.

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!

## مقالات / فيديوهات

- <a href="https://www.youtube.com/watch?v=hQAHSlTtcmY" target="_blank">تعلم React في 30 دقيقة</a>
- <a href="https://www.youtube.com/watch?v=Sklc_fQBmcs" target="_blank">Next.js في 100 ثانية // بالإضافة إلى البرنامج التعليمي الكامل</a>
- <a href="https://scrimba.com/learn/learnreact" target="_blank">دورة React من Scrimba</a>
- <a href="https://www.youtube.com/watch?v=mTz0GXj8NN0" target="_blank">كورس Next.js</a>
