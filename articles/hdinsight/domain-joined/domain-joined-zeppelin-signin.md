---
title: Не удается войти в Zeppelin в Azure HDInsight
description: Не удается войти в Zeppelin в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: d4bb9e090b238eacec77f4c19bbf9afb3e09a912
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091048"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Сценарий. Не удалось войти в Apache Zeppelin в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблемы

Не удалось выполнить вход в Apache Zeppelin после изменения пароля "добавляет пароль" в Active Directory.

## <a name="cause"></a>Причина:

Пользователь, упомянутый `activeDirectoryRealm.systemUsername` в `shiro_ini` файле, изменил пароль Active Directory.

## <a name="resolution"></a>Разрешение

1. Убедитесь, что измененный пароль является основной причиной, включив `activeDirectoryRealm.systemPassword = <new password>` в конфигурацию Zeppelin `shiro_ini` в Ambari. `activeDirectoryRealm.hadoopSecurityCredentialPath` Удалите параметр. Ниже расположено расположение `shiro ini`.

    ![широ](./media/domain-joined-zeppelin-signin/shiro.png)

1. Если пользователи теперь могут войти в Zeppelin после шага 1, создайте новый `jceks` файл с новым паролем и замените на `activeDirectoryRealm.hadoopSecurityCredentialPath` новый файл.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
