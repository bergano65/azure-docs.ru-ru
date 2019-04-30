---
title: Подключение к службе "Пользователи Office 365" с помощью Azure Logic Apps | Документация Майкрософт
description: Управляйте профилями пользователей с помощью интерфейсов REST API службы "Пользователи Office 365" и Azure Logic Apps.
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 3865fbc4fbc39da0860218565b0a8956b2dad8ee
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105877"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Начало работы с соединителем Office 365 Пользователи
Подключившись к Office 365 Пользователи, вы сможете получать профили, искать пользователей и выполнять многие другие действия. С помощью Office 365 Пользователи вы можете:

* формировать бизнес-процессы на основе данных, получаемых из Office 365 Пользователи; 
* использовать действия для получения непосредственных подчиненных, профиля пользователя для менеджера и т. д. Эти действия получают ответ и делают выходные данные доступными для использования другими действиями. Например, можно получить список непосредственных подчиненных того или иного лица и внести соответствующие изменения в базу данных SQL Azure. 

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Создание подключения к Office 365 Пользователи
При добавлении этого соединителя в приложения логики необходимо войти в учетную запись Office 365 Пользователи и разрешить приложениям логики подключаться к вашей учетной записи.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

После создания подключения укажите свойства Office 365 Пользователи, такие как идентификатор пользователя. Эти свойства описаны в **справочнике по REST API**.

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/officeusers/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).