---
title: Подключение к Dynamics 365
description: Создание записей Dynamics 365 и управление ими с помощью Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: 00bf8ea2b783e09711a95f203bdfcce0e6b90b2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82994304"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>Создание записей и управление ими в Dynamics 365 с помощью Azure Logic Apps

В Dynamics 365 используется [Common Data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro). Для подключений к Dynamics 365 используйте [соединитель Common Data Service](https://docs.microsoft.com/connectors/commondataservice/).

> [!IMPORTANT]
> [Соединитель Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/) является устаревшим, но остается в рабочем процессе до тех пор, пока не будет удален. Не используйте соединитель Dynamics 365 для новых приложений логики. Для удаления соединителя Dynamics 365 еще не указано ни одной временной шкалы. Вы не обязаны преобразовывать имеющиеся приложения логики в соединитель Common Data Service или другой плановый новый соединитель, но вам нужно будет преобразовать приложения логики после удаления соединителя. Дополнительные сведения см. в разделе [соединитель Dynamics 365 устарел](https://docs.microsoft.com/power-platform/important-changes-coming).
>
> [Соединитель Common Data Service](https://docs.microsoft.com/connectors/commondataservice/) предоставляет те же возможности, что и устаревший соединитель Dynamics 365, но включает улучшения, повышающие надежность. Сведения об использовании соединителя Common Data Service в приложениях логики см. на [этой странице](../connectors/connect-common-data-service.md).

Дополнительные сведения о Common Data Service см. в следующих разделах:

* [Дополнительные сведения: Начало работы с Common Data Service](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [Сведения: подключение и анализ данных Dynamics 365 с помощью платформы управления питанием и Common Data Service](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)