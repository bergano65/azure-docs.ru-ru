---
title: Подключение к GitHub-Azure Logic Apps
description: Отслеживайте события GitHub с помощью интерфейсов REST API GitHub и Azure Logic Apps.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 6a6e2a803ee2a272189abf0f21796b2305eea40b
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050893"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Подключение к GitHub из Azure Logic Apps

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

## <a name="connector-reference"></a>Справочник по соединителям

Технические сведения о триггерах, действиях и ограничениях, которые описаны в описании OpenAPI (ранее — Swagger) соединителя, см. на [странице справочника](/connectors/github/)по соединителю.

## <a name="next-steps"></a>Следующие шаги

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).