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
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242367"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Настройка автоматического создания разделов в Apache Kafka в HDInsight

По умолчанию [Apache Kafka](https://kafka.apache.org/) в HDInsight не включает автоматическое создание разделов. Но вы можете включить автоматическое создание разделов для существующих кластеров с помощью [Apache Ambari](https://ambari.apache.org/). Также автоматическое создание разделов можно включить при создании нового кластера Kafka с помощью шаблона Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Веб-интерфейс Apache Ambari

Чтобы включить автоматическое создание разделов в существующем кластере через пользовательский веб-интерфейс Ambari, выполните следующие действия:

1. В [портал Azure](https://portal.azure.com)выберите кластер Kafka.

1. На **панели мониторинга кластера**выберите **Ambari Home**.

    ![Изображение страницы портала с выбранной панелью мониторинга кластера](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Когда появится запрос, пройдите проверку подлинности, используя учетные данные (администратора) входа для кластера. Кроме того, можно подключиться к Амабри напрямую из `https://CLUSTERNAME.azurehdinsight.net/`, где `CLUSTERNAME` — имя кластера Kafka.

1. В списке в левой части страницы выберите службу Kafka.

    ![Вкладка списка служб Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Выберите "Конфигурации" в середине страницы.

    ![Вкладка "конфигурации" службы Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. В поле "Фильтр" введите значение параметра `auto.create`.

    ![Поле фильтра поиска Apache Ambari](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Будет отфильтрован список свойств и отобразится параметр `auto.create.topics.enable`.

1. Измените значение `auto.create.topics.enable` на `true`и нажмите кнопку **сохранить**. Добавьте заметку и выберите **Сохранить** еще раз.

    ![Изображение ввода значения для параметра auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Выберите службу Kafka и щелкните __Перезапустить__, а затем выберите __Restart all affected__ (Перезапустить все затронутые). Когда появится запрос, выберите __Conform Restart All__ (Подтвердить перезапуск всех).

    ![Apache Ambari перезапускает все затронутые](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Можно также задать значения Ambari с помощью REST API Ambari. Обычно это сложнее, так как необходимо сделать несколько вызовов RESTFUL для получения текущей конфигурации, изменить ее и т. д. Дополнительные сведения см. в статье [Управление кластерами HDInsight с помощью Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md) документе.

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

## <a name="next-steps"></a>Следующие шаги

Из этого документа вы узнали, как включить автоматическое создание разделов для Apache Kafka в HDInsight. Дополнительные сведения о работе с Kafka вы можете получить по следующим ссылкам.

* [Анализ журналов для Apache Kafka в HDInsight](apache-kafka-log-analytics-operations-management.md)
* [Репликация данных между кластерами Apache Kafka](apache-kafka-mirroring.md)
