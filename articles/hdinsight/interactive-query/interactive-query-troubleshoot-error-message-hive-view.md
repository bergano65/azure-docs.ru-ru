---
title: Сообщение об ошибке, не отображаемое в Apache Hive представлении — Azure HDInsight
description: Запрос завершается ошибкой в Apache Hive представлении без подробной информации об кластере Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 7aadef0d4c70f748b4f7a7c7f9fc16c38216fa7a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288952"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Сценарий: сообщение об ошибке запроса не отображается в Apache Hive представлении в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании интерактивных компонентов запросов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Сообщение об ошибке запроса представления Apache Hive View будет выглядеть примерно так, без дополнительных сведений:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Причина

Иногда сообщение об ошибке ошибки запроса может быть слишком большим для отображения на главной странице представления Hive.

## <a name="resolution"></a>Решение

Просмотрите вкладку уведомления в правом верхнем углу Hive_view, чтобы увидеть полный текст StackTrace и сообщение об ошибке.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]