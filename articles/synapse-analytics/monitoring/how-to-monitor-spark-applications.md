---
title: Мониторинг Apache Spark приложений
description: Используйте Azure синапсе Studio для мониторинга приложений Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430751"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Использование Azure синапсе Studio (Предварительная версия) для мониторинга приложений Apache Spark

С помощью Azure синапсе Analytics вы можете использовать Spark для запуска записных книжек, заданий и других видов приложений в пулах Spark в рабочей области.

В этой статье объясняется, как отслеживать приложения Spark, позволяющие следить за последним состоянием, проблемами и ходом выполнения.

## <a name="accessing-the-list-of-spark-applications"></a>Доступ к списку приложений Spark

Чтобы просмотреть список приложений Spark в рабочей области, сначала [откройте Azure синапсе Studio](https://web.azuresynapse.net/) и выберите рабочую область.

  > [!div class="mx-imgBorder"]
  > ![Войти в рабочую область](./media/common/login-workspace.png)

Открыв рабочую область, выберите раздел **монитор** слева.

  > [!div class="mx-imgBorder"]
  > ![Выбор концентратора мониторинга](./media/common/left-nav.png)

Выберите **приложения Spark** , чтобы просмотреть список приложений Spark.

  > [!div class="mx-imgBorder"]
  > ![Выбор приложений Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Фильтрация приложений Spark

Вы можете отфильтровать список приложений Spark на тех, которые вас интересуют. Фильтры в верхней части экрана позволяют указать поле, по которому вы хотите выполнить фильтрацию.

Например, можно отфильтровать представление, чтобы просмотреть только приложения Spark, содержащие имя "Sales":

  > [!div class="mx-imgBorder"]
  > ![Кнопка фильтра](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Образец фильтра](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Просмотр сведений о конкретном приложении Spark

Чтобы просмотреть сведения об одном из приложений Spark, выберите приложение Spark и просмотрите сведения. Если приложение Spark все еще выполняется, можно отслеживать ход выполнения.

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о выполнении конвейера мониторинга см. в статье запуск конвейера мониторинга в [Azure синапсе Studio](how-to-monitor-pipeline-runs.md) .  
