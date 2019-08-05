---
title: Web API を使用したマイクロサービス アプリケーション レイヤーの実装
description: コンテナー化された .NET アプリケーションの .NET マイクロサービス アーキテクチャ | Web API アプリケーション レイヤーの依存関係挿入、メディエーター パターン、その実装詳細について。
ms.date: 10/08/2018
ms.openlocfilehash: c8447cfcd3155a873d61ee9287f58774392c279d
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68676579"
---
# <a name="implement-the-microservice-application-layer-using-the-web-api"></a><span data-ttu-id="4485f-103">Web API を使用してマイクロサービス アプリケーション レイヤーを実装する</span><span class="sxs-lookup"><span data-stu-id="4485f-103">Implement the microservice application layer using the Web API</span></span>

## <a name="use-dependency-injection-to-inject-infrastructure-objects-into-your-application-layer"></a><span data-ttu-id="4485f-104">依存関係挿入を使用し、アプリケーション レイヤーにインフラストラクチャ オブジェクトを挿入する</span><span class="sxs-lookup"><span data-stu-id="4485f-104">Use Dependency Injection to inject infrastructure objects into your application layer</span></span>

<span data-ttu-id="4485f-105">前のセクションで述べたように、アプリケーション レイヤーは、Web API プロジェクトや MVC Web アプリ プロジェクトなどで作成する成果物 (アセンブリ) の一部として実装できます。</span><span class="sxs-lookup"><span data-stu-id="4485f-105">As mentioned previously, the application layer can be implemented as part of the artifact (assembly) you are building, such as within a Web API project or an MVC web app project.</span></span> <span data-ttu-id="4485f-106">ASP.NET Core を使用して作成されたマイクロサービスの場合、アプリケーション レイヤーは通常、Web API ライブラリになります。</span><span class="sxs-lookup"><span data-stu-id="4485f-106">In the case of a microservice built with ASP.NET Core, the application layer will usually be your Web API library.</span></span> <span data-ttu-id="4485f-107">ASP.NET Core から来るもの (そのインフラストラクチャとコントローラー) を、カスタム アプリケーション レイヤー コードと分離したい場合は、アプリケーション レイヤーを別のクラス ライブラリに配置することもできますが、これは任意です。</span><span class="sxs-lookup"><span data-stu-id="4485f-107">If you want to separate what is coming from ASP.NET Core (its infrastructure plus your controllers) from your custom application layer code, you could also place your application layer in a separate class library, but that is optional.</span></span>

<span data-ttu-id="4485f-108">たとえば、注文マイクロサービスのアプリケーション レイヤー コードは、**Ordering.API** プロジェクト (ASP.NET Core Web API プロジェクト) の一部として直接実装されます (図 7-23 を参照)。</span><span class="sxs-lookup"><span data-stu-id="4485f-108">For instance, the application layer code of the ordering microservice is directly implemented as part of the **Ordering.API** project (an ASP.NET Core Web API project), as shown in Figure 7-23.</span></span>

![Ordering.API マイクロサービスのソリューション エクスプローラー ビュー。[Application] フォルダーの下に、Behaviors、Commands、DomainEventHandlers、IntegrationEvents、Models、Queries、Validations というサブフォルダーを確認できます。](./media/image20.png)

<span data-ttu-id="4485f-110">**図 7-23**.</span><span class="sxs-lookup"><span data-stu-id="4485f-110">**Figure 7-23**.</span></span> <span data-ttu-id="4485f-111">Ordering.API ASP.NET Core Web API プロジェクト内のアプリケーション レイヤー</span><span class="sxs-lookup"><span data-stu-id="4485f-111">The application layer in the Ordering.API ASP.NET Core Web API project</span></span>

<span data-ttu-id="4485f-112">ASP.NET Core には、コンストラクター挿入を既定でサポートするシンプルな[組み込み IoC コンテナー](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) (IServiceProvider インターフェイスによって表されます) が含まれています。ASP.NET では、DI によって特定のサービスを利用可能にすることができます。</span><span class="sxs-lookup"><span data-stu-id="4485f-112">ASP.NET Core includes a simple [built-in IoC container](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) (represented by the IServiceProvider interface) that supports constructor injection by default, and ASP.NET makes certain services available through DI.</span></span> <span data-ttu-id="4485f-113">ASP.NET Core では、ユーザーが登録する型のうち、DI を通じて挿入されるものを*サービス*という用語で表します。</span><span class="sxs-lookup"><span data-stu-id="4485f-113">ASP.NET Core uses the term *service* for any of the types you register that will be injected through DI.</span></span> <span data-ttu-id="4485f-114">組み込みコンテナーのサービスは、アプリケーションの Startup クラス内にある ConfigureServices メソッドで構成します。</span><span class="sxs-lookup"><span data-stu-id="4485f-114">You configure the built-in container's services in the ConfigureServices method in your application's Startup class.</span></span> <span data-ttu-id="4485f-115">依存関係は、型に必要であり、IoC コンテナーに登録するサービス内に実装されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-115">Your dependencies are implemented in the services that a type needs and that you register in the IoC container.</span></span>

<span data-ttu-id="4485f-116">ユーザーは通常、インフラストラクチャ オブジェクトを実装する依存関係を挿入します。</span><span class="sxs-lookup"><span data-stu-id="4485f-116">Typically, you want to inject dependencies that implement infrastructure objects.</span></span> <span data-ttu-id="4485f-117">挿入する依存関係として典型的なのは、リポジトリです。</span><span class="sxs-lookup"><span data-stu-id="4485f-117">A very typical dependency to inject is a repository.</span></span> <span data-ttu-id="4485f-118">ただし、インフラストラクチャの依存関係が他にもある場合は、それらを挿入することもできます。</span><span class="sxs-lookup"><span data-stu-id="4485f-118">But you could inject any other infrastructure dependency that you may have.</span></span> <span data-ttu-id="4485f-119">単純な実装の場合は、作業単位パターン オブジェクト (EF DbContext オブジェクト) を直接実装することもできます。なぜなら、DBContext はインフラストラクチャ永続オブジェクトの実装でもあるからです。</span><span class="sxs-lookup"><span data-stu-id="4485f-119">For simpler implementations, you could directly inject your Unit of Work pattern object (the EF DbContext object), because the DBContext is also the implementation of your infrastructure persistence objects.</span></span>

<span data-ttu-id="4485f-120">次の例では、.NET Core において、必要なリポジトリ オブジェクトがコンストラクターを通じてどのように挿入されるかが示されています。</span><span class="sxs-lookup"><span data-stu-id="4485f-120">In the following example, you can see how .NET Core is injecting the required repository objects through the constructor.</span></span> <span data-ttu-id="4485f-121">このクラスは、次のセクションで取り上げるコマンド ハンドラーです。</span><span class="sxs-lookup"><span data-stu-id="4485f-121">The class is a command handler, which we will cover in the next section.</span></span>

```csharp
public class CreateOrderCommandHandler
    : IAsyncRequestHandler<CreateOrderCommand, bool>
{
    private readonly IOrderRepository _orderRepository;
    private readonly IIdentityService _identityService;
    private readonly IMediator _mediator;

    // Using DI to inject infrastructure persistence Repositories
    public CreateOrderCommandHandler(IMediator mediator,
                                     IOrderRepository orderRepository,
                                     IIdentityService identityService)
    {
        _orderRepository = orderRepository ??
                          throw new ArgumentNullException(nameof(orderRepository));
        _identityService = identityService ??
                          throw new ArgumentNullException(nameof(identityService));
        _mediator = mediator ??
                                 throw new ArgumentNullException(nameof(mediator));
    }

    public async Task<bool> Handle(CreateOrderCommand message)
    {
        // Create the Order AggregateRoot
        // Add child entities and value objects through the Order aggregate root
        // methods and constructor so validations, invariants, and business logic
        // make sure that consistency is preserved across the whole aggregate
        var address = new Address(message.Street, message.City, message.State,
                                  message.Country, message.ZipCode);
        var order = new Order(message.UserId, address, message.CardTypeId,
                              message.CardNumber, message.CardSecurityNumber,
                              message.CardHolderName, message.CardExpiration);

        foreach (var item in message.OrderItems)
        {
            order.AddOrderItem(item.ProductId, item.ProductName, item.UnitPrice,
                               item.Discount, item.PictureUrl, item.Units);
        }

        _orderRepository.Add(order);

        return await _orderRepository.UnitOfWork
            .SaveEntitiesAsync();
    }
}
```

<span data-ttu-id="4485f-122">このクラスは、挿入されたリポジトリを使用してトランザクションを実行し、状態の変更を保持します。</span><span class="sxs-lookup"><span data-stu-id="4485f-122">The class uses the injected repositories to execute the transaction and persist the state changes.</span></span> <span data-ttu-id="4485f-123">このクラスが コマンド ハンドラーなのか、ASP.NET Core Web API コントローラー メソッドなのか、それとも [DDD アプリケーション サービス](https://lostechies.com/jimmybogard/2008/08/21/services-in-domain-driven-design/)なのかは重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="4485f-123">It does not matter whether that class is a command handler, an ASP.NET Core Web API controller method, or a [DDD Application Service](https://lostechies.com/jimmybogard/2008/08/21/services-in-domain-driven-design/).</span></span> <span data-ttu-id="4485f-124">重要なのは、このクラスがリポジトリ、ドメイン エンティティ、およびその他のアプリケーション調整をコマンド ハンドラーのような方法で使用する、単純なクラスだということです。</span><span class="sxs-lookup"><span data-stu-id="4485f-124">It is ultimately a simple class that uses repositories, domain entities, and other application coordination in a fashion similar to a command handler.</span></span> <span data-ttu-id="4485f-125">依存関係挿入は、コンストラクター ベースの DI を使用したこの例のように、記述されたすべてのクラスに対して同様に動作します。</span><span class="sxs-lookup"><span data-stu-id="4485f-125">Dependency Injection works the same way for all the mentioned classes, as in the example using DI based on the constructor.</span></span>

### <a name="register-the-dependency-implementation-types-and-interfaces-or-abstractions"></a><span data-ttu-id="4485f-126">依存関係実装の型とインターフェイス (または抽象化) の登録</span><span class="sxs-lookup"><span data-stu-id="4485f-126">Register the dependency implementation types and interfaces or abstractions</span></span>

<span data-ttu-id="4485f-127">コンストラクターを通じて挿入されたオブジェクトを使用するには、まず、DI を通じてアプリケーション クラスに挿入されたオブジェクトの生成元となるインターフェイスやクラスを、どこに登録するのかを知る必要があります</span><span class="sxs-lookup"><span data-stu-id="4485f-127">Before you use the objects injected through constructors, you need to know where to register the interfaces and classes that produce the objects injected into your application classes through DI.</span></span> <span data-ttu-id="4485f-128">(先の例で示したコンストラクター ベースの DI と同様)。</span><span class="sxs-lookup"><span data-stu-id="4485f-128">(Like DI based on the constructor, as shown previously.)</span></span>

#### <a name="use-the-built-in-ioc-container-provided-by-aspnet-core"></a><span data-ttu-id="4485f-129">ASP.NET Core で提供される組み込み IoC コンテナーの使用</span><span class="sxs-lookup"><span data-stu-id="4485f-129">Use the built-in IoC container provided by ASP.NET Core</span></span>

<span data-ttu-id="4485f-130">ASP.NET Core で提供される組み込み IoC コンテナーを使用する場合は、ConfigureServices メソッドに挿入する型を、次のようなコードによって Startup.cs ファイルに登録します。</span><span class="sxs-lookup"><span data-stu-id="4485f-130">When you use the built-in IoC container provided by ASP.NET Core, you register the types you want to inject in the ConfigureServices method in the Startup.cs file, as in the following code:</span></span>

```csharp
// Registration of types into ASP.NET Core built-in container
public void ConfigureServices(IServiceCollection services)
{
    // Register out-of-the-box framework services.
    services.AddDbContext<CatalogContext>(c =>
    {
        c.UseSqlServer(Configuration["ConnectionString"]);
    },
    ServiceLifetime.Scoped
    );
    services.AddMvc();
    // Register custom application dependencies.
    services.AddScoped<IMyCustomRepository, MyCustomSQLRepository>();
}
```

<span data-ttu-id="4485f-131">IoC コンテナーに型を登録する場合の最も一般的なパターンは、型のペア (インターフェイスとその関連実装クラス) を登録するやり方です。</span><span class="sxs-lookup"><span data-stu-id="4485f-131">The most common pattern when registering types in an IoC container is to register a pair of types—an interface and its related implementation class.</span></span> <span data-ttu-id="4485f-132">その後、任意のコンストラクターを通じて IoC コンテナーからオブジェクトを要求する際に、特定のインターフェイス型のオブジェクトを要求します。</span><span class="sxs-lookup"><span data-stu-id="4485f-132">Then when you request an object from the IoC container through any constructor, you request an object of a certain type of interface.</span></span> <span data-ttu-id="4485f-133">たとえば、先の例の最後の行では、いずれかのコンストラクターに IMyCustomRepository (インターフェイスまたは抽象型) への依存関係がある場合に、IoC コンテナーが MyCustomSQLServerRepository 実装クラスのインスタンスを挿入するよう記述されています。</span><span class="sxs-lookup"><span data-stu-id="4485f-133">For instance, in the previous example, the last line states that when any of your constructors have a dependency on IMyCustomRepository (interface or abstraction), the IoC container will inject an instance of the MyCustomSQLServerRepository implementation class.</span></span>

#### <a name="use-the-scrutor-library-for-automatic-types-registration"></a><span data-ttu-id="4485f-134">自動登録用の Scrutor ライブラリの使用</span><span class="sxs-lookup"><span data-stu-id="4485f-134">Use the Scrutor library for automatic types registration</span></span>

<span data-ttu-id="4485f-135">.NET Core で DI を使用する場合は、アセンブリをスキャンし、その型を規則によって自動的に登録したい場合もあるでしょう。</span><span class="sxs-lookup"><span data-stu-id="4485f-135">When using DI in .NET Core, you might want to be able to scan an assembly and automatically register its types by convention.</span></span> <span data-ttu-id="4485f-136">この機能は、現在 ASP.NET Coreでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="4485f-136">This feature is not currently available in ASP.NET Core.</span></span> <span data-ttu-id="4485f-137">ただし、この目的のために [Scrutor](https://github.com/khellang/Scrutor) ライブラリを使用することはできます。</span><span class="sxs-lookup"><span data-stu-id="4485f-137">However, you can use the [Scrutor](https://github.com/khellang/Scrutor) library for that.</span></span> <span data-ttu-id="4485f-138">この方法は、IoC コンテナーに登録する必要がある型がたくさんある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="4485f-138">This approach is convenient when you have dozens of types that need to be registered in your IoC container.</span></span>

#### <a name="additional-resources"></a><span data-ttu-id="4485f-139">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4485f-139">Additional resources</span></span>

- <span data-ttu-id="4485f-140">**Matthew King。Scrutor でのサービスの登録** </span><span class="sxs-lookup"><span data-stu-id="4485f-140">**Matthew King. Registering services with Scrutor** </span></span>\
  <https://www.mking.net/blog/registering-services-with-scrutor>

- <span data-ttu-id="4485f-141">**Kristian Hellang。Scrutor。**</span><span class="sxs-lookup"><span data-stu-id="4485f-141">**Kristian Hellang. Scrutor.**</span></span> <span data-ttu-id="4485f-142">GitHub リポジトリ。</span><span class="sxs-lookup"><span data-stu-id="4485f-142">GitHub repo.</span></span> \
  <https://github.com/khellang/Scrutor>

#### <a name="use-autofac-as-an-ioc-container"></a><span data-ttu-id="4485f-143">Autofac を IoC コンテナーとして使用する</span><span class="sxs-lookup"><span data-stu-id="4485f-143">Use Autofac as an IoC container</span></span>

<span data-ttu-id="4485f-144">eShopOnContainers の注文マイクロサービスのように、追加の IoC コンテナーを使用し、[Autofac](https://autofac.org/) を使用する ASP.NET Core パイプラインにそれらをプラグインすることもできます。</span><span class="sxs-lookup"><span data-stu-id="4485f-144">You can also use additional IoC containers and plug them into the ASP.NET Core pipeline, as in the ordering microservice in eShopOnContainers, which uses [Autofac](https://autofac.org/).</span></span> <span data-ttu-id="4485f-145">Autofac を使用する場合は通常、モジュールを通じて型を登録します。これにより、型がどこにあるかに応じて、複数のファイル間で登録型を分割することができます (複数のクラス ライブラリ間でアプリケーション型を分散できたのと同様)。</span><span class="sxs-lookup"><span data-stu-id="4485f-145">When using Autofac you typically register the types via modules, which allow you to split the registration types between multiple files depending on where your types are, just as you could have the application types distributed across multiple class libraries.</span></span>

<span data-ttu-id="4485f-146">たとえば、次の例では、挿入する型を [Ordering.API Web API](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Services/Ordering/Ordering.API) プロジェクト用の [Autofac アプリケーション モジュール](https://github.com/dotnet-architecture/eShopOnContainers/blob/master/src/Services/Ordering/Ordering.API/Infrastructure/AutofacModules/ApplicationModule.cs)に記述しています。</span><span class="sxs-lookup"><span data-stu-id="4485f-146">For example, the following is the [Autofac application module](https://github.com/dotnet-architecture/eShopOnContainers/blob/master/src/Services/Ordering/Ordering.API/Infrastructure/AutofacModules/ApplicationModule.cs) for the [Ordering.API Web API](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Services/Ordering/Ordering.API) project with the types you will want to inject.</span></span>

```csharp
public class ApplicationModule : Autofac.Module
{
    public string QueriesConnectionString { get; }
    public ApplicationModule(string qconstr)
    {
        QueriesConnectionString = qconstr;
    }

    protected override void Load(ContainerBuilder builder)
    {
        builder.Register(c => new OrderQueries(QueriesConnectionString))
            .As<IOrderQueries>()
            .InstancePerLifetimeScope();
        builder.RegisterType<BuyerRepository>()
            .As<IBuyerRepository>()
            .InstancePerLifetimeScope();
        builder.RegisterType<OrderRepository>()
            .As<IOrderRepository>()
            .InstancePerLifetimeScope();
        builder.RegisterType<RequestManager>()
            .As<IRequestManager>()
            .InstancePerLifetimeScope();
   }
}
```

<span data-ttu-id="4485f-147">Autofac には、[アセンブリをスキャンし、命名規則で型を登録する](https://autofac.readthedocs.io/en/latest/register/scanning.html)機能もあります。</span><span class="sxs-lookup"><span data-stu-id="4485f-147">Autofac also has a feature to [scan assemblies and register types by name conventions](https://autofac.readthedocs.io/en/latest/register/scanning.html).</span></span>

<span data-ttu-id="4485f-148">登録プロセスと概念は、組み込みの ASP.NET Core IoC コンテナーに型を登録する方法とよく似ていますが、Autofac を使用する場合は構文が少し異なります。</span><span class="sxs-lookup"><span data-stu-id="4485f-148">The registration process and concepts are very similar to the way you can register types with the built-in ASP.NET Core IoC container, but the syntax when using Autofac is a bit different.</span></span>

<span data-ttu-id="4485f-149">このコード例では、IOrderRepository 抽象化が、実装クラス OrderRepository と共に登録されています。</span><span class="sxs-lookup"><span data-stu-id="4485f-149">In the example code, the abstraction IOrderRepository is registered along with the implementation class OrderRepository.</span></span> <span data-ttu-id="4485f-150">つまり、コンストラクターが IOrderRepository 抽象化またはインターフェイスを通じて依存関係を宣言している場合、IoC コンテナーは OrderRepository クラスのインスタンスを挿入するということです。</span><span class="sxs-lookup"><span data-stu-id="4485f-150">This means that whenever a constructor is declaring a dependency through the IOrderRepository abstraction or interface, the IoC container will inject an instance of the OrderRepository class.</span></span>

<span data-ttu-id="4485f-151">同じサービスや依存関係に対する要求間でインスタンスがどのように共有されるかは、インスタンス スコープ型によって決まります。</span><span class="sxs-lookup"><span data-stu-id="4485f-151">The instance scope type determines how an instance is shared between requests for the same service or dependency.</span></span> <span data-ttu-id="4485f-152">依存関係に対する要求が行われた場合、IoC コンテナーは次のいずれかの結果を返します。</span><span class="sxs-lookup"><span data-stu-id="4485f-152">When a request is made for a dependency, the IoC container can return the following:</span></span>

- <span data-ttu-id="4485f-153">有効期間範囲ごとに 1 つのインスタンス (ASP.NET Core IoC コンテナー内で *scoped* として参照されます)。</span><span class="sxs-lookup"><span data-stu-id="4485f-153">A single instance per lifetime scope (referred to in the ASP.NET Core IoC container as *scoped*).</span></span>

- <span data-ttu-id="4485f-154">依存関係ごとに 1 つの新規インスタンス (ASP.NET Core IoC コンテナー内で *transient* として参照されます)。</span><span class="sxs-lookup"><span data-stu-id="4485f-154">A new instance per dependency (referred to in the ASP.NET Core IoC container as *transient*).</span></span>

- <span data-ttu-id="4485f-155">IoC コンテナーを使用してすべてのオブジェクト間で共有される 1 つのインスタンス (ASP.NET Core IoC コンテナー内で *singleton* として参照されます)。</span><span class="sxs-lookup"><span data-stu-id="4485f-155">A single instance shared across all objects using the IoC container (referred to in the ASP.NET Core IoC container as *singleton*).</span></span>

#### <a name="additional-resources"></a><span data-ttu-id="4485f-156">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4485f-156">Additional resources</span></span>

- <span data-ttu-id="4485f-157">**ASP.NET Core での依存関係の挿入の概要** </span><span class="sxs-lookup"><span data-stu-id="4485f-157">**Introduction to Dependency Injection in ASP.NET Core** </span></span>\
  [https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection](/aspnet/core/fundamentals/dependency-injection)

- <span data-ttu-id="4485f-158">**Autofac。**</span><span class="sxs-lookup"><span data-stu-id="4485f-158">**Autofac.**</span></span> <span data-ttu-id="4485f-159">公式ドキュメント。</span><span class="sxs-lookup"><span data-stu-id="4485f-159">Official documentation.</span></span> \
  <https://docs.autofac.org/en/latest/>

- <span data-ttu-id="4485f-160">**ASP.NET Core IoC コンテナー サービスの有効期間と Autofac IoC コンテナー インスタンスの範囲の比較 - Cesar de la Torre**</span><span class="sxs-lookup"><span data-stu-id="4485f-160">**Comparing ASP.NET Core IoC container service lifetimes with Autofac IoC container instance scopes - Cesar de la Torre.**</span></span> \
  <https://devblogs.microsoft.com/cesardelatorre/comparing-asp-net-core-ioc-service-life-times-and-autofac-ioc-instance-scopes/>

## <a name="implement-the-command-and-command-handler-patterns"></a><span data-ttu-id="4485f-161">コマンドおよびコマンド ハンドラー パターンの実装</span><span class="sxs-lookup"><span data-stu-id="4485f-161">Implement the Command and Command Handler patterns</span></span>

<span data-ttu-id="4485f-162">前のセクションで示したコンストラクター ベースの DI の例では、IoC コンテナーはクラス内のコンストラクターを通じてリポジトリを挿入していました。</span><span class="sxs-lookup"><span data-stu-id="4485f-162">In the DI-through-constructor example shown in the previous section, the IoC container was injecting repositories through a constructor in a class.</span></span> <span data-ttu-id="4485f-163">ですが、これらは厳密にはどこに挿入されたのでしょうか。</span><span class="sxs-lookup"><span data-stu-id="4485f-163">But exactly where were they injected?</span></span> <span data-ttu-id="4485f-164">シンプルな Web API (たとえば、eShopOnContainers 内のカタログ マイクロサービス) では、ASP.NET Core の要求パイプラインの一部として、MVC コントローラー レベル (コントローラーのコンストラクター内) でこれらを挿入します。</span><span class="sxs-lookup"><span data-stu-id="4485f-164">In a simple Web API (for example, the catalog microservice in eShopOnContainers), you inject them at the MVC controllers’ level, in a controller constructor, as part of the request pipeline of ASP.NET Core.</span></span> <span data-ttu-id="4485f-165">しかし、このセクションの最初のコード (eShopOnContainers 内の Ordering.API サービスの [CreateOrderCommandHandler](https://github.com/dotnet-architecture/eShopOnContainers/blob/master/src/Services/Ordering/Ordering.API/Application/Commands/CreateOrderCommandHandler.cs) クラス) では、特定のコマンド ハンドラーのコンストラクターを通じて依存関係の挿入が行われています。</span><span class="sxs-lookup"><span data-stu-id="4485f-165">However, in the initial code of this section (the [CreateOrderCommandHandler](https://github.com/dotnet-architecture/eShopOnContainers/blob/master/src/Services/Ordering/Ordering.API/Application/Commands/CreateOrderCommandHandler.cs) class from the Ordering.API service in eShopOnContainers), the injection of dependencies is done through the constructor of a particular command handler.</span></span> <span data-ttu-id="4485f-166">そこで、コマンド ハンドラーとは何か、またどのような理由で使用するのかについて説明していきましょう。</span><span class="sxs-lookup"><span data-stu-id="4485f-166">Let us explain what a command handler is and why you would want to use it.</span></span>

<span data-ttu-id="4485f-167">コマンド パターンは本来、このガイドの前の方で説明した、CQRS パターンに関連するものです。</span><span class="sxs-lookup"><span data-stu-id="4485f-167">The Command pattern is intrinsically related to the CQRS pattern that was introduced earlier in this guide.</span></span> <span data-ttu-id="4485f-168">CQRS には 2 つの面があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-168">CQRS has two sides.</span></span> <span data-ttu-id="4485f-169">1 つ目の領域はクエリです。簡略化されたクエリを、[Dapper](https://github.com/StackExchange/dapper-dot-net) のマイクロ ORM と共に使用します。</span><span class="sxs-lookup"><span data-stu-id="4485f-169">The first area is queries, using simplified queries with the [Dapper](https://github.com/StackExchange/dapper-dot-net) micro ORM, which was explained previously.</span></span> <span data-ttu-id="4485f-170">2 つ目の領域はコマンドです。これは、トランザクションの開始点となるものであり、サービス外部からの入力チャネルとなるものです。</span><span class="sxs-lookup"><span data-stu-id="4485f-170">The second area is commands, which are the starting point for transactions, and the input channel from outside the service.</span></span>

<span data-ttu-id="4485f-171">図 7-24 に示すように、パターンでは基本的に、クライアント側からコマンドを受け付け、それらをドメイン モデル ルールに基づいて処理し、最後にトランザクションの状態を保持します。</span><span class="sxs-lookup"><span data-stu-id="4485f-171">As shown in Figure 7-24, the pattern is based on accepting commands from the client side, processing them based on the domain model rules, and finally persisting the states with transactions.</span></span>

![CQRS の書き込み側の概要図:UI アプリは API 経由で CommandHandler にコマンドを送信します。CommandHandler とドメイン モデルまたはインフラストラクチャとの間には依存関係があり、インフラストラクチャに依存する場合、データベースが更新されます。](./media/image21.png)

<span data-ttu-id="4485f-173">**図 7-24**.</span><span class="sxs-lookup"><span data-stu-id="4485f-173">**Figure 7-24**.</span></span> <span data-ttu-id="4485f-174">コマンド (CQRS パターンのトランザクション側) の概要</span><span class="sxs-lookup"><span data-stu-id="4485f-174">High-level view of the commands or “transactional side” in a CQRS pattern</span></span>

### <a name="the-command-class"></a><span data-ttu-id="4485f-175">コマンド クラス</span><span class="sxs-lookup"><span data-stu-id="4485f-175">The command class</span></span>

<span data-ttu-id="4485f-176">コマンドは、システムの状態を変更するアクションを実行するための、システムへの要求です。</span><span class="sxs-lookup"><span data-stu-id="4485f-176">A command is a request for the system to perform an action that changes the state of the system.</span></span> <span data-ttu-id="4485f-177">コマンドは命令型であり、1 回だけ処理されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-177">Commands are imperative, and should be processed just once.</span></span>

<span data-ttu-id="4485f-178">コマンドは命令型なので、通常は命令的な動詞 ("create" や "update" など) で名前が付けられ、集約型 (CreateOrderCommand など) が含められます。</span><span class="sxs-lookup"><span data-stu-id="4485f-178">Since commands are imperatives, they are typically named with a verb in the imperative mood (for example, "create" or "update"), and they might include the aggregate type, such as CreateOrderCommand.</span></span> <span data-ttu-id="4485f-179">イベントと違って、コマンドは過去のファクトを指すものではありません。単なる要求なので、拒否される場合もあります。</span><span class="sxs-lookup"><span data-stu-id="4485f-179">Unlike an event, a command is not a fact from the past; it is only a request, and thus may be refused.</span></span>

<span data-ttu-id="4485f-180">コマンドは、ユーザーが要求を開始した結果として UI から発生することもありますし、プロセス マネージャーが操作実行のための集約を指定した場合には、プロセス マネージャーから発生することもあります。</span><span class="sxs-lookup"><span data-stu-id="4485f-180">Commands can originate from the UI as a result of a user initiating a request, or from a process manager when the process manager is directing an aggregate to perform an action.</span></span>

<span data-ttu-id="4485f-181">コマンドの重要な特性は、単一のレシーバーによって 1 回だけ処理されなければならないという点です。</span><span class="sxs-lookup"><span data-stu-id="4485f-181">An important characteristic of a command is that it should be processed just once by a single receiver.</span></span> <span data-ttu-id="4485f-182">つまりコマンドとは、アプリケーション内で実行する、単一のアクションまたはトランザクションであるということです。</span><span class="sxs-lookup"><span data-stu-id="4485f-182">This is because a command is a single action or transaction you want to perform in the application.</span></span> <span data-ttu-id="4485f-183">たとえば、同一の注文作成コマンドを 2 回以上処理することはできません。</span><span class="sxs-lookup"><span data-stu-id="4485f-183">For example, the same order creation command should not be processed more than once.</span></span> <span data-ttu-id="4485f-184">これは、コマンドとイベントの重要な違いです。</span><span class="sxs-lookup"><span data-stu-id="4485f-184">This is an important difference between commands and events.</span></span> <span data-ttu-id="4485f-185">イベントは、複数回処理される場合があります。なぜなら、さまざまなシステムやマイクロサービスがそのイベントに関連している可能性があるからです。</span><span class="sxs-lookup"><span data-stu-id="4485f-185">Events may be processed multiple times, because many systems or microservices might be interested in the event.</span></span>

<span data-ttu-id="4485f-186">もう 1 つ重要なことは、コマンドがべき等ではない場合、そのコマンドは 1 回だけ処理されるという点です。</span><span class="sxs-lookup"><span data-stu-id="4485f-186">In addition, it is important that a command be processed only once in case the command is not idempotent.</span></span> <span data-ttu-id="4485f-187">コマンドを複数回実行しても、コマンドの性質上、またはシステムでのコマンドの処理方法上、その結果が変わらない場合には、そのコマンドはべき等であるということになります。</span><span class="sxs-lookup"><span data-stu-id="4485f-187">A command is idempotent if it can be executed multiple times without changing the result, either because of the nature of the command, or because of the way the system handles the command.</span></span>

<span data-ttu-id="4485f-188">ドメインのビジネス ルールやインバリアントに対して合理的である場合には、コマンドや更新プログラムをべき等にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4485f-188">It is a good practice to make your commands and updates idempotent when it makes sense under your domain’s business rules and invariants.</span></span> <span data-ttu-id="4485f-189">たとえば、同じ例で言うと、何らかの理由 (再試行ロジックやハッキングなど) によって、同じ CreateOrder コマンドがシステムに複数回アクセスする場合には、そのコマンドを識別し、複数の注文が作成されないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-189">For instance, to use the same example, if for any reason (retry logic, hacking, etc.) the same CreateOrder command reaches your system multiple times, you should be able to identify it and ensure that you do not create multiple orders.</span></span> <span data-ttu-id="4485f-190">そのためには、操作に何らかの ID をアタッチして、コマンドや更新プログラムが既に処理されていないかどうかを識別する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-190">To do so, you need to attach some kind of identity in the operations and identify whether the command or update was already processed.</span></span>

<span data-ttu-id="4485f-191">コマンドは単一のレシーバーに送信します。つまり、コマンドは発行するものではありません。</span><span class="sxs-lookup"><span data-stu-id="4485f-191">You send a command to a single receiver; you do not publish a command.</span></span> <span data-ttu-id="4485f-192">発行は、何かが起こり、それがイベント レシーバーに関連する可能性がある場合に、そのファクトを記述したイベントに対して行うものです。</span><span class="sxs-lookup"><span data-stu-id="4485f-192">Publishing is for events that state a fact—that something has happened and might be interesting for event receivers.</span></span> <span data-ttu-id="4485f-193">イベントの場合、パブリッシャーはイベントをどのレシーバーが取得するかや、それに対してレシーバーがどう対応するかについて、一切関知しません。</span><span class="sxs-lookup"><span data-stu-id="4485f-193">In the case of events, the publisher has no concerns about which receivers get the event or what they do it.</span></span> <span data-ttu-id="4485f-194">ただし、ドメインまたは統合イベントは前のセクションで既に説明した別のトピックです。</span><span class="sxs-lookup"><span data-stu-id="4485f-194">But domain or integration events are a different story already introduced in previous sections.</span></span>

<span data-ttu-id="4485f-195">コマンドは、データ フィールドやコレクションのほか、コマンドを実行するために必要なすべての情報を含んだクラスを使用して実装されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-195">A command is implemented with a class that contains data fields or collections with all the information that is needed in order to execute that command.</span></span> <span data-ttu-id="4485f-196">コマンドは特別な種類のデータ転送オブジェクト (DTO) であり、変更やトランザクションを要求するために使用されるものです。</span><span class="sxs-lookup"><span data-stu-id="4485f-196">A command is a special kind of Data Transfer Object (DTO), one that is specifically used to request changes or transactions.</span></span> <span data-ttu-id="4485f-197">コマンド自体は、コマンドを処理するために必要な情報のみをベースとし、その他の情報は含まれません。</span><span class="sxs-lookup"><span data-stu-id="4485f-197">The command itself is based on exactly the information that is needed for processing the command, and nothing more.</span></span>

<span data-ttu-id="4485f-198">次の例は、簡略化された CreateOrderCommand クラスを示したものです。</span><span class="sxs-lookup"><span data-stu-id="4485f-198">The following example shows the simplified CreateOrderCommand class.</span></span> <span data-ttu-id="4485f-199">これは、eShopOnContainers 内の注文マイクロサービスで使用される不変コマンドです。</span><span class="sxs-lookup"><span data-stu-id="4485f-199">This is an immutable command that is used in the ordering microservice in eShopOnContainers.</span></span>

```csharp
// DDD and CQRS patterns comment
// Note that we recommend that you implement immutable commands
// In this case, immutability is achieved by having all the setters as private
// plus being able to update the data just once, when creating the object
// through the constructor.
// References on immutable commands:
// http://cqrs.nu/Faq
// https://docs.spine3.org/motivation/immutability.html
// http://blog.gauffin.org/2012/06/griffin-container-introducing-command-support/
// https://msdn.microsoft.com/library/bb383979.aspx
[DataContract]
public class CreateOrderCommand
    :IAsyncRequest<bool>
{
    [DataMember]
    private readonly List<OrderItemDTO> _orderItems;
    [DataMember]
    public string City { get; private set; }
    [DataMember]
    public string Street { get; private set; }
    [DataMember]
    public string State { get; private set; }
    [DataMember]
    public string Country { get; private set; }
    [DataMember]
    public string ZipCode { get; private set; }
    [DataMember]
    public string CardNumber { get; private set; }
    [DataMember]
    public string CardHolderName { get; private set; }
    [DataMember]
    public DateTime CardExpiration { get; private set; }
    [DataMember]
    public string CardSecurityNumber { get; private set; }
    [DataMember]
    public int CardTypeId { get; private set; }
    [DataMember]
    public IEnumerable<OrderItemDTO> OrderItems => _orderItems;

    public CreateOrderCommand()
    {
        _orderItems = new List<OrderItemDTO>();
    }

    public CreateOrderCommand(List<BasketItem> basketItems, string city,
        string street,
        string state, string country, string zipcode,
        string cardNumber, string cardHolderName, DateTime cardExpiration,
        string cardSecurityNumber, int cardTypeId) : this()
    {
        _orderItems = MapToOrderItems(basketItems);
        City = city;
        Street = street;
        State = state;
        Country = country;
        ZipCode = zipcode;
        CardNumber = cardNumber;
        CardHolderName = cardHolderName;
        CardSecurityNumber = cardSecurityNumber;
        CardTypeId = cardTypeId;
        CardExpiration = cardExpiration;
    }

    public class OrderItemDTO
    {
        public int ProductId { get; set; }
        public string ProductName { get; set; }
        public decimal UnitPrice { get; set; }
        public decimal Discount { get; set; }
        public int Units { get; set; }
        public string PictureUrl { get; set; }
    }
}
```

<span data-ttu-id="4485f-200">コマンド クラスには基本的に、ドメイン モデル オブジェクトを使用してビジネス トランザクションを実行するために必要な、すべてのデータが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4485f-200">Basically, the command class contains all the data you need for performing a business transaction by using the domain model objects.</span></span> <span data-ttu-id="4485f-201">つまり、コマンドは読み取り専用データを含んだデータ構造であり、ビヘイビアーではありません。</span><span class="sxs-lookup"><span data-stu-id="4485f-201">Thus, commands are simply data structures that contain read-only data, and no behavior.</span></span> <span data-ttu-id="4485f-202">コマンドの名前は、その目的を示しています。</span><span class="sxs-lookup"><span data-stu-id="4485f-202">The command’s name indicates its purpose.</span></span> <span data-ttu-id="4485f-203">C# を始めとする多くの言語では、コマンドはクラスとして表されますが、オブジェクト指向における真の意味では、クラスではありません。</span><span class="sxs-lookup"><span data-stu-id="4485f-203">In many languages like C#, commands are represented as classes, but they are not true classes in the real object-oriented sense.</span></span>

<span data-ttu-id="4485f-204">もう 1 つの特徴として、コマンドは不変です。なぜなら、コマンドはドメイン モデルによって直接処理されるものと想定されているからです。</span><span class="sxs-lookup"><span data-stu-id="4485f-204">As an additional characteristic, commands are immutable, because the expected usage is that they are processed directly by the domain model.</span></span> <span data-ttu-id="4485f-205">予定された有効期間中に変更する必要がないのです。</span><span class="sxs-lookup"><span data-stu-id="4485f-205">They do not need to change during their projected lifetime.</span></span> <span data-ttu-id="4485f-206">C# クラスでは、内部状態を変更するセッターやその他のメソッドを使用しないことによって、不変性を達成できます。</span><span class="sxs-lookup"><span data-stu-id="4485f-206">In a C# class, immutability can be achieved by not having any setters or other methods that change internal state.</span></span>

<span data-ttu-id="4485f-207">意図的に、あるいは予想としてコマンドがシリアル化/逆シリアル化プロセスを通過する場合、プロパティにはプライベート セッターと `[DataMember]` (または `[JsonProperty]`) 属性が与えられる必要があります。与えられていない場合、デシリアライザーは宛先で必須値を利用してオブジェクトを再構築することができません。</span><span class="sxs-lookup"><span data-stu-id="4485f-207">Bear in mind that if you intend or expect commands will be going through a serializing/deserializing process, the properties must have private setter, and the `[DataMember]` (or `[JsonProperty]`) attribute, otherwise the deserializer will not be able to reconstruct the object at destination with the required values.</span></span>

<span data-ttu-id="4485f-208">たとえば、注文を作成するためのコマンド クラスは通常、データの観点から見れば、作成する注文と同様のものになりますが、通常、同じ属性は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="4485f-208">For example, the command class for creating an order is probably similar in terms of data to the order you want to create, but you probably do not need the same attributes.</span></span> <span data-ttu-id="4485f-209">具体的に言うと、CreateOrderCommand には注文 ID は含まれません。注文がまだ作成されていないからです。</span><span class="sxs-lookup"><span data-stu-id="4485f-209">For instance, CreateOrderCommand does not have an order ID, because the order has not been created yet.</span></span>

<span data-ttu-id="4485f-210">多くのコマンド クラスは、変更が必要な状態に関するいくつかのフィールドだけを含めればよいので、簡素化することができます。</span><span class="sxs-lookup"><span data-stu-id="4485f-210">Many command classes can be simple, requiring only a few fields about some state that needs to be changed.</span></span> <span data-ttu-id="4485f-211">たとえば、注文の状態を「処理中」から「支払済み」(または「発送済み」) 変更するだけの場合は、次のようなシンプルなコマンドで記述できます。</span><span class="sxs-lookup"><span data-stu-id="4485f-211">That would be the case if you are just changing the status of an order from “in process” to “paid” or “shipped” by using a command similar to the following:</span></span>

```csharp
[DataContract]
public class UpdateOrderStatusCommand
    :IAsyncRequest<bool>
{
    [DataMember]
    public string Status { get; private set; }

    [DataMember]
    public string OrderId { get; private set; }

    [DataMember]
    public string BuyerIdentityGuid { get; private set; }
}
```

<span data-ttu-id="4485f-212">開発者によっては、UI 要求オブジェクトをコマンド DTO と分離させることもありますが、これは各自の好みで行うものにすぎません。</span><span class="sxs-lookup"><span data-stu-id="4485f-212">Some developers make their UI request objects separate from their command DTOs, but that is just a matter of preference.</span></span> <span data-ttu-id="4485f-213">このような分離は、手間がかかる割にメリットがそれほど大きくはなく、オブジェクトはほぼ同様の形状になります。</span><span class="sxs-lookup"><span data-stu-id="4485f-213">It is a tedious separation with not much added value, and the objects are almost exactly the same shape.</span></span> <span data-ttu-id="4485f-214">たとえば、eShopOnContainers では一部のコマンドがクライアント側から直接来るようになっています。</span><span class="sxs-lookup"><span data-stu-id="4485f-214">For instance, in eShopOnContainers, some commands come directly from the client side.</span></span>

### <a name="the-command-handler-class"></a><span data-ttu-id="4485f-215">コマンド ハンドラー クラス</span><span class="sxs-lookup"><span data-stu-id="4485f-215">The Command Handler class</span></span>

<span data-ttu-id="4485f-216">各コマンドについては、特定のコマンド ハンドラー クラスを実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-216">You should implement a specific command handler class for each command.</span></span> <span data-ttu-id="4485f-217">これによってパターンが機能するようになります。コマンド オブジェクト、ドメイン オブジェクト、およびインフラストラクチャ リポジトリ オブジェクトは、このクラスで使用します。</span><span class="sxs-lookup"><span data-stu-id="4485f-217">That is how the pattern works, and it is where you will use the command object, the domain objects, and the infrastructure repository objects.</span></span> <span data-ttu-id="4485f-218">コマンド ハンドラーは、CQRS と DDD の観点から言えば、アプリケーション レイヤーの中心となるものです。</span><span class="sxs-lookup"><span data-stu-id="4485f-218">The command handler is in fact the heart of the application layer in terms of CQRS and DDD.</span></span> <span data-ttu-id="4485f-219">ただし、ドメイン ロジックはすべてドメイン クラス内に含める必要があります。つまり、アプリケーション レイヤーのクラスであるコマンド ハンドラー内ではなく、集約ルート (ルート エンティティ) 、子エンティティ、または[ドメイン サービス](https://lostechies.com/jimmybogard/2008/08/21/services-in-domain-driven-design/)内に含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-219">However, all the domain logic should be contained within the domain classes—within the aggregate roots (root entities), child entities, or [domain services](https://lostechies.com/jimmybogard/2008/08/21/services-in-domain-driven-design/), but not within the command handler, which is a class from the application layer.</span></span>

<span data-ttu-id="4485f-220">コマンド ハンドラー クラスは、前のセクションで説明した単一責任の原則 (SRP) を達成する堅固な手法となります。</span><span class="sxs-lookup"><span data-stu-id="4485f-220">The command handler class offers a strong stepping stone in the way to achieve the Single Responsibility Principle (SRP) mentioned in a previous section.</span></span>

<span data-ttu-id="4485f-221">コマンド ハンドラーはコマンドを受け取り、使用される集約の結果を取得します。</span><span class="sxs-lookup"><span data-stu-id="4485f-221">A command handler receives a command and obtains a result from the aggregate that is used.</span></span> <span data-ttu-id="4485f-222">結果は、コマンドが正常に実行されるか、例外が返されるかのいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="4485f-222">The result should be either successful execution of the command, or an exception.</span></span> <span data-ttu-id="4485f-223">例外が返された場合、システム状態は変更されません。</span><span class="sxs-lookup"><span data-stu-id="4485f-223">In the case of an exception, the system state should be unchanged.</span></span>

<span data-ttu-id="4485f-224">コマンド ハンドラーでは通常、次の手順が実行されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-224">The command handler usually takes the following steps:</span></span>

- <span data-ttu-id="4485f-225">([メディエーター](https://en.wikipedia.org/wiki/Mediator_pattern)またはその他のインフラストラクチャ オブジェクトから) コマンド オブジェクト (DTO など) を受け取ります 。</span><span class="sxs-lookup"><span data-stu-id="4485f-225">It receives the command object, like a DTO (from the [mediator](https://en.wikipedia.org/wiki/Mediator_pattern) or other infrastructure object).</span></span>

- <span data-ttu-id="4485f-226">コマンドが有効であるかどうかを検証します (メディエーターによって検証されなかった場合)。</span><span class="sxs-lookup"><span data-stu-id="4485f-226">It validates that the command is valid (if not validated by the mediator).</span></span>

- <span data-ttu-id="4485f-227">現在のコマンドの対象となっている集約ルート インスタンスをインスタンス化します。</span><span class="sxs-lookup"><span data-stu-id="4485f-227">It instantiates the aggregate root instance that is the target of the current command.</span></span>

- <span data-ttu-id="4485f-228">集約ルート インスタンスに対してコマンドを実行し、コマンドから必要なデータを取得します。</span><span class="sxs-lookup"><span data-stu-id="4485f-228">It executes the method on the aggregate root instance, getting the required data from the command.</span></span>

- <span data-ttu-id="4485f-229">集約の新しい状態を、関連するデータベースに保持します。</span><span class="sxs-lookup"><span data-stu-id="4485f-229">It persists the new state of the aggregate to its related database.</span></span> <span data-ttu-id="4485f-230">この最後の操作が、実際のトランザクションとなります。</span><span class="sxs-lookup"><span data-stu-id="4485f-230">This last operation is the actual transaction.</span></span>

<span data-ttu-id="4485f-231">通常、コマンド ハンドラーは、その集約ルート (ルート エンティティ) によって駆動される単一の集約を操作します。</span><span class="sxs-lookup"><span data-stu-id="4485f-231">Typically, a command handler deals with a single aggregate driven by its aggregate root (root entity).</span></span> <span data-ttu-id="4485f-232">単一コマンドの受信によって複数の集約が影響を受ける場合は、ドメイン イベントを使用して複数の集約に状態やアクションを伝達することができます。</span><span class="sxs-lookup"><span data-stu-id="4485f-232">If multiple aggregates should be impacted by the reception of a single command, you could use domain events to propagate states or actions across multiple aggregates.</span></span>

<span data-ttu-id="4485f-233">ここで重要なポイントは、コマンドの処理中、すべてのドメイン ロジックはドメイン モデル (集約) の内部にあり、単体テスト用に完全にカプセル化されているという点です。</span><span class="sxs-lookup"><span data-stu-id="4485f-233">The important point here is that when a command is being processed, all the domain logic should be inside the domain model (the aggregates), fully encapsulated and ready for unit testing.</span></span> <span data-ttu-id="4485f-234">コマンド ハンドラーは、データベースからドメイン モデルを取得する手段として機能し、最後にモデルが変更されたら、その変更を保持するようにインフラストラクチャ レイヤー (リポジトリ) に通知します。</span><span class="sxs-lookup"><span data-stu-id="4485f-234">The command handler just acts as a way to get the domain model from the database, and as the final step, to tell the infrastructure layer (repositories) to persist the changes when the model is changed.</span></span> <span data-ttu-id="4485f-235">このアプローチの利点は、プラミング レベル (コマンド ハンドラー、Web API、リポジトリなど) に当たるアプリケーション レイヤーやインフラストラクチャ レイヤー内のコードを変更することなく、完全にカプセル化された分離型の高機能なビヘイビアー ドメイン モデル内に、ドメイン ロジックをリファクタリングできるということです。</span><span class="sxs-lookup"><span data-stu-id="4485f-235">The advantage of this approach is that you can refactor the domain logic in an isolated, fully encapsulated, rich, behavioral domain model without changing code in the application or infrastructure layers, which are the plumbing level (command handlers, Web API, repositories, etc.).</span></span>

<span data-ttu-id="4485f-236">コマンド ハンドラーが複雑になりすぎる (ロジックが多すぎる) 場合には、コード スメルになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-236">When command handlers get complex, with too much logic, that can be a code smell.</span></span> <span data-ttu-id="4485f-237">内容を見直し、ドメイン ロジックがある場合はコードをリファクタリングして、そのドメイン ビヘイビアーをドメイン オブジェクト (集約ルートと子エンティティ) のメソッドに移動してください。</span><span class="sxs-lookup"><span data-stu-id="4485f-237">Review them, and if you find domain logic, refactor the code to move that domain behavior to the methods of the domain objects (the aggregate root and child entity).</span></span>

<span data-ttu-id="4485f-238">次のコードは、この章の最初に示したものと同じ CreateOrderCommandHandler クラスを使用して、コマンド ハンドラー クラスの例を示したものです。</span><span class="sxs-lookup"><span data-stu-id="4485f-238">As an example of a command handler class, the following code shows the same CreateOrderCommandHandler class that you saw at the beginning of this chapter.</span></span> <span data-ttu-id="4485f-239">ここでは、Handle メソッドと、ドメイン モデル オブジェクト/集約を使用した操作に注目してください。</span><span class="sxs-lookup"><span data-stu-id="4485f-239">In this case, we want to highlight the Handle method and the operations with the domain model objects/aggregates.</span></span>

```csharp
public class CreateOrderCommandHandler
    : IAsyncRequestHandler<CreateOrderCommand, bool>
{
    private readonly IOrderRepository _orderRepository;
    private readonly IIdentityService _identityService;
    private readonly IMediator _mediator;

    // Using DI to inject infrastructure persistence Repositories
    public CreateOrderCommandHandler(IMediator mediator,
                                     IOrderRepository orderRepository,
                                     IIdentityService identityService)
    {
        _orderRepository = orderRepository ??
                          throw new ArgumentNullException(nameof(orderRepository));
        _identityService = identityService ??
                          throw new ArgumentNullException(nameof(identityService));
        _mediator = mediator ??
                                 throw new ArgumentNullException(nameof(mediator));
    }

    public async Task<bool> Handle(CreateOrderCommand message)
    {
        // Create the Order AggregateRoot
        // Add child entities and value objects through the Order aggregate root
        // methods and constructor so validations, invariants, and business logic
        // make sure that consistency is preserved across the whole aggregate
        var address = new Address(message.Street, message.City, message.State,
                                  message.Country, message.ZipCode);
        var order = new Order(message.UserId, address, message.CardTypeId,
                              message.CardNumber, message.CardSecurityNumber,
                              message.CardHolderName, message.CardExpiration);

        foreach (var item in message.OrderItems)
        {
            order.AddOrderItem(item.ProductId, item.ProductName, item.UnitPrice,
                               item.Discount, item.PictureUrl, item.Units);
        }

        _orderRepository.Add(order);

        return await _orderRepository.UnitOfWork
            .SaveEntitiesAsync();
    }
}
```

<span data-ttu-id="4485f-240">次に示すのは、コマンド ハンドラーで実行される追加の手順です。</span><span class="sxs-lookup"><span data-stu-id="4485f-240">These are additional steps a command handler should take:</span></span>

- <span data-ttu-id="4485f-241">コマンドのデータを使用して、集約ルートのメソッドとビヘイビアーを操作します。</span><span class="sxs-lookup"><span data-stu-id="4485f-241">Use the command’s data to operate with the aggregate root’s methods and behavior.</span></span>

- <span data-ttu-id="4485f-242">トランザクションの実行中、ドメイン オブジェクト内で内部的にイベントを発生させます。ただしこれは、コマンド ハンドラーの視点から透過的です。</span><span class="sxs-lookup"><span data-stu-id="4485f-242">Internally within the domain objects, raise domain events while the transaction is executed, but that is transparent from a command handler point of view.</span></span>

- <span data-ttu-id="4485f-243">集約の操作結果が成功である場合は、トランザクションの完了後に、統合イベントを生成します。</span><span class="sxs-lookup"><span data-stu-id="4485f-243">If the aggregate’s operation result is successful and after the transaction is finished, raise integration events.</span></span> <span data-ttu-id="4485f-244">(これらは、リポジトリなどのインフラストラクチャ クラスによって生成される場合もあります)。</span><span class="sxs-lookup"><span data-stu-id="4485f-244">(These might also be raised by infrastructure classes like repositories.)</span></span>

#### <a name="additional-resources"></a><span data-ttu-id="4485f-245">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4485f-245">Additional resources</span></span>

- <span data-ttu-id="4485f-246">**Mark Seemann。境界においては、アプリケーションはオブジェクト指向ではない** </span><span class="sxs-lookup"><span data-stu-id="4485f-246">**Mark Seemann. At the Boundaries, Applications are Not Object-Oriented** </span></span>\
  <https://blog.ploeh.dk/2011/05/31/AttheBoundaries,ApplicationsareNotObject-Oriented/>

- <span data-ttu-id="4485f-247">**コマンドとイベント** </span><span class="sxs-lookup"><span data-stu-id="4485f-247">**Commands and events** </span></span>\
  <http://cqrs.nu/Faq/commands-and-events>

- <span data-ttu-id="4485f-248">**コマンド ハンドラーの機能**</span><span class="sxs-lookup"><span data-stu-id="4485f-248">**What does a command handler do?**</span></span> \
  <http://cqrs.nu/Faq/command-handlers>

- <span data-ttu-id="4485f-249">**Jimmy Bogard。ドメイン コマンド パターン – ハンドラー** </span><span class="sxs-lookup"><span data-stu-id="4485f-249">**Jimmy Bogard. Domain Command Patterns – Handlers** </span></span>\
  <https://jimmybogard.com/domain-command-patterns-handlers/>

- <span data-ttu-id="4485f-250">**Jimmy Bogard。ドメイン コマンド パターン – 検証** </span><span class="sxs-lookup"><span data-stu-id="4485f-250">**Jimmy Bogard. Domain Command Patterns – Validation** </span></span>\
  <https://jimmybogard.com/domain-command-patterns-validation/>

## <a name="the-command-process-pipeline-how-to-trigger-a-command-handler"></a><span data-ttu-id="4485f-251">コマンド プロセス パイプライン: コマンド ハンドラーをトリガーする方法</span><span class="sxs-lookup"><span data-stu-id="4485f-251">The Command process pipeline: how to trigger a command handler</span></span>

<span data-ttu-id="4485f-252">次に知るべきことは、コマンド ハンドラーを呼び出す方法です。</span><span class="sxs-lookup"><span data-stu-id="4485f-252">The next question is how to invoke a command handler.</span></span> <span data-ttu-id="4485f-253">コマンド ハンドラーは、関連するそれぞれの ASP.NET Core コントローラーから手動で呼び出すこともできます。</span><span class="sxs-lookup"><span data-stu-id="4485f-253">You could manually call it from each related ASP.NET Core controller.</span></span> <span data-ttu-id="4485f-254">ただし、この方法は結合性が高すぎるため、理想的ではありません。</span><span class="sxs-lookup"><span data-stu-id="4485f-254">However, that approach would be too coupled and is not ideal.</span></span>

<span data-ttu-id="4485f-255">これ以外に次の 2 つの方法があり、これらをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4485f-255">The other two main options, which are the recommended options, are:</span></span>

- <span data-ttu-id="4485f-256">インメモリのメディエーター パターン成果物を通じて呼び出す。</span><span class="sxs-lookup"><span data-stu-id="4485f-256">Through an in-memory Mediator pattern artifact.</span></span>

- <span data-ttu-id="4485f-257">コントローラーとハンドラーの間で非同期メッセージ キューを使用して呼び出す。</span><span class="sxs-lookup"><span data-stu-id="4485f-257">With an asynchronous message queue, in between controllers and handlers.</span></span>

### <a name="use-the-mediator-pattern-in-memory-in-the-command-pipeline"></a><span data-ttu-id="4485f-258">コマンド パイプラインでメディエーター パターン (インメモリ) を使用する</span><span class="sxs-lookup"><span data-stu-id="4485f-258">Use the Mediator pattern (in-memory) in the command pipeline</span></span>

<span data-ttu-id="4485f-259">図 7-25 に示すように、CQRS アプローチではインテリジェント メディエーターを使用します。これはインメモリ バスに似たもので、受信されるコマンドや DTO の種類に基づいて、適切なコマンド ハンドラーへと処理をスマートにリダイレクトすることができます。</span><span class="sxs-lookup"><span data-stu-id="4485f-259">As shown in Figure 7-25, in a CQRS approach you use an intelligent mediator, similar to an in-memory bus, which is smart enough to redirect to the right command handler based on the type of the command or DTO being received.</span></span> <span data-ttu-id="4485f-260">コンポーネント間の黒い矢印は、オブジェクト間 (多くの場合、DI を通じて挿入されます) の依存関係と、関連する相互作用を表しています。</span><span class="sxs-lookup"><span data-stu-id="4485f-260">The single black arrows between components represent the dependencies between objects (in many cases, injected through DI) with their related interactions.</span></span>

![前の画像を拡大したもの: ASP.NET Core コントローラーは MediatR のコマンド パイプラインにコマンドを送信します。コマンドは適切なハンドラーに到達します。](./media/image22.png)

<span data-ttu-id="4485f-262">**図 7-25**.</span><span class="sxs-lookup"><span data-stu-id="4485f-262">**Figure 7-25**.</span></span> <span data-ttu-id="4485f-263">単一の CQRS マイクロサービス内のプロセスにおけるメディエーター パターンの使用</span><span class="sxs-lookup"><span data-stu-id="4485f-263">Using the Mediator pattern in process in a single CQRS microservice</span></span>

<span data-ttu-id="4485f-264">メディエーター パターンの使用がなぜ合理的かというと、エンタープライズ アプリケーションでは、処理要求が複雑になる場合があるからです。</span><span class="sxs-lookup"><span data-stu-id="4485f-264">The reason that using the Mediator pattern makes sense is that in enterprise applications, the processing requests can get complicated.</span></span> <span data-ttu-id="4485f-265">そのため、ログ記録、検証、監査、セキュリティなどの横断的関心事を、多数追加できるようにすることが重要です。</span><span class="sxs-lookup"><span data-stu-id="4485f-265">You want to be able to add an open number of cross-cutting concerns like logging, validations, audit, and security.</span></span> <span data-ttu-id="4485f-266">メディエーター パイプライン (「[Mediator pattern (メディエーター パターン)](https://en.wikipedia.org/wiki/Mediator_pattern)」を参照してください) を使用すれば、それらの追加ビヘイビアーや横断的関心事を追加できるようになります。</span><span class="sxs-lookup"><span data-stu-id="4485f-266">In these cases, you can rely on a mediator pipeline (see [Mediator pattern](https://en.wikipedia.org/wiki/Mediator_pattern)) to provide a means for these extra behaviors or cross-cutting concerns.</span></span>

<span data-ttu-id="4485f-267">メディエーターは、このプロセスの「実行方法」をカプセル化するオブジェクトです。メディエーターでは、状態、コマンド ハンドラーの呼び出し方法、またはハンドラーに提供するペイロードに基づいて、プロセスの実行を調整できます。</span><span class="sxs-lookup"><span data-stu-id="4485f-267">A mediator is an object that encapsulates the “how” of this process: it coordinates execution based on state, the way a command handler is invoked, or the payload you provide to the handler.</span></span> <span data-ttu-id="4485f-268">メディエーター コンポーネントを使用すると、デコレーター (または [MediatR 3](https://www.nuget.org/packages/MediatR/3.0.0) 以降の[パイプライン ビヘイビアー](https://github.com/jbogard/MediatR/wiki/Behaviors)) を適用することで、横断的関心事を一元的かつ透過的に適用することができます。</span><span class="sxs-lookup"><span data-stu-id="4485f-268">With a mediator component you can apply cross-cutting concerns in a centralized and transparent way by applying decorators (or [pipeline behaviors](https://github.com/jbogard/MediatR/wiki/Behaviors) since [MediatR 3](https://www.nuget.org/packages/MediatR/3.0.0)).</span></span> <span data-ttu-id="4485f-269">詳細については、「[Decorator pattern (デコレーター パターン)](https://en.wikipedia.org/wiki/Decorator_pattern)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4485f-269">For more information, see the [Decorator pattern](https://en.wikipedia.org/wiki/Decorator_pattern).</span></span>

<span data-ttu-id="4485f-270">デコレーターとビヘイビアーは、[アスペクト指向プログラミング (AOP)](https://en.wikipedia.org/wiki/Aspect-oriented_programming) に似ていて、メディエーター コンポーネントによって管理される特定のプロセス パイプラインのみに適用されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-270">Decorators and behaviors are similar to [Aspect Oriented Programming (AOP)](https://en.wikipedia.org/wiki/Aspect-oriented_programming), only applied to a specific process pipeline managed by the mediator component.</span></span> <span data-ttu-id="4485f-271">横断的関心事を実装する AOP 内のアスペクトは、コンパイル時に挿入された*アスペクト ウィーバー*か、オブジェクト呼び出しインターセプションに基づいて適用されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-271">Aspects in AOP that implement cross-cutting concerns are applied based on *aspect weavers* injected at compilation time or based on object call interception.</span></span> <span data-ttu-id="4485f-272">これらの典型的な AOP アプローチはどちらも、"魔法のように" 動作すると言われます。なぜなら、AOP の動作のしくみを見ることは容易ではないからです。</span><span class="sxs-lookup"><span data-stu-id="4485f-272">Both typical AOP approaches are sometimes said to work "like magic," because it is not easy to see how AOP does its work.</span></span> <span data-ttu-id="4485f-273">深刻な問題やバグを扱う場合、AOP はデバッグが困難になることがあります。</span><span class="sxs-lookup"><span data-stu-id="4485f-273">When dealing with serious issues or bugs, AOP can be difficult to debug.</span></span> <span data-ttu-id="4485f-274">一方、これらのデコレーター/ビヘイビアーは明示的であり、メディエーターのコンテキスト内でのみ適用されるため、デバッグの予測可能性がはるかに高く、デバッグが簡単になります。</span><span class="sxs-lookup"><span data-stu-id="4485f-274">On the other hand, these decorators/behaviors are explicit and applied only in the context of the mediator, so debugging is much more predictable and easy.</span></span>

<span data-ttu-id="4485f-275">例として、eShopOnContainers の注文マイクロサービスでは、2 つのサンプル ビヘイビアーを実装しています ([LogBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/LoggingBehavior.cs) クラスと [ValidatorBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/ValidatorBehavior.cs) クラス)。</span><span class="sxs-lookup"><span data-stu-id="4485f-275">For example, in the eShopOnContainers ordering microservice, we implemented two sample behaviors, a [LogBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/LoggingBehavior.cs) class and a [ValidatorBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/ValidatorBehavior.cs) class.</span></span> <span data-ttu-id="4485f-276">ビヘイビアーの実装については、次のセクションで説明しています。具体的には、eShopOnContainers で [MediatR 3](https://www.nuget.org/packages/MediatR/3.0.0) の[ビヘイビアー](https://github.com/jbogard/MediatR/wiki/Behaviors)がどのように使用されるかを示しています。</span><span class="sxs-lookup"><span data-stu-id="4485f-276">The implementation of the behaviors is explained in the next section by showing how eShopOnContainers uses [MediatR 3](https://www.nuget.org/packages/MediatR/3.0.0) [behaviors](https://github.com/jbogard/MediatR/wiki/Behaviors).</span></span>

### <a name="use-message-queues-out-of-proc-in-the-commands-pipeline"></a><span data-ttu-id="4485f-277">コマンドのパイプラインでメッセージ キュー (プロセス外) を使用する</span><span class="sxs-lookup"><span data-stu-id="4485f-277">Use message queues (out-of-proc) in the command’s pipeline</span></span>

<span data-ttu-id="4485f-278">もう 1 つの方法は、図 7-26 に示すように、ブローカーやメッセージ キューに基づいて非同期メッセージを使用する方法です。</span><span class="sxs-lookup"><span data-stu-id="4485f-278">Another choice is to use asynchronous messages based on brokers or message queues, as shown in Figure 7-26.</span></span> <span data-ttu-id="4485f-279">この方法は、コマンド ハンドラーの直前のメディエーター コンポーネントと組み合わせることもできます。</span><span class="sxs-lookup"><span data-stu-id="4485f-279">That option could also be combined with the mediator component right before the command handler.</span></span>

![コマンドのパイプラインは高可用性のメッセージ キューで処理し、適切なハンドラーにコマンドを配信することもできます。](./media/image23.png)

<span data-ttu-id="4485f-281">**図 7-26**.</span><span class="sxs-lookup"><span data-stu-id="4485f-281">**Figure 7-26**.</span></span> <span data-ttu-id="4485f-282">CQRS コマンドでのメッセージ キュー (プロセス外およびプロセス間通信) の使用</span><span class="sxs-lookup"><span data-stu-id="4485f-282">Using message queues (out of process and inter-process communication) with CQRS commands</span></span>

<span data-ttu-id="4485f-283">メッセージ キューを使用してコマンドを受信すると、コマンドのパイプラインがさらに複雑になる恐れがあります。多くの場合、外部メッセージ キューを通じて接続された 2 つのプロセスにパイプラインを分割する必要が生じるからです。</span><span class="sxs-lookup"><span data-stu-id="4485f-283">Using message queues to accept the commands can further complicate your command’s pipeline, because you will probably need to split the pipeline into two processes connected through the external message queue.</span></span> <span data-ttu-id="4485f-284">ただし、非同期メッセージングに基づいてスケーラビリティやパフォーマンスを改善する必要がある場合には、この方法を使用することになります。</span><span class="sxs-lookup"><span data-stu-id="4485f-284">Still, it should be used if you need to have improved scalability and performance based on asynchronous messaging.</span></span> <span data-ttu-id="4485f-285">図 7-26 の場合、コントローラーはコマンド メッセージをキューにポストし、そのまま戻ります。</span><span class="sxs-lookup"><span data-stu-id="4485f-285">Consider that in the case of Figure 7-26, the controller just posts the command message into the queue and returns.</span></span> <span data-ttu-id="4485f-286">その後、コマンド ハンドラーは自分のペースでメッセージを処理します。</span><span class="sxs-lookup"><span data-stu-id="4485f-286">Then the command handlers process the messages at their own pace.</span></span> <span data-ttu-id="4485f-287">これは、キューの非常に大きな利点です。高度なスケーラビリティが必要な場合、たとえば、在庫などの大量のイングレス データを扱う場合には、メッセージ キューがバッファーの役割を果たすことができるのです。</span><span class="sxs-lookup"><span data-stu-id="4485f-287">That is a great benefit of queues: the message queue can act as a buffer in cases when hyper scalability is needed, such as for stocks or any other scenario with a high volume of ingress data.</span></span>

<span data-ttu-id="4485f-288">ただし、非同期であるというメッセージ キューの性質上、コマンドの処理が成功したかどうかについて、クライアント アプリケーションとどうやって通信するかを検討する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-288">However, because of the asynchronous nature of message queues, you need to figure out how to communicate with the client application about the success or failure of the command’s process.</span></span> <span data-ttu-id="4485f-289">原則として、「ファイア アンド フォーゲット」コマンドを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="4485f-289">As a rule, you should never use “fire and forget” commands.</span></span> <span data-ttu-id="4485f-290">どのようなビジネス アプリケーションでも、コマンドが正常に処理されたかどうかを確認する必要がありますし、少なくとも、検証を経て受け入れられたかどうかを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-290">Every business application needs to know if a command was processed successfully, or at least validated and accepted.</span></span>

<span data-ttu-id="4485f-291">しかし、非同期キューに送信されたコマンド メッセージの検証後にクライアントに応答できるようにすると、トランザクションの実行後に操作の結果を返すインプロセス コマンド プロセスに比べて、システムが複雑になります。</span><span class="sxs-lookup"><span data-stu-id="4485f-291">Thus, being able to respond to the client after validating a command message that was submitted to an asynchronous queue adds complexity to your system, as compared to an in-process command process that returns the operation’s result after running the transaction.</span></span> <span data-ttu-id="4485f-292">キューを使用する場合は、コマンド プロセスの結果を、他の操作結果メッセージを通じて返す必要があるため、システムに追加のコンポーネントやカスタム通信が必要になります。</span><span class="sxs-lookup"><span data-stu-id="4485f-292">Using queues, you might need to return the result of the command process through other operation result messages, which will require additional components and custom communication in your system.</span></span>

<span data-ttu-id="4485f-293">また、非同期コマンドは一方向のコマンドであり、多くの場合は不要になる可能性があります。これについては、Burtsev Alexey 氏と Greg Young 氏が[オンライン会話](https://groups.google.com/forum/#!msg/dddcqrs/xhJHVxDx2pM/WP9qP8ifYCwJ)で交わした興味深いやりとりの中でも説明されています。</span><span class="sxs-lookup"><span data-stu-id="4485f-293">Additionally, async commands are one-way commands, which in many cases might not be needed, as is explained in the following interesting exchange between Burtsev Alexey and Greg Young in an [online conversation](https://groups.google.com/forum/#!msg/dddcqrs/xhJHVxDx2pM/WP9qP8ifYCwJ):</span></span>

> <span data-ttu-id="4485f-294">\[Burtsev Alexey\] 私は、非同期コマンド処理や一方向のコマンド メッセージングが、特に理由もなく使用されているコードをよく見かけます (それらのコードでは、長い操作があるわけでもなく、外部の非同期コードを実行しているわけでもなく、アプリケーション境界をまたいでメッセージ バスを使用しているわけでもありません)。</span><span class="sxs-lookup"><span data-stu-id="4485f-294">\[Burtsev Alexey\] I find lots of code where people use async command handling or one way command messaging without any reason to do so (they are not doing some long operation, they are not executing external async code, they do not even cross application boundary to be using message bus).</span></span> <span data-ttu-id="4485f-295">彼らはなぜ、このようにコードを無駄に複雑化しているのでしょうか。</span><span class="sxs-lookup"><span data-stu-id="4485f-295">Why do they introduce this unnecessary complexity?</span></span> <span data-ttu-id="4485f-296">また、私はこれまでブロッキング コマンド ハンドラーを使用した CQRS コードを見たことがありませんが、この種のコードはほとんどの場合、問題なく機能します。</span><span class="sxs-lookup"><span data-stu-id="4485f-296">And actually, I haven't seen a CQRS code example with blocking command handlers so far, though it will work just fine in most cases.</span></span>
>
> <span data-ttu-id="4485f-297">\[Greg Young\] \[...\] 非同期コマンドは存在しません。これは実際には別のイベントです。</span><span class="sxs-lookup"><span data-stu-id="4485f-297">\[Greg Young\] \[...\] an asynchronous command doesn't exist; it's actually another event.</span></span> <span data-ttu-id="4485f-298">あなたが送った要求を私が受け付け、それに同意しない場合にはイベントを発生させる必要があるのであれば、あなたが私に何かを指示していることにはなりません。\[つまり、それはコマンド (指令) ではありません\]。</span><span class="sxs-lookup"><span data-stu-id="4485f-298">If I must accept what you send me and raise an event if I disagree, it's no longer you telling me to do something \[that is, it’s not a command\].</span></span> <span data-ttu-id="4485f-299">何かが実行されたことを、あなたが私に知らせているだけです。</span><span class="sxs-lookup"><span data-stu-id="4485f-299">It's you telling me something has been done.</span></span> <span data-ttu-id="4485f-300">これは一見、わずかな違いにも思えますが、大変深い意味があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-300">This seems like a slight difference at first, but it has many implications.</span></span>

<span data-ttu-id="4485f-301">非同期コマンドを使用する場合、エラーを示すための簡単な方法がないので、システムの複雑さが大幅に増します。</span><span class="sxs-lookup"><span data-stu-id="4485f-301">Asynchronous commands greatly increase the complexity of a system, because there is no simple way to indicate failures.</span></span> <span data-ttu-id="4485f-302">したがって非同期コマンドは、スケーラビリティ上の要件がある場合や、内部のマイクロサービスとメッセージを通じて通信する特別なケースを除き、推奨されません。</span><span class="sxs-lookup"><span data-stu-id="4485f-302">Therefore, asynchronous commands are not recommended other than when scaling requirements are needed or in special cases when communicating the internal microservices through messaging.</span></span> <span data-ttu-id="4485f-303">これらのケースに該当する場合は、エラー用に個別のレポート/回復システムを設計する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-303">In those cases, you must design a separate reporting and recovery system for failures.</span></span>

<span data-ttu-id="4485f-304">eShopOnContainers の初期バージョンでは、HTTP 要求から開始され、メディエーター パターンによって駆動される、同期コマンド処理が採用されました。</span><span class="sxs-lookup"><span data-stu-id="4485f-304">In the initial version of eShopOnContainers, we decided to use synchronous command processing, started from HTTP requests and driven by the Mediator pattern.</span></span> <span data-ttu-id="4485f-305">これにより、プロセスが成功したかどうかを簡単に返すことが可能になっています ([CreateOrderCommandHandler](https://github.com/dotnet-architecture/eShopOnContainers/blob/master/src/Services/Ordering/Ordering.API/Application/Commands/CreateOrderCommandHandler.cs) 実装を参照)。</span><span class="sxs-lookup"><span data-stu-id="4485f-305">That easily allows you to return the success or failure of the process, as in the [CreateOrderCommandHandler](https://github.com/dotnet-architecture/eShopOnContainers/blob/master/src/Services/Ordering/Ordering.API/Application/Commands/CreateOrderCommandHandler.cs) implementation.</span></span>

<span data-ttu-id="4485f-306">いずれにせよ、これについては、アプリケーションやマイクロサービスのビジネス要件に基づいて意思決定を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-306">In any case, this should be a decision based on your application’s or microservice’s business requirements.</span></span>

## <a name="implement-the-command-process-pipeline-with-a-mediator-pattern-mediatr"></a><span data-ttu-id="4485f-307">メディエーター パターン (MediatR) を使用したコマンド プロセス パイプラインの実装</span><span class="sxs-lookup"><span data-stu-id="4485f-307">Implement the command process pipeline with a mediator pattern (MediatR)</span></span>

<span data-ttu-id="4485f-308">このガイドでは、サンプル実装として、メディエーター パターンに基づくインプロセス パイプラインを使用してコマンド挿入を駆動し、コマンドを (メモリ内で) 適切なコマンド ハンドラーにルーティングする方法を提案します。</span><span class="sxs-lookup"><span data-stu-id="4485f-308">As a sample implementation, this guide proposes using the in-process pipeline based on the Mediator pattern to drive command ingestion and route commands, in memory, to the right command handlers.</span></span> <span data-ttu-id="4485f-309">またこのガイドでは、横断的関心事を分離するために[ビヘイビアー](https://github.com/jbogard/MediatR/wiki/Behaviors)を適用することを提案します。</span><span class="sxs-lookup"><span data-stu-id="4485f-309">The guide also proposes applying [behaviors](https://github.com/jbogard/MediatR/wiki/Behaviors) in order to separate cross-cutting concerns.</span></span>

<span data-ttu-id="4485f-310">.NET Core での実装にあたっては、メディエーターを実装するオープン ソース ライブラリが複数利用可能です。</span><span class="sxs-lookup"><span data-stu-id="4485f-310">For implementation in .NET Core, there are multiple open-source libraries available that implement the Mediator pattern.</span></span> <span data-ttu-id="4485f-311">このガイドで使用するライブラリは、 [MediatR](https://github.com/jbogard/MediatR) オープン ソース ライブラリ (作成者: Jimmy Bogard) ですが、別のアプローチを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="4485f-311">The library used in this guide is the [MediatR](https://github.com/jbogard/MediatR) open-source library (created by Jimmy Bogard), but you could use another approach.</span></span> <span data-ttu-id="4485f-312">MediatR はコンパクトかつシンプルなライブラリで、デコレーターやビヘイビアーを適用しながら、コマンドなどのインメモリ メッセージを処理することができます。</span><span class="sxs-lookup"><span data-stu-id="4485f-312">MediatR is a small and simple library that allows you to process in-memory messages like a command, while applying decorators or behaviors.</span></span>

<span data-ttu-id="4485f-313">メディエーター パターンを使用すると、結合性を低減し、要求された作業に関係する問題を分離しながら、その作業を実行するハンドラー (この場合はコマンド ハンドラー) に自動的に接続することができます。</span><span class="sxs-lookup"><span data-stu-id="4485f-313">Using the Mediator pattern helps you to reduce coupling and to isolate the concerns of the requested work, while automatically connecting to the handler that performs that work—in this case, to command handlers.</span></span>

<span data-ttu-id="4485f-314">メディエーター パターンを使用する理由については、このガイドのレビュー時に、Jimmy Bogard からもう 1 点、次のような理由が説明されました。</span><span class="sxs-lookup"><span data-stu-id="4485f-314">Another good reason to use the Mediator pattern was explained by Jimmy Bogard when reviewing this guide:</span></span>

> <span data-ttu-id="4485f-315">ここでテストについても触れておいたほうがいいと思います。システムのビヘイビアーに、一貫性ある枠組みを持たせることができます。</span><span class="sxs-lookup"><span data-stu-id="4485f-315">I think it might be worth mentioning testing here – it provides a nice consistent window into the behavior of your system.</span></span> <span data-ttu-id="4485f-316">リクエストイン、レスポンスアウト。このアスペクトは、作成したテストを一貫性を持って動作させるうえで、非常に価値があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-316">Request-in, response-out. We’ve found that aspect quite valuable in building consistently behaving tests.</span></span>

<span data-ttu-id="4485f-317">まずは、メディエーター オブジェクトを実際に使用する、サンプル WebAPI コントローラーから見ていきましょう。</span><span class="sxs-lookup"><span data-stu-id="4485f-317">First, let’s look at a sample WebAPI controller where you actually would use the mediator object.</span></span> <span data-ttu-id="4485f-318">メディエーター オブジェクトを今まで使用していなかった場合は、そのコントローラーのすべての依存関係 (ロガー オブジェクトなど) を挿入する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-318">If you were not using the mediator object, you would need to inject all the dependencies for that controller, things like a logger object and others.</span></span> <span data-ttu-id="4485f-319">そのため、コンストラクターがかなり複雑になります。</span><span class="sxs-lookup"><span data-stu-id="4485f-319">Therefore, the constructor would be quite complicated.</span></span> <span data-ttu-id="4485f-320">一方、メディエーター オブジェクトを使用している場合は、コントローラーのコンストラクターがかなりシンプルになります。横断的操作ごとに 1 つの依存関係があれば、多数の依存関係を含めなくても、いくつかの依存関係だけで事足ります。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4485f-320">On the other hand, if you use the mediator object, the constructor of your controller can be a lot simpler, with just a few dependencies instead of many dependencies if you had one per cross-cutting operation, as in the following example:</span></span>

```csharp
public class MyMicroserviceController : Controller
{
    public MyMicroserviceController(IMediator mediator,
                                    IMyMicroserviceQueries microserviceQueries)
    // ...
```

<span data-ttu-id="4485f-321">メディエーターによって、クリーンでコンパクトな Web API コントローラー コンストラクターが実現しています。</span><span class="sxs-lookup"><span data-stu-id="4485f-321">You can see that the mediator provides a clean and lean Web API controller constructor.</span></span> <span data-ttu-id="4485f-322">また、コントローラー メソッド内では、メディエーター オブジェクトにコマンドを送信するコードが、ほぼ 1 行で記述されています。</span><span class="sxs-lookup"><span data-stu-id="4485f-322">In addition, within the controller methods, the code to send a command to the mediator object is almost one line:</span></span>

```csharp
[Route("new")]
[HttpPost]
public async Task<IActionResult> ExecuteBusinessOperation([FromBody]RunOpCommand
                                                               runOperationCommand)
{
    var commandResult = await _mediator.SendAsync(runOperationCommand);

    return commandResult ? (IActionResult)Ok() : (IActionResult)BadRequest();
}
```

### <a name="implement-idempotent-commands"></a><span data-ttu-id="4485f-323">べき等コマンドの実装</span><span class="sxs-lookup"><span data-stu-id="4485f-323">Implement idempotent Commands</span></span>

<span data-ttu-id="4485f-324">**eShopOnContainers** では、上記の例よりもさらに高度なコードで、注文マイクロサービスから CreateOrderCommand オブジェクトが送信されています。</span><span class="sxs-lookup"><span data-stu-id="4485f-324">In **eShopOnContainers**, a more advanced example than the above is submitting a CreateOrderCommand object from the Ordering microservice.</span></span> <span data-ttu-id="4485f-325">ただし、この注文ビジネス プロセスはやや複雑で、(このケースでは) バスケット マイクロサービスから開始されているため、CreateOrderCommand オブジェクトを送信するこのアクションは、先の例のようにクライアント アプリから呼び出されるシンプルな WebAPI コントローラーではなく、UserCheckoutAcceptedIntegrationEvent.cs (https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/IntegrationEvents/EventHandling/UserCheckoutAcceptedIntegrationEventHandler.cs) ) という統合イベント ハンドラーから実行されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-325">But since the Ordering business process is a bit more complex and, in our case, it actually starts in the Basket microservice, this action of submitting the CreateOrderCommand object is performed from an integration-event handler named >UserCheckoutAcceptedIntegrationEvent.cs](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/IntegrationEvents/EventHandling/UserCheckoutAcceptedIntegrationEventHandler.cs) instead of a simple WebAPI controller called from the client App as in the previous simpler example.</span></span>

<span data-ttu-id="4485f-326">とは言え、MediatR にコマンドを送信するアクションは、次のコードに示すように、非常に似ています。</span><span class="sxs-lookup"><span data-stu-id="4485f-326">Nevertheless, the action of submitting the Command to MediatR is pretty similar, as shown in the following code.</span></span>

```csharp
var createOrderCommand = new CreateOrderCommand(eventMsg.Basket.Items,
                                                eventMsg.UserId, eventMsg.City,
                                                eventMsg.Street, eventMsg.State,
                                                eventMsg.Country, eventMsg.ZipCode,
                                                eventMsg.CardNumber,
                                                eventMsg.CardHolderName,
                                                eventMsg.CardExpiration,
                                                eventMsg.CardSecurityNumber,
                                                eventMsg.CardTypeId);

var requestCreateOrder = new IdentifiedCommand<CreateOrderCommand,bool>(createOrderCommand,
                                                                        eventMsg.RequestId);
result = await _mediator.Send(requestCreateOrder);
```

<span data-ttu-id="4485f-327">ただし、この例ではべき等コマンドも実装しているため、コードがもう少し高度なものになっています。</span><span class="sxs-lookup"><span data-stu-id="4485f-327">However, this case is also a little bit more advanced because we’re also implementing idempotent commands.</span></span> <span data-ttu-id="4485f-328">CreateOrderCommand プロセスはべき等なので、何らかの理由 (再試行など) により同じメッセージがネットワークから重複して送られた場合でも、同じビジネス オーダーは 1 回だけ処理されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-328">The CreateOrderCommand process should be idempotent, so if the same message comes duplicated through the network, because of any reason, like retries, the same business order will be processed just once.</span></span>

<span data-ttu-id="4485f-329">これは、ビジネス コマンド (この場合は CreateOrderCommand) をラップし、それを汎用の IdentifiedCommand 内に埋め込むことで実装されています。IdentifiedCommand は、ネットワークを通じて送られるメッセージのうち、べき等である必要があるすべてのメッセージの ID によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-329">This is implemented by wrapping the business command (in this case CreateOrderCommand) and embedding it into a generic IdentifiedCommand which is tracked by an ID of every message coming through the network that has to be idempotent.</span></span>

<span data-ttu-id="4485f-330">次のコードでは、IdentifiedCommand に、DTO と ID のほか、ラップされたビジネス コマンド オブジェクトしか含まれていません。</span><span class="sxs-lookup"><span data-stu-id="4485f-330">In the code below, you can see that the IdentifiedCommand is nothing more than a DTO with and ID plus the wrapped business command object.</span></span>

```csharp
public class IdentifiedCommand<T, R> : IRequest<R>
    where T : IRequest<R>
{
    public T Command { get; }
    public Guid Id { get; }
    public IdentifiedCommand(T command, Guid id)
    {
        Command = command;
        Id = id;
    }
}
```

<span data-ttu-id="4485f-331">次に、IdentifiedCommand の CommandHandler ([IdentifiedCommandHandler.cs](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Commands/IdentifiedCommandHandler.cs)) が、メッセージの一部として受け取った ID をチェックし、それがテーブル内に既に存在していないかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="4485f-331">Then the CommandHandler for the IdentifiedCommand named [IdentifiedCommandHandler.cs](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Commands/IdentifiedCommandHandler.cs) will basically check if the ID coming as part of the message already exists in a table.</span></span> <span data-ttu-id="4485f-332">既に存在する場合、そのコマンドは再度処理されることはなく、べき等コマンドとして動作します。</span><span class="sxs-lookup"><span data-stu-id="4485f-332">If it already exists, that command won’t be processed again, so it behaves as an idempotent command.</span></span> <span data-ttu-id="4485f-333">そのインフラストラクチャ コードは、下記の `_requestManager.ExistAsync` メソッド呼び出しによって実行されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-333">That infrastructure code is performed by the `_requestManager.ExistAsync` method call below.</span></span>

```csharp
// IdentifiedCommandHandler.cs
public class IdentifiedCommandHandler<T, R> :
                                   IAsyncRequestHandler<IdentifiedCommand<T, R>, R>
                                   where T : IRequest<R>
{
    private readonly IMediator _mediator;
    private readonly IRequestManager _requestManager;

    public IdentifiedCommandHandler(IMediator mediator,
                                    IRequestManager requestManager)
    {
        _mediator = mediator;
        _requestManager = requestManager;
    }

    protected virtual R CreateResultForDuplicateRequest()
    {
        return default(R);
    }

    public async Task<R> Handle(IdentifiedCommand<T, R> message)
    {
        var alreadyExists = await _requestManager.ExistAsync(message.Id);
        if (alreadyExists)
        {
            return CreateResultForDuplicateRequest();
        }
        else
        {
            await _requestManager.CreateRequestForCommandAsync<T>(message.Id);

            // Send the embedded business command to mediator
            // so it runs its related CommandHandler
            var result = await _mediator.Send(message.Command);

            return result;
        }
    }
}
```

<span data-ttu-id="4485f-334">IdentifiedCommand はビジネス コマンドのエンベロープのように動作するので、ビジネス コマンドを処理する必要がある場合 (ID が重複していない場合) には、その内部ビジネス コマンドを受け取り、それを [IdentifiedCommandHandler.cs](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Commands/IdentifiedCommandHandler.cs) からメディエーターへと送信します (`_mediator.Send(message.Command)` を実行している、上記のコードの最後の部分)。</span><span class="sxs-lookup"><span data-stu-id="4485f-334">Since the IdentifiedCommand acts like a business command’s envelope, when the business command needs to be processed because it is not a repeated Id, then it takes that inner business command and re-submits it to Mediator, as in the last part of the code shown above when running `_mediator.Send(message.Command)`, from the [IdentifiedCommandHandler.cs](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Commands/IdentifiedCommandHandler.cs).</span></span>

<span data-ttu-id="4485f-335">これを行う際には、ビジネス コマンド ハンドラー (この場合は、注文データベースに対してトランザクションを実行している [CreateOrderCommandHandler](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Commands/CreateOrderCommandHandler.cs)) がリンクされ、実行されます。次にコードを示します。</span><span class="sxs-lookup"><span data-stu-id="4485f-335">When doing that, it will link and run the business command handler, in this case, the [CreateOrderCommandHandler](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Commands/CreateOrderCommandHandler.cs) which is running transactions against the Ordering database, as shown in the following code.</span></span>

```csharp
// CreateOrderCommandHandler.cs
public class CreateOrderCommandHandler
                                   : IAsyncRequestHandler<CreateOrderCommand, bool>
{
    private readonly IOrderRepository _orderRepository;
    private readonly IIdentityService _identityService;
    private readonly IMediator _mediator;

    // Using DI to inject infrastructure persistence Repositories
    public CreateOrderCommandHandler(IMediator mediator,
                                     IOrderRepository orderRepository,
                                     IIdentityService identityService)
    {
        _orderRepository = orderRepository ??
                          throw new ArgumentNullException(nameof(orderRepository));
        _identityService = identityService ??
                          throw new ArgumentNullException(nameof(identityService));
        _mediator = mediator ??
                                 throw new ArgumentNullException(nameof(mediator));
    }

    public async Task<bool> Handle(CreateOrderCommand message)
    {
        // Add/Update the Buyer AggregateRoot
        var address = new Address(message.Street, message.City, message.State,
                                  message.Country, message.ZipCode);
        var order = new Order(message.UserId, address, message.CardTypeId,
                              message.CardNumber, message.CardSecurityNumber,
                              message.CardHolderName, message.CardExpiration);

        foreach (var item in message.OrderItems)
        {
            order.AddOrderItem(item.ProductId, item.ProductName, item.UnitPrice,
                               item.Discount, item.PictureUrl, item.Units);
        }

        _orderRepository.Add(order);

        return await _orderRepository.UnitOfWork
            .SaveEntitiesAsync();
    }
}
```

### <a name="register-the-types-used-by-mediatr"></a><span data-ttu-id="4485f-336">MediatR によって使用される型の登録</span><span class="sxs-lookup"><span data-stu-id="4485f-336">Register the types used by MediatR</span></span>

<span data-ttu-id="4485f-337">MediatR にコマンド ハンドラー クラスを認識させるには、メディエーター クラスとコマンド ハンドラー クラスを IoC コンテナーに登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4485f-337">In order for MediatR to be aware of your command handler classes, you need to register the mediator classes and the command handler classes in your IoC container.</span></span> <span data-ttu-id="4485f-338">既定では、MediatR は Autofac を IoC コンテナーとして使用しますが、組み込みの ASP.NET Core IoC コンテナーや、MediatR でサポートされているその他のコンテナーを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="4485f-338">By default, MediatR uses Autofac as the IoC container, but you can also use the built-in ASP.NET Core IoC container or any other container supported by MediatR.</span></span>

<span data-ttu-id="4485f-339">次のコードは、Autofac モジュールを使用している場合に、メディエーターの型とコマンドを登録する方法を示したものです。</span><span class="sxs-lookup"><span data-stu-id="4485f-339">The following code shows how to register Mediator’s types and commands when using Autofac modules.</span></span>

```csharp
public class MediatorModule : Autofac.Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder.RegisterAssemblyTypes(typeof(IMediator).GetTypeInfo().Assembly)
            .AsImplementedInterfaces();

        // Register all the Command classes (they implement IAsyncRequestHandler)
        // in assembly holding the Commands
        builder.RegisterAssemblyTypes(
                              typeof(CreateOrderCommand).GetTypeInfo().Assembly).
                                   AsClosedTypesOf(typeof(IAsyncRequestHandler<,>));
        // Other types registration
        //...
    }
}
```

<span data-ttu-id="4485f-340">"魔法が起こる" のは、まさにこの部分です。</span><span class="sxs-lookup"><span data-stu-id="4485f-340">This is where “the magic happens” with MediatR.</span></span>

<span data-ttu-id="4485f-341">各コマンド ハンドラーには汎用の `IAsyncRequestHandler<T>` インターフェイスが実装されているため、アセンブリを登録すると、`IAsyncRequestHandler` としてマークされたすべての型が `RegisteredAssemblyTypes` に登録され、`CommandHandler` クラスに記述されたリレーションシップにより、`CommandHandlers` が対応する `Commands` に関連付けられます。次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="4485f-341">Because each command handler implements the generic `IAsyncRequestHandler<T>` interface, when registering the assemblies, the code registers with `RegisteredAssemblyTypes` all the types marked as `IAsyncRequestHandler` while relating the `CommandHandlers` with their `Commands`, thanks to the relationship stated at the `CommandHandler` class, as in the following example:</span></span>

```csharp
public class CreateOrderCommandHandler
  : IAsyncRequestHandler<CreateOrderCommand, bool>
{
```

<span data-ttu-id="4485f-342">これが、コマンドをコマンド ハンドラーに関連付けるコードです。</span><span class="sxs-lookup"><span data-stu-id="4485f-342">That is the code that correlates commands with command handlers.</span></span> <span data-ttu-id="4485f-343">ハンドラーは非常にシンプルなクラスですが、`RequestHandler<T>` から継承されるクラスであり、T はコマンドの型になります。また、MediatR により、正しいペイロード (コマンド) で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-343">The handler is just a simple class, but it inherits from `RequestHandler<T>`, where T is the command type, and MediatR makes sure it is invoked with the correct payload (the command).</span></span>

## <a name="apply-cross-cutting-concerns-when-processing-commands-with-the-behaviors-in-mediatr"></a><span data-ttu-id="4485f-344">MediatR のビヘイビアーを使用して、コマンドの処理時に横断的関心事を適用する</span><span class="sxs-lookup"><span data-stu-id="4485f-344">Apply cross-cutting concerns when processing commands with the Behaviors in MediatR</span></span>

<span data-ttu-id="4485f-345">もう 1 つ重要なことがあります。それは、横断的関心事をメディエーター パイプラインに適用することです。</span><span class="sxs-lookup"><span data-stu-id="4485f-345">There is one more thing: being able to apply cross-cutting concerns to the mediator pipeline.</span></span> <span data-ttu-id="4485f-346">Autofac 登録モジュール コードの末尾を見ると、ビヘイビアー型 (カスタム LoggingBehavior クラスと ValidatorBehavior クラス) がどのように登録されるかがわかります。</span><span class="sxs-lookup"><span data-stu-id="4485f-346">You can also see at the end of the Autofac registration module code how it registers a behavior type, specifically, a custom LoggingBehavior class and a ValidatorBehavior class.</span></span> <span data-ttu-id="4485f-347">ただし、その他のカスタム ビヘイビアーを追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="4485f-347">But you could add other custom behaviors, too.</span></span>

```csharp
public class MediatorModule : Autofac.Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder.RegisterAssemblyTypes(typeof(IMediator).GetTypeInfo().Assembly)
            .AsImplementedInterfaces();

        // Register all the Command classes (they implement IAsyncRequestHandler)
        // in assembly holding the Commands
        builder.RegisterAssemblyTypes(
                              typeof(CreateOrderCommand).GetTypeInfo().Assembly).
                                   AsClosedTypesOf(typeof(IAsyncRequestHandler<,>));
        // Other types registration
        //...
        builder.RegisterGeneric(typeof(LoggingBehavior<,>)).
                                                   As(typeof(IPipelineBehavior<,>));
        builder.RegisterGeneric(typeof(ValidatorBehavior<,>)).
                                                   As(typeof(IPipelineBehavior<,>));
    }
}
```

<span data-ttu-id="4485f-348">この [LoggingBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/LoggingBehavior.cs) クラスは、次のコードとして実装することができます。これにより、実行されるコマンド ハンドラーに関する情報と、それが成功したかどうかどうかが記録されます。</span><span class="sxs-lookup"><span data-stu-id="4485f-348">That [LoggingBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/LoggingBehavior.cs) class can be implemented as the following code, which logs information about the command handler being executed and whether it was successful or not.</span></span>

```csharp
public class LoggingBehavior<TRequest, TResponse>
         : IPipelineBehavior<TRequest, TResponse>
{
    private readonly ILogger<LoggingBehavior<TRequest, TResponse>> _logger;
    public LoggingBehavior(ILogger<LoggingBehavior<TRequest, TResponse>> logger) =>
                                                                  _logger = logger;

    public async Task<TResponse> Handle(TRequest request,
                                        RequestHandlerDelegate<TResponse> next)
    {
        _logger.LogInformation($"Handling {typeof(TRequest).Name}");
        var response = await next();
        _logger.LogInformation($"Handled {typeof(TResponse).Name}");
        return response;
    }
}
```

<span data-ttu-id="4485f-349">このビヘイビアー クラスを実装し、それをパイプラインで登録することで (上記の MediatorModule)、MediatR を通じて処理されたすべてのコマンドが、実行に関するロギング情報になります。</span><span class="sxs-lookup"><span data-stu-id="4485f-349">Just by implementing this behavior class and by registering it in the pipeline (in the MediatorModule above), all the commands processed through MediatR will be logging information about the execution.</span></span>

<span data-ttu-id="4485f-350">eShopOnContainers 注文マイクロサービスでは、基本検証用に 2 つ目のビヘイビアーも適用されます。[FluentValidation](https://github.com/JeremySkinner/FluentValidation) ライブラリに依存する [ValidatorBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/ValidatorBehavior.cs) クラスです。次にコードを示します。</span><span class="sxs-lookup"><span data-stu-id="4485f-350">The eShopOnContainers ordering microservice also applies a second behavior for basic validations, the [ValidatorBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/ValidatorBehavior.cs) class that relies on the [FluentValidation](https://github.com/JeremySkinner/FluentValidation) library, as shown in the following code:</span></span>

```csharp
public class ValidatorBehavior<TRequest, TResponse>
         : IPipelineBehavior<TRequest, TResponse>
{
    private readonly IValidator<TRequest>[] _validators;
    public ValidatorBehavior(IValidator<TRequest>[] validators) =>
                                                         _validators = validators;

    public async Task<TResponse> Handle(TRequest request,
                                        RequestHandlerDelegate<TResponse> next)
    {
        var failures = _validators
            .Select(v => v.Validate(request))
            .SelectMany(result => result.Errors)
            .Where(error => error != null)
            .ToList();

        if (failures.Any())
        {
            throw new OrderingDomainException(
                $"Command Validation Errors for type {typeof(TRequest).Name}",
                        new ValidationException("Validation exception", failures));
        }

        var response = await next();
        return response;
    }
}
```

<span data-ttu-id="4485f-351">検証でエラーが出た場合はここのビヘイビアーによって例外が発行されますが、結果オブジェクトを返すこともできる場合があります。成功であればコマンド結果が含まれ、失敗であれば検証メッセージが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4485f-351">The behavior here is raising an exception if validation fails, but you could also return a result object, containing the command result if it succeeded or the validation messages in case it didn’t.</span></span> <span data-ttu-id="4485f-352">おそらくは、より簡単にユーザーに検証結果を示すことができます。</span><span class="sxs-lookup"><span data-stu-id="4485f-352">This would probably make it easier to display validation results to the user.</span></span>

<span data-ttu-id="4485f-353">その後、[FluentValidation](https://github.com/JeremySkinner/FluentValidation) ライブラリに基づいて、CreateOrderCommand によって渡されたデータ用の検証が作成されました。次にコードを示します。</span><span class="sxs-lookup"><span data-stu-id="4485f-353">Then, based on the [FluentValidation](https://github.com/JeremySkinner/FluentValidation) library, we created validation for the data passed with CreateOrderCommand, as in the following code:</span></span>

```csharp
public class CreateOrderCommandValidator : AbstractValidator<CreateOrderCommand>
{
    public CreateOrderCommandValidator()
    {
        RuleFor(command => command.City).NotEmpty();
        RuleFor(command => command.Street).NotEmpty();
        RuleFor(command => command.State).NotEmpty();
        RuleFor(command => command.Country).NotEmpty();
        RuleFor(command => command.ZipCode).NotEmpty();
        RuleFor(command => command.CardNumber).NotEmpty().Length(12, 19);
        RuleFor(command => command.CardHolderName).NotEmpty();
        RuleFor(command => command.CardExpiration).NotEmpty().Must(BeValidExpirationDate).WithMessage("Please specify a valid card expiration date");
        RuleFor(command => command.CardSecurityNumber).NotEmpty().Length(3);
        RuleFor(command => command.CardTypeId).NotEmpty();
        RuleFor(command => command.OrderItems).Must(ContainOrderItems).WithMessage("No order items found");
    }

    private bool BeValidExpirationDate(DateTime dateTime)
    {
        return dateTime >= DateTime.UtcNow;
    }

    private bool ContainOrderItems(IEnumerable<OrderItemDTO> orderItems)
    {
        return orderItems.Any();
    }
}

```

<span data-ttu-id="4485f-354">追加の検証を作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="4485f-354">You could create additional validations.</span></span> <span data-ttu-id="4485f-355">これは、コマンド検証を実装するための非常にクリーンで簡潔な方法です。</span><span class="sxs-lookup"><span data-stu-id="4485f-355">This is a very clean and elegant way to implement your command validations.</span></span>

<span data-ttu-id="4485f-356">同様の方法で、コマンドの処理時にそれらに適用したい追加のアスペクトや横断的関心事に対する、その他のビヘイビアーを実装することもできます。</span><span class="sxs-lookup"><span data-stu-id="4485f-356">In a similar way, you could implement other behaviors for additional aspects or cross-cutting concerns that you want to apply to commands when handling them.</span></span>

#### <a name="additional-resources"></a><span data-ttu-id="4485f-357">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="4485f-357">Additional resources</span></span>

##### <a name="the-mediator-pattern"></a><span data-ttu-id="4485f-358">メディエーター パターン</span><span class="sxs-lookup"><span data-stu-id="4485f-358">The mediator pattern</span></span>

- <span data-ttu-id="4485f-359">**メディエーター パターン** </span><span class="sxs-lookup"><span data-stu-id="4485f-359">**Mediator pattern** </span></span>\
  [https://en.wikipedia.org/wiki/Mediator\_pattern](https://en.wikipedia.org/wiki/Mediator_pattern)

##### <a name="the-decorator-pattern"></a><span data-ttu-id="4485f-360">デコレーター パターン</span><span class="sxs-lookup"><span data-stu-id="4485f-360">The decorator pattern</span></span>

- <span data-ttu-id="4485f-361">**デコレーター パターン** </span><span class="sxs-lookup"><span data-stu-id="4485f-361">**Decorator pattern** </span></span>\
  [https://en.wikipedia.org/wiki/Decorator\_pattern](https://en.wikipedia.org/wiki/Decorator_pattern)

##### <a name="mediatr-jimmy-bogard"></a><span data-ttu-id="4485f-362">MediatR (Jimmy Bogard)</span><span class="sxs-lookup"><span data-stu-id="4485f-362">MediatR (Jimmy Bogard)</span></span>

- <span data-ttu-id="4485f-363">**MediatR。**</span><span class="sxs-lookup"><span data-stu-id="4485f-363">**MediatR.**</span></span> <span data-ttu-id="4485f-364">GitHub リポジトリ。</span><span class="sxs-lookup"><span data-stu-id="4485f-364">GitHub repo.</span></span> \
  <https://github.com/jbogard/MediatR>

- <span data-ttu-id="4485f-365">**MediatR と AutoMapper での CQRS** </span><span class="sxs-lookup"><span data-stu-id="4485f-365">**CQRS with MediatR and AutoMapper** </span></span>\
  <https://lostechies.com/jimmybogard/2015/05/05/cqrs-with-mediatr-and-automapper/>

- <span data-ttu-id="4485f-366">**コントローラーの簡略化:POST とコマンド。**</span><span class="sxs-lookup"><span data-stu-id="4485f-366">**Put your controllers on a diet: POSTs and commands.**</span></span> \
  <https://lostechies.com/jimmybogard/2013/12/19/put-your-controllers-on-a-diet-posts-and-commands/>

- <span data-ttu-id="4485f-367">**メディエーター パイプラインでの横断的関心事への取り組み** </span><span class="sxs-lookup"><span data-stu-id="4485f-367">**Tackling cross-cutting concerns with a mediator pipeline** </span></span>\
  <https://lostechies.com/jimmybogard/2014/09/09/tackling-cross-cutting-concerns-with-a-mediator-pipeline/>

- <span data-ttu-id="4485f-368">**CQRS と REST: 完全な一致** </span><span class="sxs-lookup"><span data-stu-id="4485f-368">**CQRS and REST: the perfect match** </span></span>\
  <https://lostechies.com/jimmybogard/2016/06/01/cqrs-and-rest-the-perfect-match/>

- <span data-ttu-id="4485f-369">**MediatR パイプラインの例** </span><span class="sxs-lookup"><span data-stu-id="4485f-369">**MediatR Pipeline Examples** </span></span>\
  <https://lostechies.com/jimmybogard/2016/10/13/mediatr-pipeline-examples/>

- <span data-ttu-id="4485f-370">**MediatR と ASP.NET Core の縦方向のスライス テスト フィクスチャ** </span><span class="sxs-lookup"><span data-stu-id="4485f-370">**Vertical Slice Test Fixtures for MediatR and ASP.NET Core** </span></span>\
  <https://lostechies.com/jimmybogard/2016/10/24/vertical-slice-test-fixtures-for-mediatr-and-asp-net-core/>

- <span data-ttu-id="4485f-371">**Microsoft 依存関係挿入用の MediatR 拡張機能のリリース** </span><span class="sxs-lookup"><span data-stu-id="4485f-371">**MediatR Extensions for Microsoft Dependency Injection Released** </span></span>\
  <https://lostechies.com/jimmybogard/2016/07/19/mediatr-extensions-for-microsoft-dependency-injection-released/>

##### <a name="fluent-validation"></a><span data-ttu-id="4485f-372">Fluent 検証</span><span class="sxs-lookup"><span data-stu-id="4485f-372">Fluent validation</span></span>

- <span data-ttu-id="4485f-373">**Jeremy Skinner。FluentValidation.**</span><span class="sxs-lookup"><span data-stu-id="4485f-373">**Jeremy Skinner. FluentValidation.**</span></span> <span data-ttu-id="4485f-374">GitHub リポジトリ。</span><span class="sxs-lookup"><span data-stu-id="4485f-374">GitHub repo.</span></span> \
  <https://github.com/JeremySkinner/FluentValidation>

> [!div class="step-by-step"]
> <span data-ttu-id="4485f-375">[前へ](microservice-application-layer-web-api-design.md)
> [次へ](../implement-resilient-applications/index.md)</span><span class="sxs-lookup"><span data-stu-id="4485f-375">[Previous](microservice-application-layer-web-api-design.md)
[Next](../implement-resilient-applications/index.md)</span></span>