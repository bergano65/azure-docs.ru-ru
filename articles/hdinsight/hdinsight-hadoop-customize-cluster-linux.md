---
title: Настройка кластеров Azure HDInsight с помощью действий сценария
description: Добавление пользовательских компонентов в кластеры HDInsight с помощью действий сценария. Действия скрипта — это скрипты bash, которые можно использовать для настройки конфигурации кластера. Или добавьте дополнительные службы и служебные программы, такие как оттенок, Solr или R.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: f78157fc0873787ce13ed4e9e62ebfd3d3271d5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82192082"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>Настройка кластеров Azure HDInsight с помощью действий сценария

Azure HDInsight предоставляет метод настройки, называемый **действиями сценария** , которые вызывают пользовательские сценарии для настройки кластера. Эти скрипты используются для установки дополнительных компонентов и изменения параметров конфигурации. Действия скрипта можно использовать во время или после создания кластера.

Действия сценариев можно опубликовать в Azure Marketplace как приложение HDInsight. Дополнительные сведения о приложениях HDInsight см. в статье [Публикация приложения HDInsight в Azure Marketplace](hdinsight-apps-publish-applications.md).

## <a name="permissions"></a>Разрешения

Для присоединенного к домену кластера HDInsight для выполнения действий сценариев необходимо задать два следующих разрешения Apache Ambari:

* **AMBARI. ВЫПОЛНИТЕ\_пользовательскую\_команду**. Это разрешение по умолчанию назначено для роли администратора Ambari.
* **Кластер. ВЫПОЛНИТЕ\_пользовательскую\_команду**. Это разрешение по умолчанию назначено для администратора кластера HDInsight и администратора Ambari.

Дополнительные сведения о работе с разрешениями в присоединенном к домену кластере HDInsight см. в статье [Управление кластерами HDInsight с помощью корпоративного пакета безопасности](./domain-joined/apache-domain-joined-manage.md).

## <a name="access-control"></a>Управление доступом

Если вы не администратор или владелец подписки Azure, ваша учетная запись должна иметь доступ к группе ресурсов, содержащей кластер HDInsight, по крайней мере с правами участника.

Пользователь, имеющий по крайней мере права участника на подписку Azure, должен предварительно зарегистрировать поставщик. Регистрация поставщика происходит, когда пользователь с правами участника на подписку создает ресурс. Если вы не создаете ресурс, см. статью [Регистрация поставщика с помощью функции RESTful](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Дополнительные сведения об управлении доступом:

* [Начало работы с управлением доступом на портале Azure](../role-based-access-control/overview.md)
* [Использование назначений ролей для управления доступом к ресурсам в подписке Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>Общие сведения о действиях сценариев

Действие сценария — это сценарий Bash, который выполняется на узлах в кластере HDInsight. Характеристики и возможности действий сценариев приведены ниже.

* Они должны храниться в универсальном коде ресурса (URI), доступном из кластера HDInsight. Возможные места хранения:

    * Для обычных кластеров:

      * ADLS 1-го поколения: служба HDInsight субъекта-службы использует для доступа к Data Lake Storage должен иметь доступ для чтения к скрипту. URI для скриптов, находящихся в Data Lake Storage 1-го поколения, имеет формат `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`.

      * Большой двоичный объект в учетной записи хранения Azure, которая служит основной или дополнительной учетной записью хранения для кластера HDInsight. При создании кластера HDInsight получает доступ к обоим типам учетных записей хранения.

        > [!IMPORTANT]  
        > Не поворачивайте ключ хранилища в этой учетной записи хранения Azure, так как это приведет к последующим действиям сценария со скриптами, сохраненными в этом случае.

      * Общедоступная служба общего доступа к файлам, доступная через пути http://. Примеры: BLOB-объект Azure, GitHub, OneDrive. Примеры URI см. в разделе [Пример сценариев действий сценария](#example-script-action-scripts).

     * Для кластеров с ESP поддерживаются wasb://или wasbs://или HTTP [s]://URI.

* Действия можно ограничить выполнением только на определенных типах узлов, например головных или рабочих.

* Может быть сохранен или `ad hoc`.

    Действия сохраняемых скриптов должны иметь уникальные имена. Сохраняемые сценарии применяются при настройке новых рабочих узлов, добавленных в кластер при операциях масштабирования. Сохраняемый сценарий может также применять изменения к узлу другого типа при операциях масштабирования. Примером является головной узел.

    `Ad hoc`скрипты не сохраняются. Действия скриптов, используемые при создании кластера, автоматически сохраняются. Они не применяются на рабочих узлах, добавленных в кластер после выполнения сценария. Затем можно повысить уровень `ad hoc` скрипта до сохраненного скрипта или понизить сохраненный скрипт до `ad hoc` скрипта. Сценарии, в которых произошла ошибка, не сохраняются, даже если вы отдельно указали, что они должны быть сохранены.

* Действия сценария могут принимать параметры, используемые в процессе выполнения сценария.

* Они выполняются с помощью прав привилегированного пользователя на узлах кластера.

* Можно использовать с помощью пакета SDK .NET для портал Azure, Azure PowerShell, Azure CLI или HDInsight.

В кластере ведется журнал всех сценариев, которые в нем выполнялись. Это позволяет определить идентификатор сценария для операций повышения или понижения уровня сценария.

> [!IMPORTANT]  
> Не существует способа автоматического отката изменений, внесенных действием сценария. Отмените изменения вручную или предоставьте скрипт для их отмены.

### <a name="script-action-in-the-cluster-creation-process"></a>Действие сценария в процессе создания кластера

Действия сценариев, используемые в процессе создания кластера и выполняющиеся в имеющемся кластере, немного отличаются:

* Сценарий сохраняется автоматически.

* Ошибка в сценарии может вызвать сбой создания кластера.

На следующей схеме показано, когда выполняется действие сценария в процессе создания:

![Настройка кластера HDInsight и этапы создания кластера][img-hdi-cluster-states]

Скрипт выполняется во время настройки HDInsight. Сценарий выполняется параллельно на всех указанных узлах кластера. Он выполняется с правами привилегированного пользователя на узлах.

Вы можете выполнять такие операции, как остановка и запуск служб, включая службы, связанные с Apache Hadoop. Если вы останавливаете службы, убедитесь, что Ambari и другие службы, связанные с Hadoop, выполняются до завершения сценария. Эти необходимые службы определяют работоспособность и состояние кластера во время его создания.

При создании кластера можно использовать множество действий сценариев одновременно. Они будут вызываться в том порядке, в котором были указаны.

> [!IMPORTANT]  
> Действия сценария должны заканчиваться в течение 60 минут или истекает время ожидания. Во время подготовки кластера сценарий выполняется параллельно с другими процессами установки и настройки. Конкуренция за ресурсы, такие как ЦП или пропускная способность сети, может привести к затягиванию выполнения сценария по сравнению со временем его выполнения в среде разработки.
>
> Чтобы свести время выполнения сценария к минимуму, избегайте таких задач, как скачивание и компиляция приложений из источника. Выполните предварительную компиляцию приложения и сохраните двоичный файл в службе хранилища Azure.

### <a name="script-action-on-a-running-cluster"></a>Действие скрипта в работающем кластере

Сбой сценария в уже работающем кластере не приводит к автоматическому переходу кластера в состояние сбоя. После завершения сценария кластер должен вернуться в рабочее состояние. Даже если кластер запущен, ошибка сценария может привести к проблемам. К примеру, сценарий может удалить необходимые для работы кластера файлы.

Действия сценариев выполняются с использованием привилегий суперпользователя. Убедитесь, что вы понимаете, что делает сценарий, прежде чем применять его к кластеру.

Когда сценарий применяется в кластере, состояние кластера изменяется с **Выполняется** на **Принято**. Затем оно изменится на **Конфигурация HDInsight** и, наконец, обратно на **Выполняется** для успешного завершения сценария. Состояние сценария регистрируется в журнале действий сценариев. Эти сведения сообщают о том, успешно ли выполнен сценарий. Например, командлет PowerShell `Get-AzHDInsightScriptActionHistory` отображает состояние сценария. Эта команда возвращает следующую информацию:

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> При изменении пользователя кластера, администратора, пароля после создания кластера может происходить сбой при выполнении действий сценариев для этого кластера. Если у вас есть сохраненные действия сценариев, нацеленные на рабочие узлы, они могут завершиться ошибкой при масштабировании кластера.

## <a name="example-script-action-scripts"></a>Пример сценариев действий сценария

Сценарии действия сценария можно использовать с помощью следующих служебных программ:

* Портал Azure
* Azure PowerShell
* Azure CLI
* Пакет SDK для HDInsight .NET

В HDInsight доступны скрипты для установки следующих компонентов в кластерах HDInsight.

| Имя | Сценарий |
| --- | --- |
| добавление учетной записи хранения Azure; |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. Ознакомьтесь со статьей [Добавление дополнительных учетных записей хранения в HDInsight](hdinsight-hadoop-add-storage.md). |
| установка Hue; |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. Ознакомьтесь со статьей [Установка и использование Hue на кластерах HDInsight Hadoop](hdinsight-hadoop-hue-linux.md). |
| Предварительная загрузка библиотек Hive |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. Ознакомьтесь со статьей [Добавление пользовательских библиотек Apache Hive при создании кластера HDInsight](hdinsight-hadoop-add-hive-libraries.md). |

## <a name="script-action-during-cluster-creation"></a>Действие сценария во время создания кластера

В этом разделе объясняются различные способы использования действий сценариев при создании кластера HDInsight.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>Использование действия сценария при создании кластера с портала Azure

1. Начните создавать кластер, как описано в статье [Создание кластеров под управлением Linux в HDInsight с помощью портал Azure](hdinsight-hadoop-create-linux-clusters-portal.md). На вкладке **Настройка и цены** выберите **+ Добавить действие скрипта**.

    ![Действие сценария кластера портал Azure](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. Используйте запись __Выберите сценарий__, чтобы выбрать готовый сценарий. Чтобы использовать настраиваемый сценарий, выберите __Настраиваемый__. Затем укажите __имя__ и __универсальный код ресурса (URI) Bash-сценария__ своего сценария.

    ![Добавление скрипта в форме выбора скрипта](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    В приведенной ниже таблице описываются элементы формы.

    | Свойство | Значение |
    | --- | --- |
    | Выберите скрипт | Чтобы использовать собственный скрипт, выберите __Настраиваемый__. В противном случае выберите один из предоставленных скриптов. |
    | Имя |Укажите имя для действия сценария. |
    | URI bash-скрипта |Укажите URI сценария. |
    | Головной/рабочий/ZooKeeper |Укажите узлы, на которых выполняется скрипт: **head**, **Worker**или **ZooKeeper**. |
    | Параметры |Укажите параметры, если они требуются для сценария. |

    Используйте запись __Сохранить это действие сценария__, чтобы сценарий применялся при масштабировании.

1. Чтобы сохранить скрипт, нажмите кнопку __Создать__. Чтобы добавить еще один сценарий, вы можете использовать элемент __+ Submit new__ (+ Отправить новый).

    ![Действия нескольких скриптов HDInsight](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    По завершении добавления скриптов вы вернетесь на вкладку **Настройка и цены** .

1. Выполните оставшиеся шаги по созданию кластера, как обычно.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Использование действия сценария на основе шаблонов Azure Resource Manager

Действия сценария можно использовать с шаблонами Azure Resource Manager. С примером ознакомьтесь в статье [Create HDInsight Linux Cluster and run a script action](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/) (Создание кластера HDInsight на платформе Linux и выполнение действия сценария).

В этом примере добавляется действие сценария с помощью следующего кода:

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

Получите дополнительные сведения о развертывании шаблона:

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>Использование действия сценария при создании кластера с помощью Azure PowerShell

В этом разделе описывается использование командлета [Add-аздинсигхтскриптактион](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) для вызова сценариев для настройки кластера. Перед началом работы убедитесь, что установили и настроили Azure PowerShell. Для использования этих команд PowerShell необходим [модуль AZ](https://docs.microsoft.com/powershell/azure/overview).

Следующий сценарий демонстрирует применение действия сценария при создании кластера с помощью PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

Создание кластера может занять несколько минут.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>Использование действия сценария при создании кластера с помощью пакета SDK HDInsight для .NET

Пакет SDK HDInsight для .NET предоставляет клиентские библиотеки, которые упрощают работу с кластерами HDInsight из приложения .NET. Пример кода см. в разделе [действия скрипта](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet#script-actions).

## <a name="script-action-to-a-running-cluster"></a>Действие скрипта в работающем кластере

В этом разделе объясняется, как применять действия сценариев к работающему кластеру.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Применение действия сценария в работающем кластере с портала Azure

1. Войдите в [портал Azure](https://portal.azure.com) и нахождение своего кластера.

1. В представлении по умолчанию в разделе **Параметры** выберите **Действия скрипта**.

1. В верхней части страницы **Действия скрипта** выберите **+ Отправить новое**.

    ![Добавление скрипта в работающий кластер](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. Используйте запись __Выберите сценарий__, чтобы выбрать готовый сценарий. Чтобы использовать настраиваемый сценарий, выберите __Настраиваемый__. Затем укажите __имя__ и __универсальный код ресурса (URI) Bash-сценария__ своего сценария.

    ![Добавление скрипта в форме выбора скрипта](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    В приведенной ниже таблице описываются элементы формы.

    | Свойство | Значение |
    | --- | --- |
    | Выберите скрипт | Чтобы использовать собственный скрипт, выберите __Пользовательский__. В противном случае выберите предоставленный скрипт. |
    | Имя |Укажите имя для действия сценария. |
    | URI bash-скрипта |Укажите URI сценария. |
    | Головной, рабочий или Zookeeper |Укажите узлы, на которых выполняется скрипт: **head**, **Worker**или **ZooKeeper**. |
    | Параметры |Укажите параметры, если они требуются для сценария. |

    Используйте запись __Сохранить это действие скрипта__, чтобы скрипт применялся при масштабировании.

1. Наконец, нажмите кнопку **Создать**, чтобы применить сценарий к кластеру.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Применение действия сценария в работающем кластере с помощью Azure PowerShell

Для использования этих команд PowerShell необходим [модуль AZ](https://docs.microsoft.com/powershell/azure/overview). Следующий пример демонстрирует применение действия сценария к работающему кластеру:

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

После завершения операции вы получите приблизительно такой текст:

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Применение действия сценария в работающем кластере с помощью Azure CLI

Перед началом работы убедитесь, что установили и настроили Azure CLI. Убедитесь, что у вас установлена последняя версия. Дополнительные сведения см. в статье [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Пройдите аутентификацию в подписке Azure.

    ```azurecli
    az login
    ```

1. Примените действие сценария в работающем кластере.

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    Допустимые роли `headnode`: `workernode`, `zookeepernode`, `edgenode`,. Если скрипт должен применяться к нескольким типам узлов, разделяйте роли пробелами. Например, `--roles headnode workernode`.

    Чтобы сохранить сценарий, добавьте `--persist-on-success`. Чтобы сохранить скрипт позднее, используйте `az hdinsight script-action promote`.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>Применение действия сценария в работающем кластере с помощью REST API

Ознакомьтесь со статьей [Cluster REST API in Azure HDInsight](https://msdn.microsoft.com/library/azure/mt668441.aspx) (REST API кластера в Azure HDInsight).

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>Применение действия сценария в работающем кластере с помощью пакета SDK HDInsight для .NET

Пример использования пакета SDK для .NET для применения сценариев к кластеру см. в статье [Apply a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) (Применение действия сценариев к работающему кластеру HDInsight на основе Linux).

## <a name="view-history-and-promote-and-demote-script-actions"></a>Представление журнала, повышение уровня и изменение типа действий сценариев

### <a name="the-azure-portal"></a>Портал Azure

1. Войдите в [портал Azure](https://portal.azure.com) и нахождение своего кластера.

1. В представлении по умолчанию в разделе **Параметры** выберите **Действия скрипта**.

1. Журнал сценариев для этого кластера отображается в разделе "Действия сценария". Эти сведения включают в себя список сохраняемых скриптов. На следующем снимке экрана показано, что в этом кластере был выполнен сценарий Solr. Сохраняемых сценариев на нем не видно.

    ![Журнал отправки действий сценария портала](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. При выборе сценария в журнале отображается соответствующий раздел **Панель свойств**. В верхней части экрана можно повторно запустить скрипт или изменить его тип.

    ![Продвижение свойств действий скрипта](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. Можно также нажать кнопку с многоточием ( **...**) справа от элемента записи в разделе действия скрипта, чтобы выполнить действия.

    ![Удаление сохраненных действий сценария](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| командлет | Функция |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |Получение сведений о действиях сохраняемого сценария Этот командлет не отменяет действия, выполняемые сценарием, он удаляет только флаг PERSISTED.|
| `Get-AzHDInsightScriptActionHistory` |Получение журнала действий сценариев, применяемых в кластере, или сведений о конкретном сценарии. |
| `Set-AzHDInsightPersistedScriptAction` |Повышение уровня `ad hoc` действия скрипта до сохраненного действия скрипта. |
| `Remove-AzHDInsightPersistedScriptAction` |Понизить сохраненное действие скрипта до `ad hoc` действия. |

В приведенном ниже примере сценария показано использование командлетов для повышения уровня типа сценария (и наоборот).

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| Get-Help | Описание |
| --- | --- |
| [`az hdinsight script-action delete`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-delete) |Удаляет указанное действие сохраненного скрипта в кластере. Эта команда не отменяет действия, выполненные сценарием, но удаляет только флаг PERSISTED.|
|[`az hdinsight script-action execute`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)|Для выполнения действий скриптов в указанном кластере HDInsight.|
| [`az hdinsight script-action list`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list) |Список всех сохраненных действий скрипта для указанного кластера. |
|[`az hdinsight script-action list-execution-history`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-list-execution-history)|Список всех журналов выполнения сценариев для указанного кластера.|
|[`az hdinsight script-action promote`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-promote)|Повышает заданное прямое выполнение скрипта до сохраненного скрипта.|
|[`az hdinsight script-action show-execution-details`](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-show-execution-details)|Возвращает сведения о выполнении скрипта для заданного идентификатора выполнения скрипта.|

### <a name="hdinsight-net-sdk"></a>Пакет SDK для HDInsight .NET

Пример использования пакета SDK для .NET для получения журнала сценариев из кластера, повышения или понижения уровня сценариев см. в статье [Apply a Script Action against a running Linux-based HDInsight cluster](https://github.com/Azure-Samples/hdinsight-dotnet-script-action) (Применение действия сценариев к работающему кластеру HDInsight на основе Linux).

> [!NOTE]  
> В этом примере также показано, как установить приложение HDInsight с помощью пакета SDK для .NET.

## <a name="next-steps"></a>Дальнейшие действия

* [Разработка скриптов действия скрипта для HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Добавление дополнительных учетных записей хранения Azure в HDInsight](hdinsight-hadoop-add-storage.md)
* [Устранение неполадок, связанных с действиями скрипта](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "Этапы создания кластера"
