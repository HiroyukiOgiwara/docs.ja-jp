---
title: '方法: アセンブリを読み込み、アンロードする'
ms.date: 08/19/2019
ms.assetid: 6a4f490f-3576-471f-9533-003737cad4a3
ms.openlocfilehash: 77ea97c2fc324287e9c697d630def98241432c9f
ms.sourcegitcommit: 7b1ce327e8c84f115f007be4728d29a89efe11ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70972673"
---
# <a name="how-to-load-and-unload-assemblies"></a><span data-ttu-id="cf499-102">方法: アセンブリを読み込み、アンロードする</span><span class="sxs-lookup"><span data-stu-id="cf499-102">How to: Load and unload assemblies</span></span>
<span data-ttu-id="cf499-103">プログラムから参照されるアセンブリは、共通言語ランタイムによって自動的に読み込まれますが、特定のアセンブリを現在のアプリケーション ドメインに動的に読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="cf499-103">The assemblies referenced by your program will automatically be loaded by the common language runtime, but it is also possible to dynamically load specific assemblies into the current application domain.</span></span> <span data-ttu-id="cf499-104">詳細については、「[方法 :アプリケーション ドメインにアセンブリを読み込む](../../framework/app-domains/how-to-load-assemblies-into-an-application-domain.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf499-104">For more information, see [How to: Load assemblies into an application domain](../../framework/app-domains/how-to-load-assemblies-into-an-application-domain.md).</span></span>

<span data-ttu-id="cf499-105">.NET Framework では、アセンブリが含まれるすべてのアプリケーション ドメインをアンロードせずに、個々のアセンブリをアンロードする方法はありません。</span><span class="sxs-lookup"><span data-stu-id="cf499-105">In .NET Framework, there is no way to unload an individual assembly without unloading all of the application domains that contain it.</span></span> <span data-ttu-id="cf499-106">アセンブリがスコープの外にある場合であっても、実際のアセンブリ ファイルは、これらのアセンブリ ファイルを含むすべてのアプリケーション ドメインがアンロードされるまでは読み込まれたままになります。</span><span class="sxs-lookup"><span data-stu-id="cf499-106">Even if the assembly goes out of scope, the actual assembly file will remain loaded until all application domains that contain it are unloaded.</span></span> <span data-ttu-id="cf499-107">.Net Core では、<xref:System.Runtime.Loader.AssemblyLoadContext?displayProperty=nameWithType> クラスによってアセンブリのアンロードが処理されます。</span><span class="sxs-lookup"><span data-stu-id="cf499-107">In .NET Core, the <xref:System.Runtime.Loader.AssemblyLoadContext?displayProperty=nameWithType> class handles the unloading of assemblies.</span></span> <span data-ttu-id="cf499-108">詳細については、「[.NET Core でアセンブリのアンローダビリティを使用およびデバッグする方法](unloadability.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf499-108">For more information, see [How to use and debug assembly unloadability in .NET Core](unloadability.md).</span></span>

## <a name="load-and-unload-assemblies"></a><span data-ttu-id="cf499-109">アセンブリを読み込み、アンロードする</span><span class="sxs-lookup"><span data-stu-id="cf499-109">Load and unload assemblies</span></span>

<span data-ttu-id="cf499-110">アセンブリをアプリケーション ドメインに読み込むには、<xref:System.AppDomain> クラスと <xref:System.Reflection.Assembly> クラスに含まれるいくつかの読み込みメソッドのいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="cf499-110">To load an assembly into an application domain, use one of the several load methods contained in the classes <xref:System.AppDomain> and <xref:System.Reflection.Assembly>.</span></span> <span data-ttu-id="cf499-111">詳細については、「[方法 :アプリケーション ドメインにアセンブリを読み込む](../../framework/app-domains/how-to-load-assemblies-into-an-application-domain.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf499-111">For more information, see [How to: Load assemblies into an application domain](../../framework/app-domains/how-to-load-assemblies-into-an-application-domain.md).</span></span> <span data-ttu-id="cf499-112">.NET Core でサポートされるアプリケーション ドメインは 1 つだけであることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cf499-112">Note that .NET Core supports only a single application domain.</span></span> 

<span data-ttu-id="cf499-113">.NET Framework でアセンブリをアンロードするには、アセンブリを含まれるすべてのアプリケーション ドメインをアンロードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf499-113">To unload an assembly in the .NET Framework, you must unload all of the application domains that contain it.</span></span> <span data-ttu-id="cf499-114">アプリケーション ドメインをアンロードするには、<xref:System.AppDomain.Unload%2A?displayProperty=nameWithType> メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="cf499-114">To unload an application domain, use the <xref:System.AppDomain.Unload%2A?displayProperty=nameWithType> method.</span></span> <span data-ttu-id="cf499-115">詳細については、「[方法 :アプリケーション ドメインをアンロードする](../../framework/app-domains/how-to-unload-an-application-domain.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf499-115">For more information, see [How to: Unload an application domain](../../framework/app-domains/how-to-unload-an-application-domain.md).</span></span>

<span data-ttu-id="cf499-116">.NET Framework アプリケーションで、一部のアセンブリだけをアンロードする場合は、新しいアプリケーション ドメインを作成し、そのドメイン内でコードを実行した後、そのアプリケーション ドメインをアンロードします。</span><span class="sxs-lookup"><span data-stu-id="cf499-116">If you want to unload some assemblies but not others in a .NET Framework application, consider creating a new application domain, executing the code inside that domain, and then unloading that application domain.</span></span> <span data-ttu-id="cf499-117">詳細については、「[方法 :アプリケーション ドメインをアンロードする](../../framework/app-domains/how-to-unload-an-application-domain.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf499-117">For more information, see [How to: Unload an application domain](../../framework/app-domains/how-to-unload-an-application-domain.md).</span></span>  

## <a name="see-also"></a><span data-ttu-id="cf499-118">関連項目</span><span class="sxs-lookup"><span data-stu-id="cf499-118">See also</span></span>

- [<span data-ttu-id="cf499-119">C# プログラミング ガイド</span><span class="sxs-lookup"><span data-stu-id="cf499-119">C# programming guide</span></span>](../../csharp/programming-guide/index.md)
- [<span data-ttu-id="cf499-120">プログラミングの概念 (Visual Basic)</span><span class="sxs-lookup"><span data-stu-id="cf499-120">Programming concepts (Visual Basic)</span></span>](../../visual-basic/programming-guide/concepts/index.md)
- [<span data-ttu-id="cf499-121">.NET のアセンブリ</span><span class="sxs-lookup"><span data-stu-id="cf499-121">Assemblies in .NET</span></span>](index.md)
- [<span data-ttu-id="cf499-122">方法: アプリケーション ドメインにアセンブリを読み込む</span><span class="sxs-lookup"><span data-stu-id="cf499-122">How to: Load assemblies into an application domain</span></span>](../../framework/app-domains/how-to-load-assemblies-into-an-application-domain.md)