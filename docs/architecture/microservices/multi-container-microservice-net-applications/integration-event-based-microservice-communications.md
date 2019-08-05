---
title: マイクロサービス間でイベント ベースの通信を実装する (統合イベント)
description: コンテナー化された .NET アプリケーションの .NET マイクロサービス アーキテクチャ | マイクロサービス間でイベント ベースの通信を実装する統合イベントを理解する
ms.date: 10/02/2018
ms.openlocfilehash: 8a5cfa280063da742dc1693905fc44cf870c1fcc
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68676099"
---
# <a name="implementing-event-based-communication-between-microservices-integration-events"></a><span data-ttu-id="b5d39-103">マイクロサービス間でイベント ベースの通信を実装する (統合イベント)</span><span class="sxs-lookup"><span data-stu-id="b5d39-103">Implementing event-based communication between microservices (integration events)</span></span>

<span data-ttu-id="b5d39-104">前述のように、イベント ベースの通信を使う場合、ビジネス エンティティの更新などの重要なできごとが発生すると、マイクロサービスがイベントを発行します。</span><span class="sxs-lookup"><span data-stu-id="b5d39-104">As described earlier, when you use event-based communication, a microservice publishes an event when something notable happens, such as when it updates a business entity.</span></span> <span data-ttu-id="b5d39-105">その他のマイクロサービスは、これらのイベントをサブスクライブします。</span><span class="sxs-lookup"><span data-stu-id="b5d39-105">Other microservices subscribe to those events.</span></span> <span data-ttu-id="b5d39-106">マイクロサービスは、イベントを受信するときにそれ自体のビジネス エンティティを更新する場合があり、それによってさらにイベントが発行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-106">When a microservice receives an event, it can update its own business entities, which might lead to more events being published.</span></span> <span data-ttu-id="b5d39-107">これは、最終的な整合性の概念の本質です。</span><span class="sxs-lookup"><span data-stu-id="b5d39-107">This is the essence of the eventual consistency concept.</span></span> <span data-ttu-id="b5d39-108">この発行/サブスクライブ システムは、通常はイベント バスの実装を使って実行されます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-108">This publish/subscribe system is usually performed by using an implementation of an event bus.</span></span> <span data-ttu-id="b5d39-109">イベント バスは、イベントのサブスクライブおよびサブスクライブ解除とイベントの発行に必要な API を含むインターフェイスとして設計することができます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-109">The event bus can be designed as an interface with the API needed to subscribe and unsubscribe to events and to publish events.</span></span> <span data-ttu-id="b5d39-110">また、イベント バスは、任意のプロセス間通信またはメッセージング通信に基づく 1 つ以上の実装を使うことができます。たとえば、非同期通信と発行/サブスクライブ モデルをサポートするメッセージング キューまたはサービス バスです。</span><span class="sxs-lookup"><span data-stu-id="b5d39-110">It can also have one or more implementations based on any inter-process or messaging communication, such as a messaging queue or a service bus that supports asynchronous communication and a publish/subscribe model.</span></span>

<span data-ttu-id="b5d39-111">イベントを使って、複数のサービスにまたがるビジネス トランザクションを実装できます。これにより、サービス間の最終的な整合性が実現されます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-111">You can use events to implement business transactions that span multiple services, which gives you eventual consistency between those services.</span></span> <span data-ttu-id="b5d39-112">最終的に整合性のあるトランザクションは、一連の分散アクションで構成されます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-112">An eventually consistent transaction consists of a series of distributed actions.</span></span> <span data-ttu-id="b5d39-113">各アクションにおいて、マイクロサービスはビジネス エンティティを更新し、次のアクションをトリガーするイベントを発行します。</span><span class="sxs-lookup"><span data-stu-id="b5d39-113">At each action, the microservice updates a business entity and publishes an event that triggers the next action.</span></span>

![Basket および追加のマイクロサービスとの最終的な整合性を実現するために、イベント バス経由でイベント起動型の通信を使用する Catalog マイクロサービス。](./media/image19.png)

<span data-ttu-id="b5d39-115">**図 6-18**。</span><span class="sxs-lookup"><span data-stu-id="b5d39-115">**Figure 6-18**.</span></span> <span data-ttu-id="b5d39-116">イベント バスに基づくイベントドリブン通信</span><span class="sxs-lookup"><span data-stu-id="b5d39-116">Event-driven communication based on an event bus</span></span>

<span data-ttu-id="b5d39-117">このセクションでは、図 6-18 に示すように、ジェネリックなイベント バスのインターフェイスを使って、この種類の通信を .NET で実装する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5d39-117">This section describes how you can implement this type of communication with .NET by using a generic event bus interface, as shown in Figure 6-18.</span></span> <span data-ttu-id="b5d39-118">さまざまなテクノロジやインフラストラクチャ (RabbitMQ、Azure Service Bus、またはその他のサード パーティ製のオープン ソースまたは商用のサービス バスなど) を使った、複数の実装が可能です。</span><span class="sxs-lookup"><span data-stu-id="b5d39-118">There are multiple potential implementations, each using a different technology or infrastructure such as RabbitMQ, Azure Service Bus, or any other third-party open-source or commercial service bus.</span></span>

## <a name="using-message-brokers-and-services-buses-for-production-systems"></a><span data-ttu-id="b5d39-119">運用システムでのメッセージ ブローカーとサービス バスの使用</span><span class="sxs-lookup"><span data-stu-id="b5d39-119">Using message brokers and services buses for production systems</span></span>

<span data-ttu-id="b5d39-120">アーキテクチャのセクションで説明したように、抽象イベント バスを実装するためのメッセージング テクノロジには、複数の選択肢があります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-120">As noted in the architecture section, you can choose from multiple messaging technologies for implementing your abstract event bus.</span></span> <span data-ttu-id="b5d39-121">ただし、これらのテクノロジは動作するレベルが異なります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-121">But these technologies are at different levels.</span></span> <span data-ttu-id="b5d39-122">たとえば、メッセージング ブローカーのトランスポートである RabbitMQ は、Azure Service Bus、NServiceBus、MassTransit、Brighter などの商用の製品よりも低いレベルで動作します。</span><span class="sxs-lookup"><span data-stu-id="b5d39-122">For instance, RabbitMQ, a messaging broker transport, is at a lower level than commercial products like Azure Service Bus, NServiceBus, MassTransit, or Brighter.</span></span> <span data-ttu-id="b5d39-123">これらの製品のほとんどは、RabbitMQ または Azure Service Bus の上部で動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-123">Most of these products can work on top of either RabbitMQ or Azure Service Bus.</span></span> <span data-ttu-id="b5d39-124">製品の選択は、アプリケーションに必要な機能の数や、すぐに利用できるスケーラビリティの量によって異なります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-124">Your choice of product depends on how many features and how much out-of-the-box scalability you need for your application.</span></span>

<span data-ttu-id="b5d39-125">開発環境でイベント バスの概念実証を実装するだけの場合は、eShopOnContainers サンプルのように、コンテナーとして実行される RabbitMQ の上部の単純な実装で十分です。</span><span class="sxs-lookup"><span data-stu-id="b5d39-125">For implementing just an event bus proof-of-concept for your development environment, as in the eShopOnContainers sample, a simple implementation on top of RabbitMQ running as a container might be enough.</span></span> <span data-ttu-id="b5d39-126">ただし、高いスケーラビリティが求められるミッションクリティカルな運用システムでは、Azure Service Bus を評価して使うことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b5d39-126">But for mission-critical and production systems that need high scalability, you might want to evaluate and use Azure Service Bus.</span></span>

<span data-ttu-id="b5d39-127">分散開発を容易にする実行時間の長いプロセスのために、[Sagas](https://docs.particular.net/nservicebus/sagas/) のような高レベルの抽象化と豊富な機能が必要な場合は、NServiceBus、MassTransit、Brighter などのその他の商用およびオープン ソース サービス バスをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b5d39-127">If you require high-level abstractions and richer features like [Sagas](https://docs.particular.net/nservicebus/sagas/) for long-running processes that make distributed development easier, other commercial and open-source service buses like NServiceBus, MassTransit, and Brighter are worth evaluating.</span></span> <span data-ttu-id="b5d39-128">この場合は通常、独自の抽象化ではなく、これらの高レベルのサービス バスによって提供される抽象化と API を直接使います ([eShopOnContainers で提供される単純なイベント バスの抽象化](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/BuildingBlocks/EventBus/EventBus/Abstractions/IEventBus.cs)のように)。</span><span class="sxs-lookup"><span data-stu-id="b5d39-128">In this case, the abstractions and API to use would usually be directly the ones provided by those high-level service buses instead of your own abstractions (like the [simple event bus abstractions provided at eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/BuildingBlocks/EventBus/EventBus/Abstractions/IEventBus.cs)).</span></span> <span data-ttu-id="b5d39-129">さらに、[NServiceBus を使う eShopOnContainers の分岐](https://go.particular.net/eShopOnContainers) (Particular Software によって実装される追加の派生サンプル) を調査することもできます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-129">For that matter, you can research the [forked eShopOnContainers using NServiceBus](https://go.particular.net/eShopOnContainers) (additional derived sample implemented by Particular Software)</span></span>

<span data-ttu-id="b5d39-130">もちろん、常に RabbitMQ や Docker のような下位レベルのテクノロジの上に独自のサービス バス機能を構築することもできますが、"車輪の再発明" に必要な作業は、カスタム エンタープライズ アプリケーションにとってコストがかかりすぎる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-130">Of course, you could always build your own service bus features on top of lower-level technologies like RabbitMQ and Docker, but the work needed to “reinvent the wheel” might be too costly for a custom enterprise application.</span></span>

<span data-ttu-id="b5d39-131">繰り返しになりますが、eShopOnContainers サンプルで紹介されたサンプルのイベント バスの抽象化と実装は、概念実装としてのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-131">To reiterate: the sample event bus abstractions and implementation showcased in the eShopOnContainers sample are intended to be used only as a proof of concept.</span></span> <span data-ttu-id="b5d39-132">現在のセクションで説明されているように、非同期とイベント起動型の通信が必要であると判断したら、運用環境でのニーズに最適なサービス バス製品を選ぶ必要がります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-132">Once you have decided that you want to have asynchronous and event-driven communication, as explained in the current section, you should choose the service bus product that best fits your needs for production.</span></span>

## <a name="integration-events"></a><span data-ttu-id="b5d39-133">統合イベント</span><span class="sxs-lookup"><span data-stu-id="b5d39-133">Integration events</span></span>

<span data-ttu-id="b5d39-134">統合イベントは、ドメインを複数のマイクロサービスまたは外部システムにわたって同期するために使われます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-134">Integration events are used for bringing domain state in sync across multiple microservices or external systems.</span></span> <span data-ttu-id="b5d39-135">これは、統合イベントをマイクロサービスの外部に発行することによって実現されます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-135">This is done by publishing integration events outside the microservice.</span></span> <span data-ttu-id="b5d39-136">複数の受信側マイクロサービス (統合イベントをサブスクライブしているすべてのマイクロサービス) にイベントが発行されると、各受信側マイクロサービスの適切なイベント ハンドラーがイベントを処理します。</span><span class="sxs-lookup"><span data-stu-id="b5d39-136">When an event is published to multiple receiver microservices (to as many microservices as are subscribed to the integration event), the appropriate event handler in each receiver microservice handles the event.</span></span>

<span data-ttu-id="b5d39-137">統合イベントは、次の例のように、基本的にはデータを保持するクラスです。</span><span class="sxs-lookup"><span data-stu-id="b5d39-137">An integration event is basically a data-holding class, as in the following example:</span></span>

```csharp
public class ProductPriceChangedIntegrationEvent : IntegrationEvent
{
    public int ProductId { get; private set; }
    public decimal NewPrice { get; private set; }
    public decimal OldPrice { get; private set; }

    public ProductPriceChangedIntegrationEvent(int productId, decimal newPrice,
        decimal oldPrice)
    {
        ProductId = productId;
        NewPrice = newPrice;
        OldPrice = oldPrice;
    }
}
```

<span data-ttu-id="b5d39-138">統合イベントは各マイクロサービスのアプリケーション レベルで定義できるため、他のマイクロサービスから切り離されます。これは、ViewModels をサーバーやクライアントで定義する方法に相当します。</span><span class="sxs-lookup"><span data-stu-id="b5d39-138">The integration events can be defined at the application level of each microservice, so they are decoupled from other microservices, in a way comparable to how ViewModels are defined in the server and client.</span></span> <span data-ttu-id="b5d39-139">共通の統合イベント ライブラリを複数のマイクロサービス間で共有することはお勧めできません。これらのマイクロサービスを、単一のイベント定義データ ライブラリで結合することになるからです。</span><span class="sxs-lookup"><span data-stu-id="b5d39-139">What is not recommended is sharing a common integration events library across multiple microservices; doing that would be coupling those microservices with a single event definition data library.</span></span> <span data-ttu-id="b5d39-140">これを回避する理由は、共通のドメイン モデルを複数のマイクロサービス間で共有しない理由と同じです。つまり、マイクロサービスは完全に自律している必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-140">You do not want to do that for the same reasons that you do not want to share a common domain model across multiple microservices: microservices must be completely autonomous.</span></span>

<span data-ttu-id="b5d39-141">マイクロサービス間で共有する必要があるライブラリは数種類のみです。</span><span class="sxs-lookup"><span data-stu-id="b5d39-141">There are only a few kinds of libraries you should share across microservices.</span></span> <span data-ttu-id="b5d39-142">1 つは、eShopOnContainers の場合ように、[イベント バスのクライアント API](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/BuildingBlocks/EventBus) のような、最後のアプリケーション ブロックとなるライブラリです。</span><span class="sxs-lookup"><span data-stu-id="b5d39-142">One is libraries that are final application blocks, like the [Event Bus client API](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/BuildingBlocks/EventBus), as in eShopOnContainers.</span></span> <span data-ttu-id="b5d39-143">もう 1 つは、JSON シリアライザーのように、NuGet コンポーネントとしても共有できるツールを構成するライブラリです。</span><span class="sxs-lookup"><span data-stu-id="b5d39-143">Another is libraries that constitute tools that could also be shared as NuGet components, like JSON serializers.</span></span>

## <a name="the-event-bus"></a><span data-ttu-id="b5d39-144">イベント バス</span><span class="sxs-lookup"><span data-stu-id="b5d39-144">The event bus</span></span>

<span data-ttu-id="b5d39-145">イベント バスを使うと、図 6-19 に示すように、コンポーネントが互いを明示的に認識せずに、マイクロサービス間の発行/サブスクライブ スタイルの通信が可能になります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-145">An event bus allows publish/subscribe-style communication between microservices without requiring the components to explicitly be aware of each other, as shown in Figure 6-19.</span></span>

![基本的な pub/sub パターン、マイクロサービス A がイベント バスに発行し、これにより、発行者がサブスクライバーを知らなくても、マイクロサービス B と C をサブスクライブするように分散されます。](./media/image20.png)

<span data-ttu-id="b5d39-147">**図 6-19**。</span><span class="sxs-lookup"><span data-stu-id="b5d39-147">**Figure 6-19**.</span></span> <span data-ttu-id="b5d39-148">イベント バスでの発行/サブスクライブの基礎</span><span class="sxs-lookup"><span data-stu-id="b5d39-148">Publish/subscribe basics with an event bus</span></span>

<span data-ttu-id="b5d39-149">イベント バスは、オブザーバー パターンと発行-サブスクライブ パターンに関連しています。</span><span class="sxs-lookup"><span data-stu-id="b5d39-149">The event bus is related to the Observer pattern and the publish-subscribe pattern.</span></span>

### <a name="observer-pattern"></a><span data-ttu-id="b5d39-150">オブザーバー パターン</span><span class="sxs-lookup"><span data-stu-id="b5d39-150">Observer pattern</span></span>

<span data-ttu-id="b5d39-151">[オブザーバー パターン](https://en.wikipedia.org/wiki/Observer_pattern)では、プライマリ オブジェクト (オブザーバブルと呼ばれます) が登録されている他のオブジェクト (オブザーバーと呼ばれます) に関連情報 (イベント) を通知します。</span><span class="sxs-lookup"><span data-stu-id="b5d39-151">In the [Observer pattern](https://en.wikipedia.org/wiki/Observer_pattern), your primary object (known as the Observable) notifies other interested objects (known as Observers) with relevant information (events).</span></span>

### <a name="publishsubscribe-pubsub-pattern"></a><span data-ttu-id="b5d39-152">発行/サブスクライブ (Pub/Sub) パターン</span><span class="sxs-lookup"><span data-stu-id="b5d39-152">Publish/Subscribe (Pub/Sub) pattern</span></span>

<span data-ttu-id="b5d39-153">[発行/サブスクライブ パターン](https://docs.microsoft.com/previous-versions/msp-n-p/ff649664(v=pandp.10))の目的はオブザーバー パターンと同じです。特定のイベントが発生したことを他のサービスに通知する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-153">The purpose of the [Publish/Subscribe pattern](https://docs.microsoft.com/previous-versions/msp-n-p/ff649664(v=pandp.10)) is the same as the Observer pattern: you want to notify other services when certain events take place.</span></span> <span data-ttu-id="b5d39-154">ただし、オブザーバー パターンと Pub/Sub パターンの間には重要な違いがあります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-154">But there is an important difference between the Observer and Pub/Sub patterns.</span></span> <span data-ttu-id="b5d39-155">オブザーバー パターンでは、オブザーバブルからオブザーバーに直接ブロードキャストされるため、これらは互いを "認識" しています。</span><span class="sxs-lookup"><span data-stu-id="b5d39-155">In the observer pattern, the broadcast is performed directly from the observable to the observers, so they “know” each other.</span></span> <span data-ttu-id="b5d39-156">しかし、Pub/Sub パターンを使う場合は、ブローカー、メッセージ ブローカー、またはイベント バスと呼ばれる、発行者とサブスクライバーの両方から認識される第 3 のコンポーネントが存在します。</span><span class="sxs-lookup"><span data-stu-id="b5d39-156">But when using a Pub/Sub pattern, there is a third component, called broker or message broker or event bus, which is known by both the publisher and subscriber.</span></span> <span data-ttu-id="b5d39-157">そのため、Pub/Sub パターンを使うと、前述したイベント バス (またはメッセージ ブローカー) によって、発行者とサブスクライバーが厳密に切り離されます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-157">Therefore, when using the Pub/Sub pattern the publisher and the subscribers are precisely decoupled thanks to the mentioned event bus or message broker.</span></span>

### <a name="the-middleman-or-event-bus"></a><span data-ttu-id="b5d39-158">仲介者またはイベント バス</span><span class="sxs-lookup"><span data-stu-id="b5d39-158">The middleman or event bus</span></span>

<span data-ttu-id="b5d39-159">発行者とサブスクライバーの間の匿名性は、どのように実現すればよいのでしょうか。</span><span class="sxs-lookup"><span data-stu-id="b5d39-159">How do you achieve anonymity between publisher and subscriber?</span></span> <span data-ttu-id="b5d39-160">簡単な方法は、仲介者にすべての通信を処理させることです。</span><span class="sxs-lookup"><span data-stu-id="b5d39-160">An easy way is let a middleman take care of all the communication.</span></span> <span data-ttu-id="b5d39-161">イベント バスは、このような仲介者の 1 つです。</span><span class="sxs-lookup"><span data-stu-id="b5d39-161">An event bus is one such middleman.</span></span>

<span data-ttu-id="b5d39-162">イベント バスは通常 2 つの部分で構成されます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-162">An event bus is typically composed of two parts:</span></span>

- <span data-ttu-id="b5d39-163">抽象化またはインターフェイス。</span><span class="sxs-lookup"><span data-stu-id="b5d39-163">The abstraction or interface.</span></span>

- <span data-ttu-id="b5d39-164">1 つ以上の実装。</span><span class="sxs-lookup"><span data-stu-id="b5d39-164">One or more implementations.</span></span>

<span data-ttu-id="b5d39-165">図 6-19 では、イベント バスが、アプリケーションの観点からは、Pub/Sub のチャネルにすぎないことがわかります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-165">In Figure 6-19 you can see how, from an application point of view, the event bus is nothing more than a Pub/Sub channel.</span></span> <span data-ttu-id="b5d39-166">この非同期通信は、さまざまな方法で実装できます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-166">The way you implement this asynchronous communication can vary.</span></span> <span data-ttu-id="b5d39-167">環境の要件 (たとえば、運用環境と開発環境) に応じて実装を切り替えるために、複数の実装を使うことができます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-167">It can have multiple implementations so that you can swap between them, depending on the environment requirements (for example, production versus development environments).</span></span>

<span data-ttu-id="b5d39-168">図 6-20 では、インフラストラクチャ メッセージング テクノロジ (RabbitMQ、Azure Service Bus、またはその他のイベント/メッセージ ブローカーなど) に基づく複数の実装での、イベント バスの抽象化を確認できます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-168">In Figure 6-20 you can see an abstraction of an event bus with multiple implementations based on infrastructure messaging technologies like RabbitMQ, Azure Service Bus, or another event/message broker.</span></span>

![RabbitMQ Azure Service バスなどのいくつかのテクノロジで実装できるように、インターフェイスから定義されたイベント バスを用意することをお勧めします。](./media/image21.png)

<span data-ttu-id="b5d39-170">**図 6-20**。</span><span class="sxs-lookup"><span data-stu-id="b5d39-170">**Figure 6- 20.**</span></span> <span data-ttu-id="b5d39-171">イベント バスの複数の実装</span><span class="sxs-lookup"><span data-stu-id="b5d39-171">Multiple implementations of an event bus</span></span>

<span data-ttu-id="b5d39-172">ただし、前述したように、独自の抽象化 (イベントバスのインターフェイス) を使用することが適しているのは、その抽象化によってサポートされる基本的なイベント バスの機能が必要な場合のみです。</span><span class="sxs-lookup"><span data-stu-id="b5d39-172">However, and as mentioned previously, using your own abstractions (the event bus interface) is good only if you need basic event bus features supported by your abstractions.</span></span> <span data-ttu-id="b5d39-173">より豊富なサービス バスの機能が必要な場合は、独自の抽象化ではなく、好みの商用サービス バスによって提供される API と抽象化を使うことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b5d39-173">If you need richer service bus features, you should probably use the API and abstractions provided by your preferred commercial service bus instead of your own abstractions.</span></span>

### <a name="defining-an-event-bus-interface"></a><span data-ttu-id="b5d39-174">イベント バスのインターフェイスの定義</span><span class="sxs-lookup"><span data-stu-id="b5d39-174">Defining an event bus interface</span></span>

<span data-ttu-id="b5d39-175">イベント バスのインターフェイスの実装コードの一部と、探索のための実装例から始めましょう。</span><span class="sxs-lookup"><span data-stu-id="b5d39-175">Let’s start with some implementation code for the event bus interface and possible implementations for exploration purposes.</span></span> <span data-ttu-id="b5d39-176">以下のように、インターフェイスは汎用的で単純である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-176">The interface should be generic and straightforward, as in the following interface.</span></span>

```csharp
public interface IEventBus
{
    void Publish(IntegrationEvent @event);

    void Subscribe<T, TH>()
        where T : IntegrationEvent
        where TH : IIntegrationEventHandler<T>;

    void SubscribeDynamic<TH>(string eventName)
        where TH : IDynamicIntegrationEventHandler;

    void UnsubscribeDynamic<TH>(string eventName)
        where TH : IDynamicIntegrationEventHandler;

    void Unsubscribe<T, TH>()
        where TH : IIntegrationEventHandler<T>
        where T : IntegrationEvent;
}
```

<span data-ttu-id="b5d39-177">`Publish` メソッドは単純です。</span><span class="sxs-lookup"><span data-stu-id="b5d39-177">The `Publish` method is straightforward.</span></span> <span data-ttu-id="b5d39-178">イベント バスは、渡される統合イベントを、そのイベントをサブスクライブするすべてのマイクロサービス、または外部アプリケーションに、ブロードキャストします。</span><span class="sxs-lookup"><span data-stu-id="b5d39-178">The event bus will broadcast the integration event passed to it to any microservice, or even an external application, subscribed to that event.</span></span> <span data-ttu-id="b5d39-179">このメソッドは、イベントを発行するマイクロサービスによって使われます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-179">This method is used by the microservice that is publishing the event.</span></span>

<span data-ttu-id="b5d39-180">`Subscribe` メソッド (引数に応じていくつかの実装を使えます) は、イベントを受信するマイクロサービスによって使われます。</span><span class="sxs-lookup"><span data-stu-id="b5d39-180">The `Subscribe` methods (you can have several implementations depending on the arguments) are used by the microservices that want to receive events.</span></span> <span data-ttu-id="b5d39-181">このメソッドには、2 つの引数があります。</span><span class="sxs-lookup"><span data-stu-id="b5d39-181">This method has two arguments.</span></span> <span data-ttu-id="b5d39-182">1 つ目は、サブスクライブする統合イベントです (`IntegrationEvent`)。</span><span class="sxs-lookup"><span data-stu-id="b5d39-182">The first is the integration event to subscribe to (`IntegrationEvent`).</span></span> <span data-ttu-id="b5d39-183">2 番目の引数は、受信側マイクロサービスが統合イベントのメッセージを取得すると実行される、統合イベントのハンドラー (またはコールバック メソッド) です (`IIntegrationEventHandler<T>`)。</span><span class="sxs-lookup"><span data-stu-id="b5d39-183">The second argument is the integration event handler (or callback method), named `IIntegrationEventHandler<T>`, to be executed when the receiver microservice gets that integration event message.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="b5d39-184">[前へ](database-server-container.md)
> [次へ](rabbitmq-event-bus-development-test-environment.md)</span><span class="sxs-lookup"><span data-stu-id="b5d39-184">[Previous](database-server-container.md)
[Next](rabbitmq-event-bus-development-test-environment.md)</span></span>