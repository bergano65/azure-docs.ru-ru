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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
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

Если для службы хранилища Azure или Data Lake Storage 2-го поколения включено безопасное перемещение, URI будет иметь значение `wasbs://` или `abfss://` соответственно.  См. также сведения о [безопасной передаче](../../storage/common/storage-require-secure-transfer.md).

Для новых кластеров используйте учетную запись хранения, у которой уже есть требуемый параметр безопасной пересылки. Не изменяйте параметр безопасной пересылки для учетной записи хранения, используемой существующим кластером.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подпишитесь на [@AzureSupport](https://twitter.com/azuresupport) — официальный канал Microsoft Azure для работы с клиентами. Вступайте в сообщество Azure для получения нужных ресурсов: ответов, поддержки и советов экспертов.

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка**. Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).
