---
title: アセンブリを作成する
ms.date: 08/19/2019
helpviewer_keywords:
- assemblies [.NET Framework], multifile
- single-file assemblies
- assemblies [.NET Framework], creating
- multifile assemblies
ms.assetid: 54832ee9-dca8-4c8b-913c-c0b9d265e9a4
author: rpetrusha
ms.author: ronpet
ms.openlocfilehash: 2dda45cca182d75bc77916cdf862ada9faead9ec
ms.sourcegitcommit: 7b1ce327e8c84f115f007be4728d29a89efe11ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70972763"
---
# <a name="create-assemblies"></a><span data-ttu-id="762b1-102">アセンブリを作成する</span><span class="sxs-lookup"><span data-stu-id="762b1-102">Create assemblies</span></span>

<span data-ttu-id="762b1-103">Visual Studio などの IDE や、Windows SDK によって提供されるコンパイラやツールを使って、単一ファイルまたはマルチファイルのアセンブリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="762b1-103">You can create single-file or multifile assemblies using an IDE, such as Visual Studio, or the compilers and tools provided by the Windows SDK.</span></span> <span data-ttu-id="762b1-104">最も単純なアセンブリは、単純な名前を持ち、単一のアプリケーション ドメインに読み込まれる単一のファイルです。</span><span class="sxs-lookup"><span data-stu-id="762b1-104">The simplest assembly is a single file that has a simple name and is loaded into a single application domain.</span></span> <span data-ttu-id="762b1-105">このアセンブリはアプリケーション ディレクトリの外部にある他のアセンブリからは参照できず、バージョン チェックが行われません。</span><span class="sxs-lookup"><span data-stu-id="762b1-105">This assembly cannot be referenced by other assemblies outside the application directory and does not undergo version checking.</span></span> <span data-ttu-id="762b1-106">アセンブリで構成されるアプリケーションをアンインストールするには、アプリケーションが存在するディレクトリを削除します。</span><span class="sxs-lookup"><span data-stu-id="762b1-106">To uninstall the application made up of the assembly, you simply delete the directory where it resides.</span></span> <span data-ttu-id="762b1-107">多くの開発者は、このような機能を含むアセンブリがあれば、アプリケーションを展開できます。</span><span class="sxs-lookup"><span data-stu-id="762b1-107">For many developers, an assembly with these features is all that is needed to deploy an application.</span></span>

<span data-ttu-id="762b1-108">マルチファイル アセンブリは、複数のコード モジュールおよびリソース ファイルから作成できます。</span><span class="sxs-lookup"><span data-stu-id="762b1-108">You can create a multifile assembly from several code modules and resource files.</span></span> <span data-ttu-id="762b1-109">複数のアプリケーションで共有できるアセンブリも作成できます。</span><span class="sxs-lookup"><span data-stu-id="762b1-109">You can also create an assembly that can be shared by multiple applications.</span></span> <span data-ttu-id="762b1-110">共有アセンブリは厳密な名前を持つ必要があり、グローバル アセンブリ キャッシュに展開することができます。</span><span class="sxs-lookup"><span data-stu-id="762b1-110">A shared assembly must have a strong name and can be deployed in the global assembly cache.</span></span>

<span data-ttu-id="762b1-111">コード モジュールとリソースをアセンブリにグループ化する場合、次のような要因に応じていくつかのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="762b1-111">You have several options when grouping code modules and resources into assemblies, depending on the following factors:</span></span>

- <span data-ttu-id="762b1-112">バージョン管理</span><span class="sxs-lookup"><span data-stu-id="762b1-112">Versioning</span></span>

     <span data-ttu-id="762b1-113">同じバージョン情報が含まれている必要があるグループ モジュール。</span><span class="sxs-lookup"><span data-stu-id="762b1-113">Group modules that should have the same version information.</span></span>

- <span data-ttu-id="762b1-114">配置</span><span class="sxs-lookup"><span data-stu-id="762b1-114">Deployment</span></span>

     <span data-ttu-id="762b1-115">展開のモデルをサポートするグループ コードのモジュールおよびリソース。</span><span class="sxs-lookup"><span data-stu-id="762b1-115">Group code modules and resources that support your model of deployment.</span></span>

- <span data-ttu-id="762b1-116">再利用</span><span class="sxs-lookup"><span data-stu-id="762b1-116">Reuse</span></span>

     <span data-ttu-id="762b1-117">いくつかの目的のために論理的に同時に使用できる場合はグループ モジュール。</span><span class="sxs-lookup"><span data-stu-id="762b1-117">Group modules if they can be logically used together for some purpose.</span></span> <span data-ttu-id="762b1-118">たとえば、プログラムのメンテナンスで頻繁に使用される型とクラスで構成されるアセンブリは同じアセンブリに配置することができます。</span><span class="sxs-lookup"><span data-stu-id="762b1-118">For example, an assembly consisting of types and classes used infrequently for program maintenance can be put in the same assembly.</span></span> <span data-ttu-id="762b1-119">さらに、複数のアプリケーションで共有する予定の型をアセンブリにグループ化し、そのアセンブリを厳密な名前で署名する必要があります。</span><span class="sxs-lookup"><span data-stu-id="762b1-119">In addition, types that you intend to share with multiple applications should be grouped into an assembly and the assembly should be signed with a strong name.</span></span>

- <span data-ttu-id="762b1-120">セキュリティ</span><span class="sxs-lookup"><span data-stu-id="762b1-120">Security</span></span>

     <span data-ttu-id="762b1-121">同じセキュリティ アクセス許可が必要な型を含むグループ モジュール。</span><span class="sxs-lookup"><span data-stu-id="762b1-121">Group modules containing types that require the same security permissions.</span></span>

- <span data-ttu-id="762b1-122">スコープ</span><span class="sxs-lookup"><span data-stu-id="762b1-122">Scoping</span></span>

     <span data-ttu-id="762b1-123">参照可能範囲を同じアセンブリに制限する必要がある型で構成されるグループ モジュール。</span><span class="sxs-lookup"><span data-stu-id="762b1-123">Group modules containing types whose visibility should be restricted to the same assembly.</span></span>

<span data-ttu-id="762b1-124">アンマネージド COM アプリケーションで使用できる共通言語ランタイム アセンブリを作成するときは、特別な考慮事項があります。</span><span class="sxs-lookup"><span data-stu-id="762b1-124">There are special considerations when making common language runtime assemblies available to unmanaged COM applications.</span></span> <span data-ttu-id="762b1-125">アンマネージド コードの使用の詳細については、「[COM への .NET コンポーネントの公開](../../framework/interop/exposing-dotnet-components-to-com.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="762b1-125">For more information about working with unmanaged code, see [Expose .NET Framework components to COM](../../framework/interop/exposing-dotnet-components-to-com.md).</span></span>

## <a name="see-also"></a><span data-ttu-id="762b1-126">関連項目</span><span class="sxs-lookup"><span data-stu-id="762b1-126">See also</span></span>

- [<span data-ttu-id="762b1-127">アセンブリを使用したプログラム</span><span class="sxs-lookup"><span data-stu-id="762b1-127">Program with assemblies</span></span>](program.md)
- [<span data-ttu-id="762b1-128">アセンブリのバージョン管理</span><span class="sxs-lookup"><span data-stu-id="762b1-128">Assembly versioning</span></span>](versioning.md)
- [<span data-ttu-id="762b1-129">方法: シングルファイル アセンブリをビルドする</span><span class="sxs-lookup"><span data-stu-id="762b1-129">How to: Build a single-file assembly</span></span>](../../framework/app-domains/build-single-file-assembly.md)
- [<span data-ttu-id="762b1-130">方法: マルチファイル アセンブリをビルドする</span><span class="sxs-lookup"><span data-stu-id="762b1-130">How to: Build a multifile assembly</span></span>](../../framework/app-domains/build-multifile-assembly.md)
- [<span data-ttu-id="762b1-131">ランタイムがアセンブリを検索する方法</span><span class="sxs-lookup"><span data-stu-id="762b1-131">How the runtime locates assemblies</span></span>](../../framework/deployment/how-the-runtime-locates-assemblies.md)
- [<span data-ttu-id="762b1-132">マルチファイル アセンブリ</span><span class="sxs-lookup"><span data-stu-id="762b1-132">Multifile assemblies</span></span>](../../framework/app-domains/multifile-assemblies.md)