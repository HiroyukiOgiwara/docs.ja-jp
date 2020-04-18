---
title: x:Class ディレクティブ
ms.date: 03/30/2017
f1_keywords:
- x:Class
- xClass
- Class
helpviewer_keywords:
- Class attribute in XAML [XAML Services]
- XAML [XAML Services], x:Class attribute
- x:Class attribute [XAML Services]
ms.assetid: bc4a3d8e-76e2-423e-a5d1-159a023e82ec
ms.openlocfilehash: f589fa70c2ee1c56544fa0f0152990478aa3761f
ms.sourcegitcommit: 99b153b93bf94d0fecf7c7bcecb58ac424dfa47c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2020
ms.locfileid: "81433278"
---
# <a name="xclass-directive"></a><span data-ttu-id="ba8ac-102">x:Class ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="ba8ac-102">x:Class Directive</span></span>
<span data-ttu-id="ba8ac-103">マークアップと分離コードの間で部分クラスを結合するように XAML マークアップ コンパイルを構成します。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-103">Configures XAML markup compilation to join partial classes between markup and code-behind.</span></span> <span data-ttu-id="ba8ac-104">コード部分クラスは共通言語仕様 (CLS) 言語の個別のコード ファイルで定義されますが、マークアップ部分クラスは通常、XAML コンパイル時にコード生成によって作成されます。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-104">The code partial class is defined in a separate code file in a Common Language Specification (CLS) language, whereas the markup partial class is typically created by code generation during XAML compilation.</span></span>

## <a name="xaml-attribute-usage"></a><span data-ttu-id="ba8ac-105">XAML 属性の使用方法</span><span class="sxs-lookup"><span data-stu-id="ba8ac-105">XAML Attribute Usage</span></span>

```xaml
<object x:Class="namespace.classname"...>
  ...
</object>
```

## <a name="xaml-values"></a><span data-ttu-id="ba8ac-106">XAML 値</span><span class="sxs-lookup"><span data-stu-id="ba8ac-106">XAML Values</span></span>

|||
|-|-|
|`namespace`|<span data-ttu-id="ba8ac-107">省略可能。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-107">Optional.</span></span> <span data-ttu-id="ba8ac-108">で識別される部分クラスを含む CLR`classname`名前空間を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-108">Specifies a CLR namespace that contains the partial class identified by `classname`.</span></span> <span data-ttu-id="ba8ac-109">指定`namespace`した場合、ドット (.) は`namespace`、`classname`と を区切ります。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-109">If `namespace` is specified, a dot (.) separates `namespace` and `classname`.</span></span> <span data-ttu-id="ba8ac-110">「解説」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-110">See Remarks.</span></span>|
|`classname`|<span data-ttu-id="ba8ac-111">必須。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-111">Required.</span></span> <span data-ttu-id="ba8ac-112">読み込まれた XAML とその XAML の分離コードを接続する部分クラスの CLR 名を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-112">Specifies the CLR name of the partial class that connects the loaded XAML and your code-behind for that XAML.</span></span>|

## <a name="dependencies"></a><span data-ttu-id="ba8ac-113">依存関係</span><span class="sxs-lookup"><span data-stu-id="ba8ac-113">Dependencies</span></span>

<span data-ttu-id="ba8ac-114">`x:Class`XAML の運用環境のルート要素でのみ指定できます。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-114">`x:Class` can only be specified on the root element of a XAML production.</span></span> <span data-ttu-id="ba8ac-115">`x:Class`は、XAML 運用環境で親を持つすべてのオブジェクトに対して無効です。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-115">`x:Class` is invalid on any object that has a parent in the XAML production.</span></span> <span data-ttu-id="ba8ac-116">詳細については、 [ \[MS-XAML\]セクション 4.3.1.6](https://docs.microsoft.com/previous-versions/msp-n-p/ff650760(v=pandp.10))を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-116">For more information, see [\[MS-XAML\] Section 4.3.1.6](https://docs.microsoft.com/previous-versions/msp-n-p/ff650760(v=pandp.10)).</span></span>

## <a name="remarks"></a><span data-ttu-id="ba8ac-117">解説</span><span class="sxs-lookup"><span data-stu-id="ba8ac-117">Remarks</span></span>

<span data-ttu-id="ba8ac-118">この`namespace`値には、関連する名前空間を名前階層に整理するための追加のドットが含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-118">The `namespace` value may contain additional dots to organize related namespaces into name hierarchies, which is a common technique in .NET programming.</span></span> <span data-ttu-id="ba8ac-119">値の文字列`x:Class`の最後のドットのみが分離`namespace`に解釈され、`classname.`として使用されるクラスは入れ子`x:Class`になったクラスにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-119">Only the last dot in a string of `x:Class` values is interpreted to separate `namespace` and `classname.` The class that is used as `x:Class` cannot be a nested class.</span></span> <span data-ttu-id="ba8ac-120">入れ子になったクラスが許可されている場合、文字列のドットの意味`x:Class`を判断することはあいまいであるため、入れ子になったクラスは許可されません。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-120">Nested classes are not allowed because determining the meaning of dots for `x:Class` strings is ambiguous if nested classes are permitted.</span></span>

<span data-ttu-id="ba8ac-121">を`x:Class``x:Class`使用する既存のプログラミング モデルでは、分離コードのない XAML ページを持つことは完全に有効であるという意味ではオプションです。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-121">In existing programming models that use `x:Class`, `x:Class` is optional in the sense that it is entirely valid to have a XAML page that has no code-behind.</span></span> <span data-ttu-id="ba8ac-122">ただし、この機能は、XAML を使用するフレームワークによって実装されるビルド アクションと対話します。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-122">However, that capability interacts with the build actions as implemented by frameworks that use XAML.</span></span> <span data-ttu-id="ba8ac-123">`x:Class`また、アプリケーション モデルおよび対応するビルド アクションで、XAML で指定されたコンテンツのさまざまな分類が持つロールによっても影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-123">`x:Class` capability is also influenced by the roles that various classifications of XAML-specified content have in an application model and in the corresponding build actions.</span></span> <span data-ttu-id="ba8ac-124">XAML でイベント処理属性値を宣言する場合、または定義クラスが分離コード クラスにあるカスタム要素をインスタンス化する場合は、分離コードに対`x:Class`する適切なクラスにディレクティブ参照 (または[x:Subclass)](xsubclass-directive.md)を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-124">If your XAML declares event-handling attribute values or instantiates custom elements where the defining classes are in the code-behind class, you have to provide the `x:Class` directive reference (or [x:Subclass](xsubclass-directive.md)) to the appropriate class for code-behind.</span></span>

<span data-ttu-id="ba8ac-125">ディレクティブの`x:Class`値は、アセンブリ情報を含まないクラスの完全修飾名を指定する文字列である必要があります ( と<xref:System.Type.FullName%2A?displayProperty=nameWithType>同じです)。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-125">The value of the `x:Class` directive must be a string that specifies the fully qualified name of a class but without any assembly information (equivalent to the <xref:System.Type.FullName%2A?displayProperty=nameWithType>).</span></span> <span data-ttu-id="ba8ac-126">単純なアプリケーションでは、分離コードもその方法で構造化されている場合 (コード定義はクラス レベルで開始される) 場合は、CLR 名前空間情報を省略できます。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-126">For simple applications, you can omit CLR namespace information if the code-behind is also structured in that manner (code definition starts at the class level).</span></span>

<span data-ttu-id="ba8ac-127">ページまたはアプリケーション定義の分離コード ファイルは、コンパイル済みアプリケーションを生成し、マークアップコンパイルを伴うプロジェクトの一部として含まれるコード ファイル内になければなりません。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-127">The code-behind file for a page or application definition must be within a code file that is included as part of the project that produces a compiled application and involves markup compilation.</span></span> <span data-ttu-id="ba8ac-128">CLR クラスの名前規則に従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-128">You must follow name rules for CLR classes.</span></span> <span data-ttu-id="ba8ac-129">詳細については、「[フレームワーク設計ガイドライン](../../../api/index.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-129">For more information, see [Framework Design Guidelines](../../../api/index.md).</span></span> <span data-ttu-id="ba8ac-130">既定では、分離コード クラスは`public`;ただし[、x:ClassModifier ディレクティブ](xclassmodifier-directive.md)を使用して、異なるアクセス レベルで定義できます。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-130">By default, the code-behind class must be `public`; however, you can define it at a different access level by using the [x:ClassModifier Directive](xclassmodifier-directive.md).</span></span>

<span data-ttu-id="ba8ac-131">この属性の`x:Class`解釈は、CLR ベースの XAML 実装にのみ適用され、特に .NET XAML サービスに適用されます。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-131">This interpretation of the `x:Class` attribute applies only to a CLR-based XAML implementation, in particular to .NET XAML Services.</span></span> <span data-ttu-id="ba8ac-132">CLR に基づいておらず、.NET XAML サービスを使用しないその他の XAML 実装では、XAML マークアップを接続し、ランタイム コードをバッキングするために別の解決方法を使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-132">Other XAML implementations that are not based on CLR and that do not use .NET XAML Services might use a different resolution formula for connecting XAML markup and backing run-time code.</span></span> <span data-ttu-id="ba8ac-133">の一般的な解釈の`x:Class`詳細については、「 [ \[MS-XAML\]](https://docs.microsoft.com/previous-versions/msp-n-p/ff650760(v=pandp.10))」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-133">For more information about more general interpretations of `x:Class`, see [\[MS-XAML\]](https://docs.microsoft.com/previous-versions/msp-n-p/ff650760(v=pandp.10)).</span></span>

<span data-ttu-id="ba8ac-134">アーキテクチャの特定のレベルでは、.NET `x:Class` XAML サービスでは、の意味は定義されていません。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-134">At a certain level of architecture, the meaning of `x:Class` is undefined in .NET XAML Services.</span></span> <span data-ttu-id="ba8ac-135">これは、.NET XAML サービスでは、XAML マークアップとバッキング コードが接続されるプログラミング モデルが指定されないためです。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-135">This is because .NET XAML Services does not specify the programming model by which XAML markup and backing code are connected.</span></span> <span data-ttu-id="ba8ac-136">`x:Class`ディレクティブの追加の使用は、XAML マークアップと CLR ベースの分離コードを接続する方法を定義するプログラミング モデルまたはアプリケーション モデルを使用する特定のフレームワークによって実装される場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-136">Additional uses of the `x:Class` directive might be implemented by specific frameworks that use programming models or application models to define how to connect XAML markup and CLR-based code-behind.</span></span> <span data-ttu-id="ba8ac-137">各フレームワークには、ビルド環境に含める必要がある動作または特定のコンポーネントの一部を有効にする独自のビルド アクションを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-137">Each framework can have its own build actions that enable some of the behavior or specific components that must be included in the build environment.</span></span> <span data-ttu-id="ba8ac-138">フレームワーク内では、ビルド アクションは分離コードに使用される特定の CLR 言語によっても異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-138">Within a framework, build actions can also vary depending on the specific CLR language that is used for the code-behind.</span></span>

## <a name="xclass-in-the-wpf-programming-model"></a><span data-ttu-id="ba8ac-139">x: WPF プログラミング モデルのクラス</span><span class="sxs-lookup"><span data-stu-id="ba8ac-139">x:Class in the WPF Programming Model</span></span>

<span data-ttu-id="ba8ac-140">WPF アプリケーションおよび WPF アプリケーション`x:Class`モデルでは、XAML ファイルのルートであり、コンパイルされる要素 (ビルド アクションを伴`Page`う WPF アプリケーション プロジェクトに XAML が含まれる)、またはコンパイルされた<xref:System.Windows.Application>WPF アプリケーションのアプリケーション定義のルートの属性として宣言できます。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-140">In WPF applications and the WPF application model, `x:Class` can be declared as an attribute for any element that is the root of a XAML file and is being compiled (where the XAML is included in a WPF application project with `Page` build action), or for the <xref:System.Windows.Application> root in the application definition of a compiled WPF application.</span></span> <span data-ttu-id="ba8ac-141">ページ`x:Class`ルートまたはアプリケーション ルート以外の要素、またはコンパイルされていない WPF XAML ファイルで宣言すると、.NET Framework 3.0 および .NET Framework 3.5 WPF XAML コンパイラでコンパイル エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-141">Declaring `x:Class` on an element other than a page root or application root, or on a WPF XAML file that is not compiled, causes a compile-time error under the .NET Framework 3.0 and .NET Framework 3.5 WPF XAML compiler.</span></span> <span data-ttu-id="ba8ac-142">WPF での処理の`x:Class`その他の側面については、「 WPF の[分離コードと XAML](../../framework/wpf/advanced/code-behind-and-xaml-in-wpf.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-142">For information about other aspects of `x:Class` handling in WPF, see [Code-Behind and XAML in WPF](../../framework/wpf/advanced/code-behind-and-xaml-in-wpf.md).</span></span>

## <a name="xclass-for-windows-workflow-foundation"></a><span data-ttu-id="ba8ac-143">x:Windows ワークフローファウンデーションのクラス</span><span class="sxs-lookup"><span data-stu-id="ba8ac-143">x:Class for Windows Workflow Foundation</span></span>
<span data-ttu-id="ba8ac-144">Windows ワークフロー ファン`x:Class`デーションの場合は、XAML で完全に構成されるカスタム アクティビティのクラスに名前を付けたり、分離コードを持つアクティビティ デザイナーの XAML ページの部分クラスに名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-144">For Windows Workflow Foundation, `x:Class` names the class of a custom activity composed entirely in XAML, or names the partial class of the XAML page for  an activity designer with code-behind.</span></span>

## <a name="silverlight-usage-notes"></a><span data-ttu-id="ba8ac-145">Silverlight の使用上の注意</span><span class="sxs-lookup"><span data-stu-id="ba8ac-145">Silverlight Usage Notes</span></span>

<span data-ttu-id="ba8ac-146">Silverlight 用の `x:Class` に関しては、別途ドキュメントが用意されています。</span><span class="sxs-lookup"><span data-stu-id="ba8ac-146">`x:Class` for Silverlight is documented separately.</span></span> <span data-ttu-id="ba8ac-147">詳細については[、「XAML 名前空間 (x:)」を参照してください。言語機能 (シルバーライト)](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc188995(v=vs.95))</span><span class="sxs-lookup"><span data-stu-id="ba8ac-147">For more information, see [XAML Namespace (x:) Language Features (Silverlight)](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc188995(v=vs.95)).</span></span>

## <a name="see-also"></a><span data-ttu-id="ba8ac-148">関連項目</span><span class="sxs-lookup"><span data-stu-id="ba8ac-148">See also</span></span>

- [<span data-ttu-id="ba8ac-149">x:Subclass ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="ba8ac-149">x:Subclass Directive</span></span>](xsubclass-directive.md)
- [<span data-ttu-id="ba8ac-150">WPF における XAML とカスタム クラス</span><span class="sxs-lookup"><span data-stu-id="ba8ac-150">XAML and Custom Classes for WPF</span></span>](../../framework/wpf/advanced/xaml-and-custom-classes-for-wpf.md)
- [<span data-ttu-id="ba8ac-151">x:ClassModifier ディレクティブ</span><span class="sxs-lookup"><span data-stu-id="ba8ac-151">x:ClassModifier Directive</span></span>](xclassmodifier-directive.md)
- [<span data-ttu-id="ba8ac-152">WPF から System.Xaml に移行した型</span><span class="sxs-lookup"><span data-stu-id="ba8ac-152">Types Migrated from WPF to System.Xaml</span></span>](../../framework/wpf/advanced/types-migrated-from-wpf-to-system.md)