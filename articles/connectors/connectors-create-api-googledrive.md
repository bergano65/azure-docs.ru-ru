---
title: Подключение к Google Диск с помощью Azure Logic Apps | Документация Майкрософт
description: Создавайте файлы и управляйте ими с помощью интерфейсов REST API Google Диск и Azure Logic Apps.
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 43bd5248f1bb80c71a85935c585deac6152be78b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105095"
---
# <a name="get-started-with-the-google-drive-connector"></a>Начало работы с соединителем Google-диска
Подключитесь к Google-диску, чтобы создавать файлы, получать строки и выполнять множество других действий. С помощью Google-диска вы можете: 

* формировать бизнес-процессы на основе данных, получаемых в результате поиска; 
* использовать действия для поиска изображений, новостей и многого другого. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно найти видеоролик, а затем с помощью Twitter опубликовать его в веб-канале Twitter.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Создание подключения к Google-диску
При добавлении соединителя в приложения логики эти приложения необходимо авторизовать для подключения к Google-диску.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

После создания подключения укажите свойства Google-диска, такие как путь к папке или имя файла. 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/googledrive/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).
