---
title: Подключение к Facebook
description: Автоматизация задач и рабочих процессов, управляющих временной шкалой и страницей Facebook, с помощью Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665808"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Управляйте временной шкалой и страницей Facebook с помощью Azure Logic Apps

Подключение к Facebook позволяет оставлять публикации в хронике, получать канал страниц и выполнять другие действия. С помощью Facebook можно:

* формировать бизнес-процессы на основе данных, получаемых из Facebook; 
* использовать триггер при получении новой публикации;
* использовать действия, оставляющие публикации в хронике, получающие канал страниц и выполняющие другие действия. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно настроить трансляцию всех публикаций, которые появляются в хронике, в Twitter. 

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Создание подключения к Facebook

При добавлении соединителя в приложения логики эти приложения необходимо авторизовать для подключения к Facebook.

1. Войдите в учетную запись Facebook.

2. Выберите **Авторизовать**и разрешите приложениям логики подключаться к Facebook и использовать его. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Справочник по соединителям

Для получения технических сведений, таких как триггеры, действия и ограничения, как описано в файле OpenAPI (ранее Swagger) соединителя, см. [страницу справочника по соединителю](/connectors/facebook/).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).