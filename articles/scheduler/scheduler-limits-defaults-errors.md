---
title: Границы, квоты и пороговые значения в планировщике Azure
description: Сведения о границах, квотах, значениях по умолчанию и пороговых значениях регулирования для планировщика Azure.
services: scheduler
ms.service: scheduler
author: WenJason
ms.author: v-jay
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
origin.date: 08/18/2016
ms.date: 11/12/2018
ms.openlocfilehash: 478afb20f3dabec74d66d00bec325408ce6604fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531278"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Границы, квоты и пороговые значения регулирования в планировщике Azure

> [!IMPORTANT]
> Служба [Azure Logic Apps](../logic-apps/logic-apps-overview.md) заменяет планировщик Azure, который выводится из эксплуатации. Для планирования заданий [попробуйте использовать Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="limits-quotas-and-thresholds"></a>Границы, квоты и пороговые значения

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>Заголовок x-ms-request-id

Каждый запрос к службе планировщика возвращает заголовок ответа с именем **x-ms-request-id**. Этот заголовок содержит непрозрачное значение, которое однозначно определяет запрос. Таким образом, если запрос постоянно завершается ошибкой и вы убедились, что запрос в правильном формате, вы можете сообщить об ошибке в Майкрософт, указав значение заголовка ответа **x-ms-request-id** и включив следующие данные: 

* значение **x-ms-request-id**;
* приблизительное время, когда был выполнен запрос; 
* идентификаторы подписки Azure, коллекции задания и задания; 
* тип операции, которую пытался выполнить запрос.

## <a name="see-also"></a>См. также

* [Что такое планировщик Azure?](scheduler-intro.md)
* [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)
