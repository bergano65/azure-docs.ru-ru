---
title: Доступ к учетной записи не поддерживает ошибку http в Azure HDInsight
description: В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165557"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>Доступ к учетной записи не поддерживает ошибку http в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Получено следующее сообщение об ошибке:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Причина

Существует несколько причин получения сообщения об ошибке:

* Учетная запись хранилища имеет [безопасный перевод](../../storage/common/storage-require-secure-transfer.md) включен и [неправильноuri схема](../hdinsight-hadoop-linux-information.md#URI-and-scheme) используется.

* Кластер был создан с учетной записью хранилища, которая была *защищена от*отключения перевода. После этого на счете хранения был включен безопасный перевод.

## <a name="resolution"></a>Решение

Если безопасная передача включена для хранения Azure или хранения `wasbs://` `abfss://`данных озера Gen2, URI будет или, соответственно.  См. также сведения о [безопасной передаче](../../storage/common/storage-require-secure-transfer.md).

Для новых кластеров используйте учетную запись хранения, которая уже имеет желаемую безопасную настройку передачи. Не изменяйте настройки безопасной передачи для учетной записи хранилища, которая используется существующим кластером.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
