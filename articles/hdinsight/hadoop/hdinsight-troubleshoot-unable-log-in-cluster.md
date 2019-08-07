---
title: Не удалось войти в кластер Azure HDInsight.
description: Не удалось войти в кластер Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/31/2019
ms.openlocfilehash: eb7249fc88f37e5c15447e5a8907468a851d2416
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737450"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Сценарий: Не удалось войти в кластер Azure HDInsight.

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблемы

Не удалось войти в кластер Azure HDInsight.

## <a name="cause"></a>Причина:

Причины могут отличаться. Помните, что при входе на панели мониторинга кластера или приложения используйте учетные данные для входа в кластер или HTTP. При удаленном подключении используйте учетные данные SSH или удаленного рабочего стола.

## <a name="resolution"></a>Разрешение

Устранить распространенные неполадки можно с помощью одного или нескольких описанных методов.

* Попробуйте открыть панель мониторинга кластера на новой вкладке браузера в режиме конфиденциальности.

* Если вы не можете отозвать учетные данные SSH, вы можете [сбросить учетные данные в пользовательском интерфейсе Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
