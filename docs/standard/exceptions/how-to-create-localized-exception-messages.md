---
title: 如何：使用本地化的异常消息创建用户定义的异常
description: 了解如何使用本地化的异常消息创建用户定义的异常
author: Youssef1313
ms.author: ronpet
ms.date: 09/13/2019
ms.openlocfilehash: 1e5ef5658e4cb71d0689a1786494eaec57d4e7fb
ms.sourcegitcommit: 8b8dd14dde727026fd0b6ead1ec1df2e9d747a48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71332961"
---
# <a name="how-to-create-user-defined-exceptions-with-localized-exception-messages"></a><span data-ttu-id="62693-103">如何：使用本地化的异常消息创建用户定义的异常</span><span class="sxs-lookup"><span data-stu-id="62693-103">How to: create user-defined exceptions with localized exception messages</span></span>

<span data-ttu-id="62693-104">在本文中，你将了解如何通过使用附属程序集的本地化异常消息创建从 <xref:System.Exception> 基类继承的用户定义异常。</span><span class="sxs-lookup"><span data-stu-id="62693-104">In this article, you will learn how to create user-defined exceptions that are inherited from the base <xref:System.Exception> class with localized exception messages using satellite assemblies.</span></span>

## <a name="create-custom-exceptions"></a><span data-ttu-id="62693-105">创建自定义异常</span><span class="sxs-lookup"><span data-stu-id="62693-105">Create custom exceptions</span></span>

<span data-ttu-id="62693-106">.NET 包含许多你可以使用的不同异常。</span><span class="sxs-lookup"><span data-stu-id="62693-106">.NET contains many different exceptions that you can use.</span></span> <span data-ttu-id="62693-107">但是，在某些情况下，如果它们都无法满足你的需要，则可以创建自己的自定义异常。</span><span class="sxs-lookup"><span data-stu-id="62693-107">However, in some cases when none of them meets your needs, you can create your own custom exceptions.</span></span>

<span data-ttu-id="62693-108">假设要创建一个 `StudentNotFoundException`，其中包含 `StudentName` 属性。</span><span class="sxs-lookup"><span data-stu-id="62693-108">Let's assume you want to create a `StudentNotFoundException` that contains a `StudentName` property.</span></span>
<span data-ttu-id="62693-109">若要创建自定义异常，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="62693-109">To create a custom exception, follow these steps:</span></span>

1. <span data-ttu-id="62693-110">创建一个从 <xref:System.Exception> 继承的可序列化类。</span><span class="sxs-lookup"><span data-stu-id="62693-110">Create a serializable class that inherits from <xref:System.Exception>.</span></span> <span data-ttu-id="62693-111">类名称应以“Exception”结尾：</span><span class="sxs-lookup"><span data-stu-id="62693-111">The class name should end in "Exception":</span></span>

    ```csharp
    [Serializable]
    public class StudentNotFoundException : Exception { }
    ```

1. <span data-ttu-id="62693-112">添加默认构造函数：</span><span class="sxs-lookup"><span data-stu-id="62693-112">Add the default constructors:</span></span>

    ```csharp
    [Serializable]
    public class StudentNotFoundException : Exception
    {
        public StudentNotFoundException() { }

        public StudentNotFoundException(string message)
            : base(message) { }

        public StudentNotFoundException(string message, Exception inner)
            : base(message, inner) { }
    }
    ```

1. <span data-ttu-id="62693-113">定义任何其他属性和构造函数：</span><span class="sxs-lookup"><span data-stu-id="62693-113">Define any additional properties and constructors:</span></span>

    ```csharp
    [Serializable]
    public class StudentNotFoundException : Exception
    {
        public string StudentName { get; }

        public StudentNotFoundException() { }

        public StudentNotFoundException(string message)
            : base(message) { }

        public StudentNotFoundException(string message, Exception inner)
            : base(message, inner) { }

        public StudentNotFoundException(string message, string studentName)
            : this(message)
        {
            StudentName = studentName;
        }
    }
    ```

## <a name="create-localized-exception-messages"></a><span data-ttu-id="62693-114">创建本地化异常消息</span><span class="sxs-lookup"><span data-stu-id="62693-114">Create localized exception messages</span></span>

<span data-ttu-id="62693-115">你已创建一个自定义异常，可以使用如下所示的代码在任何位置将其抛出：</span><span class="sxs-lookup"><span data-stu-id="62693-115">You have created a custom exception, and you can throw it anywhere with code like the following:</span></span>

```csharp
throw new StudentNotFoundException("The student cannot be found.", "John");
```

<span data-ttu-id="62693-116">上一行的问题是“找不到学生。”</span><span class="sxs-lookup"><span data-stu-id="62693-116">The problem with the previous line is that "The student cannot be found."</span></span> <span data-ttu-id="62693-117">只是一个常量字符串。</span><span class="sxs-lookup"><span data-stu-id="62693-117">is just a constant string.</span></span> <span data-ttu-id="62693-118">在本地化应用程序中，你需要根据用户区域性使用不同的消息。</span><span class="sxs-lookup"><span data-stu-id="62693-118">In a localized application, you want to have different messages depending on user culture.</span></span>
<span data-ttu-id="62693-119">[附属程序集](../../framework/resources/creating-satellite-assemblies-for-desktop-apps.md)是执行此操作的好方法。</span><span class="sxs-lookup"><span data-stu-id="62693-119">[Satellite Assemblies](../../framework/resources/creating-satellite-assemblies-for-desktop-apps.md) are a good way to do that.</span></span> <span data-ttu-id="62693-120">附属程序集是一个 .dll，其中包含特定语言的资源。</span><span class="sxs-lookup"><span data-stu-id="62693-120">A satellite assembly is a .dll that contains resources for a specific language.</span></span> <span data-ttu-id="62693-121">当你在运行时请求特定资源时，CLR 将根据用户区域性查找该资源。</span><span class="sxs-lookup"><span data-stu-id="62693-121">When you ask for a specific resources at run time, the CLR finds that resource depending on user culture.</span></span> <span data-ttu-id="62693-122">如果找不到该区域性对应的附属程序集，则使用默认区域性的资源。</span><span class="sxs-lookup"><span data-stu-id="62693-122">If no satellite assembly is found for that culture, the resources of the default culture are used.</span></span>

<span data-ttu-id="62693-123">创建本地化异常消息：</span><span class="sxs-lookup"><span data-stu-id="62693-123">To create the localized exception messages:</span></span>

1. <span data-ttu-id="62693-124">创建一个名为“Resources”  的新文件夹来保存资源文件。</span><span class="sxs-lookup"><span data-stu-id="62693-124">Create a new folder named *Resources* to hold the resource files.</span></span>
1. <span data-ttu-id="62693-125">向其中添加新的资源文件。</span><span class="sxs-lookup"><span data-stu-id="62693-125">Add a new resource file to it.</span></span> <span data-ttu-id="62693-126">若要在 Visual Studio 中执行此操作，请在“解决方案资源管理器”  中右键单击该文件夹，然后选择“添加”   -> “新项”   -> “资源文件”  。</span><span class="sxs-lookup"><span data-stu-id="62693-126">To do that in Visual Studio, right-click the folder in **Solution Explorer**, and select **Add** -> **New Item** -> **Resources File**.</span></span> <span data-ttu-id="62693-127">将该文件命名为“ExceptionMessages.resx”  。</span><span class="sxs-lookup"><span data-stu-id="62693-127">Name the file *ExceptionMessages.resx*.</span></span> <span data-ttu-id="62693-128">这是默认的资源文件。</span><span class="sxs-lookup"><span data-stu-id="62693-128">This is the default resources file.</span></span>
1. <span data-ttu-id="62693-129">为异常消息添加名称/值对，如下图所示：![将资源添加到默认区域性](media/add-resources-to-default-culture.jpg)</span><span class="sxs-lookup"><span data-stu-id="62693-129">Add a name/value pair for your exception message, like the following image shows: ![Add resources to the default culture](media/add-resources-to-default-culture.jpg)</span></span>
1. <span data-ttu-id="62693-130">为法语添加新的资源文件。</span><span class="sxs-lookup"><span data-stu-id="62693-130">Add a new resource file for French.</span></span> <span data-ttu-id="62693-131">将其命名为“ExceptionMessages.fr-FR.resx”  。</span><span class="sxs-lookup"><span data-stu-id="62693-131">Name it *ExceptionMessages.fr-FR.resx*.</span></span>
1. <span data-ttu-id="62693-132">再次为异常消息添加名称/值对，但使用法语值：![将资源添加到 fr-FR 区域性](media/add-resources-to-fr-culture.jpg)</span><span class="sxs-lookup"><span data-stu-id="62693-132">Add a name/value pair for the exception message again, but with a French value: ![Add resources to the fr-FR culture](media/add-resources-to-fr-culture.jpg)</span></span>
1. <span data-ttu-id="62693-133">生成项目后，生成的输出文件夹应包含 fr-FR  文件夹，其中具有 .dll  文件，它是附属程序集。</span><span class="sxs-lookup"><span data-stu-id="62693-133">After you build the project, the build output folder should contain the *fr-FR* folder with a *.dll* file, which is the satellite assembly.</span></span>
1. <span data-ttu-id="62693-134">使用如下所示代码抛出异常：</span><span class="sxs-lookup"><span data-stu-id="62693-134">You throw the exception with code like the following:</span></span>

    ```csharp
    var resourceManager = new ResourceManager("FULLY_QIALIFIED_NAME_OF_RESOURCE_FILE", Assembly.GetExecutingAssembly());
    throw new StudentNotFoundException(resourceManager.GetString("StudentNotFound"), "John");
    ```

  > [!NOTE]
  > <span data-ttu-id="62693-135">如果项目名称为 `TestProject`，并且资源文件 ExceptionMessages.resx  位于项目的 Resources  文件夹中，则资源文件的完全限定名称为 `TestProject.Resources.ExceptionMessages`。</span><span class="sxs-lookup"><span data-stu-id="62693-135">If the project name is `TestProject` and the resource file *ExceptionMessages.resx* resides in the *Resources* folder of the project, the fully qualified name of the resource file is `TestProject.Resources.ExceptionMessages`.</span></span>

## <a name="see-also"></a><span data-ttu-id="62693-136">请参阅</span><span class="sxs-lookup"><span data-stu-id="62693-136">See also</span></span>

- [<span data-ttu-id="62693-137">如何创建用户定义的异常</span><span class="sxs-lookup"><span data-stu-id="62693-137">How to create user-defined exceptions</span></span>](how-to-create-user-defined-exceptions.md)
- [<span data-ttu-id="62693-138">创建桌面应用程序的附属程序集</span><span class="sxs-lookup"><span data-stu-id="62693-138">Creating Satellite Assemblies for Desktop Apps</span></span>](../../framework/resources/creating-satellite-assemblies-for-desktop-apps.md)
- [<span data-ttu-id="62693-139">base（C# 参考）</span><span class="sxs-lookup"><span data-stu-id="62693-139">base (C# Reference)</span></span>](../../csharp/language-reference/keywords/base.md)
- [<span data-ttu-id="62693-140">this（C# 参考）</span><span class="sxs-lookup"><span data-stu-id="62693-140">this (C# Reference)</span></span>](../../csharp/language-reference/keywords/this.md)