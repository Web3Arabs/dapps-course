# بناء تطبيق YouTube بإستخدام Livepeer

كل يوم ينتقل المزيد والمزيد من الأشخاص إلى Web3. ويتزايد الطلب على المطورين، وتعد مهارة مطور blockchain من بين أكثر المهارات المطلوبة في صناعة التكنولوجيا.

أفضل طريقة لتحسين مهاراتك في Web3 هي استخدامها في إنشاء مشاريع مختلفة والإجابة على الاسئلة التي ربما تجول في عقلك مثل كيف يمكننا التعامل مع الفيديوهات في مشاريع Web3 بطريقة لا مركزية مثل YouTube. في هذا الدرس ستقوم بإنشاء مشروع بسيط مماثل لفكرة YouTube ولكن لامركزي بشكل كامل إعتماداً على المهارات السابقة في الدروس التي في الاعلى بالإضافة الى <a href="https://livepeer.org" target="_blank">**Livepeer**</a> والذي سيساعدك في التعامل مع الفيديوهات التي سيقوم بنشرها المستخدمين.

## المتطلبات الاساسية للبدء في هذا الدرس:

1. يمكنك التعامل مع لغة البرمجة JavaScript. 
2. لقد قمت بإعداد محفظتك على Metamask.
3. انتهيت من قرأة درس اساسيات لغة Solidity.
4. لقد قمت بتثبيت Node.js على حهاز الكمبيوتر الخاص بك.

## إعداد المشروع

خلال هذا الدرس ستقوم بإنشاء العقد الذكي من اجل تخزين بيانات الفيديوهات التي سيقوم المستخدمين بنشرها ومن ثم ربط العقد الذكي بالموقع الذي سنقوم ببنائه.

ستقوم بفتح terminal وستدخل هذه الاوامر:

```bash
mkdir web3-youtube & cd web3-youtube
mkdir contract & mkdir frontend
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/settingup-project.png"/>

- في المجلد **contract** سنقوم بإعداد ونشر العقد الذكي. 
- في المجلد **frontend** سنقوم ببناء الواجهة الامامية للمشروع وربطها بالعقد الذكي.

## العقد الذكي (Smart Contract)

ستقوم بإضافة هذه الاوامر في terminal لإعداد المشروع:

```bash
cd contract
npm init --yes
npm install --save-dev hardhat @nomicfoundation/hardhat-toolbox@2 dotenv
```

سنقوم الان بتشغيل تطبيق Hardhat

```bash
npx hardhat
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/npx-hardhat.png"/>

يمكنك الان فتح مشروعك على محرر الاكواد الخاص بك.

#### سنلاحظ ان التطبيق يحتوي على 3 مجلدات رئيسية وهي:

1. contracts: الذي سنقوم من خلاله بكتابة العقود الذكية. 
2. scripts: والذي سنقوم من خلاله بالتعامل مع العقود الذكية او رفعها على الشبكات. 
3. test: والذي سنقوم من خلاله بإجراء اختبارات لعقدنا الذكي.

يمكنك الان البدء في إنشاء عقدك الذكي. قم بإنشاء ملف في مجلد contracts بإسم YouTube.sol

```solidity
//SPDX-License-Identifier: Unlicense
pragma solidity ^0.8.19;

contract YouTube {
    // يعمل المتغير على تخزين عدد الفيديوهات
    uint256 public videoCount;
    // اسم العقد الذكي الخاص بك
    string public name = "YouTube";
    // يعمل على إستدعاء الفيديو حسب موقعه mapping إنشاء
    mapping(uint256 => Video) public videos;

    // بالخصائص التالية Video يدعى struct إنشاء
    struct Video {
        uint256 id;           // رقم الفيديو
        string hash;          // تجزئة الفيديو بعد تحميله
        string title;         // عنوان الفيديو
        string description;   // الوصف
        string location;      // الموقع
        string category;      // الفئة
        string thumbnailHash; // رابط الصورة المصغرة
        uint256 createdAt;    // الوقت
        address author;       // مالك الفيديو
    }

    // يقوم بتصدير خصائص الفيديو VideoUploaded يدعى event إنشاء
    event VideoUploaded(
        uint256 id,
        string hash,
        string title,
        string description,
        string location,
        string category,
        string thumbnailHash,
        uint256 createdAt,
        address author
    );

    constructor() {}

    // تعمل الدالة على نشر الفيديو
    function uploadVideo(
        string memory _videoHash,
        string memory _title,
        string memory _description,
        string memory _location,
        string memory _category,
        string memory _thumbnailHash
    ) public {
        // التحقق من صحة تجزئة الفيديو والعنوان ومالك الفيديو
        require(bytes(_videoHash).length > 0);
        require(bytes(_title).length > 0);
        require(msg.sender != address(0));

        // زيادة عدد الفيديوهات في كل مرة يقوم العقد بنشر فيديو
        videoCount++;
        // إضافة الفيديو إلى العقد الذكي
        videos[videoCount] = Video(
            videoCount,
            _videoHash,
            _title,
            _description,
            _location,
            _category,
            _thumbnailHash,
            block.timestamp,
            msg.sender
        );
        // blockchain إضافة التغييرات/الحدث على
        emit VideoUploaded(
            videoCount,
            _videoHash,
            _title,
            _description,
            _location,
            _category,
            _thumbnailHash,
            block.timestamp,
            msg.sender
        );
    }
}
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/yt-contract.png"/>

يمكنك الان رفع عقدك الذكي بكل سهولة. سنقوم باستخدام شبكة الاختبارات وهي sepolia.
اذهب الى المجلد scripts وقم بإنشاء ملف بإسم deploy.js (في حال هناك ملف بنفس الاسم قم بإزالة الاكواد التي فيه وإجعله بهذا الشكل):

```js
const hre = require("hardhat");

async function main() {
  // نقوم بإستدعاء العقد الذكي من اجل نشره
  const YouTube = await hre.ethers.getContractFactory("YouTube");
  const youtube = await YouTube.deploy();

  await youtube.deployed();

  console.log("YouTube deployed to:", youtube.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/deploy-contarct.png"/>

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

قم بفتح ملف hardhat.config.js وقم باستيراد المفاتيح المتواجدة في ملف env. وقم بإختيار الشبكة التي تريد استخدامها لرفع العقد الذكي الخاص بك ولكننا هنا سنستخدم شبكة sepolia فلذلك سنقوم بتحديدها

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

**ملاحظة**: يمكن الحصول على بعض العملات التي تساعدك في اختبار ونشر تطبيقاتك على شبكة **Sepolia** من <a href="https://www.infura.io/faucet/sepolia" target="_blank">**Infura Faucet**</a> - ستقوم بإنشاء حساب على **Infura** ستقوم بوضع عنوان **address** الخاص بك وستحصل على بعض من العملات.

حان وقت نشر عقدك الذكي :) قم بكتابة هذا الامر

```bash
npx hardhat run scripts/deploy.js --network sepolia
```

<div className="flex justify-center items-center">
<img src="https://www.web3arabs.com/courses/dapps/livepeer/deployed-contract.png"/>

قم بحفظ عنوان عقدك الذكي حتى نتمكن من استخدامه اثناء جعله يعمل في الواجهة الامامية.

## الواجهة الامامية (Front-end)

في هذا الدرس سنقوم بإستخدام إطار العمل Next.js و TailwindCSS من اجل بناء واجهة الموقع.

قم بتشغيل هذا الامر في المجلد frontend من اجل إنشاء مشروع nextjs

```bash
npx create-next-app .
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/create-next-app.png"/>

قم بتشغيل هذا الامر لتثبيت المكاتب التي سنحتاجها في المشروع

```bash
npm install react-icons plyr-react moment ethers@5 @livepeer/react
```

ستذهب الى مجلد styles وستقوم بفتح الملف **globals.css** وستبقي هذه الاوامر في الملف

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/globals-css.png"/>

يمكنك تشغيل مشروعك الان ورؤية النتائج من خلال إدخال هذا الامر

```bash
npm run dev
```

ستقوم الان بإنشاء مجلد components ومن ثم إنشاء ملف Header.js وإضافة هذا الكود:

```jsx
import { useEffect, useState } from "react"

export default function Header() {
  const [currentAccount, setCurrentAccount] = useState("")

  // تعمل الدالة على ربط الموقع بالمحفظة
  const connectWallet = async () => {
    try {
      const { ethereum } = window

      // التحقق ما إذا كان المستخدم قام بتثبيت المحفظة
      if (!ethereum) {
        // إذا لم يقم بذلك فسيتم طباعة هذا الامر امامه
        alert("Please install MetaMask")
        return;
      }

      // في حال المستخدم قد قام بتثبيت المحفظة سيقوم بطلب إتصال المحفظة
      const accounts = await ethereum.request({
        method: "eth_requestAccounts",
      })

      // المستخدم في المتصفح address تخزين
      localStorage.setItem("walletAddress", accounts[0])
      setCurrentAccount(accounts[0])
    } catch (error) {
      console.error(error)
    }
  };

  useEffect(() => {
    connectWallet()
  }, [])

  return (
    <>
      <nav className="relative z-20 bg-white w-full md:static md:text-sm md:border-none -mt-6">
        <div className="items-center gap-x-14 px-4 max-w-screen-xl mx-auto md:flex md:px-8">
          <div className="flex items-center justify-between py-3 md:py-5 md:block">
            <a href="/">
              <img
                src="https://img.freepik.com/premium-vector/red-youtube-logo-social-media-logo_197792-1803.jpg?w=2000"
                width={80}
                height={40}
                className="-mt-1"
                alt="YouTube logo"
              />
            </a>
          </div>
          <div className="nav-menu flex-1 pb-3 mt-8 md:block md:pb-0 md:mt-0">
            <ul className="items-center space-y-6 md:flex md:space-x-6 md:space-x-reverse md:space-y-0">
              <div className='flex-1 items-center justify-end gap-x-6 space-y-3 md:flex md:space-y-0'>
                <li>
                  <a href="/upload" className="block py-3 px-4 font-medium text-center text-white bg-indigo-600 hover:bg-indigo-500 active:bg-indigo-700 active:shadow-none rounded-lg shadow md:inline">
                    نشر فيديو
                  </a>
                </li>
                  
                {!currentAccount ? (
                  <li>
                    <button onClick={() => connectWallet()} className="block py-3 px-4 font-medium text-center text-white bg-indigo-600 hover:bg-indigo-500 active:bg-indigo-700 active:shadow-none rounded-lg shadow md:inline">
                      ربط المحفظة
                    </button>
                  </li>
                ) : null}
              </div>
            </ul>
          </div>
        </div>
      </nav>
    </>
  )
}
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/header-front.png"/>

### إعداد حساب Livepeer في المشروع

Livepeer عبارة عن شبكة معالجة فيديو لامركزية ومنصة تطوير حيث يمكنك استخدامها لإنشاء تطبيقات الفيديو. لكونه سريع جدًا وسهل التكامل ورخيص. في هذا البرنامج التعليمي، سنستخدم Livepeer لتحميل مقاطع الفيديو وتقديمها للمستخدمين.

انتقل إلى <a href="https://livepeer.studio/register" target="_blank">**https://livepeer.studio/register**</a> وقم بإنشاء حساب جديد على **Livepeer Studio**.

<img src="https://www.web3arabs.com/courses/dapps/livepeer/livepeer-register.png"/>

بمجرد إنشاء حساب، في لوحة التحكم، انقر فوق **Developers** في الشريط الجانبي.

<img src="https://www.web3arabs.com/courses/dapps/livepeer/livepeer-dashboard.png"/>

بعد ذلك، انقر فوق "**Create an API Key**"، وقم بإعطاء اسم لمفتاحك ثم انسخه حيث سنحتاج إليه لاحقًا.

<img src="https://www.web3arabs.com/courses/dapps/livepeer/livepeer-devs.png"/>

<img src="https://www.web3arabs.com/courses/dapps/livepeer/livepeer-create-api.png"/>

مكتبة **Livepeer.js** عبارة عن JavaScript SDK مع خطافات جاهزة للاستخدام تتيح لنا تحميل الفيديو بسرعة وتقديم مقاطع الفيديو والاتصال بـ Livepeer Studio. إذهب إلى الملف **app.js_** في المجلد pages وقم بإعداد بهذا الشكل:

ستقوم بإضافة **API Key** الذي قمنا بإنشائه على Livepeer Studio بدلاً من **"add-api-key-here"** في الكود:

```jsx
import '@/styles/globals.css'
import { LivepeerConfig, createReactClient, studioProvider } from '@livepeer/react'

export default function App({ Component, pageProps }) {
  const LivePeerClient = createReactClient({
    provider: studioProvider({ apiKey: "add-api-key-here" }),
  });

  return (
    <LivepeerConfig client={LivePeerClient}>
      <Component {...pageProps} />
    </LivepeerConfig>
  )
}
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/appjs.png"/>

### نشر الفيديوهات

سنقوم بإعداد صفحة نشر الفيديوهات وربطها بالعقد الذكي ومكتبة Livepeer. ستذهب الى المجلد pages وستقوم بإنشاء ملف بإسم **upload.js** وستضع هذا:

```jsx
import React, { useState, useRef } from "react";
import { BiCloud } from "react-icons/bi";
import { useCreateAsset } from "@livepeer/react";
import ContractAbi from "../../contract/artifacts/contracts/Youtube.sol/YouTube.json"
import { ethers } from "ethers"
import Header from "../components/Header"

export default function Upload() {
  
  const [title, setTitle] = useState("");
  const [description, setDescription] = useState("");
  const [category, setCategory] = useState("");
  const [location, setLocation] = useState("");
  const [thumbnail, setThumbnail] = useState("https://picsum.photos/250")
  const [video, setVideo] = useState("");
  const [uploaded, setUploaded] = useState(false)

  const videoRef = useRef();

  const { mutate: createAsset, data: assets, status, progress, error } = useCreateAsset(
    video ? {
      sources: [
        {
          name: video.name,
          file: video,
          storage: {
            ipfs: true,
            metadata: {
              name: title,
              description: description,
            },
          },
        }
      ],
    } : null
  );

  // إستدعاء العقد الذكي للبدء في التعامل معه
  const getContract = async () => {
    // إنشاء مزود جديد
    const provider = new ethers.providers.Web3Provider(window.ethereum);
    // signer الحصول على
    const signer = provider.getSigner();
    // إستدعاء العقد الذكي 
    let contract = new ethers.Contract(
      // عقدك الذكي هنا address قم بإضافة
      "add-contract-address-here",
      ContractAbi.abi,
      signer
    );
    
    return contract;
  }

  // Livepeer دالة لطلب تحميل الفيديو على
  const uploadVideo = async () => {
    setUploaded(true)
    // لتحميل الفيديو useCreateAsset من الخطاف createAsset إستدعاء دالة
    await createAsset?.()
  };

  // مجرد ان ينقر المستخدم على نشر الفيديو يقوم بنشر الفيديو الى العقد الذكي مباشرة
  const postVideo = async () => {
    // استدعاء العقد الذكي
    let contract = await getContract();

    // نشر الفيديو على العقد الذكي
    let tx = await contract.uploadVideo(
      assets[0]?.id,
      title,
      description,
      location,
      category,
      thumbnail
    );
    await tx.wait()
  };

  const renderButton = () => {
    // في حال لم يتم تحميل الفيديو بعد سيقوم بإظهار زر التحميل
    if (!uploaded) {
      return (
        <button
          onClick={() => {
            uploadVideo()
          }}
          className="bg-blue-500 hover:bg-blue-700 text-white py-2 rounded-lg flex px-4 justify-between flex-row items-center"
        >
          <BiCloud />
          <p className="mr-2">تحميل</p>
        </button>
      )
    }
    else {
      // سيتوجب عليه الإنتظار اولاً Livepeer في حال لم يكتمل تحميل الفيديو على
      if (!assets) {
        return (
          <button
            className="bg-blue-500 hover:bg-blue-700 text-white py-2 rounded-lg flex px-4 justify-between flex-row items-center"
          >
            <p className="mr-2">جاري الإنتظار...</p>
          </button>
        )
      }
      // سيظهر للمستخدم زر نشر الفيديو Livepeer بمجرد ان يتم تحميل الفيديو على
      if (assets) {
        return (
          <button
            onClick={() => {
              postVideo()
            }}
            className="bg-blue-500 hover:bg-blue-700 text-white py-2 rounded-lg flex px-4 justify-between flex-row items-center"
          >
            <BiCloud />
            <p className="mr-2">نشر الفيديو</p>
          </button>
        )
      }
    }
  }

  return (
    <div dir="rtl">
      <Header />
      <div className="w-full h-screen flex flex-row -mt-8">
        <div className="flex-1 flex flex-col">
          <div className="mt-5 mr-10 flex  justify-end">
            <div className="flex items-center ml-8">
              <a href="/" className="bg-transparent text-black py-2 px-6 border rounded-lg border-black ml-6">
                رجوع
              </a>
              {renderButton()}
                                <div className="flex flex-col m-10     mt-5  lg:flex-row">
            <div className="flex lg:w-3/4 flex-col ">
              <label className="text-black text-sm">عنوان الفيديو</label>
              <input
                value={title}
                onChange={(e) => setTitle(e.target.value)}
                placeholder="لماذا يجب عليك تعلم Web3؟"
                className="w-[90%] text-black placeholder:text-gray-500 rounded-md mt-2 h-12 p-2 border border-[#444752] focus:outline-none"
              />
              <label className="text-black mt-10">الوصف</label>
              <textarea
                value={description}
                onChange={(e) => setDescription(e.target.value)}
                placeholder="يعد تعلم Web3 الان يشابه شراء البيتكوين عام 2009 والإستثمار بالعديد من العملات المشفرة حتى يومنا هذا."
                className="w-[90%] text-black h-32 placeholder:text-gray-500  rounded-md mt-2 p-2 border border-[#444752] focus:outline-none"
              />
              <label className="text-black mt-10">الصورة المصغرة</label>
              <input
                value={thumbnail}
                onChange={(e) => setThumbnail(e.target.value)}
                placeholder="قم بوضع رابط الصورة المصغرة - مثال: https://picsum.photos/250"
                className="w-[90%] text-black placeholder:text-gray-500 rounded-md mt-2 h-12 p-2 border border-[#444752] focus:outline-none"
              />

              <div className="flex flex-row mt-10 w-[90%] justify-between">
                <div className="flex flex-col w-2/5">
                  <label className="text-black text-sm">الموقع</label>
                  <input
                    value={location}
                    onChange={(e) => setLocation(e.target.value)}
                    type="text"
                    placeholder="الإمارات - دبي"
                    className="w-[90%] text-black placeholder:text-gray-500 rounded-md mt-2 h-12 p-2 border border-[#444752] focus:outline-none"
                  />
                                <div className="flex flex-col w-2/5">
                  <label className="text-black text-sm">الفئة</label>
                  <select
                    value={category}
                    onChange={(e) => setCategory(e.target.value)}
                    className="w-[90%] text-black placeholder:text-gray-500 rounded-md mt-2 h-12 p-2 border border-[#444752] focus:outline-none"
                  >
                    <option>اغاني</option>
                    <option>رياضة</option>
                    <option>العاب</option>
                    <option>اخبار</option>
                    <option>ترفيه</option>
                    <option>تعليمي</option>
                    <option>تكنولوجيا</option>
                    <option>رحلة/سفر</option>
                    <option>آخر</option>
                  </select>
                                          
            <div
              onClick={() => {
                videoRef.current.click();
              }}
              className={
                video
                  ? " w-96 rounded-md h-64 items-center justify-center flex"
                  : "border-2 border-gray-600  w-96 border-dashed rounded-md mt-8 h-64 items-center justify-center flex"
              }
            >
              {video ? (
                <video
                  controls
                  src={URL.createObjectURL(video)}
                  className="h-full rounded-md"
                />
              ) : (
                <p className="text-[#848891]">رفع فيديو</p>
              )}
                                <input
            type="file"
            className="hidden"
            ref={videoRef}
            accept={"video/*"}
            onChange={(e) => {
              setVideo(e.target.files[0]);
              console.log(e.target.files[0]);
            }}
          />
                    );
}
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/uploadjs.png"/>

لقد قمت بالتوضيح بالفعل على كل سطر من الكود حتى تتمكن من فهم ما يجري.

احفظ الملف و قم بتجربته من هنا - <a href="http://localhost:3000/upload" target="_blank">localhost:3000/upload</a> - لقد انتهينا من صفحة نشر الفيديو. يجب أن تكون الآن قادرًا على تحميل مقاطع الفيديو إلى العقد الذكي.

لا تنسى ربط محفظتك بالموقع قبل نشر الفيديو من خلال النقر على الزر "**ربط المحفظة**"

<img src="https://www.web3arabs.com/courses/dapps/livepeer/upload-page.png"/>

### إستدعاء الفيديوهات

سنقوم بإستدعاء الفيديوهات من العقد الذكي في الصفحة الرئيسية بكل سهولة.

ستذهب الى الملف **index.js** في المجلد pages وستقوم بإضافة هذا الكود هناك

```jsx
import { useState, useEffect } from 'react'
import ContractAbi from "../../contract/artifacts/contracts/YouTube.sol/YouTube.json"
import { ethers } from "ethers"
import Header from "../components/Header"
import { BiCheck } from "react-icons/bi";
import moment from "moment";

export default function home() {
  // تخزين بيانات الفيديوهات هنا بعد إستدعائها من العقد الذكي
  const [videos, setVideos] = useState([])

  // إستدعاء العقد الذكي للبدء في التعامل معه
  const getContract = async () => {
    // إنشاء مزود جديد
    const provider = new ethers.providers.Web3Provider(window.ethereum);
    // signer الحصول على
    const signer = provider.getSigner();
    // إستدعاء العقد الذكي 
    let contract = new ethers.Contract(
      // عقدك الذكي هنا address قم بإضافة
      "add-contract-address-here",
      ContractAbi.abi,
      signer
    );
    
    return contract;
  }

  // إستدعاء عدد الفيديوهات والفيديوهات من العقد الذكي
  const getCountsAndVideos = async () => {
    // استدعاء العقد الذكي
    const contract = await getContract()

    // في العقد الذكي videoCount إستدعاء عدد الفيديوهات من المتغير
    const count = await contract.videoCount()

    // تجميع الفيديوهات في مصفوفة
    const vids = []
    // إنشاء حلقة دوران لإستدعاء بيانات كل فيديو حسب موقعه في العقد الذكي
    for (let i=1; i<=count.toString(); i++) {
      vids.push(await contract.videos(i))
    }
    setVideos(vids)
  }

  useEffect(() => {
    getCountsAndVideos()
  }, [])

  return (
    <div dir="rtl">
      <Header />
      <div className="grid mx-3 gap-x-2 gap-y-5 mt-16 sm:grid-cols-2 lg:grid-cols-4">
        {videos.map((video, idx) => (
          <div
            key={idx}
            onClick={() => {
              // يقوم بنقلنا الى صفحة تشغيل الفيديو (سنقوم بإنشائها بعد الإنتهاء من هذا)
              window.location.href = `/video?id=${video.id}`;
            }}
          >
            <div className="mx-3 group sm:max-w-sm cursor-pointer">
              <img className="object-cover rounded-lg w-full h-40" src={video.thumbnailHash} />
              <div className="mt-3 space-y-2">
                <h4 className="text-md font-bold text-black mt-3">
                  {video.title}
                </h4>
                <p className="text-sm flex items-center text-[#878787] mt-1">
                  {video.category + " • " + moment(video.createdAt.toString() * 1000).fromNow()}
                </p>
                <p className="text-sm flex items-center text-[#878787] mt-1">
                  ...{video?.author?.slice(0, 9)}{" "}
                  <BiCheck size="20px" color="green" className="ml-1" />
                </p>
                                            ))}
            )
}
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/indexjs.png"/>

احفظ الملف و قم بتجربته من هنا - <a href="http://localhost:3000" target="_blank">localhost:3000</a> - لقد انتهينا من صفحة إستدعاء الفيديوهات من عقدك الذكي. يجب أن تكون الآن قادرًاعلى رؤية الفيديوهات التي قمت بنشرها على العقد الذكي الخاص بك.

<img src="https://www.web3arabs.com/courses/dapps/livepeer/home-page.png"/>


### مشغل الفيديوهات

من اجل تشغيل الفيديوهات اثناء النقر عليها في صفحة video. سنقوم اولا بالعمل على واجهة المشغل من خلال الذهاب إلى المجلد components وإنشاء ملف **Player.js** وإضافة هذا الكود هناك

```jsx
import React from "react"
import { useAsset } from "@livepeer/react"
import Plyr from "plyr-react"
import "plyr-react/plyr.css"

export default function Player({ video }) {
  // Livepeer إستدعاء الفيديو من
  const { data: asset } = useAsset(video.hash)

  return (
    <div>
      <Plyr
        source={{ type: "video", title: asset?.name, sources: [{src: asset?.downloadUrl, type: "video/mp4"}]}}
        options={{autoplay: true}}
        autoPlay={true}
      />
      <div className="flex justify-between flex-row py-4">
        <div>
          <h3 className="text-2xl text-black">{video.title}</h3>
          <p className="text-gray-600 mt-1">{video.description}</p>
          <p className="text-gray-500 mt-1">
            {video.category} •{" "}
            {new Date(video.createdAt.toString() * 1000).toLocaleString("ar-IN")}
          </p>
                    );
}
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/playerjs.png"/>

ستذهب إلى المجلد pages من أجل إنشاء الملف video.js والذي سيقوم بعرض الفيديوهات من العقد الذكي وتشغليها. قم بإضافة هذا الكود هناك

```jsx
import { useState, useEffect } from 'react'
import Player from '../components/Player'
import ContractAbi from "../../contract/artifacts/contracts/YouTube.sol/YouTube.json"
import { ethers } from "ethers"
import "plyr-react/plyr.css"
import Header from "../components/Header"
import { BiCheck } from "react-icons/bi"
import moment from "moment"

export default function VideoPage() {
  // تخزين بيانات الفيديو
  const [video, setVideo] = useState(null)
  // تخزين الفيديوهات المقترحة
  const [suggestedVideos, setSuggestedVideos] = useState([])

  // الصفحة URL تعمل هذه الدالة بإستدعاء المعلمات من
  const getUrlVars = () => {
    var vars = {}
    var parts = window.location.href.replace(
      /[?&]+([^=&]+)=([^&]*)/gi,
      function (m, key, value) {
        vars[key] = value
      }
    )
    return vars
  }

  // إستدعاء العقد الذكي للبدء في التعامل معه
  const getContract = async () => {
    // إنشاء مزود جديد
    const provider = new ethers.providers.Web3Provider(window.ethereum);
    // signer الحصول على
    const signer = provider.getSigner();
    // إستدعاء العقد الذكي 
    let contract = new ethers.Contract(
      // عقدك الذكي هنا address قم بإضافة
      "add-contract-address-here",
      ContractAbi.abi,
      signer
    );
    
    return contract;
  }

  // إستدعاء عدد الفيديوهات والفيديوهات من العقد الذكي
  const getCountsAndVideos = async () => {
    // استدعاء العقد الذكي
    const contract = await getContract()

    // في العقد الذكي videoCount إستدعاء عدد الفيديوهات من المتغير
    const count = await contract.videoCount()

    // تجميع الفيديوهات في مصفوفة
    const vids = []
    // إنشاء حلقة دوران لإستدعاء بيانات كل فيديو حسب موقعه في العقد الذكي
    for (let i=1; i<=count.toString(); i++) {
      vids.push(await contract.videos(i))
    }
    // تخزين الفيديو الذي يريد المستخدم تشغيله
    setVideo(vids[getUrlVars().id-1])
    // تخزين الفيديوهات المقترحة
    setSuggestedVideos(vids.slice(-15))
  }

  useEffect(() => {
    getCountsAndVideos()
  }, [])

  return (
    <div dir="rtl">
      <Header />
      <div className="w-full flex flex-row -mt-12">
        <div className="flex-1 flex flex-col">
          {video && (
            <div className="flex flex-col m-10 justify-between lg:flex-row">
              <div className="lg:w-4/6 w-6/6">
                <Player video={video} />
                            <div className="w-2/6">
                <h4 className="text-md font-bold text-black mr-5 mb-3">
                  فيديوهات مقترحة
                </h4>
                {suggestedVideos.map((video) => (
                  <div
                    onClick={() => {
                      window.location.href = `/video?id=${video.id}`;
                    }}
                    key={video.id}
                    className={`${video.id==getUrlVars().id ? "hidden" : null}`}
                  >
                    <div className="flex flex-row mx-5 mb-5 item-center justify-center cursor-pointer">
                      <img className="object-cover rounded-lg w-60 h-28" src={video.thumbnailHash} />
                      <div className="mr-3 w-80">
                        <h4 className="text-md font-bold text-black mt-3">
                          {video.title}
                        </h4>
                        <p className="text-sm flex items-center text-[#878787] mt-1">
                          {video.category + " • " + moment(video.createdAt.toString() * 1000).fromNow()}
                        </p>
                        <p className="text-sm flex items-center text-[#878787] mt-1">
                          ...{video?.author?.slice(0, 9)}{" "}
                          <BiCheck size="20px" color="green" className="ml-1" />
                        </p>
                                                                            ))}
                                    )}
                    )
}
```

<img src="https://www.web3arabs.com/courses/dapps/livepeer/videojs.png"/>

احفظ الملف وانقر على أي مقطع فيديو متواجد في الشاشة الرئيسية. من المفترض أن تتم إعادة توجيهك إلى شاشة الفيديو وتشغيله بهذا الشكل:

<img src="https://www.web3arabs.com/courses/dapps/livepeer/video-page.png"/>

تهانينا لقد انتهيت من الدرس يمكنك البدء في إضافة المزيد من الاشياء الى المشروع مثل شريط البحث عن الفيديوهات والمشاركة في تطوير الدرس على <a href="https://github.com/Web3Arabs/DApps-Course" target="_blank">GitHub</a>.

يمكنك الوصول الى المشروع بشكل مباشر على <a href="https://github.com/Web3Arabs/YouTube-Clone" target="_blank"> GitHub من هنا</a>

**كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://t.me/Web3ArabsDAO" target="_blank">**Telegram**</a> او <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">**Discord**</a> وسنكون أكثر من سعداء لمساعدتك!**
