---
title: Получить больше предметов или записей с pagination
description: Настройка pagination, чтобы превысить предел размера страницы по умолчанию для действий разъема в приложениях Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792110"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Получите больше данных, элементов или записей, используя pagination в приложениях логики Azure

При извлечении данных, элементов или записей с помощью действия разъема в [приложениях Azure Logic Apps](../logic-apps/logic-apps-overview.md)вы можете получить наборы результатов настолько большие, что действие не возвращает все результаты одновременно. При некоторых действиях количество результатов может превышать размер страницы разъема по умолчанию. В этом случае действие возвращает только первую страницу результатов. Например, размер страницы по умолчанию для **разъема разъема** сервера S'L Server составляет 2048, но может меняться в зависимости от других параметров.

Некоторые действия позволяют включить настройку *pagination,* чтобы приложение логики мог получить больше результатов до предела pagination, но вернуть эти результаты в виде одного сообщения, когда действие заканчивается. При использовании pagination необходимо указать *пороговое* значение, которое является целевым числом результатов, которые вы хотите вернуть действию. Действие получает результаты до достижения указанного порога. Когда общее количество элементов меньше указанного порога, действие получает все результаты.

Включение параметра pagination получает страницы результатов на основе размера страницы разъема. Такое поведение означает, что иногда вы можете получить больше результатов, чем указанный порог. Например, при использовании действия S'L Server **Get строки,** которая поддерживает настройки pagination:

* Размер страницы действия по умолчанию составляет 2048 записей на страницу.
* Предположим, что у вас есть 10000 записей и указать 5000 записей, как минимум.
* Pagination получает страницы записей, так что, чтобы получить хотя бы указанный минимум, действие возвращает 6144 записей (3 страницы x 2048 записей), а не 5000 записей.

Вот список с некоторыми из разъемов, где вы можете превысить размер страницы по умолчанию для конкретных действий:

* [Хранение Azure Blob](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [получите бесплатную учетную запись Azure](https://azure.microsoft.com/free/).

* Приложение логики и действие, где вы хотите включить pagination. Если у вас нет приложения логики, [см.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="turn-on-pagination"></a>Включите pagination

Чтобы определить, поддерживает ли действие pagination в Logic App Designer, проверьте настройки действия для **настройки Pagination.** В этом примере показано, как включить pagination в **действии строк «Получить»** сервера S'L.

1. В верхнем правом углу действия выберите кнопку эллипсов **(...**) и выберите **настройки.**

   ![Откройте настройки действия](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Если действие поддерживает pagination, действие показывает настройку **Pagination.**

1. Измените настройку **Pagination** от **Off** к **On**. В свойстве **Порога** укажите величину значения для целевого числа результатов, которые вы хотите вернуть действию.

   ![Укажите минимальное количество результатов для возврата](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Когда вы будете готовы, нажмите кнопку **Готово**.

## <a name="workflow-definition---pagination"></a>Определение рабочего процесса - pagination

При включении pagination для действия, поддерживающего эту возможность, определение рабочего процесса приложения логики включает `"paginationPolicy"` свойство вместе с `"minimumItemCount"` свойством в `"runtimeConfiguration"` свойстве этого действия, например:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Получение поддержки

Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
