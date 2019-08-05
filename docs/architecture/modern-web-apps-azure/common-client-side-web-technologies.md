---
title: 一般的なクライアント側の Web テクノロジ
description: ASP.NET Core および Azure での最新の Web アプリケーションの設計 | 一般的なクライアント側の Web テクノロジ
author: ardalis
ms.author: wiwagn
ms.date: 01/30/2019
ms.openlocfilehash: 4dc0618eceda4df7c5aa76aee3ac649deb9f5310
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68675439"
---
# <a name="common-client-side-web-technologies"></a><span data-ttu-id="89f04-103">一般的なクライアント側の Web テクノロジ</span><span class="sxs-lookup"><span data-stu-id="89f04-103">Common client side web technologies</span></span>

> <span data-ttu-id="89f04-104">"Web サイトは中から見ても外から見ても好ましいものでなければならない。"</span><span class="sxs-lookup"><span data-stu-id="89f04-104">"Websites should look good from the inside and out."</span></span>  
> <span data-ttu-id="89f04-105">_- Paul Cookson_</span><span class="sxs-lookup"><span data-stu-id="89f04-105">_- Paul Cookson_</span></span>

<span data-ttu-id="89f04-106">ASP.NET Core アプリケーションは Web アプリケーションであり、通常、HTML、CSS、JavaScript など、クライアント側の Web テクノロジに依存します。</span><span class="sxs-lookup"><span data-stu-id="89f04-106">ASP.NET Core applications are web applications and they typically rely on client-side web technologies like HTML, CSS, and JavaScript.</span></span> <span data-ttu-id="89f04-107">ページ (HTML) のコンテンツをそのレイアウト、そのスタイル (CSS)、その動作 (JavaScript 経由) から切り離して、複雑な Web アプリでも関心の分離の原則を活用できます。</span><span class="sxs-lookup"><span data-stu-id="89f04-107">By separating the content of the page (the HTML) from its layout and styling (the CSS), and its behavior (via JavaScript), complex web apps can leverage the Separation of Concerns principle.</span></span> <span data-ttu-id="89f04-108">将来、アプリケーションの構造、設計、動作を変更することになっても、関心が絡み合わなければ簡単に変更できます。</span><span class="sxs-lookup"><span data-stu-id="89f04-108">Future changes to the structure, design, or behavior of the application can be made more easily when these concerns are not intertwined.</span></span>

<span data-ttu-id="89f04-109">HTML と CSS は比較的安定していますが、Web ベースのアプリケーションを構築する際に開発者が使用するアプリケーション フレームワークであり、ユーティリティである JavaScript は猛烈な速さで進化しています。</span><span class="sxs-lookup"><span data-stu-id="89f04-109">While HTML and CSS are relatively stable, JavaScript, by means of the application frameworks and utilities developers work with to build web-based applications, is evolving at breakneck speed.</span></span> <span data-ttu-id="89f04-110">この章では、アプリケーションを開発する際に Web 開発者がどのように JavaScript を使用するのかについて説明します。クライアント側のライブラリである Angular と React の概要も説明します。</span><span class="sxs-lookup"><span data-stu-id="89f04-110">This chapter looks at a few ways JavaScript is used by web developers as part of developing applications, as provides a high-level overview of the Angular and React client side libraries.</span></span>

## <a name="html"></a><span data-ttu-id="89f04-111">HTML</span><span class="sxs-lookup"><span data-stu-id="89f04-111">HTML</span></span>

<span data-ttu-id="89f04-112">HTML (ハイパーテキスト マークアップ言語) は、Web ページと Web アプリケーションの作成に利用される標準のマークアップ言語です。</span><span class="sxs-lookup"><span data-stu-id="89f04-112">HTML (HyperText Markup Language) is the standard markup language used to create web pages and web applications.</span></span> <span data-ttu-id="89f04-113">その要素でページの基本構成要素が形成され、書式設定されたテキスト、画像、フォーム入力、その他の構造体が表されます。</span><span class="sxs-lookup"><span data-stu-id="89f04-113">Its elements form the building blocks of pages, representing formatted text, images, form inputs, and other structures.</span></span> <span data-ttu-id="89f04-114">ページやアプリケーションの取得など、ブラウザーが URL に要求を行うと、最初に HTML ドキュメントが返されます。</span><span class="sxs-lookup"><span data-stu-id="89f04-114">When a browser makes a request to a URL, whether fetching a page or an application, the first thing that is returned is an HTML document.</span></span> <span data-ttu-id="89f04-115">この HTML ドキュメントは、CSS 形式で外観やレイアウトに関する情報を、あるいは JavaScript 形式で動作に関する情報を参照することがあります。そのような情報が含まれていることもあります。</span><span class="sxs-lookup"><span data-stu-id="89f04-115">This HTML document may reference or include additional information about its look and layout in the form of CSS, or behavior in the form of JavaScript.</span></span>

## <a name="css"></a><span data-ttu-id="89f04-116">CSS</span><span class="sxs-lookup"><span data-stu-id="89f04-116">CSS</span></span>

<span data-ttu-id="89f04-117">CSS (カスケード スタイル シート) は、HTML 要素の外観やレイアウトの制御に利用されます。</span><span class="sxs-lookup"><span data-stu-id="89f04-117">CSS (Cascading Style Sheets) is used to control the look and layout of HTML elements.</span></span> <span data-ttu-id="89f04-118">CSS スタイルは HTML 要素に直接適用したり、同じページで別途定義したりできます。あるいは、別個のファイルに定義し、ページで参照できます。</span><span class="sxs-lookup"><span data-stu-id="89f04-118">CSS styles can be applied directly to an HTML element, defined separately on the same page, or defined in a separate file and referenced by the page.</span></span> <span data-ttu-id="89f04-119">スタイルは、特定の HTML 要素を選択するときの使われ方に基づいてカスケードします。</span><span class="sxs-lookup"><span data-stu-id="89f04-119">Styles cascade based on how they are used to select a given HTML element.</span></span> <span data-ttu-id="89f04-120">たとえば、スタイルをドキュメント全体に適用しても、特定の要素に適用されるスタイルによってオーバーライドされることがあります。</span><span class="sxs-lookup"><span data-stu-id="89f04-120">For instance, a style might apply to an entire document, but would be overridden by a style that applied to a particular element.</span></span> <span data-ttu-id="89f04-121">同様に、ある要素固有のスタイルは、その要素に適用された CSS クラスに適用されるスタイルでオーバーライドされ、(その ID 経由で) その要素の特定のインスタンスを対象とするスタイルでさらにオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="89f04-121">Likewise, an element-specific style would be overridden by a style that applied to a CSS class that was applied to the element, which in turn would be overridden by a style targeting a specific instance of that element (via its id).</span></span> <span data-ttu-id="89f04-122">図 6-1</span><span class="sxs-lookup"><span data-stu-id="89f04-122">Figure 6-1</span></span>

<span data-ttu-id="89f04-123">**図 6-1.**</span><span class="sxs-lookup"><span data-stu-id="89f04-123">**Figure 6-1.**</span></span> <span data-ttu-id="89f04-124">CSS 特性ルールの順序</span><span class="sxs-lookup"><span data-stu-id="89f04-124">CSS Specificity rules, in order.</span></span>

![](./media/image6-1.png)

<span data-ttu-id="89f04-125">スタイルを別個のスタイルシート ファイルで保存し、選択ベースのカスケードを利用してアプリケーション内で一貫性があり、再利用可能なスタイルを実装することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="89f04-125">It's best to keep styles in their own separate stylesheet files, and to use selection-based cascading to implement consistent and reusable styles within the application.</span></span> <span data-ttu-id="89f04-126">HTML 内にスタイル ルールを置くことは避けてください。(要素のクラス全体または特定の CSS クラスが適用された要素ではなく) 特定の個別要素にスタイルを適用することはルールではなく、例外にしてください。</span><span class="sxs-lookup"><span data-stu-id="89f04-126">Placing style rules within HTML should be avoided, and applying styles to specific individual elements (rather than whole classes of elements, or elements that have had a particular CSS class applied to them) should be the exception, not the rule.</span></span>

### <a name="css-preprocessors"></a><span data-ttu-id="89f04-127">CSS プリプロセッサ</span><span class="sxs-lookup"><span data-stu-id="89f04-127">CSS preprocessors</span></span>

<span data-ttu-id="89f04-128">CSS スタイルシートは、条件付きロジック、変数、その他のプログラミング言語機能に対応していません。</span><span class="sxs-lookup"><span data-stu-id="89f04-128">CSS stylesheets lack support for conditional logic, variables, and other programming language features.</span></span> <span data-ttu-id="89f04-129">そのため、大きなスタイルシートには多くの場合、たくさんの繰り返しが含まれます。同じ色、フォント、その他の設定がさまざまなバリエーションの HTML 要素と CSS クラスに適用されるためです。</span><span class="sxs-lookup"><span data-stu-id="89f04-129">Thus, large stylesheets often include a lot of repetition, as the same color, font, or other setting is applied to many different variations of HTML elements and CSS classes.</span></span> <span data-ttu-id="89f04-130">CSS プリプロセッサを利用すると、スタイルシートは変数やロジックに対応し、[DRY 原則](https://deviq.com/don-t-repeat-yourself/)に従うことができます。</span><span class="sxs-lookup"><span data-stu-id="89f04-130">CSS preprocessors can help your stylesheets follow the [DRY principle](https://deviq.com/don-t-repeat-yourself/) by adding support for variables and logic.</span></span>

<span data-ttu-id="89f04-131">最も人気のある CSS プリプロセッサは Sass と LESS です。</span><span class="sxs-lookup"><span data-stu-id="89f04-131">The most popular CSS preprocessors are Sass and LESS.</span></span> <span data-ttu-id="89f04-132">いずれも CSS を拡張し、CSS との後方互換性があります。つまり、プレーンな CSS ファイルは有効な Sass または LESS ファイルとなります。</span><span class="sxs-lookup"><span data-stu-id="89f04-132">Both extend CSS and are backward compatible with it, meaning that a plain CSS file is a valid Sass or LESS file.</span></span> <span data-ttu-id="89f04-133">Sass は Ruby ベースであり、LESS は JavaScript ベースであり、いずれも通常、ローカル開発プロセスの一部として実行され、</span><span class="sxs-lookup"><span data-stu-id="89f04-133">Sass is Ruby-based and LESS is JavaScript based, and both typically run as part of your local development process.</span></span> <span data-ttu-id="89f04-134">コマンド ライン ツールを利用できます。また、Visual Studio にサポートが組み込まれており、Gulp タスクや Grunt タスクで実行できます。</span><span class="sxs-lookup"><span data-stu-id="89f04-134">Both have command line tools available, as well as built-in support in Visual Studio for running them using Gulp or Grunt tasks.</span></span>

## <a name="javascript"></a><span data-ttu-id="89f04-135">JavaScript</span><span class="sxs-lookup"><span data-stu-id="89f04-135">JavaScript</span></span>

<span data-ttu-id="89f04-136">JavaScript は解釈型 (コンパイルされず、直接解釈される) の動的なプログラミング言語であり、ECMAScript 言語仕様で標準化されています。</span><span class="sxs-lookup"><span data-stu-id="89f04-136">JavaScript is a dynamic, interpreted programming language that has been standardized in the ECMAScript language specification.</span></span> <span data-ttu-id="89f04-137">Web のプログラミング言語です。</span><span class="sxs-lookup"><span data-stu-id="89f04-137">It is the programming language of the web.</span></span> <span data-ttu-id="89f04-138">CSS と同様に、JavaScript は HTML 要素内の属性として、ページや個別ファイル内のスクリプト ブロックとして定義できます。</span><span class="sxs-lookup"><span data-stu-id="89f04-138">Like CSS, JavaScript can be defined as attributes within HTML elements, as blocks of script within a page, or in separate files.</span></span> <span data-ttu-id="89f04-139">CSS と同様に、一般的に、JavaScript は個別ファイルに整理することをお勧めします。個々の Web ページやアプリケーション ビューの HTML からは可能な限り切り離します。</span><span class="sxs-lookup"><span data-stu-id="89f04-139">Just like CSS, it's generally recommended to organize JavaScript into separate files, keeping it separated as much as possible from the HTML found on individual web pages or application views.</span></span>

<span data-ttu-id="89f04-140">Web アプリケーションで JavaScript を使用するとき、通常実行する必要があるタスクがいくつか存在します。</span><span class="sxs-lookup"><span data-stu-id="89f04-140">When working with JavaScript in your web application, there are a few tasks that you'll commonly need to perform:</span></span>

- <span data-ttu-id="89f04-141">HTML 要素を選択し、その値を取得したり更新したりする</span><span class="sxs-lookup"><span data-stu-id="89f04-141">Selecting an HTML element and retrieving and/or updating its value.</span></span>

- <span data-ttu-id="89f04-142">Web API にデータを問い合わせる</span><span class="sxs-lookup"><span data-stu-id="89f04-142">Querying a Web API for data.</span></span>

- <span data-ttu-id="89f04-143">Web API にコマンドを送信する (コールバックにその結果で応答する)</span><span class="sxs-lookup"><span data-stu-id="89f04-143">Sending a command to a Web API (and responding to a callback with its result).</span></span>

- <span data-ttu-id="89f04-144">検証を実行する</span><span class="sxs-lookup"><span data-stu-id="89f04-144">Performing validation.</span></span>

<span data-ttu-id="89f04-145">これらのタスクをすべて JavaScript のみで実行できますが、タスクを楽にするライブラリがたくさん存在します。</span><span class="sxs-lookup"><span data-stu-id="89f04-145">You can perform all of these tasks with JavaScript alone, but many libraries exist to make these tasks easier.</span></span> <span data-ttu-id="89f04-146">そのようなライブラリで最も人気があるのが jQuery であり、Web ページで上記のタスクを簡単にするライブラリとして選ばれ続けています。</span><span class="sxs-lookup"><span data-stu-id="89f04-146">One of the first and most successful of these libraries is jQuery, which continues to be a popular choice for simplifying these tasks on web pages.</span></span> <span data-ttu-id="89f04-147">シングルページ アプリケーション (SPA) の場合、求められるさまざまな機能を jQuery は提供しません。Angular と React は提供します。</span><span class="sxs-lookup"><span data-stu-id="89f04-147">For Single Page Applications (SPAs), jQuery doesn't provide many of the desired features that Angular and React offer.</span></span>

### <a name="legacy-web-apps-with-jquery"></a><span data-ttu-id="89f04-148">旧 Web アプリと jQuery</span><span class="sxs-lookup"><span data-stu-id="89f04-148">Legacy web apps with jQuery</span></span>

<span data-ttu-id="89f04-149">JavaScript フレームワーク標準としては旧式ですが、jQuery は今後も、HTML/CSS を使用するときや AJAX で Web API を呼び出すアプリケーションを構築するときに広く使用されるライブラリであり続けます。</span><span class="sxs-lookup"><span data-stu-id="89f04-149">Although ancient by JavaScript framework standards, jQuery continues to be a very commonly used library for working with HTML/CSS and building applications that make AJAX calls to web APIs.</span></span> <span data-ttu-id="89f04-150">しかしながら、jQuery はブラウザーの DOM (Document Object Model/ドキュメント オブジェクト モデル) レベルで動作し、既定では、宣言型ではなく命令型のモデルのみ提供します。</span><span class="sxs-lookup"><span data-stu-id="89f04-150">However, jQuery operates at the level of the browser document object model (DOM), and by default offers only an imperative, rather than declarative, model.</span></span>

<span data-ttu-id="89f04-151">たとえば、テキストボックスの値が 10 を超えるとき、ページ上の要素を表示するとします。</span><span class="sxs-lookup"><span data-stu-id="89f04-151">For example, imagine that if a textbox's value exceeds 10, an element on the page should be made visible.</span></span> <span data-ttu-id="89f04-152">jQuery では、これは通常、テキストボックスの値を調べ、その値に基づいてターゲット要素の可視性を設定するコードでイベント ハンドラーを記述して実装されます。</span><span class="sxs-lookup"><span data-stu-id="89f04-152">In jQuery, this would typically be implemented by writing an event handler with code that would inspect the textbox's value and set the visibility of the target element based on that value.</span></span> <span data-ttu-id="89f04-153">これはコードを基盤とする命令型の手法です。</span><span class="sxs-lookup"><span data-stu-id="89f04-153">This is an imperative, code-based approach.</span></span> <span data-ttu-id="89f04-154">別のフレームワークとしては、代わりにデータ バインディングを利用し、要素の可視性をテキストボックスの値に宣言によってバインドするという方法が考えられます。</span><span class="sxs-lookup"><span data-stu-id="89f04-154">Another framework might instead use databinding to bind the visibility of the element to the value of the textbox declaratively.</span></span> <span data-ttu-id="89f04-155">この場合、コードを記述する必要はありませんが、データ バインディング属性に伴う要素を装飾する必要があります。</span><span class="sxs-lookup"><span data-stu-id="89f04-155">This would not require writing any code, but instead only requires decorating the elements involved with data binding attributes.</span></span> <span data-ttu-id="89f04-156">クライアント側の動作が複雑になると、データ バインディングの手法は一般的に、コードが少なく、条件が複雑ではなく、ソリューションとして単純になります。</span><span class="sxs-lookup"><span data-stu-id="89f04-156">As client side behaviors grow more complex, data binding approaches frequently result in simpler solutions with less code and conditional complexity.</span></span>

### <a name="jquery-vs-a-spa-framework"></a><span data-ttu-id="89f04-157">jQuery と SPA フレームワークの比較</span><span class="sxs-lookup"><span data-stu-id="89f04-157">jQuery vs a SPA Framework</span></span>

| <span data-ttu-id="89f04-158">**要素**</span><span class="sxs-lookup"><span data-stu-id="89f04-158">**Factor**</span></span> | <span data-ttu-id="89f04-159">**jQuery**</span><span class="sxs-lookup"><span data-stu-id="89f04-159">**jQuery**</span></span> | <span data-ttu-id="89f04-160">**Angular**</span><span class="sxs-lookup"><span data-stu-id="89f04-160">**Angular**</span></span>|
|--------------------------|------------|-------------|
| <span data-ttu-id="89f04-161">DOM を抽象化する</span><span class="sxs-lookup"><span data-stu-id="89f04-161">Abstracts the DOM</span></span> | <span data-ttu-id="89f04-162">**はい**</span><span class="sxs-lookup"><span data-stu-id="89f04-162">**Yes**</span></span> | <span data-ttu-id="89f04-163">**はい**</span><span class="sxs-lookup"><span data-stu-id="89f04-163">**Yes**</span></span> |
| <span data-ttu-id="89f04-164">AJAX サポート</span><span class="sxs-lookup"><span data-stu-id="89f04-164">AJAX Support</span></span> | <span data-ttu-id="89f04-165">**はい**</span><span class="sxs-lookup"><span data-stu-id="89f04-165">**Yes**</span></span> | <span data-ttu-id="89f04-166">**はい**</span><span class="sxs-lookup"><span data-stu-id="89f04-166">**Yes**</span></span> |
| <span data-ttu-id="89f04-167">宣言型データ バインディング</span><span class="sxs-lookup"><span data-stu-id="89f04-167">Declarative Data Binding</span></span> | <span data-ttu-id="89f04-168">**No**</span><span class="sxs-lookup"><span data-stu-id="89f04-168">**No**</span></span> | <span data-ttu-id="89f04-169">**はい**</span><span class="sxs-lookup"><span data-stu-id="89f04-169">**Yes**</span></span> |
| <span data-ttu-id="89f04-170">MVC スタイルのルーティング</span><span class="sxs-lookup"><span data-stu-id="89f04-170">MVC-style Routing</span></span> | <span data-ttu-id="89f04-171">**No**</span><span class="sxs-lookup"><span data-stu-id="89f04-171">**No**</span></span> | <span data-ttu-id="89f04-172">**はい**</span><span class="sxs-lookup"><span data-stu-id="89f04-172">**Yes**</span></span> |
| <span data-ttu-id="89f04-173">テンプレート</span><span class="sxs-lookup"><span data-stu-id="89f04-173">Templating</span></span> | <span data-ttu-id="89f04-174">**No**</span><span class="sxs-lookup"><span data-stu-id="89f04-174">**No**</span></span> | <span data-ttu-id="89f04-175">**はい**</span><span class="sxs-lookup"><span data-stu-id="89f04-175">**Yes**</span></span> |
| <span data-ttu-id="89f04-176">ディープリンク ルーティング</span><span class="sxs-lookup"><span data-stu-id="89f04-176">Deep-Link Routing</span></span> | <span data-ttu-id="89f04-177">**No**</span><span class="sxs-lookup"><span data-stu-id="89f04-177">**No**</span></span> | <span data-ttu-id="89f04-178">**はい**</span><span class="sxs-lookup"><span data-stu-id="89f04-178">**Yes**</span></span> |

<span data-ttu-id="89f04-179">jQuery にない機能の多くは、他のライブラリを追加すると補充できます。</span><span class="sxs-lookup"><span data-stu-id="89f04-179">Most of the features jQuery lacks intrinsically can be added with the addition of other libraries.</span></span> <span data-ttu-id="89f04-180">ただし、Angular のような SPA フレームワークはそのような機能を一層統合された形式で提供します。全部含めることを念頭に始めから設計されているためです。</span><span class="sxs-lookup"><span data-stu-id="89f04-180">However, a SPA framework like Angular provides these features in a more integrated fashion, since it's been designed with all of them in mind from the start.</span></span> <span data-ttu-id="89f04-181">また、jQuery は非常に命令的なライブラリです。つまり、jQuery で何かを行うには、jQuery 機能を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="89f04-181">Also, jQuery is a very imperative library, meaning that you need to call jQuery functions in order to do anything with jQuery.</span></span> <span data-ttu-id="89f04-182">SPA フレームワークの作業や機能の多くは宣言によって完了できます。実際のコードを記述する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="89f04-182">Much of the work and functionality that SPA frameworks provide can be done declaratively, requiring no actual code to be written.</span></span>

<span data-ttu-id="89f04-183">データ バインディングがその好例です。</span><span class="sxs-lookup"><span data-stu-id="89f04-183">Data binding is a great example of this.</span></span> <span data-ttu-id="89f04-184">jQuery では、通常、1 つの DOM 要素の値を取得したり、1 つの要素の値を設定したりするのにコード行を 1 行のみ必要とします。</span><span class="sxs-lookup"><span data-stu-id="89f04-184">In jQuery, it usually only takes one line of code to get the value of a DOM element, or to set an element's value.</span></span> <span data-ttu-id="89f04-185">しかしながら、要素の値を変更しなければならないときは必ずこのコードを記述する必要があります。1 ページで複数の関数にコードを記述することもあります。</span><span class="sxs-lookup"><span data-stu-id="89f04-185">However, you have to write this code any time you need to change the value of the element, and sometimes this will occur in multiple functions on a page.</span></span> <span data-ttu-id="89f04-186">典型的な例をもう 1 つ挙げると要素の可視性があります。</span><span class="sxs-lookup"><span data-stu-id="89f04-186">Another common example is element visibility.</span></span> <span data-ttu-id="89f04-187">jQuery では、さまざま箇所でコードを記述し、特定の要素の可視性を制御することがあります。</span><span class="sxs-lookup"><span data-stu-id="89f04-187">In jQuery, there might be many different places where you would write code to control whether certain elements were visible.</span></span> <span data-ttu-id="89f04-188">いずれの場合でも、データ バインディングを使用するとき、コードを記述する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="89f04-188">In each of these cases, when using data binding, no code would need to be written.</span></span> <span data-ttu-id="89f04-189">該当要素の値または可視性をページの*ビューモデル*にバインドするだけで、そのビューモデルの変更がバインド対象の要素に自動的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="89f04-189">You would simply bind the value or visibility of the element(s) in question to a *viewmodel* on the page, and changes to that viewmodel would automatically be reflected in the bound elements.</span></span>

### <a name="angular-spas"></a><span data-ttu-id="89f04-190">Angular SPA</span><span class="sxs-lookup"><span data-stu-id="89f04-190">Angular SPAs</span></span>

<span data-ttu-id="89f04-191">AngularJS は短期間で世界で最も人気がある JavaScript フレームワークとなりました。</span><span class="sxs-lookup"><span data-stu-id="89f04-191">AngularJS quickly became one of the world's most popular JavaScript frameworks.</span></span> <span data-ttu-id="89f04-192">Angular 2 では、([TypeScript](https://www.typescriptlang.org/) を利用し) フレームワークが一から作り直され、AngularJS から Angular にブランド名が変更されました。</span><span class="sxs-lookup"><span data-stu-id="89f04-192">With Angular 2, the team rebuilt the framework from the ground up (using [TypeScript](https://www.typescriptlang.org/)) and rebranded from AngularJS to simply Angular.</span></span> <span data-ttu-id="89f04-193">現在バージョン 4 の Angular は、シングル ページ アプリケーションを構築するための堅牢なフレームワークであり続けています。</span><span class="sxs-lookup"><span data-stu-id="89f04-193">Currently on version 4, Angular continues to be a robust framework for building Single Page Applications.</span></span>

<span data-ttu-id="89f04-194">Angular アプリケーションはコンポーネントから構築されます。</span><span class="sxs-lookup"><span data-stu-id="89f04-194">Angular applications are built from components.</span></span> <span data-ttu-id="89f04-195">コンポーネントは HTML テンプレートと特別なオブジェクトを組み合わせ、ページの一部を制御します。</span><span class="sxs-lookup"><span data-stu-id="89f04-195">Components combine HTML templates with special objects and control a portion of the page.</span></span> <span data-ttu-id="89f04-196">次は Angular のドキュメントから抜粋した単純なコンポーネントです。</span><span class="sxs-lookup"><span data-stu-id="89f04-196">A simple component from Angular's docs is shown here:</span></span>

```js
import { Component } from '@angular/core';

@Component({
    selector: 'my-app',
    template: `<h1>Hello {{name}}</h1>`
})

export class AppComponent { name = 'Angular'; }
```

<span data-ttu-id="89f04-197">コンポーネントは @Component デコレーター関数を利用して定義されます。この関数はコンポーネントに関するメタデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="89f04-197">Components are defined using the @Component decorator function, which takes in metadata about the component.</span></span> <span data-ttu-id="89f04-198">セレクター プロパティは、このコンポーネントが表示されるページで要素の ID を識別します。</span><span class="sxs-lookup"><span data-stu-id="89f04-198">The selector property identifies the id of the element on the page where this component will be displayed.</span></span> <span data-ttu-id="89f04-199">テンプレート プロパティは単純な HTML テンプレートであり、コンポーネントの名前プロパティに対応するプレースホルダーが最後の行に定義されます。</span><span class="sxs-lookup"><span data-stu-id="89f04-199">The template property is a simple HTML template that includes a placeholder that corresponds to the component's name property, defined on the last line.</span></span>

<span data-ttu-id="89f04-200">DOM 要素の代わりに、コンポーネントとテンプレートを使用して、Angular アプリは高いレベルの抽象化で動作できます。JavaScript ("vanilla JS" とも呼ばれる) や jQuery だけで記述されたアプリより全体的なコードが少なくなります。</span><span class="sxs-lookup"><span data-stu-id="89f04-200">By working with components and templates, instead of DOM elements, Angular apps can operate at a higher level of abstraction and with less overall code than apps written using just JavaScript (also called "vanilla JS") or with jQuery.</span></span> <span data-ttu-id="89f04-201">Angular にはまた、クライアント側のスクリプト ファイルの整理方法についていくつかの指示があります。</span><span class="sxs-lookup"><span data-stu-id="89f04-201">Angular also imposes some order on how you organize your client-side script files.</span></span> <span data-ttu-id="89f04-202">慣例では、Angular アプリは一般的なフォルダー構造を利用します。モジュールとコンポーネントのスクリプト ファイルはアプリ フォルダーに置かれます。</span><span class="sxs-lookup"><span data-stu-id="89f04-202">By convention, Angular apps use a common folder structure, with module and component script files located in an app folder.</span></span> <span data-ttu-id="89f04-203">アプリの構築、配置、試験に関連する Angular スクリプトは通常、上位のフォルダーに置かれます。</span><span class="sxs-lookup"><span data-stu-id="89f04-203">Angular scripts concerned with building, deploying, and testing the app are typically located in a higher-level folder.</span></span>

<span data-ttu-id="89f04-204">Angular ではまた、コマンド ライン インターフェイス (CLI) ツーリングを多用します。</span><span class="sxs-lookup"><span data-stu-id="89f04-204">Angular also makes great use of command line interface (CLI) tooling.</span></span> <span data-ttu-id="89f04-205">Angular のローカル開発は (git と npm を既にインストールしていると想定します)、GitHub からリポジトリを複製し、`npm install` と `npm start` を実行するだけで始められます。</span><span class="sxs-lookup"><span data-stu-id="89f04-205">Getting started with Angular development locally (assuming you already have git and npm installed) consists of simply cloning a repo from GitHub and running `npm install` and `npm start`.</span></span> <span data-ttu-id="89f04-206">それが終わると、Angular から独自の CLI ツールが提供されます。プロジェクトを作成したり、ファイルを追加したり、タスクのテスト、バンドル化、展開に役立てたりできます。</span><span class="sxs-lookup"><span data-stu-id="89f04-206">Beyond this, Angular ships its own CLI tool which can create projects, add files, and assist with testing, bundling, and deployment tasks.</span></span> <span data-ttu-id="89f04-207">この CLI ツールは使い勝手が良く、Angular と ASP.NET Core の親和性を上げます。ASP.NET Core もまた、CLI のサポート機能に優れています。</span><span class="sxs-lookup"><span data-stu-id="89f04-207">This CLI tooling friendliness makes Angular especially compatible with ASP.NET Core, which also features great CLI support.</span></span>

<span data-ttu-id="89f04-208">Microsoft は [eShopOnContainers](https://aka.ms/MicroservicesArchitecture) という参照アプリケーションを開発しました。このアプリケーションには、Angular SPA 実装が含まれています。</span><span class="sxs-lookup"><span data-stu-id="89f04-208">Microsoft has developed a reference application, [eShopOnContainers](https://aka.ms/MicroservicesArchitecture), which includes an Angular SPA implementation.</span></span> <span data-ttu-id="89f04-209">このアプリには、オンライン ストアの買い物カゴを管理し、そのカタログから品物を読み込んで表示し、注文作成を処理するための Angular モジュールが含まれています。</span><span class="sxs-lookup"><span data-stu-id="89f04-209">This app includes Angular modules to manage the online store's shopping basket, load and display items from its catalog, and handling order creation.</span></span> <span data-ttu-id="89f04-210">[GitHub](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Web/WebSPA) でサンプル アプリケーションを表示したり、ダウンロードしたりできます。</span><span class="sxs-lookup"><span data-stu-id="89f04-210">You can view and download the sample application from [GitHub](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Web/WebSPA).</span></span>

### <a name="react"></a><span data-ttu-id="89f04-211">React</span><span class="sxs-lookup"><span data-stu-id="89f04-211">React</span></span>

<span data-ttu-id="89f04-212">Model-View-Controller パターンの完全実装を提供する Angular とは異なり、React はビューのみを扱います。</span><span class="sxs-lookup"><span data-stu-id="89f04-212">Unlike Angular, which offers a full Model-View-Controller pattern implementation, React is only concerned with views.</span></span> <span data-ttu-id="89f04-213">これはフレームワークではなく、単なるライブラリです。そのため、SPA を構築するには、追加のライブラリを活用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="89f04-213">It's not a framework, just a library, so to build a SPA you'll need to leverage additional libraries.</span></span>

<span data-ttu-id="89f04-214">React の最も重要な特徴の 1 つはそれが仮想 DOM を使用することです。</span><span class="sxs-lookup"><span data-stu-id="89f04-214">One of React's most important features is its use of a virtual DOM.</span></span> <span data-ttu-id="89f04-215">仮想 DOM は React にさまざまな利点を与えます。たとえば、パフォーマンスが上がります。仮想 DOM は、実際の DOM の更新が必要な部分を最適化できます。また、テストが可能になります。React や React とその仮想 DOM のやり取りをテストするためのブラウザーを用意する必要がありません。</span><span class="sxs-lookup"><span data-stu-id="89f04-215">The virtual DOM provides React with several advantages, including performance (the virtual DOM can optimize which parts of the actual DOM need to be updated) and testability (no need to have a browser to test React and its interactions with its virtual DOM).</span></span>

<span data-ttu-id="89f04-216">React はまた、HTML と連動するしくみが変わっています。</span><span class="sxs-lookup"><span data-stu-id="89f04-216">React is also unusual in how it works with HTML.</span></span> <span data-ttu-id="89f04-217">(HTML 属性に表示される JavaScript を参照し) コードとマークアップを厳密に分離するのではなく、React はその JavaScript コード内に JSX として HTML を直接追加します。</span><span class="sxs-lookup"><span data-stu-id="89f04-217">Rather than having a strict separation between code and markup (with references to JavaScript appearing in HTML attributes perhaps), React adds HTML directly within its JavaScript code as JSX.</span></span> <span data-ttu-id="89f04-218">JSX は HTML に似た構文であり、純粋な JavaScript にコンパイルできます。</span><span class="sxs-lookup"><span data-stu-id="89f04-218">JSX is HTML-like syntax that can compile down to pure JavaScript.</span></span> <span data-ttu-id="89f04-219">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="89f04-219">For example:</span></span>

```js
<ul>
{ authors.map(author =>
    <li key={author.id}>{author.name}</li>
)}
</ul>
```

<span data-ttu-id="89f04-220">JavaScript を既に理解している場合、React は簡単に学習できます。</span><span class="sxs-lookup"><span data-stu-id="89f04-220">If you already know JavaScript, learning React should be easy.</span></span> <span data-ttu-id="89f04-221">Angular やその他の人気ライブラリほどの学習曲線はなく、特別な構文もありません。</span><span class="sxs-lookup"><span data-stu-id="89f04-221">There isn't nearly as much learning curve or special syntax involved as with Angular or other popular libraries.</span></span>

<span data-ttu-id="89f04-222">React は完全なフレームワークではないため、通常、ルーティング、Web API 呼び出し、依存性管理などを処理する他のライブラリが必要になります。</span><span class="sxs-lookup"><span data-stu-id="89f04-222">Because React isn't a full framework, you'll typically want other libraries to handle things like routing, web API calls, and dependency management.</span></span> <span data-ttu-id="89f04-223">その良い点は、それぞれに適したライブラリを選択できることです。ただし、欠点があり、自分ですべてを決定し、完了後、選択したライブラリがうまく連動することを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="89f04-223">The nice thing is, you can pick the best library for each of these, but the disadvantage is that you need to make all of these decisions and verify all of your chosen libraries work well together when you're done.</span></span> <span data-ttu-id="89f04-224">最良の出発点を求める場合、React Slingshot のようなスターター キットを利用できます。React と互換性のあるライブラリが既にパッケージ化されています。</span><span class="sxs-lookup"><span data-stu-id="89f04-224">If you want a good starting point, you can use a starter kit like React Slingshot, which prepackages a set of compatible libraries together with React.</span></span>

### <a name="choosing-a-spa-framework"></a><span data-ttu-id="89f04-225">SPA フレームワークを選択する</span><span class="sxs-lookup"><span data-stu-id="89f04-225">Choosing a SPA Framework</span></span>

<span data-ttu-id="89f04-226">SPA をサポートする最良の JavaScript フレームワークを求めるとき、次の事項を考慮に入れてください。</span><span class="sxs-lookup"><span data-stu-id="89f04-226">When considering which JavaScript framework will work best to support your SPA, keep in mind the following considerations:</span></span>

- <span data-ttu-id="89f04-227">チームはそのフレームワークとその依存関係に精通しているか (場合によっては TypeScript が含まれます)</span><span class="sxs-lookup"><span data-stu-id="89f04-227">Is your team familiar with the framework and its dependencies (including TypeScript in some cases)?</span></span>

- <span data-ttu-id="89f04-228">そのフレームワークの自己主張性はどの程度か (どのくらい自由度が少ないか)。そのフレームワークの初期設定には同意できるか。</span><span class="sxs-lookup"><span data-stu-id="89f04-228">How opinionated is the framework, and do you agree with its default way of doing things?</span></span>

- <span data-ttu-id="89f04-229">そのフレームワーク (またはそれに伴うライブラリ) にアプリで必要とされる機能がすべて含まれているか。</span><span class="sxs-lookup"><span data-stu-id="89f04-229">Does it (or a companion library) include all of the features your app requires?</span></span>

- <span data-ttu-id="89f04-230">文書は適切に用意されているか。</span><span class="sxs-lookup"><span data-stu-id="89f04-230">Is it well-documented?</span></span>

- <span data-ttu-id="89f04-231">そのコミュニティはどのくらい活発か。</span><span class="sxs-lookup"><span data-stu-id="89f04-231">How active is its community?</span></span> <span data-ttu-id="89f04-232">新しいプロジェクトはそれで構築されているか。</span><span class="sxs-lookup"><span data-stu-id="89f04-232">Are new projects building built with it?</span></span>

- <span data-ttu-id="89f04-233">そのコア チームはどのくらい活発か。</span><span class="sxs-lookup"><span data-stu-id="89f04-233">How active is its core team?</span></span> <span data-ttu-id="89f04-234">問題は解決されているか。新しいバージョンは定期的に出荷されているか。</span><span class="sxs-lookup"><span data-stu-id="89f04-234">Are issues being resolved and new versions shipped regularly?</span></span>

<span data-ttu-id="89f04-235">JavaScript フレームワークは今後も猛烈な速さで進化を続けます。</span><span class="sxs-lookup"><span data-stu-id="89f04-235">JavaScript frameworks continue to evolve with breakneck speed.</span></span> <span data-ttu-id="89f04-236">上記の事項を考慮し、後で後悔するようなフレームワークを選択するリスクを減らしてください。</span><span class="sxs-lookup"><span data-stu-id="89f04-236">Use the considerations listed above to help mitigate the risk of choosing a framework you'll later regret being dependent upon.</span></span> <span data-ttu-id="89f04-237">リスクが特別に好まれない場合、商業的サポートを提供しているフレームワークか大企業が開発しているフレームワークを検討してください。</span><span class="sxs-lookup"><span data-stu-id="89f04-237">If you're particularly risk-averse, consider a framework that offers commercial support and/or is being developed by a large enterprise.</span></span>

> ### <a name="references--client-web-technologies"></a><span data-ttu-id="89f04-238">参照 - クライアント Web テクノロジ</span><span class="sxs-lookup"><span data-stu-id="89f04-238">References – Client Web Technologies</span></span>
> - <span data-ttu-id="89f04-239">**HTML と CSS**</span><span class="sxs-lookup"><span data-stu-id="89f04-239">**HTML and CSS**</span></span>  
> <https://www.w3.org/standards/webdesign/htmlcss>
> - <span data-ttu-id="89f04-240">**Sass とLESS の比較**</span><span class="sxs-lookup"><span data-stu-id="89f04-240">**Sass vs. LESS**</span></span>  
> <https://www.keycdn.com/blog/sass-vs-less/>
> - <span data-ttu-id="89f04-241">**LESS、Sass、Font Awesome で ASP.NET Core アプリのスタイルを設定する**</span><span class="sxs-lookup"><span data-stu-id="89f04-241">**Styling ASP.NET Core Apps with LESS, Sass, and Font Awesome**</span></span>  
> <https://docs.microsoft.com/aspnet/core/client-side/less-sass-fa>
> - <span data-ttu-id="89f04-242">**ASP.NET Core のクライアント側開発**</span><span class="sxs-lookup"><span data-stu-id="89f04-242">**Client-Side Development in ASP.NET Core**</span></span>  
> <https://docs.microsoft.com/aspnet/core/client-side/>
> - <span data-ttu-id="89f04-243">**jQuery**</span><span class="sxs-lookup"><span data-stu-id="89f04-243">**jQuery**</span></span>  
> <https://jquery.com/>
> - <span data-ttu-id="89f04-244">**jQuery と AngularJS の比較**</span><span class="sxs-lookup"><span data-stu-id="89f04-244">**jQuery vs AngularJS**</span></span>  
> <https://www.airpair.com/angularjs/posts/jquery-angularjs-comparison-migration-walkthrough>
> - <span data-ttu-id="89f04-245">**Angular**</span><span class="sxs-lookup"><span data-stu-id="89f04-245">**Angular**</span></span>  
> <https://angular.io/>
> - <span data-ttu-id="89f04-246">**React**</span><span class="sxs-lookup"><span data-stu-id="89f04-246">**React**</span></span>  
> <https://facebook.github.io/react/>
> - <span data-ttu-id="89f04-247">**React Slingshot**</span><span class="sxs-lookup"><span data-stu-id="89f04-247">**React Slingshot**</span></span>  
> <https://github.com/coryhouse/react-slingshot>
> - <span data-ttu-id="89f04-248">**React と Angular 2 の比較**</span><span class="sxs-lookup"><span data-stu-id="89f04-248">**React vs Angular 2 Comparison**</span></span>  
> <https://www.codementor.io/codementorteam/react-vs-angular-2-comparison-beginners-guide-lvz5710ha>
> - <span data-ttu-id="89f04-249">**2017 年度最高の JavaScript フレームワーク 5 作品**</span><span class="sxs-lookup"><span data-stu-id="89f04-249">**5 Best JavaScript Frameworks of 2017**</span></span>  
> <https://hackernoon.com/5-best-javascript-frameworks-in-2017-7a63b3870282>

>[!div class="step-by-step"]
><span data-ttu-id="89f04-250">[前へ](common-web-application-architectures.md)
>[次へ](develop-asp-net-core-mvc-apps.md)</span><span class="sxs-lookup"><span data-stu-id="89f04-250">[Previous](common-web-application-architectures.md)
[Next](develop-asp-net-core-mvc-apps.md)</span></span>