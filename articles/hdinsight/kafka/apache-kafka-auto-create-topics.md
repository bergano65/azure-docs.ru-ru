---
title: Включение автоматического создания разделов в Apache Kafka — Azure HDInsight
description: Узнайте, как настроить автоматическое создание разделов в Apache Kafka в HDInsight. В Kafka вы можете указать для параметра auto.create.topics.enable значение true, используя Ambari или создавая кластер с помощью PowerShell или шаблонов Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 7ec7d15806927306b12624962facbafddf2ce08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242367"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Настройка автоматического создания разделов в Apache Kafka в HDInsight

По умолчанию [Apache Kafka](https://kafka.apache.org/) на HDInsight не позволяет автоматически создавать тему. Но вы можете включить автоматическое создание разделов для существующих кластеров с помощью [Apache Ambari](https://ambari.apache.org/). Также автоматическое создание разделов можно включить при создании нового кластера Kafka с помощью шаблона Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Веб-интерфейс Apache Ambari

Чтобы включить автоматическое создание разделов в существующем кластере через пользовательский веб-интерфейс Ambari, выполните следующие действия:

1. На [портале Azure](https://portal.azure.com)выберите кластер Kafka.

1. Из **панелей мониторинга кластера,** выберите **Ambari домой**.

    ![Изображение страницы портала с выбранной панелью мониторинга кластера](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Когда появится запрос, пройдите проверку подлинности, используя учетные данные (администратора) входа для кластера. Кроме того, вы можете подключиться `https://CLUSTERNAME.azurehdinsight.net/` к `CLUSTERNAME` Amabri прямо от того, где это имя вашего кластера Кафка.

1. В списке в левой части страницы выберите службу Kafka.

    ![Вкладка списка услуг Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Выберите "Конфигурации" в середине страницы.

    ![Apache Ambari сервис конфигурации вкладка](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. В поле "Фильтр" введите значение параметра `auto.create`.

    ![Поле поискового фильтра Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Будет отфильтрован список свойств и отобразится параметр `auto.create.topics.enable`.

1. Изменение значения `auto.create.topics.enable` на, `true`а затем выберите **Сохранить**. Добавить заметку, а затем выберите **Сохранить** снова.

    ![Изображение ввода значения для параметра auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Выберите службу Kafka и щелкните __Перезапустить__, а затем выберите __Restart all affected__ (Перезапустить все затронутые). При запросе выберите __Подтвердите перезапуск всех__.

    ![Apache Ambari перезагрузить все пострадавших](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Можно также задать значения Ambari с помощью REST API Ambari. Это, как правило, сложнее, так как вам придется сделать несколько вызовов REST, чтобы получить текущую конфигурацию, изменить ее и т.д. Для получения дополнительной информации [см. кластеры Manage HDInsight с помощью api Apache Ambari REST.](../hdinsight-hadoop-manage-ambari-rest-api.md)

## <a name="resource-manager-templates"></a>Шаблоны Resource Manager

При создании кластера Kafka с помощью шаблона Azure Resource Manager вы можете напрямую задать значение параметра `auto.create.topics.enable`, добавив его в `kafka-broker`. В следующем фрагменте JSON для этого параметра устанавливается значение `true`:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Next Steps

Из этого документа вы узнали, как включить автоматическое создание разделов для Apache Kafka в HDInsight. Дополнительные сведения о работе с Kafka вы можете получить по следующим ссылкам.

* [Анализ журналов для Apache Kafka в HDInsight](apache-kafka-log-analytics-operations-management.md)
* [Репликация данных между кластерами Apache Kafka](apache-kafka-mirroring.md)
