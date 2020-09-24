---
title: 调试 Linux 转储
description: 在本文中，你将了解如何从 Linux 环境收集和分析转储。
ms.date: 08/27/2020
ms.openlocfilehash: d62295e165f56e32ef73ab628ca9ebd77a4435d1
ms.sourcegitcommit: 43d5aca3fda42bad8843f6c4e72f6bd52daa55f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89598288"
---
# <a name="debug-linux-dumps"></a><span data-ttu-id="a53bf-103">调试 Linux 转储</span><span class="sxs-lookup"><span data-stu-id="a53bf-103">Debug Linux dumps</span></span>

<span data-ttu-id="a53bf-104">**本文适用于：** ✔️ .NET Core 3.0 SDK 及更高版本</span><span class="sxs-lookup"><span data-stu-id="a53bf-104">**This article applies to: ✔️** .NET Core 3.0 SDK and later versions</span></span>

## <a name="collect-dumps-on-linux"></a><span data-ttu-id="a53bf-105">在 Linux 上收集转储</span><span class="sxs-lookup"><span data-stu-id="a53bf-105">Collect dumps on Linux</span></span>

<span data-ttu-id="a53bf-106">在 Linux 上收集转储的两种建议方法是使用 [`dotnet-dump`](dotnet-dump.md) 或 [`createdump`](https://github.com/dotnet/runtime/blob/master/docs/design/coreclr/botr/xplat-minidump-generation.md) 工具。</span><span class="sxs-lookup"><span data-stu-id="a53bf-106">The two recommended ways of collecting dumps on Linux are the [`dotnet-dump`](dotnet-dump.md) or [`createdump`](https://github.com/dotnet/runtime/blob/master/docs/design/coreclr/botr/xplat-minidump-generation.md) tools.</span></span>

### <a name="managed-dumps-with-dotnet-dump"></a><span data-ttu-id="a53bf-107">使用 `dotnet-dump` 的托管转储</span><span class="sxs-lookup"><span data-stu-id="a53bf-107">Managed dumps with `dotnet-dump`</span></span>

<span data-ttu-id="a53bf-108">[`dotnet-dump`](dotnet-dump.md) 工具简单易用，并且不依赖于任何本机调试器。</span><span class="sxs-lookup"><span data-stu-id="a53bf-108">The [`dotnet-dump`](dotnet-dump.md) tool is simple to use, and does not have a dependency on any native debuggers.</span></span> <span data-ttu-id="a53bf-109">`dotnet-dump` 可用于各种 Linux 平台（例如 Alpine 或 ARM32/ARM64），在这些平台上，传统调试工具可能不适用。</span><span class="sxs-lookup"><span data-stu-id="a53bf-109">`dotnet-dump` works on a wide variety of Linux platforms (like Alpine or ARM32/ARM64) where traditional debugging tools may not be available.</span></span> <span data-ttu-id="a53bf-110">但是，`dotnet-dump` 只捕获托管状态，因此不能将其用于调试本机代码中的问题。</span><span class="sxs-lookup"><span data-stu-id="a53bf-110">However, `dotnet-dump` only captures managed state so it can't be used for debugging issues in native code.</span></span> <span data-ttu-id="a53bf-111">`dotnet-dump` 收集的转储在具有创建转储的相同 OS 和体系结构的环境中进行分析。</span><span class="sxs-lookup"><span data-stu-id="a53bf-111">Dumps collected by `dotnet-dump` are analyzed in an environment with the the same OS and architecture the dump was created on.</span></span> <span data-ttu-id="a53bf-112">[`dotnet-gcdump`](dotnet-gcdump.md) 工具可用作仅捕获 GC 堆信息但生成可在 Windows 上分析的转储的替代方法。</span><span class="sxs-lookup"><span data-stu-id="a53bf-112">The [`dotnet-gcdump`](dotnet-gcdump.md) tool can be used as an alternative that only captures GC heap information but produces dumps that can be analyzed on Windows.</span></span>

### <a name="core-dumps-with-createdump"></a><span data-ttu-id="a53bf-113">使用 `createdump` 的核心转储</span><span class="sxs-lookup"><span data-stu-id="a53bf-113">Core dumps with `createdump`</span></span>

<span data-ttu-id="a53bf-114">作为创建仅托管转储的 `dotnet-dump` 的替代方法，[`createdump`](https://github.com/dotnet/runtime/blob/master/docs/design/coreclr/botr/xplat-minidump-generation.md) 是推荐的工具，用于在包含本机和托管信息的 Linux 上创建核心转储。</span><span class="sxs-lookup"><span data-stu-id="a53bf-114">Alternative to `dotnet-dump` which creates managed-only dumps, [`createdump`](https://github.com/dotnet/runtime/blob/master/docs/design/coreclr/botr/xplat-minidump-generation.md) is the recommended tool for creating core dumps on Linux containing both native and managed information.</span></span> <span data-ttu-id="a53bf-115">其他工具（例如 gdb 或 gcore）也可以用于创建核心转储，但可能会缺失托管调试所需的状态，从而导致在分析过程中出现“未知”类型或函数名称。</span><span class="sxs-lookup"><span data-stu-id="a53bf-115">Other tools like gdb or gcore can also be used to create core dumps but may miss state needed for managed debugging, resulting in "UNKNOWN" type or function names during analysis.</span></span>

<span data-ttu-id="a53bf-116">`createdump` 工具与 .NET Core 运行时一起安装，可以在 libcoreclr.so（通常位于“/usr/share/dotnet/shared/Microsoft.NETCore.App/[version]”中）旁边找到。</span><span class="sxs-lookup"><span data-stu-id="a53bf-116">The `createdump` tools is installed with the .NET Core runtime and can be found next to libcoreclr.so (typically in "/usr/share/dotnet/shared/Microsoft.NETCore.App/[version]").</span></span> <span data-ttu-id="a53bf-117">该工具采用进程 ID 将转储作为其主要参数收集，还可以采用可选参数来指定要收集的转储类型（带有堆的小型转储是默认类型）。</span><span class="sxs-lookup"><span data-stu-id="a53bf-117">The tool takes a process ID to collect a dump from as its primary argument and can also take optional parameters specifying what kind of dump to collect (a minidump with heap is the default).</span></span> <span data-ttu-id="a53bf-118">选项包括：</span><span class="sxs-lookup"><span data-stu-id="a53bf-118">Options include:</span></span>

- **`<input-filename>`**

  <span data-ttu-id="a53bf-119">要转换的输入跟踪文件。</span><span class="sxs-lookup"><span data-stu-id="a53bf-119">Input trace file to be converted.</span></span> <span data-ttu-id="a53bf-120">默认为 trace.nettrace  。</span><span class="sxs-lookup"><span data-stu-id="a53bf-120">Defaults to *trace.nettrace*.</span></span>

### <a name="options"></a><span data-ttu-id="a53bf-121">选项</span><span class="sxs-lookup"><span data-stu-id="a53bf-121">Options</span></span>

- **`-f|--name <output-filename>`**

  <span data-ttu-id="a53bf-122">要将转储写入到其中的文件。</span><span class="sxs-lookup"><span data-stu-id="a53bf-122">The file to write the dump to.</span></span> <span data-ttu-id="a53bf-123">默认值为“/tmp/coredump.%p”，其中 %p 是目标进程的进程 ID。</span><span class="sxs-lookup"><span data-stu-id="a53bf-123">Default is '/tmp/coredump.%p' where %p is the process ID of the target process.</span></span>

- **`-n|--normal`**

  <span data-ttu-id="a53bf-124">创建小型转储。</span><span class="sxs-lookup"><span data-stu-id="a53bf-124">Create a minidump.</span></span>

- **`-h|--withheap`**

  <span data-ttu-id="a53bf-125">创建带有堆的小型转储（默认）。</span><span class="sxs-lookup"><span data-stu-id="a53bf-125">Create a minidump with heap (default).</span></span>

- **`-t|--triage`**

  <span data-ttu-id="a53bf-126">创建会审小型转储。</span><span class="sxs-lookup"><span data-stu-id="a53bf-126">Create a triage minidump.</span></span>

- **`-u|--full`**

  <span data-ttu-id="a53bf-127">创建完整的核心转储。</span><span class="sxs-lookup"><span data-stu-id="a53bf-127">Create a full core dump.</span></span>

- **`-d|--diag`**

  <span data-ttu-id="a53bf-128">启用诊断消息。</span><span class="sxs-lookup"><span data-stu-id="a53bf-128">Enable diagnostic messages.</span></span>

<span data-ttu-id="a53bf-129">请注意，收集核心转储需要 `SYS_PTRACE` 功能，或者需要 `createdump` 与 sudo 或 su 一起运行。</span><span class="sxs-lookup"><span data-stu-id="a53bf-129">Note that collecting core dumps requires either the `SYS_PTRACE` capability or that `createdump` be run with sudo or su.</span></span>

## <a name="analyze-dumps-on-linux"></a><span data-ttu-id="a53bf-130">在 Linux 上分析转储</span><span class="sxs-lookup"><span data-stu-id="a53bf-130">Analyze dumps on Linux</span></span>

<span data-ttu-id="a53bf-131">通过使用 `dotnet-dump analyze` 命令，可以借助 `dotnet-dump` 工具分析使用 `dotnet-dump` 收集的托管转储和使用 `createdump` 收集的核心转储。</span><span class="sxs-lookup"><span data-stu-id="a53bf-131">Both managed dumps collected with `dotnet-dump` and core dumps collected with `createdump` can be analyzed with the `dotnet-dump` tool using the `dotnet-dump analyze` command.</span></span> <span data-ttu-id="a53bf-132">`dotnet dump` 要求分析转储的环境与捕获转储的环境具有相同的 OS 和体系结构。</span><span class="sxs-lookup"><span data-stu-id="a53bf-132">The `dotnet dump` requires that the environment analyzing the dump has the same OS and architecture as the environment the dump was captured in.</span></span>

<span data-ttu-id="a53bf-133">另外，[LLDB](https://lldb.llvm.org/) 可用于分析 Linux 上的核心转储，这允许分析托管帧和本机帧。</span><span class="sxs-lookup"><span data-stu-id="a53bf-133">Alternatively, [LLDB](https://lldb.llvm.org/) can be used to analyze core dumps on Linux, which allows analysis of both managed and native frames.</span></span> <span data-ttu-id="a53bf-134">LLDB 使用 SOS 扩展调试托管代码。</span><span class="sxs-lookup"><span data-stu-id="a53bf-134">LLDB uses the SOS extension to debug managed code.</span></span> <span data-ttu-id="a53bf-135">[`dotnet-sos`](dotnet-sos.md) CLI 工具可用于安装 SOS，它具有[许多用于调试托管代码的有用命令](https://github.com/dotnet/diagnostics/blob/master/documentation/sos-debugging-extension.md)。</span><span class="sxs-lookup"><span data-stu-id="a53bf-135">The [`dotnet-sos`](dotnet-sos.md) CLI tool can be used to install SOS which has [many useful commands](https://github.com/dotnet/diagnostics/blob/master/documentation/sos-debugging-extension.md) for debugging managed code.</span></span> <span data-ttu-id="a53bf-136">若要分析 .NET Core 转储、LLDB 和 SOS，要求在创建转储的环境中使用以下 .NET Core 二进制文件：</span><span class="sxs-lookup"><span data-stu-id="a53bf-136">In order to analyze .NET Core dumps, LLDB and SOS require the following .NET Core binaries from the environment the dump was created in:</span></span>

1. <span data-ttu-id="a53bf-137">libmscordaccore.so</span><span class="sxs-lookup"><span data-stu-id="a53bf-137">libmscordaccore.so</span></span>
2. <span data-ttu-id="a53bf-138">libcoreclr.so</span><span class="sxs-lookup"><span data-stu-id="a53bf-138">libcoreclr.so</span></span>
3. <span data-ttu-id="a53bf-139">dotnet（用于启动应用的主机）</span><span class="sxs-lookup"><span data-stu-id="a53bf-139">dotnet (the host used to launch the app)</span></span>

<span data-ttu-id="a53bf-140">在大多数情况下，可以使用 [`dotnet-symbol`](dotnet-symbol.md) 工具下载这些二进制文件。</span><span class="sxs-lookup"><span data-stu-id="a53bf-140">In most cases, these binaries can be downloaded using the [`dotnet-symbol`](dotnet-symbol.md) tool.</span></span> <span data-ttu-id="a53bf-141">如果无法使用 `dotnet-symbol` 下载所需的二进制文件（例如，如果正在使用从源构建的 .NET Core 专用版本），则可能需要从创建转储的环境复制上面列出的文件。</span><span class="sxs-lookup"><span data-stu-id="a53bf-141">If the necessary binaries can't be downloaded with `dotnet-symbol` (for example, if a private version of .NET Core built from source was in use), it may be necessary to copy the files listed above from the environment the dump was created in.</span></span> <span data-ttu-id="a53bf-142">如果文件不位于转储文件旁边，则可以使用 LLDB/SOS 命令 `setclrpath <path>` 设置应从中加载文件的路径，并使用 `setsymbolserver -directory <path>` 设置用于查找符号文件的路径。</span><span class="sxs-lookup"><span data-stu-id="a53bf-142">If the files aren't located next to the dump file, you can use the LLDB/SOS command `setclrpath <path>` to set the path they should be loaded from and `setsymbolserver -directory <path>` to set the path to look in for symbol files.</span></span>

<span data-ttu-id="a53bf-143">所需文件可用后，就可以通过将 dotnet 主机指定为要调试的可执行文件来将转储加载到 LLDB 中：</span><span class="sxs-lookup"><span data-stu-id="a53bf-143">Once the necessary files are available, the dump can be loaded in LLDB by specifying the dotnet host as the executable to debug:</span></span>

```console
lldb --core <dump-file> <host-program>
```

<span data-ttu-id="a53bf-144">在上面的命令行中，`<dump-file>` 是要分析的转储路径，而 `<host-program>` 是已启动 .NET Core 应用程序的本机程序。</span><span class="sxs-lookup"><span data-stu-id="a53bf-144">In the above command line, `<dump-file>` is the path of the dump to analyze and `<host-program>` is the native program that started the .NET Core application.</span></span> <span data-ttu-id="a53bf-145">除非应用是独立应用，否则通常为 `dotnet` 二进制文件，在本例中，它是不包含 dll 扩展名的应用程序的名称。</span><span class="sxs-lookup"><span data-stu-id="a53bf-145">This is typically the `dotnet` binary unless the app is self-contained, in which case it is the name of the application without the dll extension.</span></span>

<span data-ttu-id="a53bf-146">LLDB 启动后，可能需要使用 `setsymbolserver` 命令指向正确的符号位置（`setsymbolserver -ms` 用于使用 Microsoft 的符号服务器或 `setsymbolserver -directory <path>` 用于指定本地路径）。</span><span class="sxs-lookup"><span data-stu-id="a53bf-146">Once LLDB starts, it may be necessary to use the `setsymbolserver` command to point at the correct symbol location (`setsymbolserver -ms` to use Microsoft's symbol server or `setsymbolserver -directory <path>` to specify a local path).</span></span> <span data-ttu-id="a53bf-147">可以通过运行 `loadsymbols` 来加载本机符号。</span><span class="sxs-lookup"><span data-stu-id="a53bf-147">Native symbols can be loaded by running `loadsymbols`.</span></span> <span data-ttu-id="a53bf-148">此时，[SOS 命令](https://github.com/dotnet/diagnostics/blob/master/documentation/sos-debugging-extension.md)可用于分析转储。</span><span class="sxs-lookup"><span data-stu-id="a53bf-148">At this point, [SOS commands](https://github.com/dotnet/diagnostics/blob/master/documentation/sos-debugging-extension.md) can be used to analyze the dump.</span></span>

## <a name="see-also"></a><span data-ttu-id="a53bf-149">另请参阅</span><span class="sxs-lookup"><span data-stu-id="a53bf-149">See also</span></span>

- <span data-ttu-id="a53bf-150">若要了解有关安装 SOS 扩展的更多详细信息，请参阅 [dotnet-sos](dotnet-sos.md)。</span><span class="sxs-lookup"><span data-stu-id="a53bf-150">[dotnet-sos](dotnet-sos.md) for more details on installing the SOS extension.</span></span>
- <span data-ttu-id="a53bf-151">若要了解有关安装和使用符号下载工具的更多详细信息，请参阅 [dotnet-symbol](dotnet-symbol.md)。</span><span class="sxs-lookup"><span data-stu-id="a53bf-151">[dotnet-symbol](dotnet-symbol.md) for more details on installing and using the symbol download tool.</span></span>
- <span data-ttu-id="a53bf-152">若要了解有关调试（包括有用的常见问题解答）的更多详细信息，请参阅 [.NET Core 诊断库](https://github.com/dotnet/diagnostics/blob/master/documentation/)。</span><span class="sxs-lookup"><span data-stu-id="a53bf-152">[.NET Core diagnostics repo](https://github.com/dotnet/diagnostics/blob/master/documentation/) for more details on debugging, including a useful FAQ.</span></span>
- <span data-ttu-id="a53bf-153">若要获取有关在 Linux 或 Mac 上安装 LLDB 的说明，请参阅[安装 LLDB](https://github.com/dotnet/diagnostics/blob/master/documentation/sos.md#getting-lldb)。</span><span class="sxs-lookup"><span data-stu-id="a53bf-153">[Installing LLDB](https://github.com/dotnet/diagnostics/blob/master/documentation/sos.md#getting-lldb) for instructions on installing LLDB on Linux or Mac.</span></span>