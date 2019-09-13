---
title: Использование пустых граничных узлов в кластерах Apache Hadoop в HDInsight в Azure
description: Сведения о том, как добавить пустой граничный узел в кластер HDInsight, который может использоваться в качестве клиента, а затем тестировать и разместить приложения HDInsight.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: hrasheed
ms.openlocfilehash: 31cffc71937f7be158da8cbbd1d8abe6e3fe8372
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70916652"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Использование пустых граничных узлов в кластерах Apache Hadoop в HDInsight

Сведения о том, как добавить пустой граничный узел в кластер HDInsight. Пустой граничный узел — это виртуальная машина Linux, где установлены и настроены те же клиентские средства, что и на головных узлах, но без служб [Apache Hadoop](https://hadoop.apache.org/). Граничный узел можно использовать для доступа к кластеру, а также тестирования и размещения клиентских приложений. 

Пустой граничный узел можно добавить в имеющийся кластер HDInsight или в новый кластер при его создании. Добавление пустого граничного узла осуществляется с помощью шаблона Azure Resource Manager.  В следующем примере показано, как это сделать:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Как показано в примере, при необходимости можно вызвать [действие скрипта](hdinsight-hadoop-customize-cluster-linux.md), чтобы выполнить дополнительную конфигурацию, такую как установка [Apache Hue](hdinsight-hadoop-hue-linux.md) на граничный узел. Скрипт действий скрипта должен быть общедоступен через Интернет.  Например, если скрипт хранится в службе хранилища Azure, используйте общедоступные контейнеры или общедоступные большие двоичные объекты.

Размер виртуальной машины граничного узла должен соответствовать требованиям к размеру виртуальной машины рабочего узла кластера HDInsight. Рекомендуемые размеры виртуальной машины рабочего узла приведены в разделе о [создании кластеров Apache Hadoop в HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

После создания граничного узла можно подключиться к нему с помощью SSH и запустить клиентские средства для доступа к кластеру Hadoop в HDInsight.

> [!WARNING]   
> Пользовательские компоненты, установленные на граничном узле, получают коммерчески оправданную поддержку от компании Майкрософт. В результате могут быть устранены возникающие проблемы. Или вы можете обратиться к ресурсам сообщества для получения дополнительной помощи. Ниже приведены некоторые из наиболее активных сайтов для получения помощи от сообщества.
>
> * [Форум MSDN для HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> При использовании технологии Apache можно получить помощь на соответствующих сайтах для проектов Apache, например [Apache Hadoop](https://hadoop.apache.org/) на сайте [https://apache.org](https://apache.org).

> [!IMPORTANT]
> Образы Ubuntu станут доступны для создания кластера HDInsight в течение 3 месяцев после публикации. По состоянию на январь 2019 года работающие кластеры (включая граничные узлы) **не** были автоматически исправлены. Клиенты должны использовать действия сценария или другие механизмы для исправления работающего кластера.  Дополнительные сведения см. в статье [Обновление путем частичной замены ОС для кластеров HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Добавление граничного узла в имеющийся кластер
В этом разделе описано, как использовать шаблон Resource Manager, чтобы добавить граничный узел в имеющийся кластер HDInsight.  Шаблон Resource Manager можно найти в [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Шаблон диспетчера ресурсов вызывает действие скрипта, расположенное по адресу https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh. Этот скрипт не выполняет никаких действий.  Он необходим для демонстрации вызова действия скрипта с помощью шаблона Resource Manager.

**Добавление граничного узла в имеющийся кластер**

1. Нажмите следующую кнопку, чтобы войти в Azure и открыть шаблон Azure Resource Manager на портале Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>
3. Настройте следующие свойства:
   
   * **Подписка**: Выберите подписку Azure, используемую для создания кластера.
   * **Группа ресурсов.** Выберите группу ресурсов, используемую для имеющегося кластера HDInsight.
   * **Расположение.** Выберите расположение для имеющегося кластера HDInsight.
   * **Имя кластера**. Введите имя имеющегося кластера HDInsight.
   * **Размер граничного узла**. Выберите один из размеров виртуальной машины. Размер виртуальной машины должны соответствовать требованиям к размеру виртуальной машины рабочего узла. Рекомендуемые размеры виртуальной машины рабочего узла приведены в разделе о [создании кластеров Apache Hadoop в HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Префикс граничного узла**. По умолчанию используется значение **new**.  Имя граничного узла по умолчанию — **new-edgenode**.  Префикс можно настроить на портале. В шаблоне также можно настроить полное имя.

4. Установите флажок **Я принимаю указанные выше условия** и нажмите кнопку **Приобрести**, чтобы создать граничный узел.

>[!IMPORTANT]  
> Выберите группу ресурсов Azure, используемую для существующего кластера HDInsight.  Иначе вы получите сообщение об ошибке "Не удается выполнить запрошенную операцию с вложенным ресурсом. Родительский ресурс &lt;ClusterName> не найден."

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Добавление граничного узла при создании кластера
В этом разделе описано, как использовать шаблон Resource Manager, чтобы создать кластер HDInsight с граничным узлом.  Шаблон диспетчер ресурсов можно найти в [коллекции шаблонов](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)быстрого запуска Azure. Шаблон диспетчера ресурсов вызывает действие скрипта, расположенное по адресу https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh. Этот скрипт не выполняет никаких действий.  Он необходим для демонстрации вызова действия скрипта с помощью шаблона Resource Manager.

**Создание кластера HDInsight с использованием граничного узла**

1. Создайте кластер HDInsight, если его еще нет.  Ознакомьтесь со статьей [Руководство по Hadoop. Приступая к работе с Hadoop в HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Нажмите следующую кнопку, чтобы войти в Azure и открыть шаблон Azure Resource Manager на портале Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>
3. Настройте следующие свойства:
   
   * **Подписка**: Выберите подписку Azure, используемую для создания кластера.
   * **Группа ресурсов.** Создайте группу ресурсов для кластера.
   * **Расположение.** Выберите расположение группы ресурсов.
   * **Имя кластера**. Введите имя для нового кластера.
   * **Имя пользователя для входа в кластер.** Введите имя пользователя HTTP для Hadoop.  Имя по умолчанию — **admin**.
   * **Пароль пользователя для входа в кластер.** Введите пароль пользователя HTTP для Hadoop.
   * **Имя пользователя SSH**. Введите имя пользователя SSH. Имя по умолчанию — **sshuser**.
   * **Пароль SSH**. Введите пароль пользователя SSH.
   * **Установка действия скрипта**. Примите значение по умолчанию, чтобы перейти к этой статье.
     
     Некоторые свойства жестко заданы в шаблоне: тип кластера, число рабочих узлов кластера, размер граничного узла и имя граничного узла.
4. Установите флажок **Я принимаю указанные выше условия** и нажмите кнопку **Приобрести**, чтобы создать кластер с граничным узлом.

## <a name="add-multiple-edge-nodes"></a>Добавление нескольких граничных узлов

В кластер HDInsight можно добавить несколько граничных узлов.  Настроить конфигурацию нескольких граничных узлов можно только с помощью шаблонов Azure Resource Manager.  Пример шаблона приведен в начале этой статьи.  Для параметра **targetInstanceCount** необходимо задать значение, соответствующее количеству создаваемых граничных узлов.

## <a name="access-an-edge-node"></a>Доступ к граничному узлу
Конечная точка SSH граничного узла указывается в формате &lt;имя_граничного узла>.&lt;имя_кластера>-ssh.azurehdinsight.net:22.  Например, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Граничный узел отображается в виде приложения на портале Azure.  На портале представлены сведения для доступа к граничному узлу с помощью SSH.

**Проверка конечной точки SSH граничного узла**

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Откройте кластер HDInsight с граничным узлом.
3. Щелкните **Приложения**. Затем вы увидите граничный узел.  Его имя по умолчанию — **new-edgenode**.
4. Щелкните граничный узел. Отобразится конечная точка SSH.

**Использование Hive в граничном узле**

1. К граничному узлу можно подключиться с помощью SSH. См. дополнительные сведения об [использовании SSH в HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. После подключения к граничному узлу с помощью SSH откройте консоль Hive, используя следующую команду:
   
        hive
3. Чтобы показать таблицы Hive в кластере, выполните следующую команду:
   
        show tables;

## <a name="delete-an-edge-node"></a>Удаление граничного узла
Граничный узел можно удалить на портале Azure.

**Доступ к граничному узлу**

1. Выполните вход на [портал Azure](https://portal.azure.com).
2. Откройте кластер HDInsight с граничным узлом.
3. Щелкните **Приложения**. Появится список граничных узлов.  
4. Щелкните правой кнопкой мыши граничный узел, который нужно удалить, и выберите пункт **Удалить**.
5. Нажмите кнопку **Да** для подтверждения.

## <a name="next-steps"></a>Следующие шаги
В этой статье вы узнали, как добавить граничный узел и как получить к нему доступ. Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Установка приложений HDInsight](hdinsight-apps-install-applications.md). Узнайте, как установить приложения HDInsight в кластерах.
* [Установка пользовательских приложений HDInsight](hdinsight-apps-install-custom-applications.md)— узнайте, как развернуть в HDInsight неопубликованное приложение HDInsight.
* [Публикация приложения HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md). Узнайте, как опубликовать пользовательские приложения HDInsight в Microsoft Azure Marketplace.
* [MSDN. Application](https://msdn.microsoft.com/library/mt706515.aspx) (Приложение). Узнайте, как определить приложения HDInsight.
* [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md)— узнайте, как использовать действие скрипта для установки дополнительных приложений.
* [Создание кластеров Apache Hadoop под управлением Linux в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) — узнайте, как вызывать шаблоны Resource Manager для создания кластеров HDInsight.

