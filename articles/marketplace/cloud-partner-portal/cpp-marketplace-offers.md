---
title: Предложения Azure и AppSource Marketplace | Документация Майкрософт
description: Создание предложений Azure Marketplace и AppSource Marketplace и управление ими
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: pbutlerm
ms.openlocfilehash: 0d6879c6b9be06af4bb289761cc8f26b7e56d18e
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355339"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Предложения Azure и AppSource Marketplace

В первой части этого раздела представлены общие операции, используемые для создания предложений и управления ими для Azure Marketplace и AppSource Marketplace.  Эта часть предоставляет основы, которые необходимо понять, чтобы управлять конкретными типами предложений, а также техническую информацию, которая является общей для всех типов предложений.  Большая часть этого раздела содержит подробные инструкции о том, как создавать конкретные типы предложений и управлять ими.  

В следующем видео представлены различные возможности и типы предложений, доступные в Azure Marketplace или AppSource.  Здесь также рассматриваются важные технические и бизнес-аспекты публикации приложений или службы в этих магазинах marketplace.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Сборка приложений и служб для Azure Marketplace и AppSource — Build 2018**

Дополнительные сведения об этих магазинах marketplace см. в статье [Руководство по публикации в Azure Marketplace и AppSource](../marketplace-publishers-guide.md).


## <a name="azure-marketplace-and-appsource-offer-types"></a>Типы предложений Azure Marketplace и AppSource

В следующей таблице перечислены типы текущего предложения, поддерживаемые [Порталом Cloud Partner](https://cloudpartner.azure.com).  Для каждого типа предложения перечислены магазины marketplace, в которых может быть указано предложение, а также общее описание технологии решения предложения.

|                Тип предложения                |  Marketplace  |   ОПИСАНИЕ                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Приложение Azure](./azure-applications/cpp-azure-app-offer.md) | Таблицы Azure | Решение представляет собой одну или несколько виртуальных машин, необязательный пользовательский код Azure, развернутый с помощью шаблона Azure Resource Manger.  Развертывание может осуществляться клиентом через шаблон решения или под управлением издателя. Этот тип используется для предоставления большей гибкости, чем предоставлена типом предложения виртуальной машины.  |
| [Консультационная услуга](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Оба варианта | Квалифицированные консультанты Microsoft могут размещать свои доменные службы на Azure Marketplace или в AppSource.  Их опыт помогает клиентам оценивать свои проблемы, а также создавать и развертывать правильные решения для достижения своих бизнес-целей.  |
| [Контейнер](./containers/cpp-containers-offer.md)  | Таблицы Azure | Решение — это образ контейнера Docker, предоставляемый в качестве экземпляра службы на основе Kubernetes или контейнера Azure. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Пакет, расширяющий систему планирования ресурсов предприятия (ERP) и систему управления бизнесом. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Пакет, расширяющий систему управления ресурсами клиента (CRM) с помощью модулей продаж, обслуживания, обслуживания проектов и выездного обслуживания.  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Пакет, расширяющий службу планирования ресурсов предприятия (ERP), которая поддерживает расширенное управление финансами, операциями, производством и цепочкой поставок. |
| [Модуль IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Таблицы Azure | Docker-совместимый контейнер, работающий на устройстве IoT Edge.  Он содержит небольшие вычислительные модули, которые используют комбинации пользовательского кода, других служб Azure и сторонних служб. |
| [Приложение SaaS](./saas-app/cpp-saas-offer.md) | Таблицы Azure | Решение — это подписка по модели "программное обеспечение как услуга", управляемая издателем, пользователи которого входят в систему через настраиваемый интерфейс, использующий Azure Active Directory. |
| [Виртуальная машина](./virtual-machine/cpp-virtual-machine-offer.md)  | Таблицы Azure  | Решение содержится в одной виртуальной машине, развернутой по подписке клиента.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Дополнительные сведения см. в статье [Руководство по публикации разных типов предложений](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об общих операциях, которые можно выполнять с предложениями marketplace, и об их общих технических атрибутах и ресурсах см. в статье [Manage Azure and AppSource Marketplace offers](./manage-offers/cpp-manage-offers.md) (Управление предложениями Azure и AppSource Marketplace).
