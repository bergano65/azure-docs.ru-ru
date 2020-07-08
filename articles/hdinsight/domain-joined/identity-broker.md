---
title: Использование брокера ИДЕНТИФИКАТОРов (Предварительная версия) для управления учетными данными — Azure HDInsight
description: Дополнительные сведения о брокере ИДЕНТИФИКАТОРов HDInsight для упрощения проверки подлинности присоединенных к домену кластеров Apache Hadoop.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 1bea8adbdb39d2ce83cfe7821ef052fdc1f1d512
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921329"
---
# <a name="use-id-broker-preview-for-credential-management"></a>Использование брокера ИДЕНТИФИКАТОРов (Предварительная версия) для управления учетными данными

В этой статье описывается, как настроить и использовать функцию брокера ИДЕНТИФИКАТОРов в Azure HDInsight. Эту функцию можно использовать для входа в Apache Ambari через многофакторную идентификацию Azure и получения необходимых билетов Kerberos без хэширования паролей в доменных службах Azure Active Directory (Azure AD DS).

## <a name="overview"></a>Обзор

Брокер идентификации упрощает настройку сложных проверок подлинности в следующих сценариях:

* Ваша организация полагается на Федерацию для проверки подлинности пользователей при доступе к облачным ресурсам. Ранее для использования кластеров HDInsight Корпоративный пакет безопасности (ESP) необходимо включить синхронизацию хэша паролей из локальной среды для Azure Active Directory. Это требование может быть затруднительным или нежелательным для некоторых организаций.

* Вы создаете решения, использующие технологии, основанные на разных механизмах проверки подлинности. Например, Apache Hadoop и Apache Ranger полагаются на Kerberos, тогда как Azure Data Lake Storage полагается на OAuth.

Брокер идентификации предоставляет единую инфраструктуру проверки подлинности и устраняет необходимость синхронизации хэшей паролей с Azure AD DS. Компонент Service Broker состоит из компонентов, работающих на виртуальной машине Windows Server (узел брокера ИДЕНТИФИКАТОРов), а также на узлах шлюза кластера. 

На следующей схеме показан поток проверки подлинности для всех пользователей, включая федеративных пользователей, после включения компонента ID Broker.

![Поток проверки подлинности с брокером ИДЕНТИФИКАТОРов](./media/identity-broker/identity-broker-architecture.png)

Компонент ID Broker позволяет выполнять вход в кластеры ESP с помощью многофакторной проверки подлинности без предоставления паролей. Если вы уже вошли в другие службы Azure, например портал Azure, вы можете войти в кластер HDInsight с помощью единого входа.

## <a name="enable-hdinsight-id-broker"></a>Включение брокера ИДЕНТИФИКАТОРов HDInsight

Чтобы создать кластер ESP с включенным компонентом ID Broker, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выполните базовые действия по созданию кластера ESP. Дополнительные сведения см. в статье [Создание кластера HDInsight с помощью ESP](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp).
1. Выберите **включить брокер идентификаторов HDInsight**.

Компонент брокера ID добавит в кластер одну лишнюю виртуальную машину. Эта виртуальная машина является узлом брокера ИДЕНТИФИКАТОРов и включает серверные компоненты для поддержки проверки подлинности. Узел брокера ИДЕНТИФИКАТОРов присоединен к домену Azure AD DS.

![Параметр для включения брокера ИДЕНТИФИКАТОРов](./media/identity-broker/identity-broker-enable.png)

### <a name="using-azure-resource-manager-templates"></a>Использование шаблонов диспетчера ресурсов Azure
При добавлении новой роли `idbrokernode` с именем со следующими атрибутами в профиль вычислений шаблона будет создан кластер с включенным УЗЛОМ ID Broker:

```json
.
.
.
"computeProfile": {
    "roles": [
        {
            "autoscale": null,
            "name": "headnode",
           ....
        },
        {
            "autoscale": null,
            "name": "workernode",
            ....
        },
        {
            "autoscale": null,
            "name": "idbrokernode",
            "targetInstanceCount": 1,
            "hardwareProfile": {
                "vmSize": "Standard_A2_V2"
            },
            "virtualNetworkProfile": {
                "id": "string",
                "subnet": "string"
            },
            "scriptActions": [],
            "dataDisksGroups": null
        }
    ]
}
.
.
.
```

## <a name="tool-integration"></a>Интеграция средств

[Подключаемый модуль HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-intellij-tool-plugin#integrate-with-hdinsight-identity-broker-hib) обновлен для поддержки OAuth. Этот подключаемый модуль можно использовать для подключения к кластеру и отправки заданий.

Вы также можете использовать [Spark & средства Hive для VS Code](https://docs.microsoft.com/azure/hdinsight/hdinsight-for-vscode) , чтобы использовать записную книжку и отправлять задания.

## <a name="ssh-access-without-a-password-hash-in-azure-ad-ds"></a>Доступ по протоколу SSH без хэша пароля в Azure AD DS

После включения брокера ИДЕНТИФИКАТОРов вам по-прежнему потребуется хэш пароля, хранящийся в AD DS Azure для сценариев SSH с учетными записями домена. Для подключения SSH к виртуальной машине, присоединенной к домену, или для выполнения `kinit` команды необходимо указать пароль. 

Для аутентификации SSH требуется, чтобы хэш был доступен в AD DS Azure. Если вы хотите использовать SSH только в административных сценариях, можно создать одну облачную учетную запись и использовать ее для подключения к кластеру по протоколу SSH. Другие пользователи по-прежнему могут использовать средства Ambari или HDInsight (например, подключаемый модуль IntelliJ) без использования хэша пароля в Azure AD DS.

## <a name="clients-using-oauth-to-connect-to-hdinsight-gateway-with-id-broker-setup"></a>Клиенты, использующие OAuth для подключения к шлюзу HDInsight с помощью программы установки брокера ID

В программе установки компонента Service Broker можно обновить пользовательские приложения и клиенты, подключающиеся к шлюзу, чтобы сначала получить требуемый маркер OAuth. Вы можете выполнить действия, описанные в этом [документе](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app) , чтобы получить маркер со следующими сведениями:

*   URI ресурса OAuth:`https://hib.azurehdinsight.net` 
* AppId: 7865c1d2-F040-46cc-875f-831a1ef6a28a
*   Разрешение: (имя: Cluster. ReadWrite, идентификатор: 8f89faa0-ffef-4007-974d-4989b39ad77d)

## <a name="next-steps"></a>Дальнейшие шаги

* [Настройка кластера HDInsight с Корпоративный пакет безопасности с помощью доменных служб Azure Active Directory](apache-domain-joined-configure-using-azure-adds.md)
* [Синхронизация пользователей Azure Active Directory с кластером HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
* [Мониторинг производительности кластера](../hdinsight-key-scenarios-to-monitor.md)
