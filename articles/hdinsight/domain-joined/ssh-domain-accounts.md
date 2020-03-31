---
title: Управление доступом SSH для учетных записей доменов в Azure HDInsight
description: Шаги для управления доступом sSH для учетных записей Azure AD в HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472522"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Управление доступом SSH для учетных записей доменов в Azure HDInsight

В защищенных кластерах по умолчанию всем пользователям доменов в [Azure AD DS](../../active-directory-domain-services/overview.md) разрешается [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) в голову и кромку узлов. Эти пользователи не являются частью группы судовладельцев и не получают корневого доступа. Пользователь SSH, созданный во время создания кластера, будет иметь корневой доступ.

## <a name="manage-access"></a>Управление доступом

Чтобы изменить доступ SSH к определенным `/etc/ssh/sshd_config` пользователям или группам, обновите каждый из узлов.

1. Используйте [команду ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) для подключения к кластеру. Отоверьте приведенную ниже команду, заменив CLUSTERNAME на имя кластера, а затем введите команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Откройте `ssh_confi`файл g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Изменять файл по `sshd_config` желанию. Если вы ограничиваете пользователей определенными группами, то локальные учетные записи не могут внести SSH в этот узлы. Ниже приводится только пример синтаксиса:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Затем сохраните изменения: **Ctrl X**, **Y**, **Введите**.

1. Перезагрузка sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Повторите выше шаги для каждого узла.

## <a name="ssh-authentication-log"></a>Журнал проверки подлинности SSH

В журнал аутентификации SSH записано. `/var/log/auth.log` Если вы видите какие-либо сбои входа через SSH для локальных или доменных учетных записей, вам нужно будет пройти через журнал, чтобы отладить ошибки. Часто проблема может быть связана с определенными учетными записями пользователей, и это, как правило, хорошая практика, чтобы попробовать другие учетные записи пользователей или SSH с помощью пользователя SSH по умолчанию (местная учетная запись), а затем попытаться kinit.

## <a name="ssh-debug-log"></a>SSH журнал отладки

Для обеспечения многословной регистрации необходимо `sshd` перезапустить опцию. `-d` Как `/usr/sbin/sshd -d` Вы также `sshd` можете работать в пользовательском порту (например, 2222), так что вам не придется остановить основной Daemon SSH. Вы также `-v` можете использовать опцию с клиентом SSH, чтобы получить больше журналов (представление стороны клиента сбоев).

## <a name="next-steps"></a>Дальнейшие действия

* [Управление кластерами HDInsight с помощью корпоративного пакета безопасности](./apache-domain-joined-manage.md)
* [Подключитесь к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
