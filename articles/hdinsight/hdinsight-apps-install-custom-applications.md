---
title: Установите пользовательские приложения Apache Hadoop на Azure HDInsight
description: Узнайте, как установить приложения HDInsight для кластеров Apache Hadoop в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: b25e9d860f118c1b044b13e01a80aaf7a24963cf
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768246"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Установка пользовательских приложений Apache Hadoop в Azure HDInsight

В этой статье вы узнаете, как установить приложение [Apache Hadoop](https://hadoop.apache.org/) на Azure HDInsight, которое не было опубликовано на портале Azure. Приложение, которое вы установите в этой [статье, является оттенком](https://gethue.com/).

Пользователи могут устанавливать приложения HDInsight в кластере HDInsight.  Разработчиками этих приложений могут быть корпорация Майкрософт, независимые поставщики программного обеспечения или вы сами.  

## <a name="prerequisites"></a>Предварительные требования

Если вы хотите установить приложение HDInsight в существующем кластере HDInsight, вы должны создать кластер HDInsight. Инструкции по созданию кластера см. в [этом разделе](hadoop/apache-hadoop-linux-tutorial-get-started.md). Вы также можете установить приложения HDInsight во время создания кластера HDInsight.

## <a name="install-hdinsight-applications"></a>Установка приложений HDInsight

Приложения HDInsight можно устанавливать во время создания кластера или в уже существующем кластере HDInsight. Инструкции по определению шаблонов Azure Resource Manager см. в статье [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (Установка приложения HDInsight) на сайте MSDN.

Далее перечислены файлы, необходимые для развертывания этого приложения (Hue).

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json) — шаблон Resource Manager для установки приложения HDInsight. Инструкции по разработке собственного шаблона Resource Manager см. в статье [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (Установка приложения HDInsight) на сайте MSDN.
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh) — действие скрипта, вызываемое шаблоном Resource Manager для настройки граничного узла.
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz) — двоичный файл hue, вызываемый из hui-install_v0.sh.
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz) — двоичный файл hue, вызываемый из hui-install_v0.sh.
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz) — пример веб-приложения (Tomcat), вызываемый из hui-install_v0.sh.

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>Установка приложения Hue в существующем кластере HDInsight

1. Выберите следующее изображение для вхува в Azure и откройте шаблон менеджера ресурсов на портале Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Шаблон «Менеджер ресурсов» [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)расположен по адресу .  Дополнительные сведения о создании шаблона Resource Manager см. в статье [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (Установка приложения HDInsight) на сайте MSDN.

1. Выберите существующую **группу ресурсов,** содержащую кластер из списка выпадающих. Требуется использовать ту же группу ресурсов, что и кластер.

1. Имя кластера, в котором вы хотите установить приложение. Это должен быть существующий кластер.

1. Выберите флажок, **ибо я согласен с условиями, указанными выше.**

1. Щелкните **Приобрести**.

Состояние установки можно наблюдать на плитке, закрепленной на панели мониторинга, и в уведомлении портала (щелкните значок колокольчика в верхней части портала).  Установка приложения занимает около 10 минут.

### <a name="to-install-hue-while-creating-a-cluster"></a>Установка приложения Hue во время создания кластера

1. Выберите следующее изображение для вхува в Azure и откройте шаблон менеджера ресурсов на портале Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Шаблон «Менеджер ресурсов» [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)расположен по адресу .  Дополнительные сведения о создании шаблона Resource Manager см. в статье [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (Установка приложения HDInsight) на сайте MSDN.

2. Выполните инструкции по созданию кластера и установите приложение Hue. Дополнительные сведения о создании кластеров HDInsight см. в статье [Создание кластеров Hadoop под управлением Linux в HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="other-installation-methods"></a>Другие методы установки

Вызывать шаблоны Resource Manager можно с помощью портала Azure, а также [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) и [интерфейса командной строки Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli).

## <a name="validate-the-installation"></a>проверка установки

Вы можете просмотреть состояние приложения на портале Azure, чтобы проверить установку приложения. Кроме того, вы также можете проверить все конечные точки HTTP подошел, как ожидалось, и веб-страницы, если есть один.

Для **Hue**вы можете использовать следующие действия:

### <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите кластер, в котором было установлено приложение.
1. Из меню **«Настройки»** выберите **приложения.**
1. Выберите **оттенок** из списка для просмотра свойств.  
1. Выберите ссылку на веб-страницу для проверки веб-сайта.

### <a name="azure-cli"></a>Azure CLI

`CLUSTERNAME`Замените, `RESOURCEGROUP` и с соответствующими значениями, а затем ввести команды ниже:

* Для перечисления всех приложений для кластера HDInsight.

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* Для получения свойств указанного приложения.

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>Устранение неполадок, связанных с установкой

Состояние установки приложения можно наблюдать в уведомлении портала (щелкните значок колокольчика в верхней части портала).

Если установка приложения не удалась, можно увидеть сообщения об ошибках и отладку информации из трех мест:

* Приложения HDInsight: общие сведения об ошибке.

    Откройте кластер с портала и выберите приложения из настроек:

    ![приложения hdinsight приложение ошибка установки](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png)

* Действие сценария HDInsight: если сообщение об ошибке приложений HDInsight указывает на сбой действия сценария, дополнительные сведения о сбое сценария отобразятся на панели действий сценария.

    Выберите действие сценария из настроек. Журнал действий сценария отображает сообщения об ошибках.

    ![приложения hdinsight ошибка действия сценария](./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png)

* UI Apache Ambari Web: Если скрипт установки стал причиной сбоя, используйте веб-чат Ambari для проверки полных журналов об установке скриптов.

    Для получения дополнительной [Troubleshoot script actions](./troubleshoot-script-action.md)информации см.

## <a name="remove-hdinsight-applications"></a>Удаление приложений HDInsight

### <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите кластер, в котором было установлено приложение.
1. Из меню **«Настройки»** выберите **приложения.**
1. Нажмите правой кнопкой мыши приложение, которое вы хотите удалить, а затем выберите **Удалить**.
1. Выберите **Да** для подтверждения.

### <a name="azure-cli"></a>Azure CLI

`NAME`Заменить `CLUSTERNAME`, `RESOURCEGROUP` и с соответствующими значениями, а затем ввести команду ниже:

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>Следующие шаги

* [Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(Установка приложения HDInsight) — узнайте, как разрабатывать шаблоны Resource Manager для развертывания приложений HDInsight.
* [Установка приложений HDInsight](hdinsight-apps-install-applications.md)— узнайте, как устанавливать в кластер приложения HDInsight.
* [Публикация приложений HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md)— узнайте, как опубликовать пользовательские приложения HDInsight в Azure Marketplace.
* [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md)— узнайте, как использовать действие скрипта для установки дополнительных приложений.
* [Создание кластеров Apache Hadoop под управлением Linux в HDInsight с помощью шаблонов Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) — узнайте, как вызывать шаблоны Resource Manager для создания кластеров HDInsight.
* [Использование пустых граничных узлов в HDInsight](hdinsight-apps-use-edge-node.md)— узнайте, как использовать пустой граничный узел для доступа к кластеру HDInsight, а также тестирования и размещения приложений HDInsight.
