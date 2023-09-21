# إنشاء تطبيق TikTok بإستخدام بروتوكول Lens و Livepeer

في هذا الدرس سنقوم ببناء تطبيق لمشاهدة الفيديوهات القصيرة مثل تطبيق TikTok بطريقة بسيطة بإستخدام إطار العمل Nextjs و Tailwind.

الهدف من هذا الدرس بعد ان تعرفنا في الدرس السابق على طرق إستدعاء المنشورات وغيره. في هذا الدرس سنركز على طريقة إرسال المنشورات والفيديوهات وتسجيل الدخول عن طريق حساب Lens بسهولة حتى تتمكن من بناء تطبيقات كاملة.

## إعداد المشروع

ستقوم بإستدعاء مشروع Nextjs من خلال فتح terminal وإضافة هذا الأوامر

```bash
mkdir lens-tiktok & cd lens-tiktok
npx create-next-app@latest .
```

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/create-next-app.png"/>

ستقوم بتثبيت هذه المكاتب التي سنحتاجها اثناء التعامل مع بروتوكول Lens

```bash
npm install @lens-protocol/react-web @lens-protocol/wagmi @livepeer/react axios uuid viem wagmi react-icons
```

قم بتشغيل المشروع من خلال إدخال هذا الامر

```bash
npm run dev
```

يمكنك الان الذهاب مباشرة الى - <a href="http://localhost:3000" target="_blank">localhost:3000</a> - حتى يقوم بفتح هذا

<img src="https://www.web3arabs.com/courses/lens/next-app.png"/>

بعد الإنتهاء من كل هذا قم بفتح المشروع على اي محرر اكواد مثل VS Code او غيره.

## إنشاء حساب تجريبي على بروتوكول Lens

ستقوم بإنشاء حساب تجريبي على بروتوكول Lens لغرض التجربة والتعلم.. قم بالذهاب إلى Lenster Testnet وتابع الفيديو التالي:

**ملاحظة**: قم بإضافة شبكة **Polygon Mumbai** إلى محفظتك <a href="https://chainlist.org/?search=mumbai&testnets=true" target="_blank">**من هنا**</a> عن طريق ربط محفظتك والنقر على **Add to Metamask**.

<video width="100%" height="100%" controls="controls">
  <source src="https://www.web3arabs.com/courses/lens/lens-tiktok/create-testnet-lens.mp4" type="video/mp4" />
</video>

## إعداد حساب Livepeer

Livepeer عبارة عن شبكة معالجة فيديو لامركزية ومنصة تطوير حيث يمكنك استخدامها لإنشاء تطبيقات الفيديو. لكونه سريع جدًا وسهل التكامل ورخيص. في هذا البرنامج التعليمي، سنستخدم Livepeer لتحميل مقاطع الفيديو وتقديمها للمستخدمين.

انتقل إلى <a href="https://livepeer.studio/register" target="_blank">**https://livepeer.studio/register**</a> وقم بإنشاء حساب جديد على **Livepeer Studio**.

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/livepeer-register.png"/>

بمجرد إنشاء حساب، في لوحة التحكم، انقر فوق **Developers** في الشريط الجانبي.

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/livepeer-dashboard.png"/>

بعد ذلك، انقر فوق "**Create an API Key**"، وقم بإعطاء اسم لمفتاحك ثم انسخه حيث سنحتاج إليه لاحقًا.

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/livepeer-devs.png"/>

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/livepeer-create-api.png"/>

قم بحفظ مفتاح **api** الخاص بك سنقوم بإستخدامه قريباً اثناء البدء في بناء الواجهة الأمامية للمشروع.

## إعداد حساب Pinata

سنقوم بإستخدام منصة <a href="https://www.pinata.cloud/" target="_blank">Pinata</a> من اجل ان نقوم برفع بيانات المنشورات على IPFS ونتحكم بها بكل سهولة.

قم بإنشاء حساب على <a href="https://app.pinata.cloud/register" target="_blank">Pinata</a> حتى يتم إعادك الى لوحة التحكم.

سنقوم بإنشاء مفاتيح خاصة للتعامل مع API الخاصة في Pinata. من خلال لوحة التحكم ستقوم بالنقر على **API Keys**:

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/pinata-dashboard.png"/>

ستقوم الان بالنقر على الزر **New Key** لإنشاء مفتاح جديد:

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/pinata-api-keys.png"/>

ثم قم بإعطاء كل الصلاحية للمشرفين وإعطاء اسم للمفاتيح ومن ثم النقر على الزر **Create Key** بهذا الشكل:

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/pinata-create-key.png"/>

أخيراً - ستقوم بحفظ **API Key** و **API Secret** في ملف جانبي حتى نقوم بإستخدامهم في تخزين بيانات **metadata** الخاصة بالمنشورات:

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/pinata-keys.png"/>

## بناء المشروع

ستذهب الى مجلد app وستقوم بفتح الملف globals.css وستبقي هذه الاوامر في الملف

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

body {
  background-color: black;
}
```

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/globals-css.png"/>

سنقوم الان بإعداد بروتوكول Lens في المشروع بحيث نستطيع التعامل مع مكونات البروتوكول بكل سهولة - ستذهب الى مجلد app وستقوم بإضافة هذه الأكواد في ملف **layout.tsx**

**ملاحظة**: قم بإضافة مفتاح API الذي قمنا بإنشائه على Livepeer في مكان السطر الذي يحتوي على **add-livepeer-api-key**.

```tsx
// app/layout.tsx
"use client";
import './globals.css';
import { LivepeerConfig, createReactClient, studioProvider } from '@livepeer/react'
import { WagmiConfig, configureChains, createConfig } from 'wagmi';
import { polygon, polygonMumbai } from 'wagmi/chains';
import { InjectedConnector } from 'wagmi/connectors/injected';
import { publicProvider } from 'wagmi/providers/public';
import { LensConfig, LensProvider, development, production, appId } from '@lens-protocol/react-web';
import { bindings as wagmiBindings } from '@lens-protocol/wagmi';

// Polygon تهيئة الشبكة التي ستقوم المحفظة بالإتصال بها وهي شبكة
const { publicClient, webSocketPublicClient } = configureChains(
  [polygonMumbai, polygon],
  [publicProvider()],
);

const config = createConfig({
  autoConnect: true,
  publicClient,
  webSocketPublicClient,
  connectors: [
    new InjectedConnector({
      options: {
        shimDisconnect: false, // see https://github.com/wagmi-dev/wagmi/issues/2511
      },
    }),
  ],
});

// إعداد البروتوكول في المشروع
const lensConfig: LensConfig = {
  // إضافة إسم للتطبيق
  appId: appId('TikTok'),
  // يقوم بتوفير الربط والتوقيع مع الحساب
  bindings: wagmiBindings(),
  // تحديد البيئة التي سيتعامل معها المشروع
  // production وإذا كان حساب اساسي اجعلها development إذا كان على حساب تجريبي قم بإستخدام
  environment: development,
};
 
// Livepeer إعداد المشروع مع
const LivePeerClient = createReactClient({
  // بالمفتاح الخاص بك الذي قمنا بإنشائه add-livepeer-api-key قم بتغيير
  provider: studioProvider({ apiKey: "add-livepeer-api-key" }),
});

export default function RootLayout({ children }: any) {
  return (
    <html lang="en">
      <body>
        <WagmiConfig config={config}>
          <LensProvider config={lensConfig}>
            <LivepeerConfig client={LivePeerClient}>
              {children}
            </LivepeerConfig>
          </LensProvider>
        </WagmiConfig>
      </body>
    </html>
  );
};
```

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/layout.png"/>

في المجلد app ستقوم بإنشاء ملف بإسم **header.tsx** سنقوم بإستدعائه في اعلى كل صفحة في الموقع من اجل تسجيل الدخول وتسجيل الخروج وغيره...

قم بإضافة هذا الكود في ملف **header.tsx** وتابع الشرح اعلى كل سطر:

```tsx
// app/header.tsx
import { useWalletLogin, useWalletLogout, useActiveProfile } from '@lens-protocol/react-web';
import { useAccount, useConnect, useDisconnect } from 'wagmi';
import { InjectedConnector } from 'wagmi/connectors/injected';

export default function Header() {
  // ربط المحفظة بالموقع
  const { connectAsync } = useConnect({
    connector: new InjectedConnector(),
  });

  // إستدعاء المحفظة المرتبطة بالموقع
  const { isConnected } = useAccount();
  // فصل المحفظة عن الموقع
  const { disconnectAsync } = useDisconnect();

  // تسجيل الدخول وإستدعاء الحساب من البروتوكول المرتبط بالمحفظة
  const { execute: login, isPending: isLoginPending } = useWalletLogin();
  // Lens تسجيل الخروج من حساب بروتوكول
  const { execute: logout, isPending: isLogoutPending } = useWalletLogout();

  // إستدعاء بيانات الحساب الذي في حالة تسجيل دخول
  const { data: profile, loading: isLoading } = useActiveProfile();

  // Lens تعمل الدالة على عملية ربط المحفظة وتسجيل الدخول عبر حساب
  const onLoginClick = async () => {
    if (isConnected) {
      await disconnectAsync();
    }

    const { connector } = await connectAsync();

    if (connector instanceof InjectedConnector) {
      const walletClient = await connector.getWalletClient();
      await login({
        address: walletClient.account.address,
      });
    }
  };
  
  return (
    <nav className="relative z-20 w-full md:static md:text-sm md:border-none -mt-6 mb-3">
      <div className="items-center gap-x-14 px-4 max-w-screen-xl mx-auto md:flex md:px-8">
        <div className="flex items-center justify-between py-3 md:py-5 md:block">
          <a href="/">
            <img className="bg-white rounded-full" width="50" height="50" src="https://seeklogo.com/images/T/tiktok-share-icon-black-logo-29FFD062A0-seeklogo.com.png"/>
          </a>
        </div>
        <div className="w-[55%] flex justify-center items-center">
          <input
            type="text"
            placeholder="Type to search"
            className="rounded-md border border-neutral-800 p-2 bg-transparent focus:outline-none w-[55%] text-white"
          />
        </div>
        <div className="nav-menu flex-1 pb-3 mt-8 md:block md:pb-0 md:mt-0">
          <ul className="items-center space-y-6 md:flex md:space-x-6 md:space-x-reverse md:space-y-0">
            <div className='flex-1 items-center justify-end gap-x-6 space-y-3 md:flex md:space-y-0'>
              {profile ? (
                <>
                  <li>
                    <a href="/upload" className="block py-3 px-4 font-medium text-center text-white bg-indigo-600 hover:bg-indigo-500 active:bg-indigo-700 active:shadow-none rounded-lg shadow md:inline">
                      New Video
                    </a>
                  </li>
                  <li>
                    <button onClick={logout} disabled={isLogoutPending} className="block py-3 px-4 font-medium text-center text-white bg-indigo-600 hover:bg-indigo-500 active:bg-indigo-700 active:shadow-none rounded-lg shadow md:inline">
                      Logout
                    </button>
                  </li>
                  <li>
                    {
                      profile.picture && profile.picture.__typename == 'MediaSet' ? (
                        <img src={profile.picture.original.url} width="50" height="50" className='rounded-full w-[50px] h-[50px]' />
                      ) : <div className="w-[50px] h-[50px] bg-slate-300 rounded-full" />
                    }
                  </li>
                </>
              ) : (
                <li>
                  <button onClick={onLoginClick} disabled={isLoginPending} className="block py-3 px-4 font-medium text-center text-white bg-indigo-600 hover:bg-indigo-500 active:bg-indigo-700 active:shadow-none rounded-lg shadow md:inline">
                    Login with Lens
                  </button>
                </li>
              )}
            </div>
          </ul>
        </div>
      </div>
    </nav>
  );
}
```

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/header.png"/>

الان سنقوم بإستدعاء المنشورات من نوع الفيديوهات القصيرة الى الصفحة الرئيسية.

في المجلد app ستقوم بفتح الملف **page.tsx** وستقوم بإضافة هذا الكود ومتابعة الشرح اعلى كل سطر:

```tsx
// app/page.tsx
"use client";
import { useExplorePublications, PublicationTypes, appId } from '@lens-protocol/react-web';
import Header from './header';
import Link from 'next/link';

export default function Home() {
  // نقوم بإستدعاء المنشورات المتداولة في البروتوكول
  const { data: posts, loading } = useExplorePublications({
    // نقوم بتحديد النوع بحيث يظهر لنا المنشورات فقط وليس التعليقات
    publicationTypes: [PublicationTypes.Post],
    // lenstube-bytes إستدعاء فيديوهات قصيرة من تطبيق
    sources: [appId("lenstube-bytes")],
    // نقوم بإستدعاء 25 من المنشورات
    limit: 25,
  }) as any;

  // قم بمراقبتها من هناك console طباعة المنشورات على
  console.log("posts", posts);

  // إذا مازال يقوم بإستدعاء المنشورات فسيطلب من المستخدم ان ينتظر
  if (loading) return <div className="mx-20 my-10">Loading Posts...</div>

  return (
    <div className='my-4 mx-8'>
      <Header />
      {posts?.map((post: any) => (
        <div className="mb-8" key={post.id}>
          <div className='flex flex-row justify-center items-start gap-4'>
            <div className='flex justify-center items-center w-[50px] h-[50px]'>
              {
                post.profile.picture && post.profile.picture.__typename === 'MediaSet' ? (
                  <Link href={`/${post.profile.handle}`}>
                    <img src={post.profile.picture.original.url} width="50" height="50" className='rounded-full w-[50px] h-[50px]' />
                  </Link>
                ) : <div className="w-[50px] h-[50px] bg-slate-300 rounded-full" />
              }
            </div>
            <div className='flex flex-col w-[50%]'>
              <div className='flex flex-row justify-between'>
                <Link href={`/${post.profile.handle}`}>
                  <div className='flex flex-row justify-start gap-2 text-white'>
                    <div className="text-lg font-black">{post.profile.handle}</div>
                    <div className="text-lg">{post.profile.name}</div>
                  </div>
                </Link>
                <div>
                  <button className="block py-2 px-4 font-medium text-center border border-red-600 text-red-600 bg-black hover:bg-red-100 active:shadow-none rounded-lg shadow md:inline">
                    Follow
                  </button>
                </div>
              </div>
              <div className="mb-2 text-white">
                {post.metadata.content.split("\n").map((i: string, id: number) => (<p key={id}>{i}</p>))}
              </div>
              <div>
                {post.metadata && post.metadata.media[0].__typename === 'MediaSet' ? (
                  <video width="55%" height="70%" className="w-[55%] h-[70%] rounded-lg" controls={true}>
                    <source 
                      src={post.metadata.animatedUrl?.substr(0,4) == "ipfs" ? (
                        `${post.metadata.animatedUrl?.replace('ipfs://', 'https://cloudflare-ipfs.com/ipfs/')}`
                      ) : post.metadata.animatedUrl} 
                    />
                  </video>
                ) : null}
              </div>
            </div>
          </div>
        </div>
      ))}
    </div>
  );
}
```

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/home.png"/>

عند فتح صفحتك - <a href="http://localhost:3000" target="_blank">localhost:3000</a> - يجب ان تكون بهذا الشكل:

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/home-page.png"/>

نقوم الان بإنشاء صفحة لرفع الفيديوهات ستقوم بفتح مجلد app ومن ثم إنشاء مجلد بإسم **upload** وفي هذا المجلد قم بإنشاء ملف بإسم **page.jsx**

قم بإضافة هذه الأكواد في الملف وقم بمتابعة الشرح أعلى كل سطر:

**ملاحظة**: قم بإضافة المفاتيح التي قمنا بإنشائها في منصة Pinata من اجل تخزين البيانات على IPFS.

```jsx
// app/upload/page.jsx
"use client";
import React, { useState, useRef } from "react";
import { BiCloud } from "react-icons/bi";
import { useCreateAsset } from "@livepeer/react";
import { ContentFocus, useActiveProfile, useCreatePost, VideoType, ImageType } from '@lens-protocol/react-web';
import axios from "axios";
import { v4 as uuid } from "uuid";
import Header from '../header';

// هنا API قم بإضافة مفتاح
const pinata_api_key = "add-api-key"
// هنا Secret API قم بإضافة مفتاح
const pinata_secret_api_key = "add-secret-api-key"

export default function Upload() {

  const [description, setDescription] = useState("");
  const [thumbnail, setThumbnail] = useState("");
  const [urlImage, setUrlImage] = useState("");
  const [video, setVideo] = useState("");
  const [uploaded, setUploaded] = useState(false);

  const videoRef = useRef();

  // إستدعاء بيانات الحساب الذي في حالة تسجيل دخول
  const { data: publisher, error: isError, loading: profileLoading } = useActiveProfile();
  
  // بواسطة هذا الخطاف سنقوم بالنشر على البروتوكول
  const {
    execute: create,
    error: postError,
    isPending: isPosting
  } = useCreatePost({ publisher, upload });
  

  const { mutate: createAsset, data: assets, status, progress, error } = useCreateAsset(
    video ? {sources: [{
        name: video.name,
        file: video,
        storage: {ipfs: true, metadata: {name: description, description: description}},
      }],
    } : null
  );

  // IPFS تعمل الدالة على تخزين بيانات المنشور على
  // useCreatePost تعمل الدالة في داخل خطاف
  async function upload(data) {
    // Pinata تخزين البيانات بمساعدة خدمات
    const res = await axios({method: "post",
      url: "https://api.pinata.cloud/pinning/pinJSONToIPFS",
      data: JSON.stringify(data),
      headers: {
        'pinata_api_key': pinata_api_key,
        'pinata_secret_api_key': pinata_secret_api_key,
        "Content-Type": "application/json"
      }
    })
    return `ipfs://${res.data.IpfsHash}`;
  }

  {/*
    * Pinata بإستخدام IPFS تقوم الدالة اولا بتخزين الصورة المصغرة على
    * Livepeer ثم تقوم بطلب تحميل الفيديو على
  */}
  const uploadThumbnailAndVideo = async () => {
    setUploaded(true)

    const formData = new FormData()
    formData.append("file", thumbnail)
    
    const resFile = await axios({method: "post",
      url: "https://api.pinata.cloud/pinning/pinFileToIPFS",
      data: formData,
      headers: {
        'pinata_api_key': pinata_api_key,
        'pinata_secret_api_key': pinata_secret_api_key,
        "Content-Type": "multipart/form-data"
      },
    });
    setUrlImage(`ipfs://${resFile.data.IpfsHash}`)
    
    // لتحميل الفيديو useCreateAsset من الخطاف createAsset إستدعاء دالة
    await createAsset?.()
  };

  // تقوم الدالة بنشر المنشور على البروتوكول
  async function createPost() {
    // useCreatePost إستدعاء دالة النشر من الخطاف
    await create({
      // إعطاء قيمة مميزة للمنشور بحيث من المستحيل ان تتشابه مع منشور اخر
      metadata_id: uuid(),
      // تخزين وصف الفيديو بحيث يتم عرضه في المنشور
      content: description,
      // إعطاء اسم للمنشور
      name: `Video by ${publisher.handle}`,
      // تحديد نوع المنشور وهو فيديو
      contentFocus: ContentFocus.VIDEO,
      locale: 'ar',
      tags: ['business_&_entrepreneurs'],
      // إضافة الصورة المصغيرة للفيديو بحيث يتم عرضها للمنشور
      image: {
        url: urlImage,
        mimeType: thumbnail?.type=="image/jpeg" ? ImageType.JPEG : ImageType.PNG
      },
      // بحيث يتم عرضه في المنشور Livepeer إضافة الفيديو الذي تم تخزينه على
      media: [
        {
          url: assets[0]?.storage.ipfs.url,
          cover: urlImage,
          mimeType: VideoType.MP4,
        }
      ],
    });
    // قم بطباعة هذا النص عندما ينتهي من النشر على البروتوكول
    console.log("Published post!")
  }

  const renderButton = () => {
    // في حال لم يتم تحميل الفيديو بعد سيقوم بإظهار زر التحميل
    if (!uploaded) {
      return (
        <button
          onClick={() => {
            uploadThumbnailAndVideo()
          }}
          className="bg-blue-500 hover:bg-blue-700 text-white py-2 rounded-lg flex px-4 justify-between flex-row items-center"
        >
          <BiCloud />
          <p className="mr-2">Upload</p>
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
            <p className="mr-2">Waiting...</p>
          </button>
        )
      }
      // سيظهر للمستخدم زر نشر الفيديو Livepeer بمجرد ان يتم تحميل الفيديو على
      if (assets) {
        return (
          <button
            onClick={() => {
              createPost()
            }}
            className="bg-blue-500 hover:bg-blue-700 text-white py-2 rounded-lg flex px-4 justify-between flex-row items-center"
          >
            <BiCloud />
            <p className="mr-2">Post Video</p>
          </button>
        )
      }
    }
  }

  return (
    <div className='my-4 mx-8'>
      <Header />
      <div className="w-full h-screen flex flex-row -mt-8">
        <div className="flex-1 flex flex-col">
          <div className="mt-5 mr-10 flex justify-end">
            <div className="flex items-center mr-8">
              <a href="/" className="bg-transparent text-white py-2 px-6 border rounded-lg border-white mr-6">
                Back
              </a>
              {renderButton()}
            </div>
          </div>
          <div className="flex flex-col mt-5 lg:flex-row">
            <div className="flex lg:w-3/4 flex-col">
              <label className="text-white">Description</label>
              <textarea
                value={description}
                onChange={(e) => setDescription(e.target.value)}
                placeholder="Learning Web3 now is like buying Bitcoin in 2009 and investing in many cryptocurrencies to this day."
                className="w-[90%] bg-black text-white h-32 placeholder:text-gray-500  rounded-md mt-2 p-2 border border-[#444752] focus:outline-none"
              />
              <label className="text-white mt-10">Thumbnail</label>
              <input
                type="file"
                accept={"image/*"}
                onChange={(e) => setThumbnail(e.target.files[0])}
                className="w-[90%] text-white placeholder:text-gray-500 rounded-md mt-2 h-12 p-2 border border-[#444752] focus:outline-none"
              />
            </div>

            <div
              onClick={() => videoRef.current.click()}
              className={
                video
                  ? " w-96 rounded-md h-64 items-center justify-center flex"
                  : "border-2 border-gray-600  w-96 border-dashed rounded-md mt-8 h-64 items-center justify-center flex"
              }
            >
              {video ? (
                <video controls src={URL.createObjectURL(video)} className="h-full rounded-md" />
              ) : (
                <p className="text-[#c1c5ce]">Upload Video</p>
              )}
            </div>
          </div>
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
        </div>
      </div>
    </div>
  );
}
```

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/upload.png"/>

عند فتح صفحتك - <a href="http://localhost:3000/upload" target="_blank">localhost:3000/upload</a> - يجب ان تكون بهذا الشكل:

<img src="https://www.web3arabs.com/courses/lens/lens-tiktok/upload-page.png"/>

لقد حصلت على بداية جيدة والان بنفس الطريقة التي استخدمناها قم بإنشاء طريقة لوضع الإعجابات والتعليق على الفيديوهات - ستحصل على الكثير من الافكار من خلال <a href="https://docs.lens.xyz/docs/sdk-react-intro" target="_blank">وثائق بروتوكول Lens</a>.

يمكنك الوصول الى المشروع بشكل مباشر على <a href="https://github.com/Web3Arabs/lens-tiktok" target="_blank">GitHub من هنا</a>

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://t.me/Web3ArabsDAO" target="_blank">Telegram</a> او <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
