---
title: 分散データ管理に関する課題と解決策
description: マイクロサービスの世界における分散データ管理に関する課題と解決策について説明します。
ms.date: 09/20/2018
ms.openlocfilehash: 7733a4523e147591151cd0dda26c43992dbe9a41
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68673139"
---
# <a name="challenges-and-solutions-for-distributed-data-management"></a><span data-ttu-id="a18ca-103">分散データ管理に関する課題と解決策</span><span class="sxs-lookup"><span data-stu-id="a18ca-103">Challenges and solutions for distributed data management</span></span>

## <a name="challenge-1-how-to-define-the-boundaries-of-each-microservice"></a><span data-ttu-id="a18ca-104">課題 \#1:各マイクロサービスの境界を定義する方法</span><span class="sxs-lookup"><span data-stu-id="a18ca-104">Challenge \#1: How to define the boundaries of each microservice</span></span>

<span data-ttu-id="a18ca-105">マイクロサービスの境界の定義は、おそらく、すべてのユーザーが直面する最初の課題です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-105">Defining microservice boundaries is probably the first challenge anyone encounters.</span></span> <span data-ttu-id="a18ca-106">各マイクロサービスはアプリケーションの一部である必要があり、各マイクロサービスはアプリケーションのすべての利点と課題に関して自律的である必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-106">Each microservice has to be a piece of your application and each microservice should be autonomous with all the benefits and challenges that it conveys.</span></span> <span data-ttu-id="a18ca-107">しかし、これらの境界をどのようにして識別すればよいのでしょうか。</span><span class="sxs-lookup"><span data-stu-id="a18ca-107">But how do you identify those boundaries?</span></span>

<span data-ttu-id="a18ca-108">最初に、アプリケーションの論理ドメイン モデルと関連するデータに注目する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-108">First, you need to focus on the application's logical domain models and related data.</span></span> <span data-ttu-id="a18ca-109">同じアプリケーション内で、切り離されて孤立したデータと異なるコンテキストの識別を試みてください。</span><span class="sxs-lookup"><span data-stu-id="a18ca-109">Try to identify decoupled islands of data and different contexts within the same application.</span></span> <span data-ttu-id="a18ca-110">各コンテキストは、異なるビジネス言語 (異なるビジネス用語) を使っている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-110">Each context could have a different business language (different business terms).</span></span> <span data-ttu-id="a18ca-111">コンテキストを独立して定義し、管理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-111">The contexts should be defined and managed independently.</span></span> <span data-ttu-id="a18ca-112">異なるコンテキストで使われている用語やエンティティが同じように聞こえることがあるかもしれませんが、特定のコンテキストでは、同じビジネス概念でもコンテキストによって使用目的が異なることがあり、名前が異なる場合さえあります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-112">The terms and entities that are used in those different contexts might sound similar, but you might discover that in a particular context, a business concept with one is used for a different purpose in another context, and might even have a different name.</span></span> <span data-ttu-id="a18ca-113">たとえば、ユーザーは、ID またはメンバーシップのコンテキストではユーザー、CRM のコンテキストではお客様、注文のコンテキストでは購入者、などと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-113">For instance, a user can be referred as a user in the identity or membership context, as a customer in a CRM context, as a buyer in an ordering context, and so forth.</span></span>

<span data-ttu-id="a18ca-114">それぞれドメインが異なる複数のアプリケーション コンテキスト間の境界を識別する方法は、まさに、各ビジネス マイクロサービスおよびその関連するドメイン モデルとデータの境界を識別できる方法です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-114">The way you identify boundaries between multiple application contexts with a different domain for each context is exactly how you can identify the boundaries for each business microservice and its related domain model and data.</span></span> <span data-ttu-id="a18ca-115">常に、それらのマイクロサービス間の結び付きを最小限にすることを試みます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-115">You always attempt to minimize the coupling between those microservices.</span></span> <span data-ttu-id="a18ca-116">この識別とドメイン モデルの設計については、「[マイクロサービスごとにドメイン モデルの境界を識別する](identify-microservice-domain-model-boundaries.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a18ca-116">This guide goes into more detail about this identification and domain model design in the section [Identifying domain-model boundaries for each microservice](identify-microservice-domain-model-boundaries.md) later.</span></span>

## <a name="challenge-2-how-to-create-queries-that-retrieve-data-from-several-microservices"></a><span data-ttu-id="a18ca-117">課題 \#2:複数のマイクロサービスからデータを取得するクエリを作成する方法</span><span class="sxs-lookup"><span data-stu-id="a18ca-117">Challenge \#2: How to create queries that retrieve data from several microservices</span></span>

<span data-ttu-id="a18ca-118">2 番目の課題は、リモート クライアント アプリからマイクロサービスへの通信が多くなりすぎるのを防ぎながら、複数のマイクロサービスからデータを取得するクエリを実装する方法です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-118">A second challenge is how to implement queries that retrieve data from several microservices, while avoiding chatty communication to the microservices from remote client apps.</span></span> <span data-ttu-id="a18ca-119">たとえば、バスケット、カタログ、ユーザー ID の各マイクロサービスによって所有されているユーザー情報を表示する必要がある、モバイル アプリの 1 つの画面のような場合です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-119">An example could be a single screen from a mobile app that needs to show user information that's owned by the basket, catalog, and user identity microservices.</span></span> <span data-ttu-id="a18ca-120">または、複数のマイクロサービスに存在する多数のテーブルが関係する複雑なレポートもそのような例です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-120">Another example would be a complex report involving many tables located in multiple microservices.</span></span> <span data-ttu-id="a18ca-121">適切な解決策はクエリの複雑さによって異なります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-121">The right solution depends on the complexity of the queries.</span></span> <span data-ttu-id="a18ca-122">ただし、どのような場合でも、システムの通信効率を向上させるには、情報を集計する方法が必要になります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-122">But in any case, you'll need a way to aggregate information if you want to improve the efficiency in the communications of your system.</span></span> <span data-ttu-id="a18ca-123">最も一般的な解決策を以下に示します。</span><span class="sxs-lookup"><span data-stu-id="a18ca-123">The most popular solutions are the following.</span></span>

<span data-ttu-id="a18ca-124">**API ゲートウェイ。**</span><span class="sxs-lookup"><span data-stu-id="a18ca-124">**API Gateway.**</span></span> <span data-ttu-id="a18ca-125">異なるデータベースを所有する複数のマイクロサービスから単純にデータを集計する場合の推奨される方法は、API ゲートウェイと呼ばれる集計マイクロサービスです。</span><span class="sxs-lookup"><span data-stu-id="a18ca-125">For simple data aggregation from multiple microservices that own different databases, the recommended approach is an aggregation microservice referred to as an API Gateway.</span></span> <span data-ttu-id="a18ca-126">ただし、このパターンは、システムのボトルネックになったり、マイクロサービスの自律性の原則に違反する可能性があるため、実装するときに注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-126">However, you need to be careful about implementing this pattern, because it can be a choke point in your system, and it can violate the principle of microservice autonomy.</span></span> <span data-ttu-id="a18ca-127">このような可能性を減らすには、システムの垂直方向の "スライス" またはビジネス領域に焦点を当てて細かく調整された複数の API ゲートウェイを使います。</span><span class="sxs-lookup"><span data-stu-id="a18ca-127">To mitigate this possibility, you can have multiple fined-grained API Gateways each one focusing on a vertical "slice" or business area of the system.</span></span> <span data-ttu-id="a18ca-128">API ゲートウェイ パターンに関する説明は、後述の「[API ゲートウェイ](direct-client-to-microservice-communication-versus-the-api-gateway-pattern.md#why-consider-api-gateways-instead-of-direct-client-to-microservice-communication)」セクションにあります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-128">The API Gateway pattern is explained in more detail in the [API Gateway section](direct-client-to-microservice-communication-versus-the-api-gateway-pattern.md#why-consider-api-gateways-instead-of-direct-client-to-microservice-communication) later.</span></span>

<span data-ttu-id="a18ca-129">**クエリ/読み取りテーブルを使う CQRS。**</span><span class="sxs-lookup"><span data-stu-id="a18ca-129">**CQRS with query/reads tables.**</span></span> <span data-ttu-id="a18ca-130">複数のマイクロサービスからデータを集計するもう 1 つの解決策は、[具体化されたビュー パターン](https://docs.microsoft.com/azure/architecture/patterns/materialized-view)です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-130">Another solution for aggregating data from multiple microservices is the [Materialized View pattern](https://docs.microsoft.com/azure/architecture/patterns/materialized-view).</span></span> <span data-ttu-id="a18ca-131">この方法では、複数のマイクロサービスによって所有されているデータを含む読み取り専用テーブルを、事前に (実際のクエリが行われる前に非正規化データを準備します) 生成します。</span><span class="sxs-lookup"><span data-stu-id="a18ca-131">In this approach, you generate, in advance (prepare denormalized data before the actual queries happen), a read-only table with the data that's owned by multiple microservices.</span></span> <span data-ttu-id="a18ca-132">このテーブルはクライアント アプリのニーズに合った形式にします。</span><span class="sxs-lookup"><span data-stu-id="a18ca-132">The table has a format suited to the client app's needs.</span></span>

<span data-ttu-id="a18ca-133">モバイル アプリの画面のようなものを検討します。</span><span class="sxs-lookup"><span data-stu-id="a18ca-133">Consider something like the screen for a mobile app.</span></span> <span data-ttu-id="a18ca-134">データベースが 1 つだけの場合は、複数のテーブルを含む複雑な結合を実行する SQL クエリを使って、その画面のデータをまとめて取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-134">If you have a single database, you might pull together the data for that screen using a SQL query that performs a complex join involving multiple tables.</span></span> <span data-ttu-id="a18ca-135">しかし、複数のデータベースがあり、各データベースが異なるマイクロサービスによって所有されている場合は、それらのデータベースに対するクエリを実行して、SQL 結合を作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="a18ca-135">However, when you have multiple databases, and each database is owned by a different microservice, you cannot query those databases and create a SQL join.</span></span> <span data-ttu-id="a18ca-136">複雑なクエリは困難になります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-136">Your complex query becomes a challenge.</span></span> <span data-ttu-id="a18ca-137">CQRS アプローチを使って、要件に対処することができます。クエリのためだけに使う異なるデータベースに、非正規化されたテーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="a18ca-137">You can address the requirement using a CQRS approach—you create a denormalized table in a different database that's used just for queries.</span></span> <span data-ttu-id="a18ca-138">テーブルは複雑なクエリに必要なデータ専用に設計でき、アプリケーションの画面で必要なフィールドとクエリ テーブルの列の間が、一対一のリレーションシップになるようにします。</span><span class="sxs-lookup"><span data-stu-id="a18ca-138">The table can be designed specifically for the data you need for the complex query, with a one-to-one relationship between fields needed by your application's screen and the columns in the query table.</span></span> <span data-ttu-id="a18ca-139">レポート用に使うこともできます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-139">It could also serve for reporting purposes.</span></span>

<span data-ttu-id="a18ca-140">このアプローチは、元の問題 (複数のマイクロサービス間でクエリと結合を行う方法) を解決するだけでなく、アプリケーションに必要なデータがクエリ テーブルに既にあるため、複雑な結合と比較してパフォーマンスも大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="a18ca-140">This approach not only solves the original problem (how to query and join across microservices), but it also improves performance considerably when compared with a complex join, because you already have the data that the application needs in the query table.</span></span> <span data-ttu-id="a18ca-141">もちろん、クエリ/読み取りテーブルによる CQRS (Command and Query Responsibility Segregation: コマンド クエリ責務分離) を使うと、開発作業が増えるので、最終的な整合性を採用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-141">Of course, using Command and Query Responsibility Segregation (CQRS) with query/reads tables means additional development work, and you'll need to embrace eventual consistency.</span></span> <span data-ttu-id="a18ca-142">それでも、[コラボレーション シナリオ](http://udidahan.com/2011/10/02/why-you-should-be-using-cqrs-almost-everywhere/) (または観点によっては競合シナリオ) でパフォーマンスと高スケーラビリティが必要な場合は、複数データベースでの CQRS を適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-142">Nonetheless, requirements on performance and high scalability in [collaborative scenarios](http://udidahan.com/2011/10/02/why-you-should-be-using-cqrs-almost-everywhere/) (or competitive scenarios, depending on the point of view) are where you should apply CQRS with multiple databases.</span></span>

<span data-ttu-id="a18ca-143">**中央データベースの "コールド データ"。**</span><span class="sxs-lookup"><span data-stu-id="a18ca-143">**"Cold data" in central databases.**</span></span> <span data-ttu-id="a18ca-144">リアルタイムのデータを必要としない複雑なレポートとクエリの場合の一般的な方法は、"ホット データ" (マイクロサービスからのトランザクション データ) を、レポート専用の大きなデータベースに "コールド データ" としてエクスポートすることです。</span><span class="sxs-lookup"><span data-stu-id="a18ca-144">For complex reports and queries that might not require real-time data, a common approach is to export your "hot data" (transactional data from the microservices) as "cold data" into large databases that are used only for reporting.</span></span> <span data-ttu-id="a18ca-145">中央データベース システムとしては、Hadoop のようなビッグ データ ベースのシステムや Azure SQL Data Warehouse に基づくもののようなデータ ウェアハウスを使うことができ、(サイズが問題にならない場合は) レポートだけに使う単一の SQL データベースでもかまいません。</span><span class="sxs-lookup"><span data-stu-id="a18ca-145">That central database system can be a Big Data-based system, like Hadoop, a data warehouse like one based on Azure SQL Data Warehouse, or even a single SQL database that's used just for reports (if size won't be an issue).</span></span>

<span data-ttu-id="a18ca-146">この集中データベースはリアルタイム データを必要としないクエリおよびレポートのみに使うことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a18ca-146">Keep in mind that this centralized database would be used only for queries and reports that do not need real-time data.</span></span> <span data-ttu-id="a18ca-147">真実を語る資料としての元の更新およびトランザクションは、マイクロサービス データ内に存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-147">The original updates and transactions, as your source of truth, have to be in your microservices data.</span></span> <span data-ttu-id="a18ca-148">データ同期の方法としては、イベント ドリブンの通信 (次のセクションで説明します)、またはデータベース インフラストラクチャの他のインポート/エクスポート ツールを使います。</span><span class="sxs-lookup"><span data-stu-id="a18ca-148">The way you would synchronize data would be either by using event-driven communication (covered in the next sections) or by using other database infrastructure import/export tools.</span></span> <span data-ttu-id="a18ca-149">イベント ドリブンの通信を使う場合、その統合プロセスは、前に説明した CQRS クエリ テーブルでのデータ伝達方法と似たものになります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-149">If you use event-driven communication, that integration process would be similar to the way you propagate data as described earlier for CQRS query tables.</span></span>

<span data-ttu-id="a18ca-150">ただし、複雑なクエリのために複数のマイクロサービスから頻繁に情報を集計するようなアプリケーションの設計は、悪い設計の兆候である可能性があります。マイクロサービスは、可能な限り他のマイクロサービスから分離する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-150">However, if your application design involves constantly aggregating information from multiple microservices for complex queries, it might be a symptom of a bad design -a microservice should be as isolated as possible from other microservices.</span></span> <span data-ttu-id="a18ca-151">(レポート/分析の場合はコールド データの中央データベースを常に使う必要があるので、これには含まれません)。この問題が多く発生するときは、マイクロサービスのマージが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-151">(This excludes reports/analytics that always should use cold-data central databases.) Having this problem often might be a reason to merge microservices.</span></span> <span data-ttu-id="a18ca-152">各マイクロサービスの発展とデプロイの自律性と、強い依存性、凝集度、データ集計の間のバランスをとる必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-152">You need to balance the autonomy of evolution and deployment of each microservice with strong dependencies, cohesion, and data aggregation.</span></span>

## <a name="challenge-3-how-to-achieve-consistency-across-multiple-microservices"></a><span data-ttu-id="a18ca-153">課題 \#3:複数のマイクロサービス間の整合性を実現する方法</span><span class="sxs-lookup"><span data-stu-id="a18ca-153">Challenge \#3: How to achieve consistency across multiple microservices</span></span>

<span data-ttu-id="a18ca-154">前に説明したように、各マイクロサービスが所有するデータはそのマイクロサービスにプライベートなものであり、そのマイクロサービスの API を使うことによってのみアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-154">As stated previously, the data owned by each microservice is private to that microservice and can only be accessed using its microservice API.</span></span> <span data-ttu-id="a18ca-155">そのため、複数のマイクロサービスの間で整合性を維持しながら、エンド ツー エンドのビジネス プロセスを実装する方法に関する課題があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-155">Therefore, a challenge presented is how to implement end-to-end business processes while keeping consistency across multiple microservices.</span></span>

<span data-ttu-id="a18ca-156">この問題を分析するため、[eShopOnContainers 参照アプリケーション](https://aka.ms/eshoponcontainers)の例を見てみます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-156">To analyze this problem, let's look at an example from the [eShopOnContainers reference application](https://aka.ms/eshoponcontainers).</span></span> <span data-ttu-id="a18ca-157">Catalog マイクロサービスは、製品価格など、すべての製品に関する情報を保持しています。</span><span class="sxs-lookup"><span data-stu-id="a18ca-157">The Catalog microservice maintains information about all the products, including the product price.</span></span> <span data-ttu-id="a18ca-158">Basket マイクロサービスは、利用者が買い物かごに追加する製品品目に関するテンポラル データを管理します。かごに追加された時点における品目の価格などです。</span><span class="sxs-lookup"><span data-stu-id="a18ca-158">The Basket microservice manages temporal data about product items that users are adding to their shopping baskets, which includes the price of the items at the time they were added to the basket.</span></span> <span data-ttu-id="a18ca-159">製品の価格がカタログで更新されると、その価格は、同じ製品が入っている有効な買い物かごでも更新されます。また、おそらくは、買い物かごに追加した後に品目の価格が変更されたことが利用者に通知されます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-159">When a product's price is updated in the catalog, that price should also be updated in the active baskets that hold that same product, plus the system should probably warn the user saying that a particular item's price has changed since they added it to their basket.</span></span>

<span data-ttu-id="a18ca-160">このアプリケーションが仮説上、一体構造であれば、価格テーブルの価格が変更されたとき、カタログ サブシステムでは単純に ACID トランザクションを利用し、買い物かごテーブルの現在の価格を更新することがあります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-160">In a hypothetical monolithic version of this application, when the price changes in the products table, the catalog subsystem could simply use an ACID transaction to update the current price in the Basket table.</span></span>

<span data-ttu-id="a18ca-161">一方、マイクロサービス ベースのアプリケーションでは、Product テーブルと Basket テーブルは対応するマイクロサービスによって所有されています。</span><span class="sxs-lookup"><span data-stu-id="a18ca-161">However, in a microservices-based application, the Product and Basket tables are owned by their respective microservices.</span></span> <span data-ttu-id="a18ca-162">どのマイクロサービスのトランザクションにも (直接のクエリでも)、別のマイクロサービスによって所有されるテーブル/ストレージが含まれることはありません (図 4-9 を参照)。</span><span class="sxs-lookup"><span data-stu-id="a18ca-162">No microservice should ever include tables/storage owned by another microservice in its own transactions, not even in direct queries, as shown in Figure 4-9.</span></span>

![マイクロサービスは別のマイクロサービスのテーブルに直接アクセスできません。データの同期には最終的な整合性を使用する必要があります。](./media/image9.png)

<span data-ttu-id="a18ca-164">**図 4-9**</span><span class="sxs-lookup"><span data-stu-id="a18ca-164">**Figure 4-9**.</span></span> <span data-ttu-id="a18ca-165">マイクロサービスは、別のマイクロサービスのテーブルに直接アクセスすることはできません。</span><span class="sxs-lookup"><span data-stu-id="a18ca-165">A microservice can't directly access a table in another microservice</span></span>

<span data-ttu-id="a18ca-166">Basket テーブルは Basket マイクロサービスによって所有されているので、Catalog マイクロサービスが Basket テーブルを直接更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="a18ca-166">The Catalog microservice shouldn't update the Basket table directly, because the Basket table is owned by the Basket microservice.</span></span> <span data-ttu-id="a18ca-167">Basket マイクロサービスを更新するには、Catalog マイクロサービスが最終的な整合性を使用する必要があります。この整合性はおそらく、統合イベント (メッセージとイベント ベースの通信) などの非同期通信を基盤とします。</span><span class="sxs-lookup"><span data-stu-id="a18ca-167">To make an update to the Basket microservice, the Catalog microservice should use eventual consistency probably based on asynchronous communication such as integration events (message and event-based communication).</span></span> <span data-ttu-id="a18ca-168">これは、[eShopOnContainers](https://aka.ms/eshoponcontainers) 参照アプリケーションがマイクロサービス全体でこの種類の整合性を実行している方法です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-168">This is how the [eShopOnContainers](https://aka.ms/eshoponcontainers) reference application performs this type of consistency across microservices.</span></span>

<span data-ttu-id="a18ca-169">[CAP 定理](https://en.wikipedia.org/wiki/CAP_theorem)で説明されているように、可用性と ACID の厳密な整合性のどちらかを選ぶ必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-169">As stated by the [CAP theorem](https://en.wikipedia.org/wiki/CAP_theorem), you need to choose between availability and ACID strong consistency.</span></span> <span data-ttu-id="a18ca-170">マイクロサービス ベースのほとんどのシナリオでは、厳密な整合性より可用性と高いスケーラビリティが要求されます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-170">Most microservice-based scenarios demand availability and high scalability as opposed to strong consistency.</span></span> <span data-ttu-id="a18ca-171">ミッション クリティカルなアプリケーションは稼働状態を維持する必要があり、開発者は弱い整合性または最終的な整合性を操作する手法を使って、厳密な整合性を回避できます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-171">Mission-critical applications must remain up and running, and developers can work around strong consistency by using techniques for working with weak or eventual consistency.</span></span> <span data-ttu-id="a18ca-172">ほとんどのマイクロサービス ベースのアーキテクチャでは、この方法が採用されています。</span><span class="sxs-lookup"><span data-stu-id="a18ca-172">This is the approach taken by most microservice-based architectures.</span></span>

<span data-ttu-id="a18ca-173">さらに、ACID スタイルのトランザクションや 2 フェーズ コミット トランザクションは、マイクロサービスの原則に反しているだけではありません。ほとんどの NoSQL データベース (Azure Cosmos DB、MongoDB など) は、分散型データベースで一般的な 2 フェーズ コミット トランザクションをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="a18ca-173">Moreover, ACID-style or two-phase commit transactions are not just against microservices principles; most NoSQL databases (like Azure Cosmos DB, MongoDB, etc.) do not support two-phase commit transactions, typical in distributed databases scenarios.</span></span> <span data-ttu-id="a18ca-174">ただし、サービス間およびデータベース間でデータの整合性を維持することは不可欠です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-174">However, maintaining data consistency across services and databases is essential.</span></span> <span data-ttu-id="a18ca-175">この課題は、特定のデータを冗長にする必要がある場合に、複数のマイクロサービスの間で変更を反映する方法という質問にも関連します。たとえば、製品の名前や説明を Catalog マイクロサービスと Basket マイクロサービスで保持する必要があるような場合です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-175">This challenge is also related to the question of how to propagate changes across multiple microservices when certain data needs to be redundant—for example, when you need to have the product's name or description in the Catalog microservice and the Basket microservice.</span></span>

<span data-ttu-id="a18ca-176">この問題に適した解決策は、イベント ドリブンの通信およびパブリッシュとサブスクライブ システムを通じて統合されたマイクロサービスの間の最終的整合性を使うことです。</span><span class="sxs-lookup"><span data-stu-id="a18ca-176">A good solution for this problem is to use eventual consistency between microservices articulated through event-driven communication and a publish-and-subscribe system.</span></span> <span data-ttu-id="a18ca-177">これらのトピックについては、「[Asynchronous event-driven communication](asynchronous-message-based-communication.md#asynchronous-event-driven-communication)」(非同期のイベント ドリブン通信) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a18ca-177">These topics are covered in the section [Asynchronous event-driven communication](asynchronous-message-based-communication.md#asynchronous-event-driven-communication) later in this guide.</span></span>

## <a name="challenge-4-how-to-design-communication-across-microservice-boundaries"></a><span data-ttu-id="a18ca-178">課題 \#4:マイクロサービスの境界を越える通信を設計する方法</span><span class="sxs-lookup"><span data-stu-id="a18ca-178">Challenge \#4: How to design communication across microservice boundaries</span></span>

<span data-ttu-id="a18ca-179">マイクロサービスの境界をまたいだ通信は、真の課題です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-179">Communicating across microservice boundaries is a real challenge.</span></span> <span data-ttu-id="a18ca-180">このコンテキストでは、通信とは使う必要のあるプロトコル (HTTP と REST、AMQP、メッセージングなど) のことではありません。</span><span class="sxs-lookup"><span data-stu-id="a18ca-180">In this context, communication doesn't refer to what protocol you should use (HTTP and REST, AMQP, messaging, and so on).</span></span> <span data-ttu-id="a18ca-181">そうではなくて、使う必要のある通信スタイル、特にマイクロサービス同士を結合する方法のことです。</span><span class="sxs-lookup"><span data-stu-id="a18ca-181">Instead, it addresses what communication style you should use, and especially how coupled your microservices should be.</span></span> <span data-ttu-id="a18ca-182">結合のレベルにより、障害が発生したときのシステムへの影響が大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-182">Depending on the level of coupling, when failure occurs, the impact of that failure on your system will vary significantly.</span></span>

<span data-ttu-id="a18ca-183">マイクロサービスに基づくアプリケーションのような分散システムでは、非常に多くのアーティファクトが動き回り、多くのサーバーやホストにサービスが分散しており、コンポーネントが最終的に失敗します。</span><span class="sxs-lookup"><span data-stu-id="a18ca-183">In a distributed system like a microservices-based application, with so many artifacts moving around and with distributed services across many servers or hosts, components will eventually fail.</span></span> <span data-ttu-id="a18ca-184">部分的な障害やさらに大きな停止が発生するので、このタイプの分散システムにおいて一般的なリスクを考慮して、マイクロサービスおよびマイクロサービス間の通信を設計する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-184">Partial failure and even larger outages will occur, so you need to design your microservices and the communication across them considering the common risks in this type of distributed system.</span></span>

<span data-ttu-id="a18ca-185">一般的な方法は、簡単であるという理由から、HTTP (REST) ベースのマイクロサービスを実装することです。</span><span class="sxs-lookup"><span data-stu-id="a18ca-185">A popular approach is to implement HTTP (REST)-based microservices, due to their simplicity.</span></span> <span data-ttu-id="a18ca-186">HTTP ベースの方法は何の問題もなく受け入れられます。ここでの問題はその使い方に関係します。</span><span class="sxs-lookup"><span data-stu-id="a18ca-186">An HTTP-based approach is perfectly acceptable; the issue here is related to how you use it.</span></span> <span data-ttu-id="a18ca-187">クライアント アプリケーションまたは API ゲートウェイからマイクロサービスとの対話だけに HTTP 要求および応答を使うのであれば、問題はありません。</span><span class="sxs-lookup"><span data-stu-id="a18ca-187">If you use HTTP requests and responses just to interact with your microservices from client applications or from API Gateways, that's fine.</span></span> <span data-ttu-id="a18ca-188">しかし、複数のマイクロサービスの間に同期 HTTP 呼び出しの長いチェーンを作成する場合は、マイクロサービスがモノリシック アプリケーション内のオブジェクトであるかのように境界をまたがって通信を行い、アプリケーションで最終的に問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="a18ca-188">But if you create long chains of synchronous HTTP calls across microservices, communicating across their boundaries as if the microservices were objects in a monolithic application, your application will eventually run into problems.</span></span>

<span data-ttu-id="a18ca-189">たとえば、クライアント アプリケーションが Ordering マイクロサービスのような個別のマイクロサービスに対して HTTP API 呼び出しを行うものとします。</span><span class="sxs-lookup"><span data-stu-id="a18ca-189">For instance, imagine that your client application makes an HTTP API call to an individual microservice like the Ordering microservice.</span></span> <span data-ttu-id="a18ca-190">Ordering マイクロサービスが同じ要求/応答サイクル内で HTTP を使ってさらに他のマイクロサービスを呼び出す場合、HTTP 呼び出しのチェーンが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-190">If the Ordering microservice in turn calls additional microservices using HTTP within the same request/response cycle, you're creating a chain of HTTP calls.</span></span> <span data-ttu-id="a18ca-191">最初は問題ないように思うかもしれません。</span><span class="sxs-lookup"><span data-stu-id="a18ca-191">It might sound reasonable initially.</span></span> <span data-ttu-id="a18ca-192">しかし、この過程では考慮する必要のある重要な点があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-192">However, there are important points to consider when going down this path:</span></span>

- <span data-ttu-id="a18ca-193">ブロックと低パフォーマンス。</span><span class="sxs-lookup"><span data-stu-id="a18ca-193">Blocking and low performance.</span></span> <span data-ttu-id="a18ca-194">HTTP の同期特性により、すべての内部 HTTP 呼び出しが終了するまで、最初の要求は応答を受け取りません。</span><span class="sxs-lookup"><span data-stu-id="a18ca-194">Due to the synchronous nature of HTTP, the original request doesn't get a response until all the internal HTTP calls are finished.</span></span> <span data-ttu-id="a18ca-195">このような呼び出しの数が大幅に増加すると同時に、中間にあるマイクロサービスの HTTP 呼び出しの 1 つがブロックされた場合を考えてみてください。</span><span class="sxs-lookup"><span data-stu-id="a18ca-195">Imagine if the number of these calls increases significantly and at the same time one of the intermediate HTTP calls to a microservice is blocked.</span></span> <span data-ttu-id="a18ca-196">結果としてパフォーマンスに影響があり、さらに HTTP 要求が増えると全体的なスケーラビリティは指数関数的に影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-196">The result is that performance is impacted, and the overall scalability will be exponentially affected as additional HTTP requests increase.</span></span>

- <span data-ttu-id="a18ca-197">マイクロサービスと HTTP の結合。</span><span class="sxs-lookup"><span data-stu-id="a18ca-197">Coupling microservices with HTTP.</span></span> <span data-ttu-id="a18ca-198">ビジネス マイクロサービスは、他のビジネス マイクロサービスと結合されてはなりません。</span><span class="sxs-lookup"><span data-stu-id="a18ca-198">Business microservices shouldn't be coupled with other business microservices.</span></span> <span data-ttu-id="a18ca-199">他のマイクロサービスの存在について "知らない" ことが理想です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-199">Ideally, they shouldn't "know" about the existence of other microservices.</span></span> <span data-ttu-id="a18ca-200">例のようにアプリケーションが結合しているマイクロサービスに依存している場合、マイクロサービスごとの自律性を実現することはほとんど不可能です。</span><span class="sxs-lookup"><span data-stu-id="a18ca-200">If your application relies on coupling microservices as in the example, achieving autonomy per microservice will be almost impossible.</span></span>

- <span data-ttu-id="a18ca-201">いずれか 1 つのマイクロサービスにおける障害。</span><span class="sxs-lookup"><span data-stu-id="a18ca-201">Failure in any one microservice.</span></span> <span data-ttu-id="a18ca-202">HTTP 呼び出しによってリンクされたマイクロサービスのチェーンを実装した場合、いずれかのマイクロサービスで障害が発生すると (そして最終的に複数のマイクロサービスが障害になると)、マイクロサービスのチェーン全体が障害になります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-202">If you implemented a chain of microservices linked by HTTP calls, when any of the microservices fails (and eventually they will fail) the whole chain of microservices will fail.</span></span> <span data-ttu-id="a18ca-203">マイクロサービス ベースのシステムは、部分的な障害の間も可能な限り動作し続けるように設計する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-203">A microservice-based system should be designed to continue to work as well as possible during partial failures.</span></span> <span data-ttu-id="a18ca-204">指数バックオフの再試行またはサーキット ブレーカー メカニズムを使うクライアント ロジックを実装する場合でも、HTTP 呼び出しチェーンが複雑になるほど、HTTP に基づく障害戦略の実装も複雑になります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-204">Even if you implement client logic that uses retries with exponential backoff or circuit breaker mechanisms, the more complex the HTTP call chains are, the more complex it is to implement a failure strategy based on HTTP.</span></span>

<span data-ttu-id="a18ca-205">実際、説明したように HTTP 要求のチェーンを作成することで内部マイクロサービスが通信している場合、プロセス内通信メカニズムではなくプロセス間の HTTP に基づくモノリシック アプリケーションであると言うことができます。</span><span class="sxs-lookup"><span data-stu-id="a18ca-205">In fact, if your internal microservices are communicating by creating chains of HTTP requests as described, it could be argued that you have a monolithic application, but one based on HTTP between processes instead of intra-process communication mechanisms.</span></span>

<span data-ttu-id="a18ca-206">したがって、マイクロサービスの自律性を適用して復元性を向上させるには、マイクロサービス間の要求/応答通信のチェーンの使用を最小限にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a18ca-206">Therefore, in order to enforce microservice autonomy and have better resiliency, you should minimize the use of chains of request/response communication across microservices.</span></span> <span data-ttu-id="a18ca-207">非同期メッセージとイベント ベースの通信を使うか、元の HTTP 要求/応答サイクルとは独立して (非同期) HTTP ポーリングを使うことにより、マイクロサービス間の通信には非同期対話のみを使うことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a18ca-207">It's recommended that you use only asynchronous interaction for inter-microservice communication, either by using asynchronous message- and event-based communication, or by using (asynchronous) HTTP polling independently of the original HTTP request/response cycle.</span></span>

<span data-ttu-id="a18ca-208">非同期通信の使用については、「[マイクロ サービスの自律性を強制する非同期マイクロ サービスの統合](communication-in-microservice-architecture.md#asynchronous-microservice-integration-enforces-microservices-autonomy)」および「[Asynchronous message-based communication](asynchronous-message-based-communication.md)」 (非同期メッセージ ベースの通信) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a18ca-208">The use of asynchronous communication is explained with additional details later in this guide in the sections [Asynchronous microservice integration enforces microservice's autonomy](communication-in-microservice-architecture.md#asynchronous-microservice-integration-enforces-microservices-autonomy) and [Asynchronous message-based communication](asynchronous-message-based-communication.md).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a18ca-209">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="a18ca-209">Additional resources</span></span>

- <span data-ttu-id="a18ca-210">**CAP 定理** </span><span class="sxs-lookup"><span data-stu-id="a18ca-210">**CAP theorem** </span></span>\
  <https://en.wikipedia.org/wiki/CAP_theorem>

- <span data-ttu-id="a18ca-211">**最終的な整合性** </span><span class="sxs-lookup"><span data-stu-id="a18ca-211">**Eventual consistency** </span></span>\
  <https://en.wikipedia.org/wiki/Eventual_consistency>

- <span data-ttu-id="a18ca-212">**データ整合性の概要** </span><span class="sxs-lookup"><span data-stu-id="a18ca-212">**Data Consistency Primer** </span></span>\
  <https://docs.microsoft.com/previous-versions/msp-n-p/dn589800(v=pandp.10)>

- <span data-ttu-id="a18ca-213">**Martin Fowler。コマンド クエリ責務分離 (CQRS)**  </span><span class="sxs-lookup"><span data-stu-id="a18ca-213">**Martin Fowler. CQRS (Command and Query Responsibility Segregation)** </span></span>\
  <https://martinfowler.com/bliki/CQRS.html>

- <span data-ttu-id="a18ca-214">**具体化されたビュー** </span><span class="sxs-lookup"><span data-stu-id="a18ca-214">**Materialized View** </span></span>\
  <https://docs.microsoft.com/azure/architecture/patterns/materialized-view>

- <span data-ttu-id="a18ca-215">**Charles Row。ACID とBASE:データベース トランザクション処理での pH のシフト** </span><span class="sxs-lookup"><span data-stu-id="a18ca-215">**Charles Row. ACID vs. BASE: The Shifting pH of Database Transaction Processing** </span></span>\
  <https://www.dataversity.net/acid-vs-base-the-shifting-ph-of-database-transaction-processing/>

- <span data-ttu-id="a18ca-216">**補正トランザクション** </span><span class="sxs-lookup"><span data-stu-id="a18ca-216">**Compensating Transaction** </span></span>\
  <https://docs.microsoft.com/azure/architecture/patterns/compensating-transaction>

- <span data-ttu-id="a18ca-217">**Udi Dahan。サービス指向のコンポジション** </span><span class="sxs-lookup"><span data-stu-id="a18ca-217">**Udi Dahan. Service Oriented Composition** </span></span>\
  <http://udidahan.com/2014/07/30/service-oriented-composition-with-video/>

>[!div class="step-by-step"]
><span data-ttu-id="a18ca-218">[前へ](logical-versus-physical-architecture.md)
>[次へ](identify-microservice-domain-model-boundaries.md)</span><span class="sxs-lookup"><span data-stu-id="a18ca-218">[Previous](logical-versus-physical-architecture.md)
[Next](identify-microservice-domain-model-boundaries.md)</span></span>