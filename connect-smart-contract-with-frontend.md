# ربط العقد الذكي بالواجهة الامامية

بعد ان انتهينا من كتابة عقدنا الذكي وتجربته بشكل كامل واختباره يمكننا الان رؤيته يعمل على الواجهة الامامية (Front-end).

## المتطلبات الاساسية للبدء في هذا الدرس:
1- "انتهيت من قراءة درس "إنشاء عقد ذكي لمشروع Todo-list. <br/>
2- يمكنك التعامل مع لغة البرمجة JavaScript. <br/>
3- يمكنك االتعامل مع مكتبة React/Nextjs.

سنقوم بإستخدام منصة <a href="https://floatui.com/" target="_blank">FloatUI</a> من اجل الحصول على بعض المكونات التي ستساعدنا في بناء واجهة امامية باستخدام Reactjs و TailwindCSS بكل سهولة.

سنقوم في هذا الدرس بإستخدام إطار العمل Nextjs وحزمة create-web3-dapp والتي هي إطار عمل قائم على NextJs ومتوافق مع سلاسل الكتل الأكثر استخدامًا بما في ذلك Ethereum والتي تساعد مطوري web3 على بناء تطبيق لامركزي جاهز للإنتاج بسرعة البرق باستخدام قوالب تم بنائها سابقاً: إذهب الى مجلد المشروع وقم بكتابة هذا في terminal

```bash
npx create-web3-dapp@latest
```

اسم المشروع my-app وسنقوم بإعداد المشروع كما موضح في الصورة هنا:

<img src="https://www.web3arabs.com/courses/cw3d-settings.png"/>

> في الخطوة السابعة تم إضافة API Key الذي قمنا بإستخدامه في درس <a href="/courses/d64bee08-2e38-4ad5-958e-5ab6c42ebb41/lessons/f426338c-23db-463f-9f6a-74a4d9c02b91" target="_blank">إنشاء عقد ذكي لمشروع Todo-list</a>

يمكنك تثبيت إطار العمل TailwindCSS من خلال فتح مجلد my-app في terminal <a href="https://tailwindcss.com/docs/guides/nextjs" target="_blank">ومتابعة هذا الشرح المتواجد في وثائق Tailwind</a>

سنحتاج الان الى تثبيت ethers.js والتي ستساعدنا في التعامل مع العقد الذكي وإرسال المعاملات. قم بكتابة هذا على terminal

```bash
npm install ethers@5
```

من اجل إضافة شبكة sepolia الى التطبيق سنقوم بالذهاب الى مجلد app ثم الملف layout.jsx وجعله بهذا الشكل

```jsx
"use client";
import { WagmiConfig, createConfig, sepolia } from "wagmi";
import { ConnectKitProvider, getDefaultConfig } from "connectkit";
import Navbar from "../components/instructionsComponent/navigation/navbar";
import Footer from "../components/instructionsComponent/navigation/footer";

const chains = [sepolia]

const config = createConfig(
	getDefaultConfig({
		// Required API Keys
		alchemyId: process.env.ALCHEMY_API_KEY, // or infuraId
		walletConnectProjectId: "demo",

		// Required
		appName: "You Create Web3 Dapp",

		// Optional
		appDescription: "Your App Description",
		appUrl: "https://family.co", // your app's url
		appIcon: "https://family.co/logo.png", // your app's logo,no bigger than 1024x1024px (max. 1MB)
		chains: chains
	})
);

export default function RootLayout({ children }) {
	return (
		<html lang="en">
			<WagmiConfig config={config}>
				<ConnectKitProvider mode="dark">
					<body>
						<div
							style={{
								display: "flex",
								flexDirection: "column",
								minHeight: "105vh",
							}}
						>
							<Navbar />
							<div style={{ flexGrow: 1 }}>{children}</div>
							<Footer />
						</div>
					</body>
				</ConnectKitProvider>
			</WagmiConfig>
		</html>
	);
}
```

الان اذهب الى المجلد app ثم الملف page.jsx وقم بلصق هذا الكود ومتابعة الشرح من التعليقات المتواجدة اعلى كل سطر.

```jsx
'use client'
import "./globals.css";
import { useState, useEffect } from "react"
import { ethers } from "ethers"
import { useAccount } from "wagmi"
import abi from "../utils/Todolist.json"

export default function Home() {
  // Contract Address & ABI
  const contractAddress = "add_your_smart_contract_address_here"
  const contractABI = abi.abi

  // بمراقبة حالة التطبيق ما إذا كان متصل بالمحفظة او لا useAccount ستقوم
  const { connector, isConnected } = useAccount()
  // input ستقوم بتخزين اي تحديث يحصل في
  const [name, setName] = useState("")
  // ستقوم بتخزين المهام التي ستقوم بجلبها من العقد الذكي
  const [tasks, setTasks] = useState([])

  // تعمل هذه الوظيفة بجلب المهام من العقد الذكي
  const getTasks = async () => {
    try {

      // يتم استخدام هذا للوصول إلى كائن اثيريوم والتي من تعد من الكائنات العامة
      const { ethereum } = window

      // يتحقق هذا لمعرفة ما إذا كان موفر شبكة اثيريوم متوفراً
      if (ethereum) {
        // يستخدم هذا للتفاعل مع البلوكتشين
        const provider = new ethers.providers.Web3Provider(ethereum, "any")
        // كائن مُوقع يتم استخدامه لمصادقة وتفويض المعاملات على البلوكتشين
        const signer = provider.getSigner()
        // يعمل هذا على اخذ مثيل للعقد بحيث نتمكن من التفاعل مع البلوكتشين
        const todolist = new ethers.Contract(
          contractAddress,
          contractABI,
          signer
        )

        console.log("fetching tasks...")
        // يقوم بإستدعاء وظيفة جلب المهام من العقد الذكي
        const tasks = await todolist.getTasks()
        console.log("fetched!")
        setTasks(tasks)
      } else {
        console.log("Metamask is not connected");
      }
    } catch (error) {
      console.error(error)
    }
  }

  // تعمل هذه الوظيفة بإضافة المهام
  const addTask = async () => {
    try {
      // يتم استخدام هذا للوصول إلى كائن اثيريوم والتي من تعد من الكائنات العامة
      const {ethereum} = window

      // يتحقق هذا لمعرفة ما إذا كان موفر شبكة اثيريوم متوفراً
      if (ethereum) {
        // يستخدم هذا للتفاعل مع البلوكتشين
        const provider = new ethers.providers.Web3Provider(ethereum, "any")
        // كائن مُوقع يتم استخدامه لمصادقة وتفويض المعاملات على البلوكتشين
        const signer = provider.getSigner()
        // يعمل هذا على اخذ مثيل للعقد بحيث نتمكن من التفاعل مع البلوكتشين
        const todolist = new ethers.Contract(
          contractAddress,
          contractABI,
          signer
        )

        // يقوم بإستدعاء وظيفة إضافة مهمة من العقد الذكي
        const taskTxn = await todolist.addTask(
          name ? name : "anon"
        )
        // انتظر حتى يتم تعدين الصفقة
        await taskTxn.wait()

        console.log("mined ", taskTxn.hash)
        setName("")
      } else {
        console.log("Metamask is not connected");
      }
    } catch (error) {
      console.error(error)
    }

  }

  // تعمل هذه الوظيفة بتحدث حالة مهمة معينة تريد تحديثها
  const updateStatus = async (index) => {
    try {
      // يتم استخدام هذا للوصول إلى كائن اثيريوم والتي من تعد من الكائنات العامة
      const { ethereum } = window

      // يتحقق هذا لمعرفة ما إذا كان موفر شبكة اثيريوم متوفراً
      if (ethereum) {
        // يستخدم هذا للتفاعل مع البلوكتشين
        const provider = new ethers.providers.Web3Provider(ethereum, "any")
        // كائن مُوقع يتم استخدامه لمصادقة وتفويض المعاملات على البلوكتشين
        const signer = provider.getSigner()
        // يعمل هذا على اخذ مثيل للعقد بحيث نتمكن من التفاعل مع البلوكتشين
        const todolist = new ethers.Contract(
          contractAddress,
          contractABI,
          signer
        )

        // يقوم بإستدعاء وظيفة تحديث حالة المهمة من العقد الذكي
        const taskTxn = await todolist.updateStatus(index)
        // انتظر حتى يتم تعدين الصفقة
        await taskTxn.wait()
        console.log("Updated!")
      } else {
        console.log("Metamask is not connected");
      }
    } catch (error) {
      console.error(error)
    }
  }

  // تعمل هذه الوظيفة بإزالة مهمة معينة تريد إزالتها
  const deleteTask = async (index) => {
    try {
      // يتم استخدام هذا للوصول إلى كائن اثيريوم والتي من تعد من الكائنات العامة
      const { ethereum } = window

      // يتحقق هذا لمعرفة ما إذا كان موفر شبكة اثيريوم متوفراً
      if (ethereum) {
        // يستخدم هذا للتفاعل مع البلوكتشين
        const provider = new ethers.providers.Web3Provider(ethereum, "any")
        // كائن مُوقع يتم استخدامه لمصادقة وتفويض المعاملات على البلوكتشين
        const signer = provider.getSigner()
        // يعمل هذا على اخذ مثيل للعقد بحيث نتمكن من التفاعل مع البلوكتشين
        const todolist = new ethers.Contract(
          contractAddress,
          contractABI,
          signer
        )

        // يقوم بإستدعاء وظيفة إزالة المهمة من العقد الذكي
        const taskTxn = await todolist.deleteTask(index)
        // انتظر حتى يتم تعدين الصفقة
        await taskTxn.wait()
        console.log("Deleted!")
      } else {
        console.log("Metamask is not connected");
      }
    } catch (error) {
      console.error(error)
    }
  }

  // تمثل المصفوفة في نهاية استدعاء الوظيفة ما هي تغييرات الحالة التي ستؤدي إلى هذا التغيير
  // في هذه الحالة كلما تغيرت قيم الوظيفتين سيتم استدعاء هذا التغيير مباشرة
  useEffect(() => {
    getTasks()
  }, [])

  return (
    <div dir='rtl'>
      <p className='text-center italic text-3xl text-rose-700 font-bold mt-4'>Todo-list Project</p>
      <div className="max-w-md px-4 mx-auto mt-12">
        <div className="flex justify-center">
          <input onChange={event => setName(event.target.value)} type="text" placeholder="ماهي مهمتك القادمة؟" className="w-full py-3 pl-12 pr-4 text-gray-500 border rounded-md outline-none bg-gray-50 focus:bg-white focus:border-rose-700"/>

          <button onClick={addTask} className="px-4 mr-4 py-2 text-white bg-rose-600 rounded-lg duration-150 hover:bg-rose-700 active:shadow-lg">
            اضافة
          </button>
        </div>
      </div>

      <div className='mt-20'>
        <div>
          <p className='text-center text-xl text-rose-700 font-bold'>المهمات الخاصة بك</p>
          {
            // null نقوم بالتحقق ما إذا كان التطبيق متصل بالمحفظة ليعرض المهام او لا يعرضها اي
            isConnected ? tasks.map((task, idx) => (
              <div key={idx}>
                {task.name && (
                  <div className='flex justify-center mt-5'>
                    <div className='flex justify-center'>
                      <input type="checkbox" checked={task.status} onClick={() => updateStatus(idx)} className="checkbox-item peer ml-2" />
                      <p className='text-xl text-right'>{task.name}</p>
                    </div>

                    <button onClick={() => deleteTask(idx)} className="px-4 mr-52 py-1 text-white bg-red-600 rounded-lg duration-150 hover:bg-red-700 active:shadow-lg">
                      حذف
                    </button>
                  </div>
                )}
              </div>
            )) : null
          }
        </div>
      </div>
    </div>
  )
}
```

يعمل الكود السابق بإختصار شديد على تشغيل العقد الذكي او المشروع الذي قمنا ببناء عقده (Todo-list) في الواجهة الامامية بحيث يتمكن المستخدم من ربط محفظته واضافة المهام وتحديثها وازالتها.

```jsx
// Contract Address & ABI
const contractAddress = "add_your_smart_contract_address_here"
const contractABI = abi.abi
```

لقد قمنا أولاً بإضافة عنوان العقد الذكي الخاص بنا الذي قمنا بنشره على شبكة sepolia في المتغير (contractAddress) ومن ثم قمنا بإضافة ABI العقد في المجلد (utils) وقمنا باستدعائه في المتغير (contractABI).

يمكنك الحصول على ABI الخاص بعقدك الذكي من خلال فتح تطبيق "hardhat" وفتح المجلد "artifacts" ومن ثم فتح المجلد "contracts" و "Todolist.sol" وثم بنسخ الملف "Todolist.json" الى المجلد "utils" الذي قمنا بإنشائه (يمكننا فحص هذا الملف جيداً وكل ماهو مهم بالنسبة لك هو "abi" الخاص بالعقد الذكي الذي ستتعامل معه).

يمكنك تجربة تطبيقك الان

```bash
npm run dev
```

إذهب الى <a href="http://localhost:3000" target="_blank">localhost:3000</a> وقم بالإتصال بالتطبيق من خلال محفظتك وقم بإضافة بعض المهام

<img src="https://www.web3arabs.com/courses/todolist-app.png" />

إنه يعمل, لقد انتهيت من بناء اول تطبيق DApps بنجاح 🥳🥳

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://t.me/Web3ArabsDAO" target="_blank">Telegram</a> او <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
