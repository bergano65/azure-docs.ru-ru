---
title: Подключение к OneDrive для бизнеса с помощью Azure Logic Apps | Документация Майкрософт
description: Передавайте файлы и управляйте ими с помощью интерфейсов REST API OneDrive для бизнеса и Azure Logic Apps.
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
ms.openlocfilehash: 87a162338cc67a3f2d2fc425bbcbefe2976661e5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104924"
---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>Начало работы с соединителем OneDrive для бизнеса
Подключитесь к OneDrive для бизнеса, чтобы управлять файлами. Вы можете выполнять различные действия, такие как отправка, обновление, получение и удаление файлов.

Для начала можно создать приложение логики, как описано [здесь](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-onedrive-for-business"></a>Создание подключения к OneDrive для бизнеса
Для создания приложений логики с помощью OneDrive для бизнеса необходимо создать **подключение**, а затем указать данные для приведенных ниже свойств.

| Свойство | Обязательно для заполнения | Описание |
| --- | --- | --- |
| Токен |Да |Укажите учетные данные OneDrive для бизнеса |

Созданное подключение можно использовать для выполнения действий и прослушивания триггеров, описанных в этой статье.

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Информацию о существующих ограничениях, а также о триггерах и действиях, определенных в Swagger, см. в статье со [сведениями о соединителях](/connectors/onedriveforbusinessconnector/).

## <a name="more-connectors"></a>Дополнительные сведения о соединителях
Вы можете вернуться к [списку интерфейсов API](apis-list.md).