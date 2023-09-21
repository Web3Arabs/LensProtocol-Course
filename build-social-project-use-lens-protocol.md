# بناء تطبيق تواصل إجتماعي بإستخدام بروتوكول Lens

في هذا الدرس سنقوم ببناء اول تطبيق تواصل إجتماعي بسيط بمجموعة بسيطة من الاكواد بإستخدام إطار العمل Nextjs و Tailwind.

الهدف من هذا الدرس هو مساعدتك في فهم طريقة عمل هذا البروتوكول والتعامل مع <a href="https://docs.lens.xyz/docs" target="_blank">مرجع بروتوكل Lens</a> من اجل بناء مشروع كامل بإستخدام عقلك وربما تطلقه لجميع مستخدمين Lens وتحصل على تمويل من بروتوكل Lens في حال كان يستحق بواجهة جيدة مثل مشروع <a href="https://lenster.xyz" target="_blank">Lenster</a>.

## إعداد المشروع

ستقوم بإستدعاء مشروع Nextjs من خلال فتح terminal وإضافة هذا الأوامر

```bash
mkdir lens-app & cd lens-app
npx create-next-app@latest .
```

<img src="https://www.web3arabs.com/courses/lens/lens-app/create-lens-app.png"/>

ستقوم بتثبيت هذه المكاتب التي سنحتاجها اثناء التعامل مع بروتوكول Lens

```bash
npm install @lens-protocol/react-web @lens-protocol/wagmi wagmi@1.4.1 viem@1.10.9
```

قم بتشغيل المشروع من خلال إدخال هذا الامر

```bash
npm run dev
```

يمكنك الان الذهاب مباشرة الى - <a href="http://localhost:3000" target="_blank">localhost:3000</a> - حتى يقوم بفتح هذا

<img src="https://www.web3arabs.com/courses/lens/next-app.png"/>

بعد الإنتهاء من كل هذا قم بفتح المشروع على اي محرر اكواد مثل VS Code او غيره.

## بناء المشروع

ستذهب الى مجلد app وستقوم بفتح الملف **globals.css** وستبقي هذه الاوامر في الملف

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

<img src="https://www.web3arabs.com/courses/lens/lens-app/globals-css.png"/>

سنقوم الان بإعداد بروتوكول Lens في المشروع بحيث نستطيع التعامل مع مكونات البروتوكول بكل سهولة - ستذهب الى مجلد app وستقوم بإضافة هذه الأكواد في ملف **layout.tsx**

```tsx
// app/layout.tsx
"use client";
import "./globals.css";
import { polygonMumbai, polygon } from "wagmi/chains";
import { configureChains, createConfig, WagmiConfig } from "wagmi";
import { publicProvider } from "wagmi/providers/public";
import { InjectedConnector } from "wagmi/connectors/injected";
import { LensProvider, LensConfig, production } from "@lens-protocol/react-web";
import { bindings as wagmiBindings } from "@lens-protocol/wagmi";

// Polygon تهيئة الشبكة التي ستقوم المحفظة بالإتصال بها وهي شبكة
const { publicClient, webSocketPublicClient } = configureChains(
  [polygonMumbai, polygon],
  [publicProvider()]
);

const config = createConfig({
  autoConnect: true,
  publicClient,
  webSocketPublicClient,
  connectors: [
    new InjectedConnector({
      options: {
        shimDisconnect: false,
      },
    }),
  ],
});

// إعداد البروتوكول في المشروع
const lensConfig: LensConfig = {
  // يقوم بتوفير الربط والتوقيع مع الحساب
  bindings: wagmiBindings(),
  // تحديد البيئة التي سيتعامل معها المشروع
  // production وإذا كان حساب اساسي اجعلها development إذا كان على حساب تجريبي قم بإستخدام
  environment: production,
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <WagmiConfig config={config}>
        <LensProvider config={lensConfig}>
          <body>{children}</body>
        </LensProvider>
      </WagmiConfig>
    </html>
  );
}
```

<img src="https://www.web3arabs.com/courses/lens/lens-app/layout.png"/>

سنقوم الان في الصفحة الرئيسية بعرض المنشورات التي يقوم المستخدمين بنشرها على مشروعنا عن طريق إستخدام احد الخطافات وهو **useExplorePublications**.

قم بفتح الملف **page.tsx** في المجلد app وقم بإضافة هذا الكود - لقد قمنا بشرح الكود اعلى كل سطر

```tsx
// app/page.tsx
"use client";
import { PublicationSortCriteria, PublicationTypes, useExplorePublications } from '@lens-protocol/react-web'
import Link from 'next/link'

export default function Home() {
  // نقوم بإستدعاء المنشورات المتداولة في البروتوكول
  const { data: posts, loading } = useExplorePublications({
    // نقوم بتحديد النوع بحيث يظهر لنا المنشورات فقط وليس التعليقات
    publicationTypes: [PublicationTypes.Post],
    // نقوم بتحديد المنشورات التي تحتوي على تعليقات اكبر
    sortCriteria: PublicationSortCriteria.TopCommented,
    // نقوم بإستدعاء 25 من المنشورات
    limit: 25
  }) as any
  
  // قم بمراقبتها من هناك console طباعة المنشورات على
  console.log(posts)

  // إذا مازال يقوم بإستدعاء المنشورات فسيطلب من المستخدم ان ينتظر
  if (loading) return <p className='p-20 text-5xl'>Loading...</p>

  return (
    <div className='px-20 py-10 w-[70%]'>
      <h1 className='text-5xl'>Posts</h1>
      {posts?.map((post: any) => (
        <div className="flex flex-col my-6" key={post?.id}>
          <Link href={`/${post.profile.handle}`}>
            <div className="flex flex-row gap-2">
              <div className='flex justify-center items-center'>
              {
                post.profile.picture && post.profile.picture.__typename === 'MediaSet' ? (
                  <img src={post.profile.picture.original.url} width="50" height="50" className='rounded-full' />
                ) : <div className="w-[50px] h-[50px] bg-slate-500" />
              }
              </div>
              <div className="flex flex-col justify-center">
                <p className='text-md'>{post.profile.name}</p>
                <p className='text-sm'>{post.profile.handle}</p>
              </div>
            </div>
          </Link>
          <div className='flex flex-col my-4 px-5'>
            <Link href={`/post/${post.id}`}>
              <div className="text-lg">
                {post.metadata.content.split("\n").map((i: string, id: number) => (<p key={id}>{i}<br/></p>))}
              </div>
              <div>
                {post.metadata.image && post.metadata.media[0].__typename === 'MediaSet' ? (
                  <img src={post.metadata?.media[0]?.original.url} width="100%" height="100%" className='mt-2' />
                ) : null}
              </div>
            </Link>
          </div>
        </div>
      ))}
    </div>
  )
}
```

<img src="https://www.web3arabs.com/courses/lens/lens-app/home.png"/>

عند فتح صفحتك - <a href="http://localhost:3000" target="_blank">localhost:3000</a> - يجب ان تكون بهذا الشكل:

<img src="https://www.web3arabs.com/courses/lens/lens-app/posts-page.png"/>

ستلاحظ انه عند النقر على حساب مالك المنشور سيقوم بإرسالك الى صفحة ثانية. دعونا نقوم بعرض حساب المستخدم والمنشوارت التي قام بنشرها.

فقط سنقوم بإستخدام الخطاف **useProfile** لعرض بيانات الحساب والخطاف **usePublications** لعرض المنشورات التي قام بنشرها.

ستذهب الى المجلد app وستقوم بإنشاء مجلد بإسم **[id]** وفي هذا المجلد قم بإنشاء ملف بإسم **page.tsx** وقم بإضافة هذا الكود - لقد قمنا بشرح الكود اعلى كل سطر

```tsx
// app/[id]/page.tsx
"use client"
import { useProfile, profileId, usePublications, PublicationTypes } from '@lens-protocol/react-web'
import Link from 'next/link'

export default function page({ params }: any) {
  // الذي يقوم المستخدم بإضافته في الرابط id إستدعاء
  const { id } = params

  // web3arabs.lens الذي يقوم بطلبه المستخدم مثل id يقوم بإستدعاء الحساب المتعلق في
  const { data: profile, loading } = useProfile({ handle: id }) as any

  // إستدعاء المنشورات
  const { data: posts } = usePublications({ 
    // معين id إستدعاء منشورات من المستخدم الذي يحمل
    profileId: profileId(profile?.id),
    // نقوم بتحديد النوع بحيث يظهر لنا المنشورات فقط وليس التعليقات
    publicationTypes: [PublicationTypes.Post],
    // نقوم بإستدعاء 15 من المنشورات
    limit: 15
  }) as any

  // قم بمراقبتها من هناك console طباعة بيانات الحساب على
  console.log(profile)

  // إذا مازال يقوم بإستدعاء بيانات الحساب فسيطلب من المستخدم ان ينتظر
  if (loading) return <p className='p-20 text-5xl'>Loading...</p>

  return (
    <div className='px-20 py-10 w-[70%]'>
      <h1 className='text-5xl'>Profile</h1>
      <div className='my-12'>
        {
          profile?.picture && profile?.picture.__typename === 'MediaSet' ? (
            <img src={profile?.picture.original.url} width="120" height="120" />
          ) : <div className="w-[120px] h-[120px] bg-slate-500" />
        }
        <h3 className="text-3xl mt-4">{profile?.name}</h3>
        <h4 className="text-2xl mt-2">{profile?.handle}</h4>
        <h6 className="text-lg mt-2">
          {profile?.bio.split("\n").map((i: string, id: number) => <p key={id}>{i}</p>)}
        </h6>
        <div className="flex gap-5 my-4">
          <p className='text-md'>Following: {profile?.stats.totalFollowing}</p>
          <p className='text-md'>Followers: {profile?.stats.totalFollowers}</p>
        </div>
      </div>

      {posts?.map((post: any) => (
        <div className="flex flex-col my-6" key={post?.id}>
          <div className="flex flex-row gap-2">
            <div className='flex justify-center items-center'>
            {
              profile?.picture && profile?.picture.__typename === 'MediaSet' ? (
                <img src={profile?.picture.original.url} width="50" height="50" className='rounded-full' />
              ) : <div className="w-[50px] h-[50px] bg-slate-500" />
            }
            </div>
            <div className="flex flex-col justify-center">
              <p className='text-md'>{profile?.name}</p>
              <p className='text-sm'>{profile?.handle}</p>
            </div>
          </div>
          <div className='flex flex-col my-4 px-5'>
            <Link href={`/post/${post.id}`}>
              <div className="text-lg">
                {post.metadata.content.split("\n").map((i: string, id: number) => (<p key={id}>{i}<br/></p>))}
              </div>
              <div>
                {post.metadata.image && post.metadata.media[0].__typename === 'MediaSet' ? (
                  <img src={post.metadata?.media[0]?.original.url} width="100%" height="100%" className='mt-2' />
                ) : null}
              </div>
            </Link>
          </div>
        </div>
      ))}
    </div>
  )
}
```

<img src="https://www.web3arabs.com/courses/lens/lens-app/lens.png"/>

عند فتح حساب Web3Arabs على المشروع - <a href="http://localhost:3000/web3arabs.lens" target="_blank">localhost:3000/web3arabs.lens</a> - يجب ان تكون بهذا الشكل:

<img src="https://www.web3arabs.com/courses/lens/lens-app/profile-page.png"/>

لقد حصلت على بداية جيدة والان بنفس الطريقة التي استخدمناها قم بإنشاء صفحة لعرض المنشور اثناء النقر عليه وإظهار جميع البيانات.

ستقوم بإستخدام خطاف **usePublication** بكل بساطة كما قمنا سابقاً. إذهب الى <a href="https://docs.lens.xyz/docs/use-publication" target="_blank">الوثائق من هنا</a> وقم بدمج الافكار.

يمكنك الوصول الى المشروع بشكل مباشر على <a href="https://github.com/Web3Arabs/lens-app" target="_blank">GitHub من هنا</a>

كما هو الحال دائمًا، إذا كانت لديك أي أسئلة أو شعرت بالتعثر أو أردت فقط أن تقول مرحبًا، فقم بالإنضمام على <a href="https://t.me/Web3ArabsDAO" target="_blank">Telegram</a> او <a href="https://discord.gg/ykgUvqMc4Q" target="_blank">Discord</a> وسنكون أكثر من سعداء لمساعدتك!
