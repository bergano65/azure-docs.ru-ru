---
title: Подключение к GitHub при помощи Azure Logic Apps | Документация Майкрософт
description: Отслеживайте события GitHub с помощью интерфейсов REST API GitHub и Azure Logic Apps.
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 0d2ff9368bc244a5afd6fafc40cf476b90a80a52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462603"
---
# <a name="connect-to-github"></a>Подключение к GitHub

GitHub — это веб-служба, где размещен репозиторий Git. Она обеспечивает все возможности распределенного управления версиями и исходным кодом (SCM) в Git, а также другие функции.

Чтобы приступить к работе с соединителем GitHub, [сначала создайте приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Создание подключения к GitHub

Чтобы использовать в приложении логики соединитель GitHub, сначала создайте *подключение*, а затем введите сведения для следующих свойств: 

| Свойство | Обязательно для заполнения | Описание | 
| -------- | -------- | ----------- | 
| Токен | Да | Укажите свои учетные данные GitHub. |

Создав подключение, вы можете выполнять действия и ожидать передачи данных от триггеров, описанных в этой статье.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>Сведения о соединителях

Сведения о триггерах, действиях, определенных в Swagger, и всех ограничениях см. в статье о [соединителях](/connectors/github/).

## <a name="find-more-connectors"></a>Другие соединители

* Просмотрите [список соединителей](apis-list.md).