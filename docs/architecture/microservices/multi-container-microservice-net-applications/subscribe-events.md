---
title: イベントへのサブスクライブ
description: コンテナー化された .NET アプリケーションの .NET マイクロサービス アーキテクチャ | 統合イベントの発行とサブスクライブについて。
ms.date: 10/02/2018
ms.openlocfilehash: c0eaacce51b186191431bf827bb84d3a2d2b7b1f
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68675869"
---
# <a name="subscribing-to-events"></a><span data-ttu-id="af385-103">イベントへのサブスクライブ</span><span class="sxs-lookup"><span data-stu-id="af385-103">Subscribing to events</span></span>

<span data-ttu-id="af385-104">イベント バスを使用するための最初のステップは、受信したいイベントにマイクロサービスをサブスクライブすることです。</span><span class="sxs-lookup"><span data-stu-id="af385-104">The first step for using the event bus is to subscribe the microservices to the events they want to receive.</span></span> <span data-ttu-id="af385-105">これは、受信側マイクロサービスで行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-105">That should be done in the receiver microservices.</span></span>

<span data-ttu-id="af385-106">次の単純なコードは、各受信側マイクロサービスが (`Startup` クラスで) サービスを起動して必要なイベントにサブスクライブするために実装する必要があるコードを示しています。</span><span class="sxs-lookup"><span data-stu-id="af385-106">The following simple code shows what each receiver microservice needs to implement when starting the service (that is, in the `Startup` class) so it subscribes to the events it needs.</span></span> <span data-ttu-id="af385-107">このケースでは、`basket.api` マイクロサービスが `ProductPriceChangedIntegrationEvent` メッセージと `OrderStartedIntegrationEvent` メッセージにサブスクライブする必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-107">In this case, the `basket.api` microservice needs to subscribe to `ProductPriceChangedIntegrationEvent` and the `OrderStartedIntegrationEvent` messages.</span></span>

<span data-ttu-id="af385-108">たとえば、`ProductPriceChangedIntegrationEvent` イベントにサブスクライブした場合、買い物かごマイクロサービスは商品価格に加えられた変更を認識し、その商品がユーザーの買い物かごに入っている場合は変更に関する警告をユーザーに表示できるようになります。</span><span class="sxs-lookup"><span data-stu-id="af385-108">For instance, when subscribing to the `ProductPriceChangedIntegrationEvent` event, that makes the basket microservice aware of any changes to the product price and lets it warn the user about the change if that product is in the user’s basket.</span></span>

```csharp
var eventBus = app.ApplicationServices.GetRequiredService<IEventBus>();

eventBus.Subscribe<ProductPriceChangedIntegrationEvent,
                   ProductPriceChangedIntegrationEventHandler>();

eventBus.Subscribe<OrderStartedIntegrationEvent,
                   OrderStartedIntegrationEventHandler>();

```

<span data-ttu-id="af385-109">このコードの実行後、サブスクライバー マイクロサービスは、RabbitMQ チャネルを介してリッスンするようになります。</span><span class="sxs-lookup"><span data-stu-id="af385-109">After this code runs, the subscriber microservice will be listening through RabbitMQ channels.</span></span> <span data-ttu-id="af385-110">ProductPriceChangedIntegrationEvent 型のメッセージが到着すると、コードは渡されたイベント ハンドラーを起動し、イベントを処理します。</span><span class="sxs-lookup"><span data-stu-id="af385-110">When any message of type ProductPriceChangedIntegrationEvent arrives, the code invokes the event handler that is passed to it and processes the event.</span></span>

## <a name="publishing-events-through-the-event-bus"></a><span data-ttu-id="af385-111">イベント バスを介したイベントの発行</span><span class="sxs-lookup"><span data-stu-id="af385-111">Publishing events through the event bus</span></span>

<span data-ttu-id="af385-112">最後に、メッセージ送信元 (送信元マイクロサービス) が、次の例のようなコードで統合イベントを発行します</span><span class="sxs-lookup"><span data-stu-id="af385-112">Finally, the message sender (origin microservice) publishes the integration events with code similar to the following example.</span></span> <span data-ttu-id="af385-113">(これは原子性を考慮していない単純化された例です)。イベントを複数のマイクロサービスにわたって伝達する必要がある場合は、同様のコードを、通常は送信元マイクロサービスからデータまたはトランザクションがコミットされた直後に実装します。</span><span class="sxs-lookup"><span data-stu-id="af385-113">(This is a simplified example that does not take atomicity into account.) You would implement similar code whenever an event must be propagated across multiple microservices, usually right after committing data or transactions from the origin microservice.</span></span>

<span data-ttu-id="af385-114">まず、次のコードに示すように、(RabbitMQ またはサービス バスに基づく) イベント バス実装オブジェクトをコントローラー コンストラクターで挿入します。</span><span class="sxs-lookup"><span data-stu-id="af385-114">First, the event bus implementation object (based on RabbitMQ or based on a service bus) would be injected at the controller constructor, as in the following code:</span></span>

```csharp
[Route("api/v1/[controller]")]
public class CatalogController : ControllerBase
{
    private readonly CatalogContext _context;
    private readonly IOptionsSnapshot<Settings> _settings;
    private readonly IEventBus _eventBus;

    public CatalogController(CatalogContext context,
        IOptionsSnapshot<Settings> settings,
        IEventBus eventBus)
    {
        _context = context;
        _settings = settings;
        _eventBus = eventBus;
    }
    // ...
}
```

<span data-ttu-id="af385-115">次に、コントローラーのメソッド (UpdateProduct メソッドなど) でそれを使用します。</span><span class="sxs-lookup"><span data-stu-id="af385-115">Then you use it from your controller’s methods, like in the UpdateProduct method:</span></span>

```csharp
[Route("items")]
[HttpPost]
public async Task<IActionResult> UpdateProduct([FromBody]CatalogItem product)
{
    var item = await _context.CatalogItems.SingleOrDefaultAsync(
        i => i.Id == product.Id);
    // ...
    if (item.Price != product.Price)
    {
        var oldPrice = item.Price;
        item.Price = product.Price;
        _context.CatalogItems.Update(item);
        var @event = new ProductPriceChangedIntegrationEvent(item.Id,
            item.Price,
            oldPrice);
        // Commit changes in original transaction
        await _context.SaveChangesAsync();
        // Publish integration event to the event bus
        // (RabbitMQ or a service bus underneath)
        _eventBus.Publish(@event);
        // ...
    }
    // ...
}
```

<span data-ttu-id="af385-116">このケースでは、送信元マイクロサービスが単純な CRUD マイクロサービスであるため、そのコードを Web API コントローラー内に直接配置しています。</span><span class="sxs-lookup"><span data-stu-id="af385-116">In this case, since the origin microservice is a simple CRUD microservice, that code is placed right into a Web API controller.</span></span>

<span data-ttu-id="af385-117">CQRS アプローチを使用する場合など、より高度なマイクロサービスでは、`CommandHandler` クラスの `Handle()` メソッド内に実装することもできます。</span><span class="sxs-lookup"><span data-stu-id="af385-117">In more advanced microservices, like when using CQRS approaches, it can be implemented in the `CommandHandler` class, within the `Handle()` method.</span></span>

### <a name="designing-atomicity-and-resiliency-when-publishing-to-the-event-bus"></a><span data-ttu-id="af385-118">イベント バスに発行するときの原子性と回復性の設計</span><span class="sxs-lookup"><span data-stu-id="af385-118">Designing atomicity and resiliency when publishing to the event bus</span></span>

<span data-ttu-id="af385-119">イベント バスのような分散メッセージング システムを通じて統合イベントを発行するときは、元のデータベースの更新とイベントの発行のアトミックな実行という問題が生じます (つまり、両方の操作が完了するか、いずれも完了しない)。</span><span class="sxs-lookup"><span data-stu-id="af385-119">When you publish integration events through a distributed messaging system like your event bus, you have the problem of atomically updating the original database and publishing an event (that is, either both operations complete or none of them).</span></span> <span data-ttu-id="af385-120">たとえば、先ほどの単純化された例では、コードは商品価格が変更されたときにデータベースにデータをコミットし、その後に ProductPriceChangedIntegrationEvent メッセージを発行します。</span><span class="sxs-lookup"><span data-stu-id="af385-120">For instance, in the simplified example shown earlier, the code commits data to the database when the product price is changed and then publishes a ProductPriceChangedIntegrationEvent message.</span></span> <span data-ttu-id="af385-121">一見すると、これらの 2 つの操作がアトミックに実行されることは不可欠のように思われます。</span><span class="sxs-lookup"><span data-stu-id="af385-121">Initially, it might look essential that these two operations be performed atomically.</span></span> <span data-ttu-id="af385-122">しかし、[Microsoft Message Queuing (MSMQ)](https://msdn.microsoft.com/library/windows/desktop/ms711472(v=vs.85).aspx) などの以前のシステムを使用している場合と同じく、データベースとメッセージ ブローカーが関与する分散トランザクションを使用している場合には、これは [CAP 定理](https://www.quora.com/What-Is-CAP-Theorem-1)で説明されている理由によって推奨されません。</span><span class="sxs-lookup"><span data-stu-id="af385-122">However, if you are using a distributed transaction involving the database and the message broker, as you do in older systems like [Microsoft Message Queuing (MSMQ)](https://msdn.microsoft.com/library/windows/desktop/ms711472(v=vs.85).aspx), this is not recommended for the reasons described by the [CAP theorem](https://www.quora.com/What-Is-CAP-Theorem-1).</span></span>

<span data-ttu-id="af385-123">基本的に、マイクロサービスは、スケーラブルで可用性の高いシステムを構築するために使用します。</span><span class="sxs-lookup"><span data-stu-id="af385-123">Basically, you use microservices to build scalable and highly available systems.</span></span> <span data-ttu-id="af385-124">簡単に言えば、CAP 定理は、継続的な可用性、強い一貫性、*かつ*分断への耐性を備えた (分散) データベース (またはそのモデルを持つマイクロサービス) を構築することは不可能であると述べています。</span><span class="sxs-lookup"><span data-stu-id="af385-124">Simplifying somewhat, the CAP theorem says that you cannot build a (distributed) database (or a microservice that owns its model) that is continually available, strongly consistent, *and* tolerant to any partition.</span></span> <span data-ttu-id="af385-125">これらの 3 つの特性のうち、2 つを選択しなければなりません。</span><span class="sxs-lookup"><span data-stu-id="af385-125">You must choose two of these three properties.</span></span>

<span data-ttu-id="af385-126">マイクロサービス ベースのアーキテクチャでは、可用性と耐性を選択して、厳密な一貫性は重視しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-126">In microservices-based architectures, you should choose availability and tolerance, and you should deemphasize strong consistency.</span></span> <span data-ttu-id="af385-127">したがって、最近のほとんどのマイクロサービス ベースのアプリケーションでは、[MSMQ](https://msdn.microsoft.com/library/windows/desktop/ms711472(v=vs.85).aspx) を使用して Windows の分散トランザクション コーディネーター (DTC) に基づく[分散トランザクション](https://docs.microsoft.com/previous-versions/windows/desktop/ms681205(v=vs.85))を実装する場合と同じく、メッセージングに分散トランザクションを使用しないのが普通です。</span><span class="sxs-lookup"><span data-stu-id="af385-127">Therefore, in most modern microservice-based applications, you usually do not want to use distributed transactions in messaging, as you do when you implement [distributed transactions](https://docs.microsoft.com/previous-versions/windows/desktop/ms681205(v=vs.85)) based on the Windows Distributed Transaction Coordinator (DTC) with [MSMQ](https://msdn.microsoft.com/library/windows/desktop/ms711472(v=vs.85).aspx).</span></span>

<span data-ttu-id="af385-128">最初の問題とその例に話を戻しましょう。</span><span class="sxs-lookup"><span data-stu-id="af385-128">Let’s go back to the initial issue and its example.</span></span> <span data-ttu-id="af385-129">データベースが更新された後 (このケースでは、\_context.SaveChangesAsync() を含むコード行の直後) で、統合イベントが発行される前にサービスがクラッシュした場合、システム全体の一貫性が失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="af385-129">If the service crashes after the database is updated (in this case, right after the line of code with \_context.SaveChangesAsync()), but before the integration event is published, the overall system could become inconsistent.</span></span> <span data-ttu-id="af385-130">これは、処理しているビジネス操作によっては、ビジネスに重大な問題を生じさせるおそれがあります。</span><span class="sxs-lookup"><span data-stu-id="af385-130">This might be business critical, depending on the specific business operation you are dealing with.</span></span>

<span data-ttu-id="af385-131">前のアーキテクチャのセクションで説明したように、この問題に対処するためのアプローチはいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="af385-131">As mentioned earlier in the architecture section, you can have several approaches for dealing with this issue:</span></span>

- <span data-ttu-id="af385-132">完全な[イベント ソーシング パターン](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)を使用する。</span><span class="sxs-lookup"><span data-stu-id="af385-132">Using the full [Event Sourcing pattern](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).</span></span>

- <span data-ttu-id="af385-133">[トランザクション ログ マイニング](https://www.scoop.it/t/sql-server-transaction-log-mining)を使用する。</span><span class="sxs-lookup"><span data-stu-id="af385-133">Using [transaction log mining](https://www.scoop.it/t/sql-server-transaction-log-mining).</span></span>

- <span data-ttu-id="af385-134">[送信トレイ パターン](http://gistlabs.com/2014/05/the-outbox/)を使用する。</span><span class="sxs-lookup"><span data-stu-id="af385-134">Using the [Outbox pattern](http://gistlabs.com/2014/05/the-outbox/).</span></span> <span data-ttu-id="af385-135">これは、(ローカル トランザクションを拡張して) 統合イベントを格納するトランザクション テーブルです。</span><span class="sxs-lookup"><span data-stu-id="af385-135">This is a transactional table to store the integration events (extending the local transaction).</span></span>

<span data-ttu-id="af385-136">このシナリオでは、完全なイベント ソーシング (ES) パターンを使用することが、*最良*とは言わないまでも適切なアプローチの 1 つです。</span><span class="sxs-lookup"><span data-stu-id="af385-136">For this scenario, using the full Event Sourcing (ES) pattern is one of the best approaches, if not *the* best.</span></span> <span data-ttu-id="af385-137">ただし、多くのアプリケーション シナリオでは、完全な ES システムを実装できない場合があります。</span><span class="sxs-lookup"><span data-stu-id="af385-137">However, in many application scenarios, you might not be able to implement a full ES system.</span></span> <span data-ttu-id="af385-138">ES とは、現在の状態データを格納するのではなく、ドメイン イベントのみをトランザクション データベースに格納することを意味します。</span><span class="sxs-lookup"><span data-stu-id="af385-138">ES means storing only domain events in your transactional database, instead of storing current state data.</span></span> <span data-ttu-id="af385-139">ドメイン イベントのみを格納する方法には、システムの履歴を保持できる、過去の任意の時点におけるシステムの状態を確認できるなどの大きなメリットがあります。</span><span class="sxs-lookup"><span data-stu-id="af385-139">Storing only domain events can have great benefits, such as having the history of your system available and being able to determine the state of your system at any moment in the past.</span></span> <span data-ttu-id="af385-140">しかし、完全な ES システムを実装するにはシステムの大部分を再構築する必要があり、それ以外にも多くの複雑さと要件が生じます。</span><span class="sxs-lookup"><span data-stu-id="af385-140">However, implementing a full ES system requires you to rearchitect most of your system and introduces many other complexities and requirements.</span></span> <span data-ttu-id="af385-141">たとえば、イベント ソーシング用に特別に作成されたデータベース ([イベント ストア](https://eventstore.org/)など) や、Azure Cosmos DB、MongoDB、Cassandra、CouchDB、RavenDB といったドキュメント指向のデータベースの使用が必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="af385-141">For example, you would want to use a database specifically made for event sourcing, such as [Event Store](https://eventstore.org/), or a document-oriented database such as Azure Cosmos DB, MongoDB, Cassandra, CouchDB, or RavenDB.</span></span> <span data-ttu-id="af385-142">ES はこの問題に対する優れたアプローチですが、イベント ソーシングに精通している人以外には最も簡単なソリューションではありません。</span><span class="sxs-lookup"><span data-stu-id="af385-142">ES is a great approach for this problem, but not the easiest solution unless you are already familiar with event sourcing.</span></span>

<span data-ttu-id="af385-143">トランザクション ログ マイニングを使用するという選択肢は、一見すると透明性の高い方法のように思われます。</span><span class="sxs-lookup"><span data-stu-id="af385-143">The option to use transaction log mining initially looks very transparent.</span></span> <span data-ttu-id="af385-144">しかし、このアプローチを使用するには、マイクロサービスを SQL Server トランザクション ログなどの RDBMS トランザクション ログと結合する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-144">However, to use this approach, the microservice has to be coupled to your RDBMS transaction log, such as the SQL Server transaction log.</span></span> <span data-ttu-id="af385-145">これはおそらく望ましくないでしょう。</span><span class="sxs-lookup"><span data-stu-id="af385-145">This is probably not desirable.</span></span> <span data-ttu-id="af385-146">もう 1 つの欠点は、トランザクション ログに記録された低レベルの更新が、高レベルの統合イベントと同じレベルではない可能性があるという点です。</span><span class="sxs-lookup"><span data-stu-id="af385-146">Another drawback is that the low-level updates recorded in the transaction log might not be at the same level as your high-level integration events.</span></span> <span data-ttu-id="af385-147">その場合、それらのトランザクション ログ操作をリバース エンジニアリングするプロセスが難しくなるおそれがあります。</span><span class="sxs-lookup"><span data-stu-id="af385-147">If so, the process of reverse-engineering those transaction log operations can be difficult.</span></span>

<span data-ttu-id="af385-148">バランスの取れたアプローチは、トランザクション データベース テーブルと単純化された ES パターンを組み合わせることです。</span><span class="sxs-lookup"><span data-stu-id="af385-148">A balanced approach is a mix of a transactional database table and a simplified ES pattern.</span></span> <span data-ttu-id="af385-149">“イベント発行準備完了” のような状態を使用できます。これは、元のイベントを統合イベント テーブルにコミットするときに、元のイベントで設定します。</span><span class="sxs-lookup"><span data-stu-id="af385-149">You can use a state such as “ready to publish the event,” which you set in the original event when you commit it to the integration events table.</span></span> <span data-ttu-id="af385-150">その後、イベント バスへのイベントの発行を試みます。</span><span class="sxs-lookup"><span data-stu-id="af385-150">You then try to publish the event to the event bus.</span></span> <span data-ttu-id="af385-151">イベント発行アクションが成功した場合は、発行元のサービスで別のトランザクションを開始し、状態を “イベント発行準備完了” から “イベント発行済み” に移行します。</span><span class="sxs-lookup"><span data-stu-id="af385-151">If the publish-event action succeeds, you start another transaction in the origin service and move the state from “ready to publish the event” to “event already published.”</span></span>

<span data-ttu-id="af385-152">イベント バス内でイベント発行アクションが失敗した場合、発行元のマイクロサービス内ではデータの一貫性がまだ維持されており (“イベント発行準備完了” とマークされたままの状態)、サービスの残りの部分に関しても一貫性が維持されます。</span><span class="sxs-lookup"><span data-stu-id="af385-152">If the publish-event action in the event bus fails, the data still will not be inconsistent within the origin microservice—it is still marked as “ready to publish the event,” and with respect to the rest of the services, it will eventually be consistent.</span></span> <span data-ttu-id="af385-153">トランザクションや統合イベントの状態は、バックグラウンド ジョブによって常に確認できます。</span><span class="sxs-lookup"><span data-stu-id="af385-153">You can always have background jobs checking the state of the transactions or integration events.</span></span> <span data-ttu-id="af385-154">ジョブが “イベント発行準備完了” 状態のイベントを検出した場合、イベント バスに対するそのイベントの再発行を試みることができます。</span><span class="sxs-lookup"><span data-stu-id="af385-154">If the job finds an event in the “ready to publish the event” state, it can try to republish that event to the event bus.</span></span>

<span data-ttu-id="af385-155">このアプローチでは、各発行元マイクロサービスの統合イベントと、他のマイクロサービスまたは外部システムに伝達したいイベントのみを保持するという点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="af385-155">Notice that with this approach, you are persisting only the integration events for each origin microservice, and only the events that you want to communicate to other microservices or external systems.</span></span> <span data-ttu-id="af385-156">これに対して、完全な ES システムでは、すべてのドメイン イベントも格納します。</span><span class="sxs-lookup"><span data-stu-id="af385-156">In contrast, in a full ES system, you store all domain events as well.</span></span>

<span data-ttu-id="af385-157">したがって、このバランスの取れたアプローチは、単純化された ES システムということになります。</span><span class="sxs-lookup"><span data-stu-id="af385-157">Therefore, this balanced approach is a simplified ES system.</span></span> <span data-ttu-id="af385-158">統合イベントとその現在の状態 (“発行準備完了” または “発行済み”) の一覧が必要です。</span><span class="sxs-lookup"><span data-stu-id="af385-158">You need a list of integration events with their current state (“ready to publish” versus “published”).</span></span> <span data-ttu-id="af385-159">しかし、必要なのは、統合イベントのこれらの状態を実装することだけです。</span><span class="sxs-lookup"><span data-stu-id="af385-159">But you only need to implement these states for the integration events.</span></span> <span data-ttu-id="af385-160">また、このアプローチでは、完全な ES システムの場合のように、すべてのドメイン データをイベントとしてトランザクション データベースに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="af385-160">And in this approach, you do not need to store all your domain data as events in the transactional database, as you would in a full ES system.</span></span>

<span data-ttu-id="af385-161">既にリレーショナル データベースを使用している場合は、トランザクション テーブルを使用して統合イベントを格納できます。</span><span class="sxs-lookup"><span data-stu-id="af385-161">If you are already using a relational database, you can use a transactional table to store integration events.</span></span> <span data-ttu-id="af385-162">アプリケーションで原子性を実現するには、ローカル トランザクションに基づく 2 ステップのプロセスを使用します。</span><span class="sxs-lookup"><span data-stu-id="af385-162">To achieve atomicity in your application, you use a two-step process based on local transactions.</span></span> <span data-ttu-id="af385-163">基本的には、ドメイン エンティティを保持しているのと同じデータベースで IntegrationEvent テーブルを保持します。</span><span class="sxs-lookup"><span data-stu-id="af385-163">Basically, you have an IntegrationEvent table in the same database where you have your domain entities.</span></span> <span data-ttu-id="af385-164">そのテーブルが原子性を実現するための保証として機能して、保持された統合イベントを、ドメイン データをコミットしているのと同じトランザクションに格納できるようにします。</span><span class="sxs-lookup"><span data-stu-id="af385-164">That table works as an insurance for achieving atomicity so that you include persisted integration events into the same transactions that are committing your domain data.</span></span>

<span data-ttu-id="af385-165">次のように、このプロセスは段階的に進みます。</span><span class="sxs-lookup"><span data-stu-id="af385-165">Step by step, the process goes like this:</span></span>

1. <span data-ttu-id="af385-166">アプリケーションによってローカル データベース トランザクションが開始されます。</span><span class="sxs-lookup"><span data-stu-id="af385-166">The application begins a local database transaction.</span></span>

2. <span data-ttu-id="af385-167">次に、ドメイン エンティティの状態を更新し、統合イベント テーブルにイベントを挿入します。</span><span class="sxs-lookup"><span data-stu-id="af385-167">It then updates the state of your domain entities and inserts an event into the integration event table.</span></span>

3. <span data-ttu-id="af385-168">最後に、トランザクションがコミットされます。これで目的の原子性が与えられます。次に、</span><span class="sxs-lookup"><span data-stu-id="af385-168">Finally, it commits the transaction, so you get the desired atomicity and then</span></span>

4. <span data-ttu-id="af385-169">何らかの方法でイベントを発行します (次へ)。</span><span class="sxs-lookup"><span data-stu-id="af385-169">You publish the event somehow (next).</span></span>

<span data-ttu-id="af385-170">イベント発行のステップを実装するときは、次の選択肢があります。</span><span class="sxs-lookup"><span data-stu-id="af385-170">When implementing the steps of publishing the events, you have these choices:</span></span>

- <span data-ttu-id="af385-171">トランザクションをコミットした直後に統合イベントを発行し、別のローカル トランザクションを使用して、テーブル内のイベントを発行中とマークする。</span><span class="sxs-lookup"><span data-stu-id="af385-171">Publish the integration event right after committing the transaction and use another local transaction to mark the events in the table as being published.</span></span> <span data-ttu-id="af385-172">その後、リモート マイクロサービスで問題が発生した場合は、テーブルを単なるアーティファクトとして使用して統合イベントを追跡し、格納された統合イベントに基づいて補正アクションを実行する。</span><span class="sxs-lookup"><span data-stu-id="af385-172">Then, use the table just as an artifact to track the integration events in case of issues in the remote microservices, and perform compensatory actions based on the stored integration events.</span></span>

- <span data-ttu-id="af385-173">テーブルを一種のキューとして使用する。</span><span class="sxs-lookup"><span data-stu-id="af385-173">Use the table as a kind of queue.</span></span> <span data-ttu-id="af385-174">独立したアプリケーション スレッドまたはプロセスが統合イベント テーブルのクエリを実行し、イベント バスにイベントを発行した後、ローカル トランザクションを使用してイベントを発行済みとマークする。</span><span class="sxs-lookup"><span data-stu-id="af385-174">A separate application thread or process queries the integration event table, publishes the events to the event bus, and then uses a local transaction to mark the events as published.</span></span>

<span data-ttu-id="af385-175">図 6-22 は、1 番目のアプローチのアーキテクチャを示しています。</span><span class="sxs-lookup"><span data-stu-id="af385-175">Figure 6-22 shows the architecture for the first of these approaches.</span></span>

![イベント発行時に原子性を処理する 1 つのアプローチ: あるトランザクションを使用してイベントログ テーブルにイベントをコミットし、次に別のトランザクションを使用して発行します (eShopOnContainers で使用)](./media/image23.png)

<span data-ttu-id="af385-177">**図 6-22**。</span><span class="sxs-lookup"><span data-stu-id="af385-177">**Figure 6-22**.</span></span> <span data-ttu-id="af385-178">イベント バスにイベントを発行するときの原子性</span><span class="sxs-lookup"><span data-stu-id="af385-178">Atomicity when publishing events to the event bus</span></span>

<span data-ttu-id="af385-179">図 6-22 に示したアプローチには、発行した統合イベントの成功のチェックと確認を行う、追加のワーカー マイクロサービスがありません。</span><span class="sxs-lookup"><span data-stu-id="af385-179">The approach illustrated in Figure 6-22 is missing an additional worker microservice that is in charge of checking and confirming the success of the published integration events.</span></span> <span data-ttu-id="af385-180">失敗した場合、その追加のチェッカー ワーカー マイクロサービスは、テーブルからイベントを読み取って再発行できます。つまり、手順 2 を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="af385-180">In case of failure, that additional checker worker microservice can read events from the table and republish them, that is, repeat step number 2.</span></span>

<span data-ttu-id="af385-181">2 番目のアプローチでは、EventLog テーブルをキューとして使用し、常に常にワーカー マイクロサービスを使用してメッセージを発行します。</span><span class="sxs-lookup"><span data-stu-id="af385-181">About the second approach: you use the EventLog table as a queue and always use a worker microservice to publish the messages.</span></span> <span data-ttu-id="af385-182">その場合、プロセスは図 6-23 のようになります。</span><span class="sxs-lookup"><span data-stu-id="af385-182">In that case, the process is like that shown in Figure 6-23.</span></span> <span data-ttu-id="af385-183">これには追加のマイクロサービスが表示されており、テーブルはイベントを発行する際の単一のソースです。</span><span class="sxs-lookup"><span data-stu-id="af385-183">This shows an additional microservice, and the table is the single source when publishing events.</span></span>

![原子性を処理するためのもう 1 つのアプローチ: イベント ログ テーブルに発行し、別のマイクロサービス (バックグラウンド ワーカー) からイベントを発行するようにします。](./media/image24.png)

<span data-ttu-id="af385-185">**図 6-23**。</span><span class="sxs-lookup"><span data-stu-id="af385-185">**Figure 6-23**.</span></span> <span data-ttu-id="af385-186">ワーカー マイクロサービスを使用してイベント バスにイベントを発行するときの原子性</span><span class="sxs-lookup"><span data-stu-id="af385-186">Atomicity when publishing events to the event bus with a worker microservice</span></span>

<span data-ttu-id="af385-187">わかりやすくするために、eShopOnContainers サンプルでは、1 番目のアプローチ (追加のプロセスまたはチェッカー マイクロサービスなし) とイベント バスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="af385-187">For simplicity, the eShopOnContainers sample uses the first approach (with no additional processes or checker microservices) plus the event bus.</span></span> <span data-ttu-id="af385-188">ただし、eShopOnContainers は、考えられるすべてのエラー ケースに対応しません。</span><span class="sxs-lookup"><span data-stu-id="af385-188">However, the eShopOnContainers is not handling all possible failure cases.</span></span> <span data-ttu-id="af385-189">クラウドにデプロイされた実際のアプリケーションでは、将来的に問題が発生して、チェックと再送信のロジックを実装しなければならなくなることを承知しておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-189">In a real application deployed to the cloud, you must embrace the fact that issues will arise eventually, and you must implement that check and resend logic.</span></span> <span data-ttu-id="af385-190">テーブルをキューとして使用する方法は、そのテーブルをイベント バスを通じて (ワーカーで) イベントを発行するときの単一のイベント ソースとする場合には、1 番目のアプローチよりも効果的な可能性があります。</span><span class="sxs-lookup"><span data-stu-id="af385-190">Using the table as a queue can be more effective than the first approach if you have that table as a single source of events when publishing them (with the worker) through the event bus.</span></span>

### <a name="implementing-atomicity-when-publishing-integration-events-through-the-event-bus"></a><span data-ttu-id="af385-191">イベント バスを通じて統合イベントを発行するときの原子性の実装</span><span class="sxs-lookup"><span data-stu-id="af385-191">Implementing atomicity when publishing integration events through the event bus</span></span>

<span data-ttu-id="af385-192">次のコードは、複数の DbContext オブジェクト (更新される元のデータに関連する 1 つのコンテキストと、IntegrationEventLog テーブルに関連するもう 1 つのコンテキスト) を含む単一のトランザクションを作成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="af385-192">The following code shows how you can create a single transaction involving multiple DbContext objects—one context related to the original data being updated, and the second context related to the IntegrationEventLog table.</span></span>

<span data-ttu-id="af385-193">下のサンプル コードに示したトランザクションは、コードの実行時にデータベースへの接続に問題が発生した場合の回復性を備えていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="af385-193">Note that the transaction in the example code below will not be resilient if connections to the database have any issue at the time when the code is running.</span></span> <span data-ttu-id="af385-194">これは、サーバー間でデータベースを移動する可能性がある Azure SQL DB のようなクラウドベースのシステムで発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="af385-194">This can happen in cloud-based systems like Azure SQL DB, which might move databases across servers.</span></span> <span data-ttu-id="af385-195">複数のコンテキストにわたる回復性を備えたトランザクションの実装については、このガイドの後のセクション「[回復性の高い Entity Framework Core SQL 接続の実装](../implement-resilient-applications/implement-resilient-entity-framework-core-sql-connections.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="af385-195">For implementing resilient transactions across multiple contexts, see the [Implementing resilient Entity Framework Core SQL connections](../implement-resilient-applications/implement-resilient-entity-framework-core-sql-connections.md) section later in this guide.</span></span>

<span data-ttu-id="af385-196">わかりやすくするために、次の例では、プロセス全体を 1 つのコード片で示しています。</span><span class="sxs-lookup"><span data-stu-id="af385-196">For clarity, the following example shows the whole process in a single piece of code.</span></span> <span data-ttu-id="af385-197">ただし、eShopOnContainers の実装は実際にはリファクターされ、このロジックは管理しやすいように複数のクラスに分割されます。</span><span class="sxs-lookup"><span data-stu-id="af385-197">However, the eShopOnContainers implementation is actually refactored and split this logic into multiple classes so it is easier to maintain.</span></span>

```csharp
// Update Product from the Catalog microservice
//
public async Task<IActionResult> UpdateProduct([FromBody]CatalogItem productToUpdate)
{
  var catalogItem =
       await _catalogContext.CatalogItems.SingleOrDefaultAsync(i => i.Id ==
                                                               productToUpdate.Id);
  if (catalogItem == null) return NotFound();

  bool raiseProductPriceChangedEvent = false;
  IntegrationEvent priceChangedEvent = null;

  if (catalogItem.Price != productToUpdate.Price)
          raiseProductPriceChangedEvent = true;

  if (raiseProductPriceChangedEvent) // Create event if price has changed
  {
      var oldPrice = catalogItem.Price;
      priceChangedEvent = new ProductPriceChangedIntegrationEvent(catalogItem.Id,
                                                                  productToUpdate.Price,
                                                                  oldPrice);
  }
  // Update current product
  catalogItem = productToUpdate;

  // Just save the updated product if the Product's Price hasn't changed.
  if (!raiseProductPriceChangedEvent)
  {
      await _catalogContext.SaveChangesAsync();
  }
  else  // Publish to event bus only if product price changed
  {
        // Achieving atomicity between original DB and the IntegrationEventLog
        // with a local transaction
        using (var transaction = _catalogContext.Database.BeginTransaction())
        {
           _catalogContext.CatalogItems.Update(catalogItem);
           await _catalogContext.SaveChangesAsync();

           // Save to EventLog only if product price changed
           if(raiseProductPriceChangedEvent)
               await _integrationEventLogService.SaveEventAsync(priceChangedEvent);

           transaction.Commit();
        }

      // Publish the integration event through the event bus
      _eventBus.Publish(priceChangedEvent);

      integrationEventLogService.MarkEventAsPublishedAsync(
                                                priceChangedEvent);
  }

  return Ok();
}

```

<span data-ttu-id="af385-198">ProductPriceChangedIntegrationEvent 統合イベントが作成された後は、元のドメイン操作 (カタログ アイテムの更新) を格納するトランザクションも、EventLog テーブル内のイベントの保持を含むようになります。</span><span class="sxs-lookup"><span data-stu-id="af385-198">After the ProductPriceChangedIntegrationEvent integration event is created, the transaction that stores the original domain operation (update the catalog item) also includes the persistence of the event in the EventLog table.</span></span> <span data-ttu-id="af385-199">これは単一のトランザクションで行われ、イベント メッセージが送信されたかどうかを常に確認できるようになります。</span><span class="sxs-lookup"><span data-stu-id="af385-199">This makes it a single transaction, and you will always be able to check whether event messages were sent.</span></span>

<span data-ttu-id="af385-200">元のデータベース操作では、同じデータベースに対するローカル トランザクションを使用して、イベント ログ テーブルがアトミックに更新されます。</span><span class="sxs-lookup"><span data-stu-id="af385-200">The event log table is updated atomically with the original database operation, using a local transaction against the same database.</span></span> <span data-ttu-id="af385-201">何らかの操作が失敗した場合は、例外がスローされ、完了した操作がトランザクションによってロールバックされます。それによって、ドメイン操作と、テーブルに保存されたイベント メッセージの間の一貫性が維持されます。</span><span class="sxs-lookup"><span data-stu-id="af385-201">If any of the operations fail, an exception is thrown and the transaction rolls back any completed operation, thus maintaining consistency between the domain operations and the event messages saved to the table.</span></span>

### <a name="receiving-messages-from-subscriptions-event-handlers-in-receiver-microservices"></a><span data-ttu-id="af385-202">サブスクリプションからのメッセージの受信: 受信側マイクロサービスのイベント ハンドラー</span><span class="sxs-lookup"><span data-stu-id="af385-202">Receiving messages from subscriptions: event handlers in receiver microservices</span></span>

<span data-ttu-id="af385-203">イベント サブスクリプションのロジックに加えて、統合イベント ハンドラー (コールバック メソッドなど) 用の内部コードも実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-203">In addition to the event subscription logic, you need to implement the internal code for the integration event handlers (like a callback method).</span></span> <span data-ttu-id="af385-204">イベント ハンドラーでは、特定の種類のイベント メッセージが受信および処理される場所を指定します。</span><span class="sxs-lookup"><span data-stu-id="af385-204">The event handler is where you specify where the event messages of a certain type will be received and processed.</span></span>

<span data-ttu-id="af385-205">イベント ハンドラーは、最初に、イベント バスからイベント インスタンスを受信します。</span><span class="sxs-lookup"><span data-stu-id="af385-205">An event handler first receives an event instance from the event bus.</span></span> <span data-ttu-id="af385-206">次に、その統合イベントに関連する処理対象のコンポーネントを見つけ出し、受信側マイクロサービス内でそのイベントを状態の変更として伝達および保持します。</span><span class="sxs-lookup"><span data-stu-id="af385-206">Then it locates the component to be processed related to that integration event, propagating and persisting the event as a change in state in the receiver microservice.</span></span> <span data-ttu-id="af385-207">たとえば、カタログ マイクロサービスで ProductPriceChanged イベントが発生した場合、次のコードに示すように、イベントは買い物かごマイクロサービス内で処理され、この受信側買い物かごマイクロサービス内でも状態が変更されます。</span><span class="sxs-lookup"><span data-stu-id="af385-207">For example, if a ProductPriceChanged event originates in the catalog microservice, it is handled in the basket microservice and changes the state in this receiver basket microservice as well, as shown in the following code.</span></span>

```csharp
namespace Microsoft.eShopOnContainers.Services.Basket.API.IntegrationEvents.EventHandling
{
    public class ProductPriceChangedIntegrationEventHandler :
        IIntegrationEventHandler<ProductPriceChangedIntegrationEvent>
    {
        private readonly IBasketRepository _repository;

        public ProductPriceChangedIntegrationEventHandler(
            IBasketRepository repository)
        {
            _repository = repository;
        }

        public async Task Handle(ProductPriceChangedIntegrationEvent @event)
        {
            var userIds = await _repository.GetUsers();
            foreach (var id in userIds)
            {
                var basket = await _repository.GetBasket(id);
                await UpdatePriceInBasketItems(@event.ProductId, @event.NewPrice, basket);
            }
        }

        private async Task UpdatePriceInBasketItems(int productId, decimal newPrice,
            CustomerBasket basket)
        {
            var itemsToUpdate = basket?.Items?.Where(x => int.Parse(x.ProductId) ==
                productId).ToList();
            if (itemsToUpdate != null)
            {
                foreach (var item in itemsToUpdate)
                {
                    if(item.UnitPrice != newPrice)
                    {
                        var originalPrice = item.UnitPrice;
                        item.UnitPrice = newPrice;
                        item.OldUnitPrice = originalPrice;
                    }
                }
                await _repository.UpdateBasket(basket);
            }
        }
    }
}
```

<span data-ttu-id="af385-208">イベント ハンドラーは、その商品がいずれかの買い物かごインスタンス内に存在するかどうかを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-208">The event handler needs to verify whether the product exists in any of the basket instances.</span></span> <span data-ttu-id="af385-209">また、関連する買い物かご品目の品目価格をすべて更新します。</span><span class="sxs-lookup"><span data-stu-id="af385-209">It also updates the item price for each related basket line item.</span></span> <span data-ttu-id="af385-210">最後に、図 6-24 に示すように、ユーザーに表示する価格変更についてのアラートを作成します。</span><span class="sxs-lookup"><span data-stu-id="af385-210">Finally, it creates an alert to be displayed to the user about the price change, as shown in Figure 6-24.</span></span>

![ブラウザーの画像。ユーザー カートの価格変更通知。](./media/image25.png)

<span data-ttu-id="af385-212">**図 6-24**。</span><span class="sxs-lookup"><span data-stu-id="af385-212">**Figure 6-24**.</span></span> <span data-ttu-id="af385-213">統合イベントによって通知された、買い物かご内の品目の価格変更の表示</span><span class="sxs-lookup"><span data-stu-id="af385-213">Displaying an item price change in a basket, as communicated by integration events</span></span>

## <a name="idempotency-in-update-message-events"></a><span data-ttu-id="af385-214">更新メッセージ イベントでのべき等</span><span class="sxs-lookup"><span data-stu-id="af385-214">Idempotency in update message events</span></span>

<span data-ttu-id="af385-215">更新メッセージ イベントにおける重要な側面の 1 つは、通信のどの時点でエラーが発生しても、メッセージが再試行される必要があるという点です。</span><span class="sxs-lookup"><span data-stu-id="af385-215">An important aspect of update message events is that a failure at any point in the communication should cause the message to be retried.</span></span> <span data-ttu-id="af385-216">そうでない場合、バックグラウンド タスクが発行済みのイベントを発行しようと試みて、競合状態が発生するおそれがあります。</span><span class="sxs-lookup"><span data-stu-id="af385-216">Otherwise a background task might try to publish an event that has already been published, creating a race condition.</span></span> <span data-ttu-id="af385-217">更新がべき等であるか、または、確実に重複を検出し、破棄し、1 つの応答のみを送信するために十分な情報が更新によって提供されることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-217">You need to make sure that the updates are either idempotent or that they provide enough information to ensure that you can detect a duplicate, discard it, and send back only one response.</span></span>

<span data-ttu-id="af385-218">前述のように、べき等とは、操作を複数回実行しても結果が変わらないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="af385-218">As noted earlier, idempotency means that an operation can be performed multiple times without changing the result.</span></span> <span data-ttu-id="af385-219">メッセージング環境では、イベントを通信する際に、イベントを複数回送信しても受信側マイクロサービスにとっての結果が変わらない場合、イベントはべき等であるといえます。</span><span class="sxs-lookup"><span data-stu-id="af385-219">In a messaging environment, as when communicating events, an event is idempotent if it can be delivered multiple times without changing the result for the receiver microservice.</span></span> <span data-ttu-id="af385-220">これは、イベント自体の性質、またはシステムがイベント処理する方法により、必要となります。</span><span class="sxs-lookup"><span data-stu-id="af385-220">This may be necessary because of the nature of the event itself, or because of the way the system handles the event.</span></span> <span data-ttu-id="af385-221">メッセージのべき等性は、イベント バス パターンを実装するアプリケーションだけでなく、メッセージングを使用するすべてのアプリケーションにおいて重要です。</span><span class="sxs-lookup"><span data-stu-id="af385-221">Message idempotency is important in any application that uses messaging, not just in applications that implement the event bus pattern.</span></span>

<span data-ttu-id="af385-222">べき等操作の一例として、データがまだテーブルに存在しない場合にのみテーブルにデータを挿入する SQL ステートメントがあります。</span><span class="sxs-lookup"><span data-stu-id="af385-222">An example of an idempotent operation is a SQL statement that inserts data into a table only if that data is not already in the table.</span></span> <span data-ttu-id="af385-223">INSERT SQL ステートメントを何回実行するかは重要ではなく、結果 (テーブルにそのデータが含まれること) は同じになります。</span><span class="sxs-lookup"><span data-stu-id="af385-223">It does not matter how many times you run that insert SQL statement; the result will be the same—the table will contain that data.</span></span> <span data-ttu-id="af385-224">このようなべき等性は、複数回送信され、複数回処理される可能性があるメッセージを扱う場合にも必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="af385-224">Idempotency like this can also be necessary when dealing with messages if the messages could potentially be sent and therefore processed more than once.</span></span> <span data-ttu-id="af385-225">たとえば、再試行ロジックによって送信元がまったく同じメッセージを複数回送信する場合、メッセージがべき等であることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-225">For instance, if retry logic causes a sender to send exactly the same message more than once, you need to make sure that it is idempotent.</span></span>

<span data-ttu-id="af385-226">べき等なメッセージを設計することは可能です。</span><span class="sxs-lookup"><span data-stu-id="af385-226">It is possible to design idempotent messages.</span></span> <span data-ttu-id="af385-227">たとえば、"商品価格を $25 に設定する" というイベントを、"商品価格に $5 を追加する" というイベントの代わりに作成できます。</span><span class="sxs-lookup"><span data-stu-id="af385-227">For example, you can create an event that says "set the product price to $25" instead of "add $5 to the product price."</span></span> <span data-ttu-id="af385-228">最初のメッセージを何回でも安全に処理することができ、結果は同じになります。</span><span class="sxs-lookup"><span data-stu-id="af385-228">You could safely process the first message any number of times and the result will be the same.</span></span> <span data-ttu-id="af385-229">2 番目のメッセージでは、そうではありません。</span><span class="sxs-lookup"><span data-stu-id="af385-229">That is not true for the second message.</span></span> <span data-ttu-id="af385-230">しかし、1 番目のケースであっても、システムが新しい価格変更イベントを送信済みでその新しい価格を上書きする可能性もあるため、1 番目のイベントを処理することは望ましくありません。</span><span class="sxs-lookup"><span data-stu-id="af385-230">But even in the first case, you might not want to process the first event, because the system could also have sent a newer price-change event and you would be overwriting the new price.</span></span>

<span data-ttu-id="af385-231">もう 1 つの例は、複数のサブスクライバーに伝達される注文完了イベントです。</span><span class="sxs-lookup"><span data-stu-id="af385-231">Another example might be an order-completed event being propagated to multiple subscribers.</span></span> <span data-ttu-id="af385-232">同じ注文完了イベントに対する重複したメッセージ イベントがある場合でも、他のシステムで注文情報が 1 回だけ更新されることが重要です。</span><span class="sxs-lookup"><span data-stu-id="af385-232">It is important that order information be updated in other systems just once, even if there are duplicated message events for the same order-completed event.</span></span>

<span data-ttu-id="af385-233">各イベントが各受信者に対して 1 回のみ処理されるように強制するロジックを作成できるように、何らかの ID があると便利です。</span><span class="sxs-lookup"><span data-stu-id="af385-233">It is convenient to have some kind of identity per event so that you can create logic that enforces that each event is processed only once per receiver.</span></span>

<span data-ttu-id="af385-234">メッセージ処理の中には、本質的にべき等なものもあります。</span><span class="sxs-lookup"><span data-stu-id="af385-234">Some message processing is inherently idempotent.</span></span> <span data-ttu-id="af385-235">たとえば、システムがサムネイル画像を生成する場合、生成されたサムネイルに関するメッセージが処理される回数は関係ありません。結果はサムネイルが生成されたということであり、これは毎回同じです。</span><span class="sxs-lookup"><span data-stu-id="af385-235">For example, if a system generates image thumbnails, it might not matter how many times the message about the generated thumbnail is processed; the outcome is that the thumbnails are generated and they are the same every time.</span></span> <span data-ttu-id="af385-236">一方、支払いゲートウェイを呼び出してクレジット カードに請求するというような操作は、べき等である必要はまったくありません。</span><span class="sxs-lookup"><span data-stu-id="af385-236">On the other hand, operations such as calling a payment gateway to charge a credit card may not be idempotent at all.</span></span> <span data-ttu-id="af385-237">そのようなケースでは、メッセージを複数回処理することで目的の効果が得られるということを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-237">In these cases, you need to ensure that processing a message multiple times has the effect that you expect.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="af385-238">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="af385-238">Additional resources</span></span>

- <span data-ttu-id="af385-239">**メッセージのべき等性に従う**</span><span class="sxs-lookup"><span data-stu-id="af385-239">**Honoring message idempotency**</span></span> <br/>
    <https://docs.microsoft.com/previous-versions/msp-n-p/jj591565(v=pandp.10)#honoring-message-idempotency>

## <a name="deduplicating-integration-event-messages"></a><span data-ttu-id="af385-240">統合イベント メッセージの重複除去</span><span class="sxs-lookup"><span data-stu-id="af385-240">Deduplicating integration event messages</span></span>

<span data-ttu-id="af385-241">メッセージ イベントが 1 つのサブスクライバーにつき 1 回のみ送信および処理されたということは、さまざまなレベルで確認できます。</span><span class="sxs-lookup"><span data-stu-id="af385-241">You can make sure that message events are sent and processed just once per subscriber at different levels.</span></span> <span data-ttu-id="af385-242">1 つの方法は、使用しているメッセージング インフラストラクチャが提供している重複除去機能を使用することです。</span><span class="sxs-lookup"><span data-stu-id="af385-242">One way is to use a deduplication feature offered by the messaging infrastructure you are using.</span></span> <span data-ttu-id="af385-243">もう 1 つの方法は、送信先マイクロサービスにカスタムロジックを実装することです。</span><span class="sxs-lookup"><span data-stu-id="af385-243">Another is to implement custom logic in your destination microservice.</span></span> <span data-ttu-id="af385-244">トランスポート レベルとアプリケーション レベルの両方で検証を行うことは、最善の方法です。</span><span class="sxs-lookup"><span data-stu-id="af385-244">Having validations at both the transport level and the application level is your best bet.</span></span>

### <a name="deduplicating-message-events-at-the-eventhandler-level"></a><span data-ttu-id="af385-245">EventHandler レベルでのメッセージ イベントの重複除去</span><span class="sxs-lookup"><span data-stu-id="af385-245">Deduplicating message events at the EventHandler level</span></span>

<span data-ttu-id="af385-246">イベントが受信者によって 1 回だけ処理されたことを確認する 1 つの方法は、イベント ハンドラーでメッセージ イベントを処理する際に特定のロジックを実装することです。</span><span class="sxs-lookup"><span data-stu-id="af385-246">One way to make sure that an event is processed just once by any receiver is by implementing certain logic when processing the message events in event handlers.</span></span> <span data-ttu-id="af385-247">たとえば、それは eShopOnContainers アプリケーションで使用されるアプローチで、UserCheckoutAcceptedIntegrationEvent 統合イベントを受け取るときに [UserCheckoutAcceptedIntegrationEventHandler クラスのソース コード](https://github.com/dotnet-architecture/eShopOnContainers/blob/master/src/Services/Ordering/Ordering.API/Application/IntegrationEvents/EventHandling/UserCheckoutAcceptedIntegrationEventHandler.cs)で確認できます</span><span class="sxs-lookup"><span data-stu-id="af385-247">For example, that is the approach used in the eShopOnContainers application, as you can see in the [source code of the UserCheckoutAcceptedIntegrationEventHandler class](https://github.com/dotnet-architecture/eShopOnContainers/blob/master/src/Services/Ordering/Ordering.API/Application/IntegrationEvents/EventHandling/UserCheckoutAcceptedIntegrationEventHandler.cs) when it receives an UserCheckoutAcceptedIntegrationEvent integration event.</span></span> <span data-ttu-id="af385-248">(この場合、コマンド ハンドラーに送信する前に、識別子として eventMsg.RequestId を使用し、IdentifiedCommand で CreateOrderCommand をラップします。)。</span><span class="sxs-lookup"><span data-stu-id="af385-248">(In this case we wrap the CreateOrderCommand with an IdentifiedCommand, using the eventMsg.RequestId as an identifier, before sending it to the command handler).</span></span>

### <a name="deduplicating-messages-when-using-rabbitmq"></a><span data-ttu-id="af385-249">RabbitMQ を使用するときのメッセージの重複除去</span><span class="sxs-lookup"><span data-stu-id="af385-249">Deduplicating messages when using RabbitMQ</span></span>

<span data-ttu-id="af385-250">断続的なネットワーク エラーが発生する場合、メッセージが重複している可能性があり、メッセージ受信側はこれらの重複したメッセージを処理する準備ができている必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-250">When intermittent network failures happen, messages can be duplicated, and the message receiver must be ready to handle these duplicated messages.</span></span> <span data-ttu-id="af385-251">受信側は、可能な場合、べき等な方法でメッセージを処理する必要があります。これは、重複除去でメッセージを明示的に処理する方法よりも優れています。</span><span class="sxs-lookup"><span data-stu-id="af385-251">If possible, receivers should handle messages in an idempotent way, which is better than explicitly handling them with deduplication.</span></span>

<span data-ttu-id="af385-252">[RabbitMQ のドキュメント](https://www.rabbitmq.com/reliability.html#consumer)によると、メッセージがコンシューマーに配信された後で (コンシューマーとの接続が切断される前に受信確認されなかったなどの理由で) キューに再登録された場合、RabbitMQ は、メッセージが再配信されるときに (配信先が同じコンシューマーか別のコンシューマーかにかかわらず) 再配信フラグをメッセージに設定します。</span><span class="sxs-lookup"><span data-stu-id="af385-252">According to the [RabbitMQ documentation](https://www.rabbitmq.com/reliability.html#consumer), “If a message is delivered to a consumer and then requeued (because it was not acknowledged before the consumer connection dropped, for example) then RabbitMQ will set the redelivered flag on it when it is delivered again (whether to the same consumer or a different one).</span></span>

<span data-ttu-id="af385-253">“再配信” フラグが設定されている場合、受信側はそれを考慮する必要があります。なぜなら、メッセージが既に処理されている可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="af385-253">If the “redelivered” flag is set, the receiver must take that into account, because the message might already have been processed.</span></span> <span data-ttu-id="af385-254">しかしそれは保証されているわけではありません。メッセージはメッセージ ブローカーから送信された後、ネットワークの問題が原因で一度も受信側に到達していない可能性もあります。</span><span class="sxs-lookup"><span data-stu-id="af385-254">But that is not guaranteed; the message might never have reached the receiver after it left the message broker, perhaps because of network issues.</span></span> <span data-ttu-id="af385-255">その一方で、“再配信” フラグが設定されていない場合、メッセージが複数回送信されていないことが保証されます。</span><span class="sxs-lookup"><span data-stu-id="af385-255">On the other hand, if the “redelivered” flag is not set, it is guaranteed that the message has not been sent more than once.</span></span> <span data-ttu-id="af385-256">したがって、受信側は、“再配信” フラグがメッセージに設定されている場合にのみ、メッセージを重複除去するか、べき等な方法でメッセージを処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="af385-256">Therefore, the receiver needs to deduplicate messages or process messages in an idempotent way only if the “redelivered” flag is set in the message.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="af385-257">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="af385-257">Additional resources</span></span>

- <span data-ttu-id="af385-258">**NServiceBus を使用するフォークされた eShopOnContainers (Particular Software)**  </span><span class="sxs-lookup"><span data-stu-id="af385-258">**Forked eShopOnContainers using NServiceBus (Particular Software)** </span></span>\
    <https://go.particular.net/eShopOnContainers>

- <span data-ttu-id="af385-259">**イベント駆動型メッセージング** </span><span class="sxs-lookup"><span data-stu-id="af385-259">**Event Driven Messaging** </span></span>\
    [http://soapatterns.org/design\_patterns/event\_driven\_messaging](http://soapatterns.org/design_patterns/event_driven_messaging)

- <span data-ttu-id="af385-260">**Jimmy Bogard。復元性を目指したリファクタリング: 結合の評価** </span><span class="sxs-lookup"><span data-stu-id="af385-260">**Jimmy Bogard. Refactoring Towards Resilience: Evaluating Coupling** </span></span>\
    <https://jimmybogard.com/refactoring-towards-resilience-evaluating-coupling/>

- <span data-ttu-id="af385-261">**発行-サブスクライブ チャンネル** </span><span class="sxs-lookup"><span data-stu-id="af385-261">**Publish-Subscribe channel** </span></span>\
    <https://www.enterpriseintegrationpatterns.com/patterns/messaging/PublishSubscribeChannel.html>

- <span data-ttu-id="af385-262">**境界コンテキスト間の通信** </span><span class="sxs-lookup"><span data-stu-id="af385-262">**Communicating Between Bounded Contexts** </span></span>\
    <https://docs.microsoft.com/previous-versions/msp-n-p/jj591572(v=pandp.10)>

- <span data-ttu-id="af385-263">**最終的な整合性** </span><span class="sxs-lookup"><span data-stu-id="af385-263">**Eventual Consistency** </span></span>\
    [https://en.wikipedia.org/wiki/Eventual\_consistency](https://en.wikipedia.org/wiki/Eventual_consistency)

- <span data-ttu-id="af385-264">**Philip Brown。境界コンテキストを統合するための戦略** </span><span class="sxs-lookup"><span data-stu-id="af385-264">**Philip Brown. Strategies for Integrating Bounded Contexts** </span></span>\
    <https://www.culttt.com/2014/11/26/strategies-integrating-bounded-contexts/>

- <span data-ttu-id="af385-265">**Chris Richardson。集約、イベント ソース、CQRS を使用したトランザクション マイクロサービスの開発 - パート 2** </span><span class="sxs-lookup"><span data-stu-id="af385-265">**Chris Richardson. Developing Transactional Microservices Using Aggregates, Event Sourcing and CQRS - Part 2** </span></span>\
    <https://www.infoq.com/articles/microservices-aggregates-events-cqrs-part-2-richardson>

- <span data-ttu-id="af385-266">**Chris Richardson。イベント ソーシング パターン** </span><span class="sxs-lookup"><span data-stu-id="af385-266">**Chris Richardson. Event Sourcing pattern** </span></span>\
    <https://microservices.io/patterns/data/event-sourcing.html>

- <span data-ttu-id="af385-267">**イベント ソーシングの概要** </span><span class="sxs-lookup"><span data-stu-id="af385-267">**Introducing Event Sourcing** </span></span>\
    <https://docs.microsoft.com/previous-versions/msp-n-p/jj591559(v=pandp.10)>

- <span data-ttu-id="af385-268">**イベント ストア データベース**。</span><span class="sxs-lookup"><span data-stu-id="af385-268">**Event Store database**.</span></span> <span data-ttu-id="af385-269">公式サイト。</span><span class="sxs-lookup"><span data-stu-id="af385-269">Official site.</span></span> \
    <https://geteventstore.com/>

- <span data-ttu-id="af385-270">**Patrick Nommensen。マイクロサービス用のイベント ドリブン データ管理** </span><span class="sxs-lookup"><span data-stu-id="af385-270">**Patrick Nommensen. Event-Driven Data Management for Microservices** </span></span>\
    <https://dzone.com/articles/event-driven-data-management-for-microservices-1>

- <span data-ttu-id="af385-271">**CAP 定理** </span><span class="sxs-lookup"><span data-stu-id="af385-271">**The CAP Theorem** </span></span>\
    [https://en.wikipedia.org/wiki/CAP\_theorem](https://en.wikipedia.org/wiki/CAP_theorem)

- <span data-ttu-id="af385-272">**CAP 定理とは?**</span><span class="sxs-lookup"><span data-stu-id="af385-272">**What is CAP Theorem?**</span></span> \
    <https://www.quora.com/What-Is-CAP-Theorem-1>

- <span data-ttu-id="af385-273">**データ整合性の概要** </span><span class="sxs-lookup"><span data-stu-id="af385-273">**Data Consistency Primer** </span></span>\
    <https://docs.microsoft.com/previous-versions/msp-n-p/dn589800(v=pandp.10)>

- <span data-ttu-id="af385-274">**Rick Saling。CAP 定理: クラウドとインターネットでは "すべてが異なる" 理由** </span><span class="sxs-lookup"><span data-stu-id="af385-274">**Rick Saling. The CAP Theorem: Why “Everything is Different” with the Cloud and Internet** </span></span>\
    <https://blogs.msdn.microsoft.com/rickatmicrosoft/2013/01/03/the-cap-theorem-why-everything-is-different-with-the-cloud-and-internet/>

- <span data-ttu-id="af385-275">**Eric Brewer。12 年後の CAP: "規則" はどのように変わったか** </span><span class="sxs-lookup"><span data-stu-id="af385-275">**Eric Brewer. CAP Twelve Years Later: How the "Rules" Have Changed** </span></span>\
    <https://www.infoq.com/articles/cap-twelve-years-later-how-the-rules-have-changed>

- <span data-ttu-id="af385-276">**Azure Service Bus。ブローカー メッセージング: 重複データ検出**  </span><span class="sxs-lookup"><span data-stu-id="af385-276">**Azure Service Bus. Brokered Messaging: Duplicate Detection**  </span></span>\
    <https://code.msdn.microsoft.com/Brokered-Messaging-c0acea25>

- <span data-ttu-id="af385-277">**信頼性ガイド** (RabbitMQ ドキュメント) </span><span class="sxs-lookup"><span data-stu-id="af385-277">**Reliability Guide** (RabbitMQ documentation) </span></span>\
    [https://www.rabbitmq.com/reliability.html\#consumer](https://www.rabbitmq.com/reliability.html#consumer)

> [!div class="step-by-step"]
> <span data-ttu-id="af385-278">[前へ](rabbitmq-event-bus-development-test-environment.md)
> [次へ](test-aspnet-core-services-web-apps.md)</span><span class="sxs-lookup"><span data-stu-id="af385-278">[Previous](rabbitmq-event-bus-development-test-environment.md)
[Next](test-aspnet-core-services-web-apps.md)</span></span>