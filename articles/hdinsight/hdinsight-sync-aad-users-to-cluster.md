---
title: Synchronize Azure Active Directory users to HDInsight cluster
description: Synchronize authenticated users from Azure Active Directory to an HDInsight cluster.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: acacb9c10250d43e22b5b5b1d073b18461561512
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406830"
---
# <a name="synchronize-azure-active-directory-users-to-an-hdinsight-cluster"></a>Синхронизация пользователей Azure Active Directory с кластером HDInsight

[Кластеры HDInsight с Корпоративным пакетом безопасности (ESP)](hdinsight-domain-joined-introduction.md) могут использовать строгую аутентификацию пользователей Azure Active Directory (Azure AD), а также политики *управления доступом на основе ролей* (RBAC). As you add users and groups to Azure AD, you can synchronize the users who need access to your cluster.

## <a name="prerequisites"></a>Технические условия

Если вы еще этого не сделали, [создайте кластер HDInsight с Корпоративным пакетом безопасности](hdinsight-domain-joined-configure.md).

## <a name="add-new-azure-ad-users"></a>Добавление новых пользователей Azure AD

Чтобы просмотреть узлы, откройте веб-интерфейс Ambari. Для каждого узла будут заданы новые параметры автоматического обновления.

1. From the [Azure portal](https://portal.azure.com), navigate to the Azure AD directory associated with your ESP cluster.

2. Выберите **All users** (Все пользователи) в левом меню, а затем щелкните **New user** (Новый пользователь).

    ![Azure portal users and groups all](./media/hdinsight-sync-aad-users-to-cluster/users-and-groups-new.png)

3. Заполните форму нового пользователя. Выберите группы, созданные для назначения разрешений на основе кластеров. В этом примере создайте группу с именем HiveUsers, которой можно назначить новых пользователей. [Примеры инструкций](hdinsight-domain-joined-configure.md) для создания кластера ESP включают в себя добавление двух групп: `HiveUsers` и `AAD DC Administrators`.

    ![Azure portal user pane select groups](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-new-user-form.png)

4. Нажмите кнопку **Создать**.

## <a name="use-the-apache-ambari-rest-api-to-synchronize-users"></a>Синхронизация пользователей с помощью REST API Apache Ambari

Группы пользователей, указанные во время создания кластера, уже синхронизированы. Синхронизация пользователей выполняется автоматически один раз в час. Чтобы немедленно синхронизировать пользователей или группы, которые не указаны при создании кластера, используйте REST API Ambari.

Следующий метод использует POST с REST API Ambari. Дополнительные сведения см. в статье [Управление кластерами HDInsight с помощью REST API Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

1. Use [ssh command](hdinsight-hadoop-linux-use-ssh-unix.md) to connect to your cluster. Измените приведенную ниже команду, заменив `CLUSTERNAME` именем своего кластера, а затем введите команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. После аутентификации введите следующую команду:

    ```bash
    curl -u admin:PASSWORD -sS -H "X-Requested-By: ambari" \
    -X POST -d '{"Event": {"specs": [{"principal_type": "groups", "sync_type": "existing"}]}}' \
    "https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events"
    ```

    Результат должен выглядеть следующим образом.

    ```json
    {
      "resources" : [
        {
          "href" : "http://hn0-hadoop.<YOUR DOMAIN>.com:8080/api/v1/ldap_sync_events/1",
          "Event" : {
            "id" : 1
          }
        }
      ]
    }
    ```

1. Чтобы просмотреть состояние синхронизации, выполните новую команду `curl`:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/ldap_sync_events/1
    ```

    Результат должен выглядеть следующим образом.

    ```json
    {
      "href" : "http://hn0-hadoop.YOURDOMAIN.com:8080/api/v1/ldap_sync_events/1",
      "Event" : {
        "id" : 1,
        "specs" : [
          {
            "sync_type" : "existing",
            "principal_type" : "groups"
          }
        ],
        "status" : "COMPLETE",
        "status_detail" : "Completed LDAP sync.",
        "summary" : {
          "groups" : {
            "created" : 0,
            "removed" : 0,
            "updated" : 0
          },
          "memberships" : {
            "created" : 1,
            "removed" : 0
          },
          "users" : {
            "created" : 1,
            "removed" : 0,
            "skipped" : 0,
            "updated" : 0
          }
        },
        "sync_time" : {
          "end" : 1497994072182,
          "start" : 1497994071100
        }
      }
    }
    ```

1. This result shows that the status is **COMPLETE**, one new user was created, and the user was assigned a membership. В этом примере пользователь назначен для группы HiveUsers, синхронизированной по протоколу LDAP, так как он был добавлен в ту же группу в Azure AD.

    > [!NOTE]  
    > The previous method only synchronizes the Azure AD groups specified in the **Access user group** property of the domain settings during cluster creation. Дополнительные сведения см. в статье [Настройка среды с присоединенной к домену песочницей HDInsight](domain-joined/apache-domain-joined-configure.md).

## <a name="verify-the-newly-added-azure-ad-user"></a>Проверка только что добавленного пользователя Azure AD

Откройте [веб-интерфейс Apache Ambari](hdinsight-hadoop-manage-ambari.md) и убедитесь, что новый пользователь Azure AD добавлен. Получите доступ к веб-интерфейсу Ambari, перейдя к **`https://CLUSTERNAME.azurehdinsight.net`** . Укажите имя администратора и пароль кластера.

1. На панели мониторинга Ambari выберите **Manage Ambari** (Управление Ambari) в меню **admin**.

    ![Apache Ambari dashboard Manage Ambari](./media/hdinsight-sync-aad-users-to-cluster/manage-apache-ambari.png)

2. Выберите **Пользователи** в группе меню **User + Group Management** (Управление пользователями и группами) в левой части страницы.

    ![HDInsight users and groups menu](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-menu-item.png)

3. Новый пользователь должен быть указан в таблице "Пользователи". Для типа необходимо задать значение `LDAP`, а не `Local`.

    ![HDInsight aad users page overview](./media/hdinsight-sync-aad-users-to-cluster/hdinsight-users-page.png)

## <a name="log-in-to-ambari-as-the-new-user"></a>Вход в Ambari в качестве нового пользователя

Когда новый пользователь (или любой другой пользователь домена) входит в Ambari, он использует полное имя пользователя и учетные данные домена Azure AD.  В Ambari показан псевдоним пользователя, являющийся отображаемым именем пользователя в Azure AD.
В новом примере имя пользователя — `hiveuser3@contoso.com`. В Ambari этот пользователь отображается как `hiveuser3`, но он входит в Ambari как `hiveuser3@contoso.com`.

## <a name="see-also"></a>Дополнительные материалы

* [Настройка политик Apache Hive в кластере HDInsight с ESP](hdinsight-domain-joined-run-hive.md)
* [Управление кластерами HDInsight с помощью корпоративного пакета безопасности](hdinsight-domain-joined-manage.md)
* [Предоставление пользователям доступа к представлениям Apache Ambari](hdinsight-authorize-users-to-ambari.md)
