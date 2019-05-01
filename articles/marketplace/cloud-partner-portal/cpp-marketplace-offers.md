---
title: Предложения Azure и AppSource Marketplace
description: Создание предложений Azure Marketplace и AppSource Marketplace и управление ими
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: pabutler
ms.openlocfilehash: 2d440adc5111db11486d1fc606abbda0ffae9595
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942531"
---
# <a name="azure-and-appsource-marketplace-offers"></a>Предложения Azure и AppSource Marketplace

В первой части этого раздела представлены общие операции, используемые для создания предложений и управления ими для Azure Marketplace и AppSource Marketplace.  Эта часть предоставляет основы, которые необходимо понять, чтобы управлять конкретными типами предложений, а также техническую информацию, которая является общей для всех типов предложений.  Большая часть этого раздела содержит подробные инструкции о том, как создавать конкретные типы предложений и управлять ими.  

В следующем видео представлены различные возможности и типы предложений, доступные в Azure Marketplace или AppSource.  Здесь также рассматриваются важные технические и бизнес-аспекты публикации приложений или службы в этих магазинах marketplace.

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK2513/player]

**Сборка приложений и служб для Azure Marketplace и AppSource — Build 2018**

Дополнительные сведения об этих магазинах marketplace см. в статье [Руководство по публикации в Azure Marketplace и AppSource](../marketplace-publishers-guide.md).


## <a name="common-offer-operations"></a>Основные операции предложения

Процесс создания нового предложения сильно различается в зависимости от типа предложения, например, в [​​предложении приложения Azure](./azure-applications/cpp-azure-app-offer.md) и [предложении консультационных служб](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md).  А для многих других операций, которые выполняются в предложении на [Портале Cloud Partner](https://cloudpartner.azure.com), достаточно стандартизированы разные типы предложений.  Основные операции, включая публикацию, просмотр статуса, обновление и удаление, описаны в статье [Управление предложениями Azure и AppSource Marketplace](./manage-offers/cpp-manage-offers.md)


## <a name="test-drive"></a>Тестовый выпуск

*Тестовый выпуск* — функция Marketplace, которая предоставляет клиентам возможность демонстрации "попробуй, прежде чем купить" для каждого включенного предложения.  Возможности тестового выпуска ограничены следующим подмножеством типов предложений: [приложения Azure](./azure-applications/cpp-azure-app-offer.md), [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md), [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md), [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md), [приложения SaaS](./saas-app/cpp-saas-offer.md), и [виртуальные машины](./virtual-machine/cpp-virtual-machine-offer.md).  Для этого издателю необходимо создать шаблон тестового выпуска, настроенный для своего предложения.  Дополнительные сведения см. в статье [Что такое тестовый выпуск?](./test-drive/what-is-test-drive.md).

Примените фильтр [Тестовый выпуск](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=test-drive), чтобы просмотреть существующие предложения в Marketplace, которые предлагают демонстрации тестового выпуска. 


## <a name="azure-marketplace-and-appsource-offer-types"></a>Типы предложений Azure Marketplace и AppSource

В следующей таблице перечислены типы текущего предложения, поддерживаемые [Порталом Cloud Partner](https://cloudpartner.azure.com).  Для каждого типа предложения перечислены магазины marketplace, в которых может быть указано предложение, а также общее описание технологии решения предложения.

|                Тип предложения                |  Marketplace  |   ОПИСАНИЕ                                                           |
|                ----------                |  -----------  |   -----------                                                           |
| [Приложение Azure](./azure-applications/cpp-azure-app-offer.md) | Таблицы Azure | Решение представляет собой одну или несколько виртуальных машин, необязательный пользовательский код Azure, развернутый с помощью шаблона Azure Resource Manger.  Развертывание может осуществляться клиентом через шаблон решения или под управлением издателя. Этот тип используется для предоставления большей гибкости, чем предоставлена типом предложения виртуальной машины.  |
| [Консультационная услуга](./consulting-services/cloud-partner-portal-consulting-services-publishing-offer.md) | Оба варианта | Квалифицированные консультанты Microsoft могут размещать свои доменные службы на Azure Marketplace или в AppSource.  Их опыт помогает клиентам оценивать свои проблемы, а также создавать и развертывать правильные решения для достижения своих бизнес-целей.  |
| [Контейнер](./containers/cpp-containers-offer.md)  | Таблицы Azure | Решение — это образ контейнера Docker, предоставляемый в качестве экземпляра службы на основе Kubernetes или контейнера Azure. |
| [Dynamics 365 Business Central](../cloud-partner-portal-orig/cpp-business-central-offer.md) | AppSource | Пакет, расширяющий систему планирования ресурсов предприятия (ERP) и систему управления бизнесом. |
| [Dynamics 365 for Customer Engagement](./dyn365ce/cpp-customer-engagement-offer.md) | AppSource | Пакет, который расширяет этот клиент system management (CRM) ресурсов, через продаж, службы, службы проектов и модули службы поля  |
| [Dynamics 365 for Finance and Operations](../cloud-partner-portal-orig/cpp-dynamics-365-operations-offer.md) | AppSource | Пакет, который расширяет этот управления ресурсами предприятия (ERP) служба, которая поддерживает дополнительные финансовые операции, производство и управление логистическими цепочками |
| [Модуль IoT Edge](./iot-edge-module/cpp-offer-process-parts.md) | Таблицы Azure | Docker-совместимый контейнер, работающий на устройстве IoT Edge.  Он содержит небольшие вычислительные модули, которые используют комбинации пользовательского кода, других служб Azure и сторонних служб. |
| [Приложение Power BI](./power-bi/cpp-power-bi-offer.md) | AppSource | Приложение Power BI, который упаковывает настраиваемое содержимое Power BI, включая наборы данных, отчеты и панели мониторинга |
| [Приложение SaaS](./saas-app/cpp-saas-offer.md) | Таблицы Azure | Решением является программного обеспечения как службы подписки, управляемой поставщиком, какие пользователи входят через настраиваемый интерфейс, который использует Azure Active Directory. |
| [Виртуальная машина](./virtual-machine/cpp-virtual-machine-offer.md)  | Таблицы Azure  | Решение содержится в одной виртуальной машине, развернутой по подписке клиента.  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |   |   |

Дополнительные сведения см. в статье [Руководство по публикации разных типов предложений](../publisher-guide-by-offer-type.md).


## <a name="next-steps"></a>Дальнейшие действия

Вы узнаете об основных операциях, которые можно выполнять на предложения marketplace распространенные технические характеристики и их ресурсы в этой статье [управление предложениями](./manage-offers/cpp-manage-offers.md).
