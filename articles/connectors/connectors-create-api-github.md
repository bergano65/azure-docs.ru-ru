---
title: Доступ, мониторинг и управление репо GitHub
description: Мониторинг событий GitHub и управление репо GitHub путем создания автоматизированных рабочих процессов с Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378455"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Мониторинг и управление репо GitHub с помощью приложений логики Azure

GitHub — это веб-служба, где размещен репозиторий Git. Она обеспечивает все возможности распределенного управления версиями и исходным кодом (SCM) в Git, а также другие функции.

Чтобы приступить к работе с соединителем GitHub, [сначала создайте приложение логики](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Создание подключения к GitHub

Чтобы использовать в приложении логики соединитель GitHub, сначала создайте *подключение*, а затем введите сведения для следующих свойств: 

| Свойство | Обязательно | Описание | 
| -------- | -------- | ----------- | 
| Токен | Да | Укажите свои учетные данные GitHub. |

Создав подключение, вы можете выполнять действия и ожидать передачи данных от триггеров, описанных в этой статье.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Справочник по соединителям

Для получения технических сведений о триггерах, действиях и ограничениях, описанных описанием OpenAPI разъема (ранее Swagger), просмотрите ссылку на [страницу разъема.](/connectors/github/)

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения о других [соединителях Logic Apps](../connectors/apis-list.md).