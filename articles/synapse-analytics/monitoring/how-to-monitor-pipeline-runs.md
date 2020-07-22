---
title: Мониторинг конвейера запускает Azure синапсе Studio (Предварительная версия)
description: Используйте Azure синапсе Studio для мониторинга выполнения конвейера рабочей области.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 16400d7f292ad5844add3d4a5fc019e84bd27127
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85194898"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Использование Azure синапсе Studio для мониторинга выполнения конвейера рабочей области

С помощью Azure синапсе Analytics можно создавать сложные конвейеры, которые могут автоматизировать и управлять перемещением данных, преобразованием данных и операциями вычисления в решении. Вы можете создавать и отслеживать эти конвейеры с помощью Azure синапсе Studio (Предварительная версия).

В этой статье объясняется, как отслеживать запуски конвейеров, что позволяет следить за последним состоянием, проблемами и ходом выполнения конвейеров.

## <a name="access-the-list-of-pipeline-runs"></a>Доступ к списку запусков конвейеров

Чтобы просмотреть список запусков конвейеров в рабочей области, сначала [откройте Azure синапсе Studio](https://web.azuresynapse.net/) и выберите рабочую область.

![Войти в рабочую область](./media/common/login-workspace.png)

Открыв рабочую область, выберите раздел **монитор** слева.

![Выбор концентратора мониторинга](./media/common/left-nav.png)

Выберите **запуски конвейера** , чтобы просмотреть список запусков конвейера.

![Выбор запусков конвейера](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Фильтрация выполнения конвейера

Вы можете отфильтровать список выполнений конвейера до тех, которые вас интересуют. Фильтры в верхней части экрана позволяют указать поле, по которому вы хотите выполнить фильтрацию.

Например, можно отфильтровать представление, чтобы увидеть только запуски конвейера для конвейера с именем "праздник":

![Кнопка фильтра](./media/common/filter-button.png)

![Образец фильтра](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Просмотр сведений об определенном выполнении конвейера

Чтобы просмотреть сведения о выполнении конвейера, выберите Запуск конвейера. Затем просмотрите запуски действий, связанные с выполнением конвейера. Если конвейер все еще выполняется, можно отслеживать ход выполнения. 
  
## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о мониторинге приложений см. в статье [monitor Apache Spark Applications](how-to-monitor-spark-applications.md) . 
