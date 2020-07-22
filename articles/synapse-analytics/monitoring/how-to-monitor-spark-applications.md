---
title: Мониторинг приложений Apache Spark
description: Используйте Azure синапсе Studio для мониторинга приложений Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: e646f1bc1a25f58dd54437cbd77750479ac9ae99
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970911"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Использование Azure синапсе Studio (Предварительная версия) для мониторинга приложений Apache Spark

С помощью Azure синапсе Analytics вы можете использовать Spark для запуска записных книжек, заданий и других видов приложений в пулах Spark в рабочей области.

В этой статье объясняется, как отслеживать Apache Spark приложения, позволяя следить за последним состоянием, проблемами и ходом выполнения.

## <a name="accessing-the-list-of-apache-spark-applications"></a>Доступ к списку приложений Apache Spark

Чтобы просмотреть список Apache Spark приложений в рабочей области, сначала [откройте Azure синапсе Studio](https://web.azuresynapse.net/) и выберите рабочую область.

![Войти в рабочую область](./media/common/login-workspace.png)

Открыв рабочую область, выберите раздел **монитор** слева.

![Выбор концентратора мониторинга](./media/common/left-nav.png)

Выберите **Apache Spark приложения** , чтобы просмотреть список приложений Apache Spark.

 ![Выбор приложений Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-apache-spark-applications"></a>Фильтрация приложений Apache Spark

Вы можете отфильтровать список Apache Spark приложений в тех, которые вас интересуют. Фильтры в верхней части экрана позволяют указать поле, по которому вы хотите выполнить фильтрацию.

Например, можно отфильтровать представление, чтобы просмотреть только Apache Spark приложения, содержащие имя "Sales":

![Кнопка фильтра](./media/common/filter-button.png)

![Образец фильтра](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-apache-spark-application"></a>Просмотр сведений о конкретном приложении Apache Spark

Чтобы просмотреть сведения об одном из Apache Spark приложений, выберите Apache Spark приложение и просмотрите сведения. Если приложение Apache Spark все еще выполняется, можно отслеживать ход выполнения. [Дополнительные сведения](apache-spark-applications.md).

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о выполнении конвейера мониторинга см. в статье запуск конвейера мониторинга в [Azure синапсе Studio](how-to-monitor-pipeline-runs.md) . 

Дополнительные сведения об отладке Apache Spark приложении см. в статье [мониторинг Apache Spark приложений в Azure синапсе Studio](apache-spark-applications.md) .