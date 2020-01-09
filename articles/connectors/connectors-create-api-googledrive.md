---
title: Подключение к Google Drive
description: Автоматизируйте рабочие процессы, создающие файлы для Google диска и управляющие ими с помощью Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 155a579438747ca7de67eba6449b8a0191e86e2c
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666845"
---
# <a name="create-and-manage-files-for-google-drive-by-using-azure-logic-apps"></a>Создание файлов для Google диска и управление ими с помощью Azure Logic Apps

Подключитесь к Google-диску, чтобы создавать файлы, получать строки и выполнять множество других действий. С помощью Google-диска вы можете: 

* формировать бизнес-процессы на основе данных, получаемых в результате поиска; 
* использовать действия для поиска изображений, новостей и многого другого. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно найти видеоролик, а затем с помощью Twitter опубликовать его в веб-канале Twitter.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Создание подключения к Google-диску

При добавлении соединителя в приложения логики эти приложения необходимо авторизовать для подключения к Google-диску.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

После создания подключения укажите свойства Google-диска, такие как путь к папке или имя файла. 

## <a name="connector-specific-details"></a>Сведения о соединителях

Технические сведения о триггерах, действиях и ограничениях, которые описаны в описании Swagger соединителя, см. на [странице справочника по соединителю](/connectors/googledrive/).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](apis-list.md).
