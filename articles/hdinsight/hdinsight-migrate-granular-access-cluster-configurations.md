---
title: Перенос в детализированный доступ на основе ролей для конфигурации кластера — Azure HDInsight
description: Дополнительные сведения об изменениях, необходимых в процессе миграции детальный доступ на основе ролей для конфигурации кластера HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 982c5dcc052f92afe381235db0bf066262fd82c6
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304287"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Переход на детализированный доступ на основе ролей для конфигураций кластера

Мы представляем некоторые важные изменения для поддержки более точный контроль ролевого доступа для получения конфиденциальной информации. По мере изменения часть из них, некоторые **действие может потребоваться** при использовании одного из [затронутых сущностей и сценарии](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Что изменяется?

Ранее, секреты может быть получен через HDInsight API пользователями кластера имеет владельца, участника или читателя [роли RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles), как они были доступны любому пользователю с `*/read` разрешение.
Забегая вперед, доступ к таким секретам потребует `Microsoft.HDInsight/clusters/configurations/*` разрешение, то есть они могут больше не обращаются пользователи с ролью читателя. Секреты, заданы как значения, которые могут использоваться для получения более с повышенными правами доступа, чем роли пользователя должен быть разрешен. К ним относятся значения, такие как учетные данные кластера шлюза HTTP, ключи учетной записи хранения и учетные данные базы данных.

Мы также Представляем новый [оператор кластера HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) роли, будут иметь возможность получать секреты без наличия административных разрешений участника или владельца. Итог:

| Роль                                  | Ранее                                                                                       | Забегая вперед       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Читатель                                | -Доступ на чтение, включая секреты                                                                   | -Доступ на чтение, **за исключением** секретов |           |   |   |
| Оператор кластера HDInsight<br>(Новая роль) | Н/Д                                                                                              | — Чтение и запись, включая секреты         |   |   |
| Участник                           | — Чтение и запись, включая секреты<br>-Создание и управление ими все типы ресурсов Azure.     | Без изменений |
| Владелец.                                 | — Доступ чтение и запись, включая секреты<br>— Полный доступ ко всем ресурсам<br>-Делегировать доступ другим пользователям | Без изменений |

Сведения о том, как добавить назначение роли оператора кластера HDInsight пользователю, чтобы предоставлять им чтения и записи доступа к секретам кластера, см. в разделе под разделом, [добавить оператор кластера HDInsight назначение роли пользователю](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Затронуты этими изменениями?

Затрагиваются следующие сущности и сценариев:

- [API](#api). Пользователи, использующие `/configurations` или `/configurations/{configurationName}` конечные точки.
- [Средства Azure HDInsight для Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) версии 1.1.1 или ниже.
- [Набор средств Azure для IntelliJ](#azure-toolkit-for-intellij) версии 3.20.0 или ниже.
- [Azure Data Lake и Stream Analytics Tools для Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) ниже версии 2.3.9000.1.
- [Набор средств Azure для Eclipse](#azure-toolkit-for-eclipse) версии 3.15.0 или ниже.
- [Пакет SDK для .NET](#sdk-for-net)
    - [версии 1.x или 2.x](#versions-1x-and-2x): Пользователи, использующие `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` или `DisableHttp` методы из класса ConfigurationsOperationsExtensions.
    - [версии 3.x и более](#versions-3x-and-up): Пользователи, использующие `Get`, `Update`, `EnableHttp`, или `DisableHttp` методы из `ConfigurationsOperationsExtensions` класса.
- [Пакет SDK для Python](#sdk-for-python): Пользователи, использующие `get` или `update` методы из `ConfigurationsOperations` класса.
- [Пакет SDK для Java](#sdk-for-java): Пользователи, использующие `update` или `get` методы из `ConfigurationsInner` класса.
- [Пакет SDK для Go](#sdk-for-go): Пользователи, использующие `Get` или `Update` методы из `ConfigurationsClient` структуры.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) ниже версии 2.0.0.
См. разделы ниже (или перейдите по ссылкам выше), чтобы ознакомиться с этапами миграции для своего сценария.

### <a name="api"></a>API

Следующие API будет изменились или устарели:

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (конфиденциальные данные удалены)
    - Ранее, используется для получения отдельной конфигурации типов (включая секретов).
    - Этот вызов API теперь возвращает типы отдельных конфигураций с секретными данными опущен. Для получения всех конфигураций, включая секреты, используйте новый вызов /configurations POST. Чтобы получить только параметры шлюза, используйте новый вызов /getGatewaySettings POST.
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (устаревшая версия)
    - Ранее, используемый для получения всех конфигураций (в том числе секреты)
    - Этот вызов API, больше не будет поддерживаться. Для получения всех конфигураций, в дальнейшем используется новый вызов /configurations POST. Чтобы получить конфигурацию с конфиденциальные параметры опущен, используйте вызов /configurations/ {configurationName} GET.
- [**POST /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (устаревшая версия)
    - Ранее, используется для обновления учетных данных шлюза.
    - Этот вызов API устарело и больше не поддерживается. Вместо этого используйте новые /updateGatewaySettings POST.

При замене следующие интерфейсы API были добавлены:</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Этот API можно используйте для получения всех конфигураций, включая секреты.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Этот API можно используйте для получения параметров шлюза.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Этот API можно используйте для обновления параметров шлюза (имя пользователя или пароль).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Средства Azure HDInsight для Visual Studio Code

Если вы используете версии 1.1.1 или ниже, обновление до [последнюю версию средства Azure HDInsight для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) избежать прерывания работы.

### <a name="azure-toolkit-for-intellij"></a>Набор средств Azure для IntelliJ

Если вы используете версию 3.20.0 или ниже, обновление до [последнюю версию набора средств Azure для подключаемого модуля IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) избежать прерывания работы.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Azure Data Lake и Stream Analytics Tools для Visual Studio

Обновление до версии 2.3.9000.1 или более поздней версией [Azure Data Lake и Stream Analytics Tools для Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) избежать прерывания работы.  Для получения справки по обновления, см. в разделе документации по [обновления Data Lake Tools для Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Набор средств Azure для Eclipse

Если вы используете версию 3.15.0 или ниже, обновление до [последнюю версию набора средств Azure для Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) избежать прерывания работы.

### <a name="sdk-for-net"></a>Пакет SDK для .NET

#### <a name="versions-1x-and-2x"></a>Версии 1.x и 2.x

Обновление до [версии 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) пакета SDK HDInsight для .NET. Если вы используете метод затронуты этими изменениями, могут потребоваться изменения строк кода:

- `ClusterOperationsExtensions.GetClusterConfigurations` будет **больше не возвращает конфиденциальные параметры** , такие как ключи к хранилищу (core-site) или учетных данных HTTP (шлюз).
    - Получить все конфигурации, включая конфиденциальные параметры с помощью `ClusterOperationsExtensions.ListConfigurations` Забегая вперед.  Обратите внимание на то, что пользователи с ролью «Читатель» не смогут использовать этот метод. Это обеспечивает детальный контроль, по которому пользователи можно получить доступ к конфиденциальной информации для кластера.
    - Чтобы получить только учетные данные шлюза HTTP, используйте `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` Теперь считается устаревшим и будет заменен `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` Теперь считается устаревшим и будет заменен `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` и `DisableHttp` уже устарели. HTTP теперь всегда включена, поэтому эти методы больше не требуются.

#### <a name="versions-3x-and-up"></a>Версии 3.x и более

Обновление до [версии 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) или более поздней версии пакета SDK HDInsight для .NET. Если вы используете метод затронуты этими изменениями, могут потребоваться изменения строк кода:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) будет **больше не возвращает конфиденциальные параметры** , такие как ключи к хранилищу (core-site) или учетных данных HTTP (шлюз).
    - Получить все конфигурации, включая конфиденциальные параметры с помощью [ `ConfigurationOperationsExtensions.List` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) в дальнейшем.  Обратите внимание на то, что пользователи с ролью «Читатель» не смогут использовать этот метод. Это обеспечивает детальный контроль, по которому пользователи можно получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза HTTP, используйте [ `ClusterOperationsExtensions.GetGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) Теперь считается устаревшим и будет заменен [ `ClusterOperationsExtensions.UpdateGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) и [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) уже устарели. HTTP теперь всегда включена, поэтому эти методы больше не требуются.

### <a name="sdk-for-python"></a>Пакет SDK для Python

Обновление до [версии 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) или более поздней версии пакета SDK HDInsight для Python. Если вы используете метод затронуты этими изменениями, могут потребоваться изменения строк кода:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) будет **больше не возвращает конфиденциальные параметры** , такие как ключи к хранилищу (core-site) или учетных данных HTTP (шлюз).
    - Получить все конфигурации, включая конфиденциальные параметры с помощью [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) в дальнейшем.  Обратите внимание на то, что пользователи с ролью «Читатель» не смогут использовать этот метод. Это обеспечивает детальный контроль, по которому пользователи можно получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза HTTP, используйте [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) Теперь считается устаревшим и будет заменен [ `ClusterOperations.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>Пакет SDK для Java

Обновление до [версии 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) или более поздней версии пакета SDK HDInsight для Java. Если вы используете метод затронуты этими изменениями, могут потребоваться изменения строк кода:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) будет **больше не возвращает конфиденциальные параметры** , такие как ключи к хранилищу (core-site) или учетных данных HTTP (шлюз).
    - Получить все конфигурации, включая конфиденциальные параметры с помощью [ `ConfigurationsInner.list` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) в дальнейшем.  Обратите внимание на то, что пользователи с ролью «Читатель» не смогут использовать этот метод. Это обеспечивает детальный контроль, по которому пользователи можно получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза HTTP, используйте [ `ClustersInner.getGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) Теперь считается устаревшим и будет заменен [ `ClustersInner.updateGatewaySettings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>Пакет SDK для Go

Обновление до [версии 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) или более поздней версии пакета SDK HDInsight для Go. Если вы используете метод затронуты этими изменениями, могут потребоваться изменения строк кода:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) будет **больше не возвращает конфиденциальные параметры** , такие как ключи к хранилищу (core-site) или учетных данных HTTP (шлюз).
    - Получить все конфигурации, включая конфиденциальные параметры с помощью [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) в дальнейшем.  Обратите внимание на то, что пользователи с ролью «Читатель» не смогут использовать этот метод. Это обеспечивает детальный контроль, по которому пользователи можно получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза HTTP, используйте [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) Теперь считается устаревшим и будет заменен [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Обновление до [Az PowerShell версии 2.0.0](https://www.powershellgallery.com/packages/Az) или более поздней версии, чтобы избежать прерывания работы.  Если вы используете метод затронуты этими изменениями, могут потребоваться изменения минимальный требуемый код.
- `Grant-AzHDInsightHttpServicesAccess` Теперь считается устаревшим и будет заменен новой `Set-AzHDInsightGatewayCredential` командлета.
- `Get-AzHDInsightJobOutput` был обновлен для поддержки детализированный доступ на основе ролей для ключа хранилища.
    - На пользователей с ролями оператора, участника или владельца кластера HDInsight это не повлияет.
    - Только роль читателя пользователям нужно будет указать `DefaultStorageAccountKey` параметр явным образом.
- `Revoke-AzHDInsightHttpServicesAccess` Теперь считается устаревшим. HTTP теперь всегда включена, поэтому этот командлет больше не используется.
 См. в разделе [az. Руководство по миграции HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) для получения дополнительных сведений.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Добавить оператор кластера HDInsight назначение роли пользователю

Пользователь с [участник](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) или [владельца](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) роли можно назначить [оператор кластера HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) роли для пользователей, которые требуется иметь доступ на чтение и запись к учетом Значения конфигурации кластера HDInsight (например, учетные данные кластера шлюза и ключи учетной записи хранения).

### <a name="using-the-azure-cli"></a>Использование Azure CLI

Самый простой способ добавить это назначение роли при помощи `az role assignemnt create` команду в Azure CLI.

> [!NOTE]
> Эта команда должны выполняться пользователем с ролями владельца или участника, как только они могут предоставлять эти разрешения. `--assignee` — Это адрес электронной почты пользователя, которому вы хотите назначить роль оператора кластера HDInsight.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Предоставление роли на уровне ресурсов (кластер)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Предоставление роли на уровне группы ресурсов

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Предоставление роли на уровне подписки

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Использование портала Azure

Также можно использовать портал Azure для добавления оператор кластера HDInsight назначение роли пользователю. См. в документации [управление доступом к ресурсам Azure с помощью RBAC и портала Azure: Добавление назначения роли](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
