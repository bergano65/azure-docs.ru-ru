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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82515486"
---
# <a name="rest-api-to-query-apache-hbase-in-azure-hdinsight"></a>REST API запрос Apache HBase в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Использование интерфейса RESTFUL Apache HBase для запроса таблицы в пространстве имен, отличном от значения по умолчанию, приводит к ошибке времени выполнения (HTTP Status 500).

## <a name="cause"></a>Причина

REST API HBase поддерживается только при использовании пространства имен по умолчанию. Это известная проблема, связанная с использованием пространств имен HBase или выполнением вызовов, ссылающихся на конкретные столбцы с семействами столбцов с помощью сервера RESTFUL в HDInsight. Это обусловлено проблемами безопасности в шлюзе HDInsight. При использовании API для создания таблицы с пространством имен, обращающейся к столбцам через семейства столбцов, необходимо указать `:` символ, который считается проблемой безопасности в модуле шлюза IIS.

## <a name="mitigation"></a>Меры по снижению риска

Пропустите сервер шлюза или сервера RESTFUL, развернув приложение на виртуальной машине, расположенной в той же виртуальной сети Azure, что и кластер HDInsight. Затем вы можете обмениваться данными с HBase либо напрямую через RPC (обход сервера RESTFUL полностью), либо с отдельными серверами RESTFUL, работающими на рабочих узлах, минуя шлюзы HDInsight.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подпишитесь на [@AzureSupport](https://twitter.com/azuresupport) — официальный канал Microsoft Azure для работы с клиентами. Вступайте в сообщество Azure для получения нужных ресурсов: ответов, поддержки и советов экспертов.

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка**. Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).
