---
title: Границы, квоты и пороговые значения в планировщике Azure
description: Сведения о границах, квотах, значениях по умолчанию и пороговых значениях регулирования для планировщика Azure.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898530"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Границы, квоты и пороговые значения регулирования в планировщике Azure

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) заменяет Azure Scheduler, который [в настоящее время удался.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Чтобы продолжить работу с заданиями, настроенными в Scheduler, пожалуйста, [перейти на azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) как можно скорее. 
>
> Планировщик больше не доступен на портале Azure, но на данный момент доступны смдлеты [REST API](/rest/api/scheduler) и [Azure Scheduler PowerShell,](scheduler-powershell-reference.md) чтобы вы могли управлять своими рабочими местами и коллекциями заданий.

## <a name="limits-quotas-and-thresholds"></a>Границы, квоты и пороговые значения

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>Заголовок x-ms-request-id

Каждый запрос, сделанный в отношении службы Scheduler, возвращает заголовок ответа под названием **x-ms-request-id.** Этот заголовок содержит непрозрачное значение, которое однозначно идентифицирует запрос. Таким образом, если запрос постоянно завершается ошибкой и вы убедились, что запрос в правильном формате, вы можете сообщить об ошибке в Майкрософт, указав значение заголовка ответа **x-ms-request-id** и включив следующие данные: 

* значение **x-ms-request-id**;
* приблизительное время, когда был выполнен запрос; 
* идентификаторы подписки Azure, коллекции задания и задания; 
* тип операции, которую пытался выполнить запрос.

## <a name="next-steps"></a>Дальнейшие действия

* [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)
* [Тарифные планы и выставление счетов для планировщика Azure](scheduler-plans-billing.md)
* [Справочник по API REST планировщика Azure](/rest/api/scheduler)
* [Справочник по командлетам PowerShell планировщика Azure](scheduler-powershell-reference.md)