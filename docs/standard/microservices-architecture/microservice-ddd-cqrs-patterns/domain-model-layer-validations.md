---
title: "设计域模型层中的验证"
description: "为容器化的.NET 应用程序的.NET 微服务体系结构 |设计域模型层中的验证"
keywords: "Docker, 微服务, ASP.NET, 容器"
author: CESARDELATORRE
ms.author: wiwagn
ms.date: 05/26/2017
ms.prod: .net-core
ms.technology: dotnet-docker
ms.topic: article
ms.openlocfilehash: f4870d0568c3539f296bcb3f577291cb0250cfca
ms.sourcegitcommit: 62d3e3e74c1b7ffa927590012c0b9f87de1b0848
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="designing-validations-in-the-domain-model-layer"></a><span data-ttu-id="370de-104">设计域模型层中的验证</span><span class="sxs-lookup"><span data-stu-id="370de-104">Designing validations in the domain model layer</span></span>

<span data-ttu-id="370de-105">在 DDD，验证规则都可以看作固定协定。</span><span class="sxs-lookup"><span data-stu-id="370de-105">In DDD, validation rules can be thought as invariants.</span></span> <span data-ttu-id="370de-106">聚合的主要责任是在该聚合内的所有实体的状态更改之间强制实施固定协定。</span><span class="sxs-lookup"><span data-stu-id="370de-106">The main responsibility of an aggregate is to enforce invariants across state changes for all the entities within that aggregate.</span></span>

<span data-ttu-id="370de-107">域实体应始终为有效的实体。</span><span class="sxs-lookup"><span data-stu-id="370de-107">Domain entities should always be valid entities.</span></span> <span data-ttu-id="370de-108">有一定数量的固定条件应始终为 true 的对象。</span><span class="sxs-lookup"><span data-stu-id="370de-108">There are a certain number of invariants for an object that should always be true.</span></span> <span data-ttu-id="370de-109">例如，订单项对象始终必须拥有的数量必须为正整数，加上项目名称和价格。</span><span class="sxs-lookup"><span data-stu-id="370de-109">For example, an order item object always has to have a quantity that must be a positive integer, plus an article name and price.</span></span> <span data-ttu-id="370de-110">因此，固定协定强制负责 （尤其是的聚合根） 的域实体的和的实体对象时应不能存在不是有效。</span><span class="sxs-lookup"><span data-stu-id="370de-110">Therefore, invariants enforcement is the responsibility of the domain entities (especially of the aggregate root) and an entity object should not be able to exist without being valid.</span></span> <span data-ttu-id="370de-111">固定规则只需表达为协定，而它们违反时引发异常或通知。</span><span class="sxs-lookup"><span data-stu-id="370de-111">Invariant rules are simply expressed as contracts, and exceptions or notifications are raised when they are violated.</span></span>

<span data-ttu-id="370de-112">这背后的原因是因为对象在它们永远不会已在进行的状态，则出现 bug 都很多。</span><span class="sxs-lookup"><span data-stu-id="370de-112">The reasoning behind this is that many bugs occur because objects are in a state they should never have been in.</span></span> <span data-ttu-id="370de-113">下面是从在 Greg Young 详细讲解[联机讨论](http://jeffreypalermo.com/blog/the-fallacy-of-the-always-valid-entity/):</span><span class="sxs-lookup"><span data-stu-id="370de-113">The following is a good explanation from Greg Young in an [online discussion](http://jeffreypalermo.com/blog/the-fallacy-of-the-always-valid-entity/):</span></span>

<span data-ttu-id="370de-114">让我们建议我们现在有 SendUserCreationEmailService 采用 UserProfile...如何可以我们合理利用在该名称不为 null 的服务中？</span><span class="sxs-lookup"><span data-stu-id="370de-114">Let's propose we now have a SendUserCreationEmailService that takes a UserProfile ... how can we rationalize in that service that Name is not null?</span></span> <span data-ttu-id="370de-115">我们它再次检查？</span><span class="sxs-lookup"><span data-stu-id="370de-115">Do we check it again?</span></span> <span data-ttu-id="370de-116">或更有可能...你只是你不必费神以检查并"最佳希望"— 您却希望人担忧若要发送到你之前验证它。</span><span class="sxs-lookup"><span data-stu-id="370de-116">Or more likely ... you just don't bother to check and "hope for the best"—you hope that someone bothered to validate it before sending it to you.</span></span> <span data-ttu-id="370de-117">当然，使用 TDD 应，如果发送具有空名称的客户也应引发错误编写我们的第一个测试。</span><span class="sxs-lookup"><span data-stu-id="370de-117">Of course, using TDD one of the first tests we should be writing is that if I send a customer with a null name that it should raise an error.</span></span> <span data-ttu-id="370de-118">但是，一旦我们开始反复编写这些类型的测试我们认识到..."请稍等如果我们永远不会允许名称成为 null 我们就不会所有这些测试"</span><span class="sxs-lookup"><span data-stu-id="370de-118">But once we start writing these kinds of tests over and over again we realize ... "wait if we never allowed name to become null we wouldn't have all of these tests"</span></span>

## <a name="implementing-validations-in-the-domain-model-layer"></a><span data-ttu-id="370de-119">在域模型层中实现验证</span><span class="sxs-lookup"><span data-stu-id="370de-119">Implementing validations in the domain model layer</span></span>

<span data-ttu-id="370de-120">在域实体构造函数中或可以更新实体的方法中，通常被实现验证。</span><span class="sxs-lookup"><span data-stu-id="370de-120">Validations are usually implemented in domain entity constructors or in methods that can update the entity.</span></span> <span data-ttu-id="370de-121">有多种方法来实现验证，例如验证数据和在验证失败时引发的异常。</span><span class="sxs-lookup"><span data-stu-id="370de-121">There are multiple ways to implement validations, such as verifying data and raising exceptions if the validation fails.</span></span> <span data-ttu-id="370de-122">还有更高级的模式，例如，验证，使用的规范模式和通知模式，以返回而不是返回每个验证异常，所发生的错误的集合。</span><span class="sxs-lookup"><span data-stu-id="370de-122">There are also more advanced patterns such as using the Specification pattern for validations, and the Notification pattern to return a collection of errors instead of returning an exception for each validation as it occurs.</span></span>

### <a name="validating-conditions-and-throwing-exceptions"></a><span data-ttu-id="370de-123">验证条件并引发异常</span><span class="sxs-lookup"><span data-stu-id="370de-123">Validating conditions and throwing exceptions</span></span>

<span data-ttu-id="370de-124">下面的代码示例通过引发异常，在域实体中显示验证的最简单方法。</span><span class="sxs-lookup"><span data-stu-id="370de-124">The following code example shows the simplest approach to validation in a domain entity by raising an exception.</span></span> <span data-ttu-id="370de-125">在本部分末尾的引用表中，你可以看到指向更高级的实现根据我们前面讨论的模式。</span><span class="sxs-lookup"><span data-stu-id="370de-125">In the references table at the end of this section you can see links to more advanced implementations based on the patterns we have discussed previously.</span></span>

```csharp
public void SetAddress(Address address)
{
    _shippingAddress = address?? throw new ArgumentNullException(nameof(address));
}
```

<span data-ttu-id="370de-126">更好的示例将演示，需要确保，内部状态未更改，或者在方法的所有变化都时发生。</span><span class="sxs-lookup"><span data-stu-id="370de-126">A better example would demonstrate the need to ensure that either the internal state did not change, or that all the mutations for a method occurred.</span></span> <span data-ttu-id="370de-127">例如，下面的实现将使对象保持处于无效状态：</span><span class="sxs-lookup"><span data-stu-id="370de-127">For example, the following implementation would leave the object in an invalid state:</span></span>

```csharp
public void SetAddress(string line1, string line2,
    string city, string state, int zip)
{
    _shipingAddress.line1 = line1 ?? throw new ...
    _shippingAddress.line2 = line2;
    _shippingAddress.city = city ?? throw new ...
    _shippingAddress.state = (IsValid(state) ? state : throw new …);
}
```

<span data-ttu-id="370de-128">如果状态的值无效，第一个地址行和城市已更改。</span><span class="sxs-lookup"><span data-stu-id="370de-128">If the value of the state is invalid, the first address line and the city have already been changed.</span></span> <span data-ttu-id="370de-129">可能使的地址无效。</span><span class="sxs-lookup"><span data-stu-id="370de-129">That might make the address invalid.</span></span>

<span data-ttu-id="370de-130">引发异常以确保在创建后的实体是有效的实体的构造函数中，可以使用类似的方法。</span><span class="sxs-lookup"><span data-stu-id="370de-130">A similar approach can be used in the entity’s constructor, raising an exception to make sure that the entity is valid once it is created.</span></span>

### <a name="using-validation-attributes-in-the-model-based-on-data-annotations"></a><span data-ttu-id="370de-131">在基于数据批注的模型中使用验证属性</span><span class="sxs-lookup"><span data-stu-id="370de-131">Using validation attributes in the model based on data annotations</span></span>

<span data-ttu-id="370de-132">另一种方法是使用基于数据注释的验证属性。</span><span class="sxs-lookup"><span data-stu-id="370de-132">Another approach is to use validation attributes based on data annotations.</span></span> <span data-ttu-id="370de-133">验证特性提供了如何配置从概念上讲到验证对数据库表中的字段类似的模型验证。</span><span class="sxs-lookup"><span data-stu-id="370de-133">Validation attributes provide a way to configure model validation, which is similar conceptually to validation on fields in database tables.</span></span> <span data-ttu-id="370de-134">这包括如将数据类型或必填的字段分配的约束。</span><span class="sxs-lookup"><span data-stu-id="370de-134">This includes constraints such as assigning data types or required fields.</span></span> <span data-ttu-id="370de-135">其他类型的验证包括将模式应用于数据以强制实施业务规则，如信用卡号码，电话号码或电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="370de-135">Other types of validation include applying patterns to data to enforce business rules, such as a credit card number, phone number, or email address.</span></span> <span data-ttu-id="370de-136">验证特性，可以方便以强制执行需求。</span><span class="sxs-lookup"><span data-stu-id="370de-136">Validation attributes make it easy to enforce requirements.</span></span>

<span data-ttu-id="370de-137">但是，下面的代码中所示，此方法可能太彻底在 DDD 模型中，因为它将依赖关系 ModelState.IsValid 从 Microsoft.AspNetCore.Mvc.ModelState，必须从你的 MVC 控制器调用。</span><span class="sxs-lookup"><span data-stu-id="370de-137">However, as shown in the following code, this approach might be too intrusive in a DDD model, because it takes a dependency on ModelState.IsValid from Microsoft.AspNetCore.Mvc.ModelState, which you must call from your MVC controllers.</span></span> <span data-ttu-id="370de-138">在正在调用的每个控制器操作之前，将执行模型验证和控制器方法负责检查的结果调用 ModelState.IsValid 并相应地作出反应。</span><span class="sxs-lookup"><span data-stu-id="370de-138">The model validation occurs prior to each controller action being invoked, and it is the controller method’s responsibility to inspect the result of calling ModelState.IsValid and react appropriately.</span></span> <span data-ttu-id="370de-139">若要使用它的决策取决于如何紧密耦合你想要与该基础结构的模型。</span><span class="sxs-lookup"><span data-stu-id="370de-139">The decision to use it depends on how tightly coupled you want the model to be with that infrastructure.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
// Other using statements ...
// Entity is a custom base class which has the ID
public class Product : Entity
{
    [Required]
    [StringLength(100)]
    public string Title { get; private set; }

    [Required]
    [Range(0, 999.99)]
    public decimal Price { get; private set; }

    [Required]
    [VintageProduct(1970)]
    [DataType(DataType.Date)]
    public DateTime ReleaseDate { get; private set; }

    [Required]
    [StringLength(1000)]
    public string Description { get; private set; }

    // Constructor...
    // Additional methods for entity logic and constructor...
}
```

<span data-ttu-id="370de-140">但是，从 DDD 的角度来看，域模型最好保存精益异常使用在实体的行为方法中，或通过实现的规范和通知模式，以强制执行验证规则。</span><span class="sxs-lookup"><span data-stu-id="370de-140">However, from a DDD point of view, the domain model is best kept lean with the use of exceptions in your entity’s behavior methods, or by implementing the Specification and Notification patterns to enforce validation rules.</span></span> <span data-ttu-id="370de-141">验证框架，如在 ASP.NET 核心数据注释或任何其他验证框架如 FluentValidation 执行需要调用应用程序框架。</span><span class="sxs-lookup"><span data-stu-id="370de-141">Validation frameworks like data annotations in ASP.NET Core or any other validation frameworks like FluentValidation carry a requirement to invoke the application framework.</span></span> <span data-ttu-id="370de-142">例如，当调用 ModelState.IsValid 方法中数据注释时，你需要调用 ASP.NET 控制器。</span><span class="sxs-lookup"><span data-stu-id="370de-142">For example, when calling the ModelState.IsValid method in data annotations, you need to invoke ASP.NET controllers.</span></span>

<span data-ttu-id="370de-143">它很有意义，要在将接受输入，以允许 UI 层中的模型验证的 ViewModel 类 （而不是域实体） 的应用程序层使用数据注释。</span><span class="sxs-lookup"><span data-stu-id="370de-143">It can make sense to use data annotations at the application layer in ViewModel classes (instead of domain entities) that will accept input, to allow for model validation within the UI layer.</span></span> <span data-ttu-id="370de-144">但是，这不应在验证域模型中排除。</span><span class="sxs-lookup"><span data-stu-id="370de-144">However, this should not be done at the exclusion of validation within the domain model.</span></span>

### <a name="validating-entities-by-implementing-the-specification-pattern-and-the-notification-pattern"></a><span data-ttu-id="370de-145">通过实现规范模式和通知模式验证实体</span><span class="sxs-lookup"><span data-stu-id="370de-145">Validating entities by implementing the Specification pattern and the Notification pattern</span></span>

<span data-ttu-id="370de-146">最后，在域模型中实现验证的更复杂方法是通过结合使用通知模式中，实现规范模式中更高版本列出的其他资源的某些所述。</span><span class="sxs-lookup"><span data-stu-id="370de-146">Finally, a more elaborate approach to implementing validations in the domain model is by implementing the Specification pattern in conjunction with the Notification pattern, as explained in some of the additional resources listed later.</span></span>

<span data-ttu-id="370de-147">值得一提的是，你还可以使用这些模式的其中一个 — 例如，验证手动控制语句中，但它使用的通知模式以堆栈并返回验证错误的列表。</span><span class="sxs-lookup"><span data-stu-id="370de-147">It is worth mentioning that you can also use just one of those patterns—for example, validating manually with control statements, but using the Notification pattern to stack and return a list of validation errors.</span></span>

### <a name="using-deferred-validation-in-the-domain"></a><span data-ttu-id="370de-148">使用的域中的延迟的验证</span><span class="sxs-lookup"><span data-stu-id="370de-148">Using deferred validation in the domain</span></span>

<span data-ttu-id="370de-149">有各种方法来处理域中的延迟验证。</span><span class="sxs-lookup"><span data-stu-id="370de-149">There are various approaches to deal with deferred validations in the domain.</span></span> <span data-ttu-id="370de-150">在书籍[Implementing Domain-Driven 设计](https://www.amazon.com/Implementing-Domain-Driven-Design-Vaughn-Vernon/dp/0321834577)，Vaughn Vernon 讨论这些部分中的验证。</span><span class="sxs-lookup"><span data-stu-id="370de-150">In his book [Implementing Domain-Driven Design](https://www.amazon.com/Implementing-Domain-Driven-Design-Vaughn-Vernon/dp/0321834577), Vaughn Vernon discusses these in the section on validation.</span></span>

### <a name="two-step-validation"></a><span data-ttu-id="370de-151">双重验证</span><span class="sxs-lookup"><span data-stu-id="370de-151">Two-step validation</span></span>

<span data-ttu-id="370de-152">此外考虑双重验证。</span><span class="sxs-lookup"><span data-stu-id="370de-152">Also consider two-step validation.</span></span> <span data-ttu-id="370de-153">使用命令数据传输对象 (Dto) 和域级别验证在你的实体上的字段级别验证。</span><span class="sxs-lookup"><span data-stu-id="370de-153">Use field-level validation on your command Data Transfer Objects (DTOs) and domain-level validation inside your entities.</span></span> <span data-ttu-id="370de-154">通过返回结果对象改为异常以使其更轻松地处理验证错误，可以执行此操作。</span><span class="sxs-lookup"><span data-stu-id="370de-154">You can do this by returning a result object instead exceptions in order to make it easier to deal with the validation errors.</span></span>

<span data-ttu-id="370de-155">使用数据注释字段验证，例如，你不重复的验证定义。</span><span class="sxs-lookup"><span data-stu-id="370de-155">Using field validation with data annotations, for example, you do not duplicate the validation definition.</span></span> <span data-ttu-id="370de-156">执行，但是，可以是服务器端和客户端在 Dto 的情况下 (命令和 Viewmodel 实例)。</span><span class="sxs-lookup"><span data-stu-id="370de-156">The execution, though, can be both server-side and client-side in the case of DTOs (commands and ViewModels, for instance).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="370de-157">其他资源</span><span class="sxs-lookup"><span data-stu-id="370de-157">Additional resources</span></span>

-   <span data-ttu-id="370de-158">**Rachel Appel。ASP.NET 核心 mvc 模型验证简介**
    [*https://docs.microsoft.com/aspnet/core/mvc/models/validation*](https://docs.microsoft.com/aspnet/core/mvc/models/validation)</span><span class="sxs-lookup"><span data-stu-id="370de-158">**Rachel Appel. Introduction to model validation in ASP.NET Core MVC**
[*https://docs.microsoft.com/aspnet/core/mvc/models/validation*](https://docs.microsoft.com/aspnet/core/mvc/models/validation)</span></span>

-   <span data-ttu-id="370de-159">**Rick Anderson。添加验证**
    [*https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/validation*](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/validation)</span><span class="sxs-lookup"><span data-stu-id="370de-159">**Rick Anderson. Adding validation**
[*https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/validation*](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/validation)</span></span>

-   <span data-ttu-id="370de-160">**Martin Fowler。替换在验证中引发异常通知**
    [*https://martinfowler.com/articles/replaceThrowWithNotification.html*](https://martinfowler.com/articles/replaceThrowWithNotification.html)</span><span class="sxs-lookup"><span data-stu-id="370de-160">**Martin Fowler. Replacing Throwing Exceptions with Notification in Validations**
[*https://martinfowler.com/articles/replaceThrowWithNotification.html*](https://martinfowler.com/articles/replaceThrowWithNotification.html)</span></span>

-   <span data-ttu-id="370de-161">**规范和通知模式**
    [*https://www.codeproject.com/Tips/790758/Specification-and-Notification-Patterns*](https://www.codeproject.com/Tips/790758/Specification-and-Notification-Patterns)</span><span class="sxs-lookup"><span data-stu-id="370de-161">**Specification and Notification Patterns**
[*https://www.codeproject.com/Tips/790758/Specification-and-Notification-Patterns*](https://www.codeproject.com/Tips/790758/Specification-and-Notification-Patterns)</span></span>

-   <span data-ttu-id="370de-162">**列弗 Gorodinski。域驱动设计 (DDD) 中的验证**
    [*http://gorodinski.com/blog/2012/05/19/validation-in-domain-driven-design-ddd/*](http://gorodinski.com/blog/2012/05/19/validation-in-domain-driven-design-ddd/)</span><span class="sxs-lookup"><span data-stu-id="370de-162">**Lev Gorodinski. Validation in Domain-Driven Design (DDD)**
[*http://gorodinski.com/blog/2012/05/19/validation-in-domain-driven-design-ddd/*](http://gorodinski.com/blog/2012/05/19/validation-in-domain-driven-design-ddd/)</span></span>

-   <span data-ttu-id="370de-163">**Colin 上插座。域模型验证**
    [*http://colinjack.blogspot.com/2008/03/domain-model-validation.html*](http://colinjack.blogspot.com/2008/03/domain-model-validation.html)</span><span class="sxs-lookup"><span data-stu-id="370de-163">**Colin Jack. Domain Model Validation**
[*http://colinjack.blogspot.com/2008/03/domain-model-validation.html*](http://colinjack.blogspot.com/2008/03/domain-model-validation.html)</span></span>

-   <span data-ttu-id="370de-164">**Jimmy Bogard。DDD 生活中的验证**
    [*https://lostechies.com/jimmybogard/2009/02/15/validation-in-a-ddd-world/*](https://lostechies.com/jimmybogard/2009/02/15/validation-in-a-ddd-world/)</span><span class="sxs-lookup"><span data-stu-id="370de-164">**Jimmy Bogard. Validation in a DDD world**
[*https://lostechies.com/jimmybogard/2009/02/15/validation-in-a-ddd-world/*](https://lostechies.com/jimmybogard/2009/02/15/validation-in-a-ddd-world/)</span></span>


>[!div class="step-by-step"]
<span data-ttu-id="370de-165">[以前](枚举-类-转移-枚举-types.md) [下一步] (客户端端 validation.md)</span><span class="sxs-lookup"><span data-stu-id="370de-165">[Previous] (enumeration-classes-over-enum-types.md) [Next] (client-side-validation.md)</span></span>