---
title: Подключение к Google Drive
description: Создавайте файлы и управляйте ими с помощью интерфейсов REST API Google Диск и Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 6310c3b7e5b84915fa336708bc702e94317ad04c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789718"
---
# <a name="get-started-with-the-google-drive-connector"></a>Начало работы с соединителем Google-диска

Подключитесь к Google-диску, чтобы создавать файлы, получать строки и выполнять множество других действий. С помощью Google-диска вы можете: 

* формировать бизнес-процессы на основе данных, получаемых в результате поиска; 
* использовать действия для поиска изображений, новостей и многого другого. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно найти видеоролик, а затем с помощью Twitter опубликовать его в веб-канале Twitter.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Создание подключения к Google-диску

При добавлении соединителя в приложения логики эти приложения необходимо авторизовать для подключения к Google-диску.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

После создания подключения укажите свойства Google-диска, такие как путь к папке или имя файла. 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/googledrive/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях

Вы можете вернуться к [списку интерфейсов API](apis-list.md).
