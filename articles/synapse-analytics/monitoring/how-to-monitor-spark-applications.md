---
title: Мониторинг Apache Spark приложений в синапсе Studio
description: Узнайте, как отслеживать Apache Spark приложения с помощью синапсе Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 2b8dbd20e79b9a6f48ca2d39079ebb452a3b46b2
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530817"
---
# <a name="use-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Использование синапсе Studio (Предварительная версия) для мониторинга приложений Apache Spark

С помощью Azure синапсе Analytics вы можете использовать Spark для запуска записных книжек, заданий и других видов приложений в пулах Spark в рабочей области.

В этой статье объясняется, как отслеживать Apache Spark приложения, позволяя следить за последним состоянием, проблемами и ходом выполнения.

## <a name="access-apache-spark-applications-list"></a>Доступ к списку приложений Apache Spark

Чтобы просмотреть список Apache Spark приложений в рабочей области, сначала [откройте синапсе Studio](https://web.azuresynapse.net/) и выберите рабочую область.

![Войти в рабочую область](./media/common/login-workspace.png)

Открыв рабочую область, выберите раздел **монитор** слева.

![Выбор концентратора мониторинга](./media/common/left-nav.png)

Выберите **Apache Spark приложения** , чтобы просмотреть список приложений Apache Spark.

 ![Выбор приложений Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filter-your-apache-spark-applications"></a>Фильтрация приложений Apache Spark

Вы можете отфильтровать список Apache Spark приложений в тех, которые вас интересуют. Фильтры в верхней части экрана позволяют указать поле, по которому вы хотите выполнить фильтрацию.

Например, можно отфильтровать представление, чтобы просмотреть только Apache Spark приложения, содержащие имя "Sales":

![Кнопка фильтра](./media/common/filter-button.png)

![Образец фильтра](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>Просмотр сведений о конкретном приложении Apache Spark

Чтобы просмотреть сведения об одном из Apache Spark приложений, выберите Apache Spark приложение и просмотрите сведения. Если приложение Apache Spark все еще выполняется, можно отслеживать ход выполнения. [Дополнительные сведения](apache-spark-applications.md).

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о выполнении мониторинга конвейера см. в статье [конвейер мониторинга запуск синапсе Studio](how-to-monitor-pipeline-runs.md) . 

Дополнительные сведения об отладке Apache Spark приложении см. в статье [мониторинг Apache Spark приложений в синапсе Studio](apache-spark-applications.md) .