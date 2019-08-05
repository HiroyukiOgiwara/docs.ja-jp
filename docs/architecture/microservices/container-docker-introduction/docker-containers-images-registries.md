---
title: Docker コンテナー、イメージ、レジストリ
description: コンテナー化された .NET アプリケーションの .NET マイクロサービス アーキテクチャ | Docker コンテナー、イメージ、レジストリ
ms.date: 08/31/2018
ms.openlocfilehash: 520f8d4d54f1fdd227ff9a1e88660b62e75f927f
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68674899"
---
# <a name="docker-containers-images-and-registries"></a><span data-ttu-id="c25e9-103">Docker コンテナー、イメージ、レジストリ</span><span class="sxs-lookup"><span data-stu-id="c25e9-103">Docker containers, images, and registries</span></span>

<span data-ttu-id="c25e9-104">Docker を使用する場合、開発者はアプリケーションまたはサービスを作成し、そのアプリケーションまたはサービスとその依存関係をコンテナー イメージにパッケージ化します。</span><span class="sxs-lookup"><span data-stu-id="c25e9-104">When using Docker, a developer creates an app or service and packages it and its dependencies into a container image.</span></span> <span data-ttu-id="c25e9-105">イメージとは、アプリケーションまたはサービスと、その構成と依存関係の静的な表現です。</span><span class="sxs-lookup"><span data-stu-id="c25e9-105">An image is a static representation of the app or service and its configuration and dependencies.</span></span>

<span data-ttu-id="c25e9-106">アプリケーションまたはサービスを実行するために、アプリケーションのイメージはインスタンス化され、コンテナーが作成されます。このコンテナーが Docker ホスト上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="c25e9-106">To run the app or service, the app's image is instantiated to create a container, which will be running on the Docker host.</span></span> <span data-ttu-id="c25e9-107">コンテナーは、最初に開発環境または PC でテストされます。</span><span class="sxs-lookup"><span data-stu-id="c25e9-107">Containers are initially tested in a development environment or PC.</span></span>

<span data-ttu-id="c25e9-108">開発者はイメージをレジストリに保存する必要があります。レジストリはイメージのライブラリとして機能し、実稼働環境のオーケストレーターに展開するときに必要になります。</span><span class="sxs-lookup"><span data-stu-id="c25e9-108">Developers should store images in a registry, which acts as a library of images and is needed when deploying to production orchestrators.</span></span> <span data-ttu-id="c25e9-109">Docker は [Docker Hub](https://hub.docker.com/) 経由で公開レジストリを保守します。他のベンダーは、[Azure Container Registry](https://azure.microsoft.com/services/container-registry/)を含むさまざまなイメージのコレクション用のレジストリを用意しています。</span><span class="sxs-lookup"><span data-stu-id="c25e9-109">Docker maintains a public registry via [Docker Hub](https://hub.docker.com/); other vendors provide registries for different collections of images, including [Azure Container Registry](https://azure.microsoft.com/services/container-registry/).</span></span> <span data-ttu-id="c25e9-110">また、企業は自社の Docker イメージ用に非公開のレジストリをオンプレミスに持つことができます。</span><span class="sxs-lookup"><span data-stu-id="c25e9-110">Alternatively, enterprises can have a private registry on-premises for their own Docker images.</span></span>

<span data-ttu-id="c25e9-111">図 2-4 は、Docker のイメージとレジストリが他のコンポーネントとどのように関係しているかを示しています。</span><span class="sxs-lookup"><span data-stu-id="c25e9-111">Figure 2-4 shows how images and registries in Docker relate to other components.</span></span> <span data-ttu-id="c25e9-112">また、ベンダーから提供される複数のレジストリも示しています。</span><span class="sxs-lookup"><span data-stu-id="c25e9-112">It also shows the multiple registry offerings from vendors.</span></span>

![Docker での基本的な分類:レジストリはイメージが格納される本棚のようなものであり、サービスや Web アプリを実行するコンテナーを構築するためにプルして使用できます。](./media/image5.PNG)

<span data-ttu-id="c25e9-117">**図 2-4**</span><span class="sxs-lookup"><span data-stu-id="c25e9-117">**Figure 2-4**.</span></span> <span data-ttu-id="c25e9-118">Docker の用語と概念の分類</span><span class="sxs-lookup"><span data-stu-id="c25e9-118">Taxonomy of Docker terms and concepts</span></span>

<span data-ttu-id="c25e9-119">レジストリにイメージを配置すると、フレームワーク レベルのすべての依存関係を含め、静的で不変なアプリケーション ビットを格納できます。</span><span class="sxs-lookup"><span data-stu-id="c25e9-119">Putting images in a registry lets you store static and immutable application bits, including all their dependencies at a framework level.</span></span> <span data-ttu-id="c25e9-120">このようなイメージは複数の環境でバージョン管理および展開できるので、一貫した展開ユニットを提供できます。</span><span class="sxs-lookup"><span data-stu-id="c25e9-120">Those images can then be versioned and deployed in multiple environments and therefore provide a consistent deployment unit.</span></span>

<span data-ttu-id="c25e9-121">オンプレミスまたはクラウドでホストされる非公開のイメージ レジストリは、次の場合に推奨されます。</span><span class="sxs-lookup"><span data-stu-id="c25e9-121">Private image registries, either hosted on-premises or in the cloud, are recommended when:</span></span>

- <span data-ttu-id="c25e9-122">機密保持のためにイメージを一般公開して共有することができない場合。</span><span class="sxs-lookup"><span data-stu-id="c25e9-122">Your images must not be shared publicly due to confidentiality.</span></span>

- <span data-ttu-id="c25e9-123">イメージと選択した展開環境間のネットワーク待機時間を最小限に抑えたい場合。</span><span class="sxs-lookup"><span data-stu-id="c25e9-123">You want to have minimum network latency between your images and your chosen deployment environment.</span></span> <span data-ttu-id="c25e9-124">たとえば、実稼働環境が Azure クラウドの場合は、[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) にイメージを保存して、ネットワークの待機時間を最小限に抑える方法があります。</span><span class="sxs-lookup"><span data-stu-id="c25e9-124">For example, if your production environment is Azure cloud, you probably want to store your images in [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) so that network latency will be minimal.</span></span> <span data-ttu-id="c25e9-125">同様に、実稼働環境がオンプレミスの場合は、同じローカル ネットワーク内でオンプレミスの Docker Trusted Registry を使用できるようにする方法があります。</span><span class="sxs-lookup"><span data-stu-id="c25e9-125">In a similar way, if your production environment is on-premises, you might want to have an on-premises Docker Trusted Registry available within the same local network.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="c25e9-126">[前へ](docker-terminology.md)
>[次へ](../net-core-net-framework-containers/index.md)</span><span class="sxs-lookup"><span data-stu-id="c25e9-126">[Previous](docker-terminology.md)
[Next](../net-core-net-framework-containers/index.md)</span></span>