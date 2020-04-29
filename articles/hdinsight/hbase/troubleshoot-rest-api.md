---
title: REST API запрос Apache HBase в Azure HDInsight
description: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/08/2020
ms.openlocfilehash: 3bf63aa08ec4c1deff2551cfcc0cf188a75261bc
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515486"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>REST API запрос Apache HBase в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Использование интерфейса RESTFUL Apache HBase для запроса таблицы в пространстве имен, отличном от значения по умолчанию, приводит к ошибке времени выполнения (HTTP Status 500).

## <a name="cause"></a>Причина

REST API HBase поддерживается только при использовании пространства имен по умолчанию. Это известная проблема, связанная с использованием пространств имен HBase или выполнением вызовов, ссылающихся на конкретные столбцы с семействами столбцов с помощью сервера RESTFUL в HDInsight. Это обусловлено проблемами безопасности в шлюзе HDInsight. При использовании API для создания таблицы с пространством имен, обращающейся к столбцам через семейства столбцов, необходимо указать `:` символ, который считается проблемой безопасности в модуле шлюза IIS.

## <a name="mitigation"></a>Решение

Пропустите сервер шлюза или сервера RESTFUL, развернув приложение на виртуальной машине, расположенной в той же виртуальной сети Azure, что и кластер HDInsight. Затем вы можете обмениваться данными с HBase либо напрямую через RPC (обход сервера RESTFUL полностью), либо с отдельными серверами RESTFUL, работающими на рабочих узлах, минуя шлюзы HDInsight.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
