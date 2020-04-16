---
title: Мониторинг приложений Apache Spark
description: Используйте студию Azure Synapse для мониторинга приложений Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430751"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Используйте студию Azure Synapse (предварительный просмотр) для мониторинга приложений Apache Spark

С помощью Azure Synapse Analytics вы можете использовать Spark для запуска ноутбуков, заданий и других типов приложений в пулах Spark в рабочем пространстве.

В этой статье объясняется, как контролировать приложения Spark, что позволяет следить за последнимсостоянием, проблемами и прогрессом.

## <a name="accessing-the-list-of-spark-applications"></a>Доступ к списку приложений Spark

Чтобы увидеть список приложений Spark в рабочем пространстве, сначала [откройте студию Azure Synapse](https://web.azuresynapse.net/) и выберите рабочее пространство.

  > [!div class="mx-imgBorder"]
  > ![Вход в рабочее пространство](./media/common/login-workspace.png)

После открытия рабочего пространства выберите раздел **Монитор** слева.

  > [!div class="mx-imgBorder"]
  > ![Выберите концентратор монитора](./media/common/left-nav.png)

Выберите **приложения Spark** для просмотра списка приложений Spark.

  > [!div class="mx-imgBorder"]
  > ![Выберите приложения Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Фильтрация приложений Spark

Список приложений Spark можно отфильтровать на интересующие вас приложения Spark. Фильтры в верхней части экрана позволяют указать поле, на котором вы хотите отфильтровать.

Например, можно отфильтровать представление, чтобы увидеть только приложения Spark, содержащие имя "продажи":

  > [!div class="mx-imgBorder"]
  > ![Кнопка фильтра](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Образец фильтра](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Просмотр сведений о конкретном приложении Spark

Чтобы просмотреть сведения об одном из приложений Spark, выберите приложение Spark и просмотрите детали. Если приложение Spark все еще работает, можно следить за ходом работы.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о [Monitor pipeline runs Azure Synapse Studio](how-to-monitor-pipeline-runs.md) мониторинге запусков конвейеров см.  
