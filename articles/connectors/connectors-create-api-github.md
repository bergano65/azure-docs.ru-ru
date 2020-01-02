---
title: Подключение к GitHub
description: Отслеживайте события GitHub с помощью интерфейсов REST API GitHub и Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 7dc865f520b6f4667ace720e656a210e0252d1a1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789762"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>Подключение к GitHub из Azure Logic Apps

GitHub — это веб-служба, где размещен репозиторий Git. Она обеспечивает все возможности распределенного управления версиями и исходным кодом (SCM) в Git, а также другие функции.

Чтобы приступить к работе с соединителем GitHub, [сначала создайте приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Создание подключения к GitHub

Чтобы использовать в приложении логики соединитель GitHub, сначала создайте *подключение*, а затем введите сведения для следующих свойств: 

| Свойство | Обязательно для заполнения | Описание | 
| -------- | -------- | ----------- | 
| по маркеру | ДА | Укажите свои учетные данные GitHub. |

Создав подключение, вы можете выполнять действия и ожидать передачи данных от триггеров, описанных в этой статье.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Справочник по соединителям

Технические сведения о триггерах, действиях и ограничениях, которые описаны в описании OpenAPI (ранее — Swagger) соединителя, см. на [странице справочника по соединителю](/connectors/github/).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).