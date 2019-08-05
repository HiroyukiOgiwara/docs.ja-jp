---
title: メッセージベースの非同期通信
description: '.NET マイクロサービス: コンテナー化された .NET アプリケーションのアーキテクチャ | メッセージベースの非同期通信はマイクロサービスにとって極めて重要な概念です。マイクロサービス間の独立性を維持し、同時に、最終的には同期させる最良の方法であるためです。'
ms.date: 09/20/2018
ms.openlocfilehash: 65bd0cd2b316fe7011ad8e878852547ee5949f09
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68673319"
---
# <a name="asynchronous-message-based-communication"></a><span data-ttu-id="ced03-103">メッセージベースの非同期通信</span><span class="sxs-lookup"><span data-stu-id="ced03-103">Asynchronous message-based communication</span></span>

<span data-ttu-id="ced03-104">非同期メッセージ通信およびイベントドリブン通信は、複数のマイクロサービスとそれに関連するドメイン モデル間で変更を反映するときに重要です。</span><span class="sxs-lookup"><span data-stu-id="ced03-104">Asynchronous messaging and event-driven communication are critical when propagating changes across multiple microservices and their related domain models.</span></span> <span data-ttu-id="ced03-105">マイクロサービスと境界付けられたコンテキスト (BC) について前に説明したとおり、モデル (ユーザー、カスタマー、製品、アカウントなど) は、マイクロサービスまたは BC ごとに異なるものを意味することがあります。</span><span class="sxs-lookup"><span data-stu-id="ced03-105">As mentioned earlier in the discussion microservices and Bounded Contexts (BCs), models (User, Customer, Product, Account, etc.) can mean different things to different microservices or BCs.</span></span> <span data-ttu-id="ced03-106">つまり、変更が発生したとき、異なるモデル間で変更を調整する方法が必要になります。</span><span class="sxs-lookup"><span data-stu-id="ced03-106">That means that when changes occur, you need some way to reconcile changes across the different models.</span></span> <span data-ttu-id="ced03-107">解決策は、最終的な整合性と、非同期メッセージングに基づくイベントドリブン通信です。</span><span class="sxs-lookup"><span data-stu-id="ced03-107">A solution is eventual consistency and event-driven communication based on asynchronous messaging.</span></span>

<span data-ttu-id="ced03-108">メッセージングを使用するとき、プロセスはメッセージを非同期で交換して通信します。</span><span class="sxs-lookup"><span data-stu-id="ced03-108">When using messaging, processes communicate by exchanging messages asynchronously.</span></span> <span data-ttu-id="ced03-109">クライアントは、サービスにメッセージを送信することでコマンドまたは要求を作成します。</span><span class="sxs-lookup"><span data-stu-id="ced03-109">A client makes a command or a request to a service by sending it a message.</span></span> <span data-ttu-id="ced03-110">サービスが返信する必要がある場合は、別のメッセージをクライアントに送信します。</span><span class="sxs-lookup"><span data-stu-id="ced03-110">If the service needs to reply, it sends a different message back to the client.</span></span> <span data-ttu-id="ced03-111">メッセージベースの通信であるため、クライアントは、返信をすぐに受信しないこと、返信がまったくない場合もあると仮定します。</span><span class="sxs-lookup"><span data-stu-id="ced03-111">Since it's a message-based communication, the client assumes that the reply won't be received immediately, and that there might be no response at all.</span></span>

<span data-ttu-id="ced03-112">メッセージはヘッダー (ID 情報やセキュリティ情報などメタデータ) と本文で構成されます。</span><span class="sxs-lookup"><span data-stu-id="ced03-112">A message is composed by a header (metadata such as identification or security information) and a body.</span></span> <span data-ttu-id="ced03-113">通常、メッセージは AMQP のような非同期プロトコルを介して送信されます。</span><span class="sxs-lookup"><span data-stu-id="ced03-113">Messages are usually sent through asynchronous protocols like AMQP.</span></span>

<span data-ttu-id="ced03-114">マイクロサービス コミュニティにおいてこの種の通信で好まれるインフラストラクチャは、軽量メッセージ ブローカーです。これは、SOA で使用される大規模なブローカーやオーケストレーターとは異なります。</span><span class="sxs-lookup"><span data-stu-id="ced03-114">The preferred infrastructure for this type of communication in the microservices community is a lightweight message broker, which is different than the large brokers and orchestrators used in SOA.</span></span> <span data-ttu-id="ced03-115">軽量メッセージ ブローカーの場合、インフラストラクチャは通常は "ダム" であり、RabbitMQ などのシンプルな実装、または Azure Service Bus のようなクラウドのスケーラブル サービス バスに対して、メッセージ ブローカーとしてのみ作動します。</span><span class="sxs-lookup"><span data-stu-id="ced03-115">In a lightweight message broker, the infrastructure is typically "dumb," acting only as a message broker, with simple implementations such as RabbitMQ or a scalable service bus in the cloud like Azure Service Bus.</span></span> <span data-ttu-id="ced03-116">このシナリオでは、"スマート" 思考のほとんどは、メッセージを生成および処理するエンドポイント、すなわちマイクロサービスに存在します。</span><span class="sxs-lookup"><span data-stu-id="ced03-116">In this scenario, most of the "smart" thinking still lives in the endpoints that are producing and consuming messages-that is, in the microservices.</span></span>

<span data-ttu-id="ced03-117">できるだけ従うことをお勧めするもう 1 つのルールは、内部サービス間では非同期メッセージングのみを使用し、クライアント アプリからフロントエンド サービス (API ゲートウェイと第 1 レベルのマイクロサービス) では同期通信 (HTTPなど) のみを使用するということです。</span><span class="sxs-lookup"><span data-stu-id="ced03-117">Another rule you should try to follow, as much as possible, is to use only asynchronous messaging between the internal services, and to use synchronous communication (such as HTTP) only from the client apps to the front-end services (API Gateways plus the first level of microservices).</span></span>

<span data-ttu-id="ced03-118">非同期メッセージング通信には 2 つの種類があります。単一受信者メッセージベースの通信と複数受信者メッセージベースの通信です。</span><span class="sxs-lookup"><span data-stu-id="ced03-118">There are two kinds of asynchronous messaging communication: single receiver message-based communication, and multiple receivers message-based communication.</span></span> <span data-ttu-id="ced03-119">次のセクションでは、これらについて詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="ced03-119">The following sections provide details about them.</span></span>

## <a name="single-receiver-message-based-communication"></a><span data-ttu-id="ced03-120">単一受信者メッセージベースの通信</span><span class="sxs-lookup"><span data-stu-id="ced03-120">Single-receiver message-based communication</span></span>

<span data-ttu-id="ced03-121">単一受信者メッセージベースの非同期通信とは、チャネルを読み取るコンシューマーの 1 つのみにメッセージを届ける 2 点間の通信があり、メッセージが 1 回しか処理されないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="ced03-121">Message-based asynchronous communication with a single receiver means there's point-to-point communication that delivers a message to exactly one of the consumers that's reading from the channel, and that the message is processed just once.</span></span> <span data-ttu-id="ced03-122">ただし、特殊な状況があります。</span><span class="sxs-lookup"><span data-stu-id="ced03-122">However, there are special situations.</span></span> <span data-ttu-id="ced03-123">たとえば、障害から自動的に復旧しようとするクラウド システムでは、同じメッセージが何回も送信されることがあります。</span><span class="sxs-lookup"><span data-stu-id="ced03-123">For instance, in a cloud system that tries to automatically recover from failures, the same message could be sent multiple times.</span></span> <span data-ttu-id="ced03-124">ネットワークやその他の障害のために、クライアントがメッセージの再送信を行えることが必要です。また、サーバーはべき等の処理を実装して、特定のメッセージを 1 回だけ処理するようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ced03-124">Due to network or other failures, the client has to be able to retry sending messages, and the server has to implement an operation to be idempotent in order to process a particular message just once.</span></span>

<span data-ttu-id="ced03-125">単一受信者メッセージベース通信は、非同期コマンドを 1 つのマイクロサービスから別のマイクロサービスに送信する際に特に適しています。図 4-18 にこのアプローチを示します。</span><span class="sxs-lookup"><span data-stu-id="ced03-125">Single-receiver message-based communication is especially well suited for sending asynchronous commands from one microservice to another as shown in Figure 4-18 that illustrates this approach.</span></span>

<span data-ttu-id="ced03-126">いったんメッセージベース通信 (コマンドまたはイベントを含む) の送信を開始したら、メッセージベース通信と同期 HTTP 通信を混在させないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="ced03-126">Once you start sending message-based communication (either with commands or events), you should avoid mixing message-based communication with synchronous HTTP communication.</span></span>

![非同期メッセージを受信する 1 つのマイクロサービス](./media/image18.png)

<span data-ttu-id="ced03-128">**図 4-18**.</span><span class="sxs-lookup"><span data-stu-id="ced03-128">**Figure 4-18**.</span></span> <span data-ttu-id="ced03-129">非同期メッセージを受信する 1 つのマイクロサービス</span><span class="sxs-lookup"><span data-stu-id="ced03-129">A single microservice receiving an asynchronous message</span></span>

<span data-ttu-id="ced03-130">クライアント アプリケーションからコマンドが届くと、それらのコマンドは HTTP 同期コマンドとして実装できることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ced03-130">Note that when the commands come from client applications, they can be implemented as HTTP synchronous commands.</span></span> <span data-ttu-id="ced03-131">メッセージベースのコマンドを使用する必要があるのは、高いスケーラビリティが必要な場合、またはメッセージベースのビジネス プロセスを既に使用している場合です。</span><span class="sxs-lookup"><span data-stu-id="ced03-131">You should use message-based commands when you need higher scalability or when you're already in a message-based business process.</span></span>

## <a name="multiple-receivers-message-based-communication"></a><span data-ttu-id="ced03-132">複数受信者メッセージベースの通信</span><span class="sxs-lookup"><span data-stu-id="ced03-132">Multiple-receivers message-based communication</span></span>

<span data-ttu-id="ced03-133">より柔軟性の高いアプローチとして、パブリッシュ/サブスクライブ メカニズムを使用し、送信側からの通信を、追加のサブスクライバー マクロサービスまたは外部アプリケーションに届けることができます。</span><span class="sxs-lookup"><span data-stu-id="ced03-133">As a more flexible approach, you might also want to use a publish/subscribe mechanism so that your communication from the sender will be available to additional subscriber microservices or to external applications.</span></span> <span data-ttu-id="ced03-134">これは、送信サービスの[開放/閉鎖原則](https://en.wikipedia.org/wiki/Open/closed_principle)に従うために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ced03-134">Thus, it helps you to follow the [open/closed principle](https://en.wikipedia.org/wiki/Open/closed_principle) in the sending service.</span></span> <span data-ttu-id="ced03-135">このようにすると、将来サブスクライバーを追加する際に送信側サービスを変更する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="ced03-135">That way, additional subscribers can be added in the future without the need to modify the sender service.</span></span>

<span data-ttu-id="ced03-136">パブリッシュ/サブスクライブ通信を使用するとき、イベント バス インターフェイスを使用して、イベントをサブスクライバーにパブリッシュすることがあります。</span><span class="sxs-lookup"><span data-stu-id="ced03-136">When you use a publish/subscribe communication, you might be using an event bus interface to publish events to any subscriber.</span></span>

## <a name="asynchronous-event-driven-communication"></a><span data-ttu-id="ced03-137">非同期イベントドリブン通信</span><span class="sxs-lookup"><span data-stu-id="ced03-137">Asynchronous event-driven communication</span></span>

<span data-ttu-id="ced03-138">非同期イベントドリブン通信を使用すると、製品カタログ マイクロサービスでの価格変更のように、ドメインで何かが起きて他のマイクロサービスがそれを認識する必要があるとき、マイクロサービスが統合イベントをパブリッシュします。</span><span class="sxs-lookup"><span data-stu-id="ced03-138">When using asynchronous event-driven communication, a microservice publishes an integration event when something happens within its domain and another microservice needs to be aware of it, like a price change in a product catalog microservice.</span></span> <span data-ttu-id="ced03-139">他のマイクロサービスはイベントをサブスクライブしているので、非同期でイベントを受信できます。</span><span class="sxs-lookup"><span data-stu-id="ced03-139">Additional microservices subscribe to the events so they can receive them asynchronously.</span></span> <span data-ttu-id="ced03-140">これが発生すると、受信側が自らのドメイン エンティティを更新する場合があり、別の統合イベントのパブリッシュにつながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ced03-140">When that happens, the receivers might update their own domain entities, which can cause more integration events to be published.</span></span> <span data-ttu-id="ced03-141">この発行/サブスクライブ システムは、通常はイベント バスの実装を使って実行されます。</span><span class="sxs-lookup"><span data-stu-id="ced03-141">This publish/subscribe system is usually performed by using an implementation of an event bus.</span></span> <span data-ttu-id="ced03-142">イベント バスは、イベントのサブスクライブとサブスクライブ解除、およびイベントのパブリッシュに必要な API を備えた抽象化またはインターフェイスとして設計できます。</span><span class="sxs-lookup"><span data-stu-id="ced03-142">The event bus can be designed as an abstraction or interface, with the API that's needed to subscribe or unsubscribe to events and to publish events.</span></span> <span data-ttu-id="ced03-143">また、イベント バスは、非同期通信とパブリッシュ/サブスクライブ モデルをサポートするメッセージング キューまたはサービス バスなど、インタープロセスとメッセージング ブローカーに基づいて 1 つ以上の実装を持つこともできます。</span><span class="sxs-lookup"><span data-stu-id="ced03-143">The event bus can also have one or more implementations based on any inter-process and messaging broker, like a messaging queue or service bus that supports asynchronous communication and a publish/subscribe model.</span></span>

<span data-ttu-id="ced03-144">システムが、統合イベントによって駆動される最終的な整合性を使用する場合は、エンド ユーザーに対してこのアプローチを完全に明らかにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ced03-144">If a system uses eventual consistency driven by integration events, it's recommended that this approach is made completely clear to the end user.</span></span> <span data-ttu-id="ced03-145">システムは、SignalR やクライアントからのポーリング システムなど、統合イベントを模倣するアプローチを使用してはなりません。</span><span class="sxs-lookup"><span data-stu-id="ced03-145">The system shouldn't use an approach that mimics integration events, like SignalR or polling systems from the client.</span></span> <span data-ttu-id="ced03-146">エンド ユーザーおよび事業主は、最終的な整合性をシステムに明示的に組み込む必要があります。また、明示的に行う限り、多くのケースでこのアプローチによってビジネスに問題が生じないことを理解する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ced03-146">The end user and the business owner have to explicitly embrace eventual consistency in the system and realize that in many cases the business doesn't have any problem with this approach, as long as it's explicit.</span></span> <span data-ttu-id="ced03-147">これは重要なことです。なぜなら、ユーザーはいくつかの結果がすぐに表示されることを期待するからです。最終的な整合性ではそれが起こらないことがあります。</span><span class="sxs-lookup"><span data-stu-id="ced03-147">This is important because users might expect to see some results immediately and this might not happen with eventual consistency.</span></span>

<span data-ttu-id="ced03-148">「[分散データ管理に関する課題とソリューション](distributed-data-management.md)」セクションで説明したように、統合イベントを使用すると、複数のマイクロサービスにまたがるビジネス タスクを実装できます。</span><span class="sxs-lookup"><span data-stu-id="ced03-148">As noted earlier in the [Challenges and solutions for distributed data management](distributed-data-management.md) section, you can use integration events to implement business tasks that span multiple microservices.</span></span> <span data-ttu-id="ced03-149">このようにして、サービス間の最終的な整合性を実現します。</span><span class="sxs-lookup"><span data-stu-id="ced03-149">Thus, you'll have eventual consistency between those services.</span></span> <span data-ttu-id="ced03-150">最終的に整合性のあるトランザクションは、分散アクションのコレクションで構成されます。</span><span class="sxs-lookup"><span data-stu-id="ced03-150">An eventually consistent transaction is made up of a collection of distributed actions.</span></span> <span data-ttu-id="ced03-151">各アクションでは、関連するマイクロサービスがドメイン エンティティを更新し、もう 1 つの統合イベントをパブリッシュします。これにより、同一のエンドツーエンド ビジネス タスク内で次のアクションが発生します。</span><span class="sxs-lookup"><span data-stu-id="ced03-151">At each action, the related microservice updates a domain entity and publishes another integration event that raises the next action within the same end-to-end business task.</span></span>

<span data-ttu-id="ced03-152">重要な点は、同じイベントをサブクライブしている複数のマイクロサービスに通信を行う必要があるということです。</span><span class="sxs-lookup"><span data-stu-id="ced03-152">An important point is that you might want to communicate to multiple microservices that are subscribed to the same event.</span></span> <span data-ttu-id="ced03-153">このためには、イベントドリブン通信に基づいたパブリッシュ/サブスクライブ メッセージングを使用できます (図 4-19 を参照)。</span><span class="sxs-lookup"><span data-stu-id="ced03-153">To do so, you can use publish/subscribe messaging based on event-driven communication, as shown in Figure 4-19.</span></span> <span data-ttu-id="ced03-154">このパブリッシュ/サブスクライブ メカニズムは、マイクロサービス アーキテクチャ専用ではありません。</span><span class="sxs-lookup"><span data-stu-id="ced03-154">This publish/subscribe mechanism isn't exclusive to the microservice architecture.</span></span> <span data-ttu-id="ced03-155">これは、DDD において[境界付けられたコンテキスト](https://martinfowler.com/bliki/BoundedContext.html)が通信する方法、または[コマンド クエリ責務分離 (CQRS)](https://martinfowler.com/bliki/CQRS.html) アーキテクチャ パターンで書き込みデータベースを読み取りデータベースに反映する方法に似ています。</span><span class="sxs-lookup"><span data-stu-id="ced03-155">It's similar to the way [Bounded Contexts](https://martinfowler.com/bliki/BoundedContext.html) in DDD should communicate, or to the way you propagate updates from the write database to the read database in the [Command and Query Responsibility Segregation (CQRS)](https://martinfowler.com/bliki/CQRS.html) architecture pattern.</span></span> <span data-ttu-id="ced03-156">目的は、分散システム全体の複数のデータ ソース間で最終的な整合性を得ることです。</span><span class="sxs-lookup"><span data-stu-id="ced03-156">The goal is to have eventual consistency between multiple data sources across your distributed system.</span></span>

![非同期イベント駆動型の通信では、1 つのマイクロサービスがイベントをイベント バスに発行し、たくさんのマイクロサービスがそれをサブスクライブし、通知を受け、対処できます。](./media/image19.png)

<span data-ttu-id="ced03-158">**図 4-19**</span><span class="sxs-lookup"><span data-stu-id="ced03-158">**Figure 4-19**.</span></span> <span data-ttu-id="ced03-159">非同期イベントドリブン メッセージ通信</span><span class="sxs-lookup"><span data-stu-id="ced03-159">Asynchronous event-driven message communication</span></span>

<span data-ttu-id="ced03-160">ご使用の実装によって、イベントドリブンのメッセージベース通信で使用されるプロトコルが決まります。</span><span class="sxs-lookup"><span data-stu-id="ced03-160">Your implementation will determine what protocol to use for event-driven, message-based communications.</span></span> <span data-ttu-id="ced03-161">[AMQP](https://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) は、キューにある通信の信頼性を向上できます。</span><span class="sxs-lookup"><span data-stu-id="ced03-161">[AMQP](https://en.wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) can help achieve reliable queued communication.</span></span>

<span data-ttu-id="ced03-162">イベント バスを使用する場合は、関連する実装に基づき、クラスにおいて抽象化レベル (イベント バス インターフェイスなど) を使用することができます。[RabbitMQ](https://www.rabbitmq.com/) のようなメッセージ ブローカーまたは「[Azure Service Bus with Topics](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)」(Azure Service Bus トピック) で説明したサービス バスの API をコードで使用します。</span><span class="sxs-lookup"><span data-stu-id="ced03-162">When you use an event bus, you might want to use an abstraction level (like an event bus interface) based on a related implementation in classes with code using the API from a message broker like [RabbitMQ](https://www.rabbitmq.com/) or a service bus like [Azure Service Bus with Topics](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions).</span></span> <span data-ttu-id="ced03-163">または、NServiceBus、MassTransit、Brighter のような高レベルのサービス バスを使用して、イベント バスとパブリッシュ/サブスクライブ システムを統合することもできます。</span><span class="sxs-lookup"><span data-stu-id="ced03-163">Alternatively, you might want to use a higher-level service bus like NServiceBus, MassTransit, or Brighter to articulate your event bus and publish/subscribe system.</span></span>

## <a name="a-note-about-messaging-technologies-for-production-systems"></a><span data-ttu-id="ced03-164">運用システムでのメッセージング テクノロジに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="ced03-164">A note about messaging technologies for production systems</span></span>

<span data-ttu-id="ced03-165">抽象イベント バスの実装に使用できるメッセージング テクノロジにはさまざまなレベルがあります。</span><span class="sxs-lookup"><span data-stu-id="ced03-165">The messaging technologies available for implementing your abstract event bus are at different levels.</span></span> <span data-ttu-id="ced03-166">たとえば、RabbitMQ (メッセージング ブローカー転送) や Azure Service Bus のような製品は、NServiceBus、MassTransit、Brighter といった他の製品よりも下のレベルにあるため、こらの製品は RabbitMQ や Azure Service Bus の上で作動することができます。</span><span class="sxs-lookup"><span data-stu-id="ced03-166">For instance, products like RabbitMQ (a messaging broker transport) and Azure Service Bus sit at a lower level than other products like, NServiceBus, MassTransit, or Brighter, which can work on top of RabbitMQ and Azure Service Bus.</span></span> <span data-ttu-id="ced03-167">アプリケーション レベルに高度な機能がどれだけあるかや、すぐに利用できるスケーラビリティがアプリケーションで必要かどうかによって、何を選択するかが決まります。</span><span class="sxs-lookup"><span data-stu-id="ced03-167">Your choice depends on how many rich features at the application level and out-of-the-box scalability you need for your application.</span></span> <span data-ttu-id="ced03-168">開発環境で概念実証のためのイベント バスを実装するだけの場合は、eShopOnContainers サンプルで行ったように、Docker コンテナーで実行する RabbitMQ 上のシンプルな実装で十分です。</span><span class="sxs-lookup"><span data-stu-id="ced03-168">For implementing just a proof-of-concept event bus for your development environment, as it was done in the eShopOnContainers sample, a simple implementation on top of RabbitMQ running on a Docker container might be enough.</span></span>

<span data-ttu-id="ced03-169">ただし、非常に高いスケーラビリティが求められるミッションクリティカルな運用システムでは、Azure Service Bus を評価してもよいでしょう。</span><span class="sxs-lookup"><span data-stu-id="ced03-169">However, for mission-critical and production systems that need hyper-scalability, you might want to evaluate Azure Service Bus.</span></span> <span data-ttu-id="ced03-170">分散アプリケーションの開発を容易にする高レベルの抽象化と機能の場合には、NServiceBus、MassTransit、Brighter など市販やオープンソースのサービス バスも評価することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ced03-170">For high-level abstractions and features that make the development of distributed applications easier, we recommend that you evaluate other commercial and open-source service buses, such as NServiceBus, MassTransit, and Brighter.</span></span> <span data-ttu-id="ced03-171">もちろん、RabbitMQ や Docker など低レベル テクノロジの上に独自のサービスバス機能を構築することもできます。</span><span class="sxs-lookup"><span data-stu-id="ced03-171">Of course, you can build your own service-bus features on top of lower-level technologies like RabbitMQ and Docker.</span></span> <span data-ttu-id="ced03-172">ただし、そのような組み込み作業はカスタム エンタープライズ アプリケーションにとってコストがかかりすぎる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ced03-172">But that plumbing work might cost too much for a custom enterprise application.</span></span>

## <a name="resiliently-publishing-to-the-event-bus"></a><span data-ttu-id="ced03-173">イベント バスに対する弾力的なパブリッシュ</span><span class="sxs-lookup"><span data-stu-id="ced03-173">Resiliently publishing to the event bus</span></span>

<span data-ttu-id="ced03-174">複数のマイクロサービスにイベントドリブン アーキテクチャを実装する際の課題は、関連する統合イベントをイベント バスに弾力的にパブリッシュする一方、なんらかの方法でトランザクションに合わせて、どうやって元のマイクロサービスの状態をアトミックに更新するかということです。</span><span class="sxs-lookup"><span data-stu-id="ced03-174">A challenge when implementing an event-driven architecture across multiple microservices is how to atomically update state in the original microservice while resiliently publishing its related integration event into the event bus, somehow based on transactions.</span></span> <span data-ttu-id="ced03-175">これを実現するいくつかの方法を次に示しますが、他のアプローチも考えられます。</span><span class="sxs-lookup"><span data-stu-id="ced03-175">The following are a few ways to accomplish this, although there could be additional approaches as well.</span></span>

- <span data-ttu-id="ced03-176">MSMQ などのトランザクション (DTC ベース) キューを使用します。</span><span class="sxs-lookup"><span data-stu-id="ced03-176">Using a transactional (DTC-based) queue like MSMQ.</span></span> <span data-ttu-id="ced03-177">(ただし、これは従来のアプローチです。)</span><span class="sxs-lookup"><span data-stu-id="ced03-177">(However, this is a legacy approach.)</span></span>

- <span data-ttu-id="ced03-178">[トランザクション ログ マイニング](https://www.scoop.it/t/sql-server-transaction-log-mining)を使用します。</span><span class="sxs-lookup"><span data-stu-id="ced03-178">Using [transaction log mining](https://www.scoop.it/t/sql-server-transaction-log-mining).</span></span>

- <span data-ttu-id="ced03-179">完全な[イベント ソーシング パターン](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)を使用します。</span><span class="sxs-lookup"><span data-stu-id="ced03-179">Using full [Event Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) pattern.</span></span>

- <span data-ttu-id="ced03-180">[送信トレイ パターン](http://gistlabs.com/2014/05/the-outbox/)を使用します。これは、イベントを作成してパブリッシュするイベントクリエーター コンポーネントの基盤となる、メッセージ キューとしてのトランザクション データベース テーブルです。</span><span class="sxs-lookup"><span data-stu-id="ced03-180">Using the [Outbox pattern](http://gistlabs.com/2014/05/the-outbox/): a transactional database table as a message queue that will be the base for an event-creator component that would create the event and publish it.</span></span>

<span data-ttu-id="ced03-181">非同期通信を使用する際に考慮する必要がある他のトピックは、メッセージのべき等性とメッセージの重複除去です。</span><span class="sxs-lookup"><span data-stu-id="ced03-181">Additional topics to consider when using asynchronous communication are message idempotence and message deduplication.</span></span> <span data-ttu-id="ced03-182">これらのトピックについては、このガイドで後から説明する「[Implementing event-based communication between microservices (integration events)](../multi-container-microservice-net-applications/integration-event-based-microservice-communications.md)」(マイクロサービス (統合イベント) 間でのイベントベース通信の実装) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ced03-182">These topics are covered in the section [Implementing event-based communication between microservices (integration events)](../multi-container-microservice-net-applications/integration-event-based-microservice-communications.md) later in this guide.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ced03-183">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="ced03-183">Additional resources</span></span>

- <span data-ttu-id="ced03-184">**イベント駆動型メッセージング** </span><span class="sxs-lookup"><span data-stu-id="ced03-184">**Event Driven Messaging** </span></span>\
  <http://soapatterns.org/design_patterns/event_driven_messaging>

- <span data-ttu-id="ced03-185">**発行/サブスクライブ チャネル** </span><span class="sxs-lookup"><span data-stu-id="ced03-185">**Publish/Subscribe Channel** </span></span>\
  <https://www.enterpriseintegrationpatterns.com/patterns/messaging/PublishSubscribeChannel.html>

- <span data-ttu-id="ced03-186">**Udi Dahan。CQRS の明確化** </span><span class="sxs-lookup"><span data-stu-id="ced03-186">**Udi Dahan. Clarified CQRS** </span></span>\
  <http://udidahan.com/2009/12/09/clarified-cqrs/>

- <span data-ttu-id="ced03-187">**コマンド クエリ責務分離 (CQRS)**  </span><span class="sxs-lookup"><span data-stu-id="ced03-187">**Command and Query Responsibility Segregation (CQRS)** </span></span>\
  <https://docs.microsoft.com/azure/architecture/patterns/cqrs>

- <span data-ttu-id="ced03-188">**境界コンテキスト間の通信** </span><span class="sxs-lookup"><span data-stu-id="ced03-188">**Communicating Between Bounded Contexts** </span></span>\
  <https://docs.microsoft.com/previous-versions/msp-n-p/jj591572(v=pandp.10)>

- <span data-ttu-id="ced03-189">**最終的な整合性** </span><span class="sxs-lookup"><span data-stu-id="ced03-189">**Eventual consistency** </span></span>\
  <https://en.wikipedia.org/wiki/Eventual_consistency>

- <span data-ttu-id="ced03-190">**Jimmy Bogard。復元性を目指したリファクタリング: 結合の評価** </span><span class="sxs-lookup"><span data-stu-id="ced03-190">**Jimmy Bogard. Refactoring Towards Resilience: Evaluating Coupling** </span></span>\
  <https://jimmybogard.com/refactoring-towards-resilience-evaluating-coupling/>

> [!div class="step-by-step"]
> <span data-ttu-id="ced03-191">[前へ](communication-in-microservice-architecture.md)
> [次へ](maintain-microservice-apis.md)</span><span class="sxs-lookup"><span data-stu-id="ced03-191">[Previous](communication-in-microservice-architecture.md)
[Next](maintain-microservice-apis.md)</span></span>