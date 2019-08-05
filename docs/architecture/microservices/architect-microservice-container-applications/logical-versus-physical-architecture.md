---
title: 逻辑体系结构与物理体系结构
description: 了解逻辑和物理体系结构之间的差异。
ms.date: 09/20/2018
ms.openlocfilehash: c269369e9b5391e8d25ece46e6b08e34a82fbbba
ms.sourcegitcommit: f20dd18dbcf2275513281f5d9ad7ece6a62644b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68673054"
---
# <a name="logical-architecture-versus-physical-architecture"></a>逻辑体系结构与物理体系结构

此时若停下步伐并探讨逻辑体系结构和物理体系结构之间的区别，以及如何将其用于设计基于微服务的应用程序，定会受益匪浅。

首先，生成微服务不需要使用任何特殊技术。 例如，创建基于微服务的体系结构对 Docker 容器并没有强制性要求。 这些微服务也可以像普通进程那样运行。 微服务是一种逻辑体系结构。

此外，即使微服务能够作为单一服务、进程或容器进行物理实现（为简便起见，采用 [eShopOnContainers](https://aka.ms/MicroservicesArchitecture) 原始版本中使用的方法），生成由数十个甚至数百个服务组成的大型复杂应用程序时，商业微服务和物理服务或容器之间的奇偶校验并非必要。

这就是应用程序的逻辑体系结构和物理体系结构之间存在的差异。 系统的逻辑体系结构和逻辑边界不一定要与物理或部署体系结构一对一映射。 这种情况存在一定的可能性，但通常不会发生。

尽管已确定某些业务的微服务或界定上下文，但这并不意味着实现它们的最佳方式总是为每个业务微服务创建单一服务（如 ASP.NET Web API）或者单一 Docker 容器。 必须使用单一服务或容器实现每个业务微服务的这一规则过于严苛。

因此，业务微服务或界定上下文是一种不一定与物理体系结构一致的逻辑体系结构。 必须通过使代码和状态可独立、可进行版本控制、部署和缩放，让商业微服务或界定上下文能够自治，这一点很重要。

如图 4-8 所示，目录商业微服务可由几个服务或进程组成。 这些服务可以是多个 ASP.NET Web API 服务，也可以是使用 HTTP 或其他任何协议的其他任何服务。 更重要的是，这些服务只要对于相同业务领域具有内聚性，便能共享相同数据。

![Catalog 商业微服务关系图，其中包含 API 服务、搜索服务和 SQL Server 数据库。](./media/image8.png)

**图 4-8**。 使用多个物理服务的商业微服务

示例中的服务共享相同的数据模型，因为 Web API 服务针对的是与搜索服务相同的数据。 因此，在商业微服务的物理实现中，拆分该功能便可根据需要扩展或缩减每个内部服务。 Web API 服务通常比搜索服务需要更多的实例，反之亦然。

简而言之，微服务的逻辑体系结构并不总是与物理部署体系结构一致。 本指南中所涉及的微服务是指可以映射到一个或多个（物理）服务的商业微服务或逻辑微服务。 大多数情况下，可以映射到单个服务，但也有可能映射到多个服务。

>[!div class="step-by-step"]
>[上一页](data-sovereignty-per-microservice.md)
>[下一页](distributed-data-management.md)