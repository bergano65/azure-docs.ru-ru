---
title: Подключение к Facebook — Azure Logic Apps
description: Управляйте своей временной шкалой и страницей с помощью интерфейсов REST API Facebook и Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 83431184d7e9c5970ece6af143ee9b5166da96d5
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050952"
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

Для получения технических сведений, таких как триггеры, действия и ограничения, как описано в файле OpenAPI (ранее Swagger) соединителя, см. [страницу справочника](/connectors/facebook/)по соединителю.

## <a name="next-steps"></a>Следующие шаги

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).