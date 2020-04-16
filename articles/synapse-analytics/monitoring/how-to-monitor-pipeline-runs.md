---
title: Монитор конвейер айдирует Azure Synapse Studio (предварительный просмотр)
description: Используйте студию Azure Synapse для мониторинга работы конвейера.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430790"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Используйте студию Azure Synapse для мониторинга работы конвейера

С помощью Azure Synapse Analytics можно создавать сложные конвейеры, которые могут автоматизировать и организовать движение данных, преобразование данных и вычисление деятельности в рамках решения. Вы можете авторизировать и контролировать эти конвейеры с помощью Azure Synapse Studio (предварительный просмотр).

В этой статье объясняется, как контролировать ходы конвейеров, что позволяет следить за последнимсостоянием, проблемами и ходом работ ы конвейеров.

## <a name="access-the-list-of-pipeline-runs"></a>Доступ к списку запусков конвейера

Чтобы увидеть список работ конвейера в рабочем пространстве, сначала [откройте студию Azure Synapse](https://web.azuresynapse.net/) и выберите рабочее пространство.

![Вход в рабочее пространство](./media/common/login-workspace.png)

После открытия рабочего пространства выберите раздел **Монитор** слева.

![Выберите концентратор монитора](./media/common/left-nav.png)

Выберите **запуски трубопроводов** для просмотра списка запусков конвейеров.

![Выбор прогонов трубопроводов](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Фильтрация проводов

Можно отфильтровать список проводов, которые вас интересуют. Фильтры в верхней части экрана позволяют указать поле, на котором вы хотите отфильтровать.

Например, можно отфильтровать представление, чтобы увидеть только запуск конвейера для конвейера под названием "праздник":

![Кнопка фильтра](./media/common/filter-button.png)

![Образец фильтра](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Просмотр сведений о конкретном запуске конвейера

Чтобы просмотреть сведения о запуске конвейера, выберите запуск конвейера. Затем просмотрите запуски активности, связанные с запуском конвейера. Если конвейер все еще работает, можно следить за ходом выполнения. 
  
## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о [Monitor Apache Spark applications](how-to-monitor-spark-applications.md) приложениях мониторинга, см. 
