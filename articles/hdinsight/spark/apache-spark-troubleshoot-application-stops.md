---
title: Apache Spark приложение потоковой передачи остановлено через 24 дня в Azure HDInsight
description: Приложение потоковой передачи Apache Spark останавливается после выполнения в течение 24 дней, и в файлах журнала ошибок нет.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 83bdb4a9913ae817204fb37320f5bdb8174d5baf
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288034"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Сценарий: Apache Spark приложение потоковой передачи останавливается после выполнения в течение 24 дней в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы исправления проблем, возникающих при использовании компонентов Apache Spark в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Приложение потоковой передачи Apache Spark останавливается после выполнения в течение 24 дней, и в файлах журнала ошибок нет.

## <a name="cause"></a>Причина

`livy.server.session.timeout`Значение определяет, сколько времени Apache Livy должен ожидать завершения сеанса. Когда длина сеанса достигнет `session.timeout` значения, сеанс Livy и приложение будут автоматически уничтожены.

## <a name="resolution"></a>Решение

Для длительных заданий увеличьте значение `livy.server.session.timeout` с помощью пользовательского интерфейса Ambari. Вы можете получить доступ к конфигурации Livy из пользовательского интерфейса Ambari, используя URL-адрес `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

Замените `<yourclustername>` именем кластера HDInsight, как показано на портале.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]