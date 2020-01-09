---
title: Подключение к пользователям Office 365
description: Автоматизируйте задачи и рабочие процессы, которые получают профили и управляют ими в профилях пользователей Office 365 с помощью Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666862"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Получение профилей и управление ими в Office 365 пользователи с помощью Azure Logic Apps

Подключившись к Office 365 Пользователи, вы сможете получать профили, искать пользователей и выполнять многие другие действия. С помощью Office 365 Пользователи вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Office 365 Пользователи; 
* использовать действия для получения непосредственных подчиненных, профиля пользователя для менеджера и т. д. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно получить список непосредственных подчиненных того или иного лица и внести соответствующие изменения в базу данных SQL Azure. 

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Создание подключения к Office 365 Пользователи

При добавлении этого соединителя в приложения логики необходимо войти в учетную запись Office 365, чтобы Azure Logic Apps мог подключиться к вашей учетной записи.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

После создания подключения укажите свойства Office 365 Пользователи, такие как идентификатор пользователя. Эти свойства описаны в **справочнике по REST API**.

## <a name="connector-specific-details"></a>Сведения о соединителях

Технические сведения о триггерах, действиях и ограничениях, которые описаны в описании Swagger соединителя, см. на [странице справочника по соединителю](/connectors/officeusers/).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](apis-list.md).