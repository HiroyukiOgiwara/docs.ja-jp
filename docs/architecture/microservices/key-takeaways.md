---
title: 重要な習得事項
description: コンテナー化された .NET アプリケーションのための .NET マイクロサービス アーキテクチャに関するガイド/電子書籍の重要なポイントを取り上げ、マイクロサービス アーキテクチャを使用する際に発生する高レベルの問題の概要 (長所と短所、設計と開発の DDD パターン、回復性、セキュリティ、オーケストレーターの使用など) を説明します。
ms.date: 10/19/2018
ms.openlocfilehash: 3b8b7be9b3903c64221cba7c6abdb1e38f5d944f
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68674459"
---
# <a name="key-takeaways"></a><span data-ttu-id="9ab20-103">重要な習得事項</span><span class="sxs-lookup"><span data-stu-id="9ab20-103">Key Takeaways</span></span>

<span data-ttu-id="9ab20-104">概要および重要な習得事項として、次にこのガイドの最も重要な結論を示します。</span><span class="sxs-lookup"><span data-stu-id="9ab20-104">As a summary and key takeaways, the following are the most important conclusions from this guide.</span></span>

<span data-ttu-id="9ab20-105">**コンテナーを使用するメリット**</span><span class="sxs-lookup"><span data-stu-id="9ab20-105">**Benefits of using containers.**</span></span> <span data-ttu-id="9ab20-106">コンテナーベースのソリューションは、運用環境での依存関係の欠如によって発生する導入の問題を減らすために役立つため、大幅なコスト削減につながります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-106">Container-based solutions provide important cost savings because they help reduce deployment problems caused by failing dependencies in production environments.</span></span> <span data-ttu-id="9ab20-107">コンテナーは、DevOps および運用操作を大幅に向上させます。</span><span class="sxs-lookup"><span data-stu-id="9ab20-107">Containers significantly improve DevOps and production operations.</span></span>

<span data-ttu-id="9ab20-108">**コンテナーは、至る所で使用されます。**</span><span class="sxs-lookup"><span data-stu-id="9ab20-108">**Containers will be ubiquitous.**</span></span> <span data-ttu-id="9ab20-109">Docker ベースのコンテナーは、Microsoft、Amazon AWS、Google、IBM などの Windows および Linux エコシステムの主要ベンダーでサポートされており、業界内で事実上の標準になりつつあります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-109">Docker-based containers are becoming the de facto standard in the industry, supported by key vendors in the Windows and Linux ecosystems, such as Microsoft, Amazon AWS, Google, and IBM.</span></span> <span data-ttu-id="9ab20-110">Docker は、間もなくクラウドとオンプレミスの両方のデータセンターで広く使用されるようになるでしょう。</span><span class="sxs-lookup"><span data-stu-id="9ab20-110">Docker will probably soon be ubiquitous in both the cloud and on-premises datacenters.</span></span>

<span data-ttu-id="9ab20-111">**配置の展開の単位。**</span><span class="sxs-lookup"><span data-stu-id="9ab20-111">**Containers as a unit of deployment.**</span></span> <span data-ttu-id="9ab20-112">Docker コンテナーは、サーバー ベースのアプリケーションまたはサービスの配置の標準的な単位になっています。</span><span class="sxs-lookup"><span data-stu-id="9ab20-112">A Docker container is becoming the standard unit of deployment for any server-based application or service.</span></span>

<span data-ttu-id="9ab20-113">**マイクロサービス。**</span><span class="sxs-lookup"><span data-stu-id="9ab20-113">**Microservices.**</span></span> <span data-ttu-id="9ab20-114">マイクロサービス アーキテクチャは、自律的なサービスの形式の多数の独立したサブシステムを基にした、分散された大規模または複雑なミッションクリティカルなアプリケーションに対して推奨されるアプローチになりつつあります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-114">The microservices architecture is becoming the preferred approach for distributed and large or complex mission-critical applications based on many independent subsystems in the form of autonomous services.</span></span> <span data-ttu-id="9ab20-115">マイクロサービス ベースのアーキテクチャでは、個別に開発、テスト、バージョン管理、展開、およびスケールが行われるサービスのコレクションとしてアプリケーションが構築されます。</span><span class="sxs-lookup"><span data-stu-id="9ab20-115">In a microservice-based architecture, the application is built as a collection of services that are developed, tested, versioned, deployed, and scaled independently.</span></span> <span data-ttu-id="9ab20-116">各サービスには、関連する自律的なデータベースを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="9ab20-116">Each service can include any related autonomous database.</span></span>

<span data-ttu-id="9ab20-117">**ドメイン駆動設計と SOA。**</span><span class="sxs-lookup"><span data-stu-id="9ab20-117">**Domain-driven design and SOA.**</span></span> <span data-ttu-id="9ab20-118">マイクロサービス アーキテクチャ パターンは、サービス指向アーキテクチャ (SOA) とドメイン駆動設計 (DDD) から派生します。</span><span class="sxs-lookup"><span data-stu-id="9ab20-118">The microservices architecture patterns derive from service-oriented architecture (SOA) and domain-driven design (DDD).</span></span> <span data-ttu-id="9ab20-119">ビジネス ニーズとルールが進化してる状況で環境用のマイクロサービスを設計および開発する場合は、DDD アプローチとパターンを考慮することが重要です。</span><span class="sxs-lookup"><span data-stu-id="9ab20-119">When you design and develop microservices for environments with evolving business needs and rules, it's important to consider DDD approaches and patterns.</span></span>

<span data-ttu-id="9ab20-120">**マイクロサービスの課題。**</span><span class="sxs-lookup"><span data-stu-id="9ab20-120">**Microservices challenges.**</span></span> <span data-ttu-id="9ab20-121">マイクロサービスは、独立した展開、強力なサブシステムの境界などの多くの強力な機能とテクノロジの多様性を提供します。</span><span class="sxs-lookup"><span data-stu-id="9ab20-121">Microservices offer many powerful capabilities, like independent deployment, strong subsystem boundaries, and technology diversity.</span></span> <span data-ttu-id="9ab20-122">ただし、断片化と独立したデータ モデル、マイクロサービス間の回復力のある通信、最終的な一貫性、ログ情報と監視情報を複数のマイクロサービスから収集する結果としての運用の複雑さなどの分散アプリケーションの開発に関連する多くの新しい課題も発生させます。</span><span class="sxs-lookup"><span data-stu-id="9ab20-122">However, they also raise many new challenges related to distributed application development, such as fragmented and independent data models, resilient communication between microservices, eventual consistency, and operational complexity that results from aggregating logging and monitoring information from multiple microservices.</span></span> <span data-ttu-id="9ab20-123">このような側面により、従来のモノリシック アプリケーションよりもはるかに複雑になります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-123">These aspects introduce a much higher complexity level than a traditional monolithic application.</span></span> <span data-ttu-id="9ab20-124">その結果、特定のシナリオだけが、マイクロサービスベースのアプリケーションに適しています。</span><span class="sxs-lookup"><span data-stu-id="9ab20-124">As a result, only specific scenarios are suitable for microservice-based applications.</span></span> <span data-ttu-id="9ab20-125">たとえば、複数の進化するサブシステムを持つ大規模で複雑なアプリケーションなどです。</span><span class="sxs-lookup"><span data-stu-id="9ab20-125">These include large and complex applications with multiple evolving subsystems.</span></span> <span data-ttu-id="9ab20-126">このようなケースでは、より複雑なソフトウェア アーキテクチャに投資する価値があります。それは、長期的な機敏性とアプリケーションのメンテナスを実現できるためです。</span><span class="sxs-lookup"><span data-stu-id="9ab20-126">In these cases, it's worth investing in a more complex software architecture, because it will provide better long-term agility and application maintenance.</span></span>

<span data-ttu-id="9ab20-127">**任意のアプリケーションのコンテナー。**</span><span class="sxs-lookup"><span data-stu-id="9ab20-127">**Containers for any application.**</span></span> <span data-ttu-id="9ab20-128">コンテナーはマイクロサービスに便利ですが、Windows コンテナーを使用する場合は、従来の .NET Framework に基づくモノリシック アプリケーションにも役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9ab20-128">Containers are convenient for microservices, but can also be useful for monolithic applications based on the traditional .NET Framework, when using Windows Containers.</span></span> <span data-ttu-id="9ab20-129">展開から実稼働に関する多くの問題の解決、最先端の開発およびテストの環境の提供など、Docker を使用することの利点は、さまざまな種類のアプリケーションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="9ab20-129">The benefits of using Docker, such as solving many deployment-to-production issues and providing state-of-the-art Dev and Test environments, apply to many different types of applications.</span></span>

<span data-ttu-id="9ab20-130">**CLI と IDE の比較。**</span><span class="sxs-lookup"><span data-stu-id="9ab20-130">**CLI versus IDE.**</span></span> <span data-ttu-id="9ab20-131">Microsoft のツールを使用し、優先されるアプローチを使用して、コンテナー化された .NET アプリケーションを開発できます。</span><span class="sxs-lookup"><span data-stu-id="9ab20-131">With Microsoft tools, you can develop containerized .NET applications using your preferred approach.</span></span> <span data-ttu-id="9ab20-132">Docker CLI と Visual Studio Code を使用し、CLI とエディター ベースの環境を使用して開発できます。</span><span class="sxs-lookup"><span data-stu-id="9ab20-132">You can develop with a CLI and an editor-based environment by using the Docker CLI and Visual Studio Code.</span></span> <span data-ttu-id="9ab20-133">また、Visual Studio による IDE 重視のアプローチと、その Docker 用の独自の機能 (マルチコンテナー デバッグなど) を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="9ab20-133">Or you can use an IDE-focused approach with Visual Studio and its unique features for Docker, such as multi-container debugging.</span></span>

<span data-ttu-id="9ab20-134">**回復力のあるクラウド アプリケーション。**</span><span class="sxs-lookup"><span data-stu-id="9ab20-134">**Resilient cloud applications.**</span></span> <span data-ttu-id="9ab20-135">クラウド ベースのシステムおよび分散システムでは一般的に、部分的な障害のリスクがあります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-135">In cloud-based systems and distributed systems in general, there is always the risk of partial failure.</span></span> <span data-ttu-id="9ab20-136">クライアントとサービスは別のプロセス (コンテナー) であるため、サービスが、クライアントの要求に迅速に応答できないことがあります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-136">Since clients and services are separate processes (containers), a service might not be able to respond in a timely way to a client’s request.</span></span> <span data-ttu-id="9ab20-137">たとえば、部分的な障害またはメンテナンスのためにサービスが停止したり、サービスが過負荷状態で要求に対する応答が遅くなったり、または、ネットワークの問題のために短時間アクセスできない場合があります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-137">For example, a service might be down because of a partial failure or for maintenance; the service might be overloaded and responding slowly to requests; or it might not be accessible for a short time because of network issues.</span></span> <span data-ttu-id="9ab20-138">したがって、クラウド ベースのアプリケーションはそのような障害を受け入れ、そのような障害に対応するための戦略を用意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-138">Therefore, a cloud-based application must embrace those failures and have a strategy in place to respond to those failures.</span></span> <span data-ttu-id="9ab20-139">これらの戦略には、再試行ポリシー (メッセージの再送信または要求を再試行) を含めることができ、繰り返しの要求の急増を回避する遮断器パターンを実装できます。</span><span class="sxs-lookup"><span data-stu-id="9ab20-139">These strategies can include retry policies (resending messages or retrying requests) and implementing circuit-breaker patterns to avoid exponential load of repeated requests.</span></span> <span data-ttu-id="9ab20-140">基本的には、クラウドベースのアプリケーションには、オーケストレーターやサービス バスから提供されるの高レベルのメカニズムとして、クラウド インフラストラクチャまたはカスタムに基づいた回復メカニズムを搭載している必要があります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-140">Basically, cloud-based applications must have resilient mechanisms—either based on cloud infrastructure or custom, as the high-level ones provided by  orchestrators or service buses.</span></span>

<span data-ttu-id="9ab20-141">**セキュリティ。**</span><span class="sxs-lookup"><span data-stu-id="9ab20-141">**Security.**</span></span> <span data-ttu-id="9ab20-142">コンテナーとマイクロサービスによる新しい世界では、新たな脆弱性が公開される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-142">Our modern world of containers and microservices can expose new vulnerabilities.</span></span> <span data-ttu-id="9ab20-143">認証と承認に基づいて、基本的なアプリケーション セキュリティを実装する方法はいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-143">There are several ways to implement basic application security, based on authentication and authorization.</span></span> <span data-ttu-id="9ab20-144">ただし、コンテナーのセキュリティでは、本質的により安全なアプリケーションになる追加の主要コンポーネントを考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-144">However, container security must consider additional key components that result in inherently safer applications.</span></span> <span data-ttu-id="9ab20-145">コンテナー アプリケーションを構築するための重要な要素は、他のアプリケーションやシステムとの通信を安全に行うことです。多くの場合このためには、資格情報、トークン、パスワードなど (一般的にアプリケーション シークレットと呼ばれます) が必要です。</span><span class="sxs-lookup"><span data-stu-id="9ab20-145">A critical element of building safer apps is having a secure way of communicating with other apps and systems, something that often requires credentials, tokens, passwords, and the like, commonly referred to as application secrets.</span></span> <span data-ttu-id="9ab20-146">セキュリティで保護されたソリューションは、送信中および保存時のシークレットの暗号化、最終的なアプリケーションで使用される際のシークレットの漏えい防止など、セキュリティのベスト プラクティスに従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-146">Any secure solution must follow security best practices, such as encrypting secrets while in transit and at rest, and preventing secrets from leaking when consumed by the final application.</span></span> <span data-ttu-id="9ab20-147">このようなシークレットは、Azure Key Vault を使用する場合と同様に安全に保管して維持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9ab20-147">Those secrets need to be stored and kept safely, as when using Azure Key Vault.</span></span>

<span data-ttu-id="9ab20-148">**オーケストレーター。**</span><span class="sxs-lookup"><span data-stu-id="9ab20-148">**Orchestrators.**</span></span> <span data-ttu-id="9ab20-149">Azure Kubernetes Service や Azure Service Fabric などのコンテナー ベースのオーケストレーターは、重要なマイクロサービスおよびコンテナーベースのアプリケーションの主要部分を占めています。</span><span class="sxs-lookup"><span data-stu-id="9ab20-149">Container-based orchestrators, such as Azure Kubernetes Service and Azure Service Fabric are key part of any significant microservice and container-based application.</span></span> <span data-ttu-id="9ab20-150">このようなアプリケーションは非常に複雑でスケーラビリティのニーズがあり、常に進化し続けています。</span><span class="sxs-lookup"><span data-stu-id="9ab20-150">These applications carry with them high complexity, scalability needs, and go through constant evolution.</span></span> <span data-ttu-id="9ab20-151">このガイドでは、オーケストレーターの概要と、マイクロサービスベースおよびコンテナーベースのソリューションでのその役割を紹介しました。</span><span class="sxs-lookup"><span data-stu-id="9ab20-151">This guide has introduced orchestrators and their role in microservice-based and container-based solutions.</span></span> <span data-ttu-id="9ab20-152">アプリケーションで複雑なコンテナー化されたアプリケーションに移行する必要がある場合は、オーケストレーターについて学習するための追加のリソースを探すと役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="9ab20-152">If your application needs are moving you toward complex containerized apps, you will find it useful to seek out additional resources for learning more about orchestrators.</span></span>

>[!div class="step-by-step"]
>[<span data-ttu-id="9ab20-153">前へ</span><span class="sxs-lookup"><span data-stu-id="9ab20-153">Previous</span></span>](secure-net-microservices-web-applications/azure-key-vault-protects-secrets.md)