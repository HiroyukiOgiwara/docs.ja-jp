---
title: マイクロサービスを基にしている複合 UI を作成する
description: マイクロサービス アーキテクチャは、バックエンド専用ではありません。 フロントエンドで使用してピーク ビューを取得します。
ms.date: 09/20/2018
ms.openlocfilehash: 55cb2a8096cc8122c94cae50af4384e9392868cf
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68672999"
---
# <a name="creating-composite-ui-based-on-microservices"></a><span data-ttu-id="c6ba8-104">マイクロサービスを基にしている複合 UI を作成する</span><span class="sxs-lookup"><span data-stu-id="c6ba8-104">Creating composite UI based on microservices</span></span>

<span data-ttu-id="c6ba8-105">マイクロサービス アーキテクチャは、多くの場合、データおよびロジックを処理するサーバー側から始まります。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-105">Microservices architecture often starts with the server-side handling data and logic.</span></span> <span data-ttu-id="c6ba8-106">ただし、より高度なアプローチとして、マイクロサービスに基づいたアプリケーション UI を設計する方法もあります。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-106">However, a more advanced approach is to design your application UI based on microservices as well.</span></span> <span data-ttu-id="c6ba8-107">これは、サーバー上にマイクロサービスが置かれモノリシック クライアント アプリのみでマイクロサービスが利用されるというのでなく、マイクロサービスによって複合 UI が生成されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-107">That means having a composite UI produced by the microservices, instead of having microservices on the server and just a monolithic client app consuming the microservices.</span></span> <span data-ttu-id="c6ba8-108">このアプローチの場合、ビルドするマイクロサービスは、ロジックおよび視覚的表現の両方を備えることができます。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-108">With this approach, the microservices you build can be complete with both logic and visual representation.</span></span>

<span data-ttu-id="c6ba8-109">図 4-20 に、モノリシック クライアント アプリケーションからマイクロサービスを利用するだけの簡単なアプローチを示します。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-109">Figure 4-20 shows the simpler approach of just consuming microservices from a monolithic client application.</span></span> <span data-ttu-id="c6ba8-110">当然ながら、HTML および JavaScript の生成の間に ASP.NET MVC サービスを含めることが可能です。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-110">Of course, you could have an ASP.NET MVC service in between producing the HTML and JavaScript.</span></span> <span data-ttu-id="c6ba8-111">図は簡単なものであり、マイクロサービスを利用している単一 (モノリシック) のクライアント UI が強調表示されています。またこれらのマイクロサービスでは、UI シェイプ (HTML および JavaScript) ではなく、ロジックとデータにのみ焦点を当てています。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-111">The figure is a simplification that highlights that you have a single (monolithic) client UI consuming the microservices, which just focus on logic and data and not on the UI shape (HTML and JavaScript).</span></span>

![個々のマイクロサービスに接続するモノリシック UI アプリケーション。](./media/image20.png)

<span data-ttu-id="c6ba8-113">**図 4-20**</span><span class="sxs-lookup"><span data-stu-id="c6ba8-113">**Figure 4-20**.</span></span> <span data-ttu-id="c6ba8-114">バックエンド マイクロサービスを利用するモノリシック UI アプリケーション</span><span class="sxs-lookup"><span data-stu-id="c6ba8-114">A monolithic UI application consuming back-end microservices</span></span>

<span data-ttu-id="c6ba8-115">これに対し、複合 UI は正確に生成され、マイクロサービス自体で構成されます。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-115">In contrast, a composite UI is precisely generated and composed by the microservices themselves.</span></span> <span data-ttu-id="c6ba8-116">一部のマイクロサービスでは、UI の特定の領域のビジュアル シェイプをドライブしています。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-116">Some of the microservices drive the visual shape of specific areas of the UI.</span></span> <span data-ttu-id="c6ba8-117">その場合の主な違いは、クライアント UI コンポーネント (TypeScript クラスなど) がテンプレートに基づいており、それらのテンプレートのデータ シェイプ UI ViewModel が各マイクロサービスから取得されるということです。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-117">The key difference is that you have client UI components (TypeScript classes, for example) based on templates, and the data-shaping-UI ViewModel for those templates comes from each microservice.</span></span>

<span data-ttu-id="c6ba8-118">クライアント アプリケーションの起動時、各クライアント UI コンポーネント (TypeScript クラスなど) は、特定のシナリオで ViewModels を提供できるインフラストラクチャ マイクロサービスに自らを登録します。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-118">At client application start-up time, each of the client UI components (TypeScript classes, for example) registers itself with an infrastructure microservice capable of providing ViewModels for a given scenario.</span></span> <span data-ttu-id="c6ba8-119">マイクロサービスがシェイプを変更すると、UI も変わります。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-119">If the microservice changes the shape, the UI changes also.</span></span>

<span data-ttu-id="c6ba8-120">図 4-21 に、この複合 UI アプローチのバージョンを示します。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-120">Figure 4-21 shows a version of this composite UI approach.</span></span> <span data-ttu-id="c6ba8-121">これは、異なる手法に基づく細分化された部分を集計する他のマイクロサービスが存在する可能性があるため、簡略化されています。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-121">This is simplified because you might have other microservices that are aggregating granular parts that are based on different techniques.</span></span> <span data-ttu-id="c6ba8-122">それは、従来の Web アプローチ (ASP.NET MVC) または SPA (シングル ページ アプリケーション) のどちらを構築しているかに依存します。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-122">It depends on whether you're building a traditional web approach (ASP.NET MVC) or an SPA (Single Page Application).</span></span>

![複合 UI アプリケーションでは、各 UI セクションは、UI コンポジション マイクロサービスによって生成され、ミニゲートウェイのように機能します。](./media/image21.png)

<span data-ttu-id="c6ba8-124">**図 4-21**</span><span class="sxs-lookup"><span data-stu-id="c6ba8-124">**Figure 4-21**.</span></span> <span data-ttu-id="c6ba8-125">バックエンド マイクロサービスによって成形された複合 UI アプリケーションの例</span><span class="sxs-lookup"><span data-stu-id="c6ba8-125">Example of a composite UI application shaped by back-end microservices</span></span>

<span data-ttu-id="c6ba8-126">これらの UI コンポジション マイクロサービスのそれぞれは、小規模な API ゲートウェイに似ています。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-126">Each of those UI composition microservices would be similar to a small API Gateway.</span></span> <span data-ttu-id="c6ba8-127">ただし、この場合、それぞれが小規模な UI 領域を担当します。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-127">But in this case each one is responsible for a small UI area.</span></span>

<span data-ttu-id="c6ba8-128">マイクロサービスによってドライブされる複合 UI アプローチは、使用する UI テクノロジに応じて、直面する困難の度合いが異なる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-128">A composite UI approach that's driven by microservices can be more challenging or less so, depending on what UI technologies you're using.</span></span> <span data-ttu-id="c6ba8-129">たとえば、SPA またはネイティブ モバイル アプリをビルドするために使用する技法 (Xamarin アプリを開発する場合などで、このアプローチの場合は難易度がより高くなるため) が従来の Web アプリケーションをビルドするために使用する技法と異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-129">For instance, you won't use the same techniques for building a traditional web application that you use for building an SPA or for native mobile app (as when developing Xamarin apps, which can be more challenging for this approach).</span></span>

<span data-ttu-id="c6ba8-130">[eShopOnContainers](https://aka.ms/MicroservicesArchitecture) サンプル アプリケーションでは複数の理由からモノリシック UI アプローチを使用します。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-130">The [eShopOnContainers](https://aka.ms/MicroservicesArchitecture) sample application uses the monolithic UI approach for multiple reasons.</span></span> <span data-ttu-id="c6ba8-131">まず、マイクロサービスとコンテナーの場合です。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-131">First, it's an introduction to microservices and containers.</span></span> <span data-ttu-id="c6ba8-132">複合 UI は高度な技法であり、UI の設計および開発するときには複雑な処理も求められます。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-132">A composite UI is more advanced but also requires further complexity when designing and developing the UI.</span></span> <span data-ttu-id="c6ba8-133">次に、eShopOnContainers でも Xamarin に基づいたネイティブ モバイル アプリが実現されます。この場合は、クライアント C\# 側での処理がより複雑になります。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-133">Second, eShopOnContainers also provides a native mobile app based on Xamarin, which would make it more complex on the client C\# side.</span></span>

<span data-ttu-id="c6ba8-134">次の参照情報を使用してマイクロサービスに基づく複合 UI に関する知識を深めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="c6ba8-134">However, we encourage you to use the following references to learn more about composite UI based on microservices.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c6ba8-135">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="c6ba8-135">Additional resources</span></span>

- <span data-ttu-id="c6ba8-136">**ASP.NET を使用した複合 UI (特定のワークショップ)**  </span><span class="sxs-lookup"><span data-stu-id="c6ba8-136">**Composite UI using ASP.NET (Particular's Workshop)** </span></span>\
  <https://github.com/Particular/Workshop/tree/master/demos/asp-net-core>

- <span data-ttu-id="c6ba8-137">**Ruben Oostinga。マイクロサービス アーキテクチャでのモノリシック フロントエンド** </span><span class="sxs-lookup"><span data-stu-id="c6ba8-137">**Ruben Oostinga. The Monolithic Frontend in the Microservices Architecture** </span></span>\
  <https://blog.xebia.com/the-monolithic-frontend-in-the-microservices-architecture/>

- <span data-ttu-id="c6ba8-138">**Mauro Servienti。優れた UI コンポジションの秘密** </span><span class="sxs-lookup"><span data-stu-id="c6ba8-138">**Mauro Servienti. The secret of better UI composition** </span></span>\
  <https://particular.net/blog/secret-of-better-ui-composition>

- <span data-ttu-id="c6ba8-139">**Viktor Farcic。フロントエンド Web コンポーネントをマイクロサービスに含める** </span><span class="sxs-lookup"><span data-stu-id="c6ba8-139">**Viktor Farcic. Including Front-End Web Components Into Microservices** </span></span>\
  <https://technologyconversations.com/2015/08/09/including-front-end-web-components-into-microservices/>

- <span data-ttu-id="c6ba8-140">**マイクロサービス アーキテクチャでのフロントエンドの管理** </span><span class="sxs-lookup"><span data-stu-id="c6ba8-140">**Managing Frontend in the Microservices Architecture** </span></span>\
  <https://allegro.tech/2016/03/Managing-Frontend-in-the-microservices-architecture.html>

>[!div class="step-by-step"]
><span data-ttu-id="c6ba8-141">[前へ](microservices-addressability-service-registry.md)
>[次へ](resilient-high-availability-microservices.md)</span><span class="sxs-lookup"><span data-stu-id="c6ba8-141">[Previous](microservices-addressability-service-registry.md)
[Next](resilient-high-availability-microservices.md)</span></span>