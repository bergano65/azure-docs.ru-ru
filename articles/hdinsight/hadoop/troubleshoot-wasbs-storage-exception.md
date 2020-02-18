---
title: Учетная запись, к которой осуществляется доступ, не поддерживает ошибку HTTP в Azure HDInsight
description: В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165557"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>Учетная запись, к которой осуществляется доступ, не поддерживает ошибку HTTP в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Получено следующее сообщение об ошибке:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Причина

Существует несколько причин получения сообщения об ошибке.

* Для учетной записи хранения включено [безопасное перемещение](../../storage/common/storage-require-secure-transfer.md) , и используется неправильная [Схема URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) .

* Кластер был создан с учетной записью хранения, для которой *отключена*Безопасная отправка. После этого в учетной записи хранения было включено безопасное перемещение.

## <a name="resolution"></a>Решение

Если для службы хранилища Azure или Data Lake Storage 2-го поколения включено безопасное перемещение, URI будет `wasbs://` или `abfss://`соответственно.  См. также сведения о [безопасной передаче](../../storage/common/storage-require-secure-transfer.md).

Для новых кластеров используйте учетную запись хранения, у которой уже есть требуемый параметр безопасной пересылки. Не изменяйте параметр безопасной пересылки для учетной записи хранения, используемой существующим кластером.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества взаимодействия с клиентами. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
