---
title: Не удается войти в Zeppelin в Azure HDInsight
description: Не удается войти в Zeppelin в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: e9a81d458d1bab68bf94e9e9d0ebd87fac4580c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896155"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Сценарий: не удается войти в Apache Zeppelin в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Не удалось выполнить вход в Apache Zeppelin после изменения пароля "добавляет пароль" в Active Directory.

## <a name="cause"></a>Причина

Пользователь, упомянутый в `activeDirectoryRealm.systemUsername` файла `shiro_ini`, изменил пароль Active Directory.

## <a name="resolution"></a>Разрешение

1. Убедитесь, что измененный пароль является основной причиной, включая `activeDirectoryRealm.systemPassword = <new password>` в файле конфигурации Zeppelin `shiro_ini` в Ambari. Удалите параметр `activeDirectoryRealm.hadoopSecurityCredentialPath`. Ниже расположено `shiro ini`.

    ![широ](./media/domain-joined-zeppelin-signin/shiro.png)

1. Если пользователи теперь могут войти в Zeppelin после шага 1, создайте новый файл `jceks` с новым паролем и замените `activeDirectoryRealm.hadoopSecurityCredentialPath` новым файлом.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества взаимодействия с клиентами. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
