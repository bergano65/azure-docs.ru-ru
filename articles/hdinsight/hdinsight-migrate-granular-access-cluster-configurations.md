---
title: Переход на детализированный доступ на основе ролей для конфигураций кластера — Azure HDInsight
description: Изучите изменения, необходимые в процессе миграции, чтобы детально получить доступ к конфигурациям кластера HDInsight на основе ролей.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ebb1723a9a2b2d069a1766d4f78151f2b684c5b9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464663"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Переход на детализированный доступ на основе ролей для конфигураций кластера

Мы представляем некоторые важные изменения для поддержки более детализированного доступа на основе ролей для получения конфиденциальной информации. В рамках этих изменений **может потребоваться выполнить некоторые действия** , если вы используете одну из затронутых [сущностей или сценариев](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Что изменяется?

Ранее секретные данные можно было получить через API HDInsight для пользователей кластера, имеющих роли "владелец", "участник" или "читатель" ( [RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)), так как `*/read` они были доступны любому пользователю с разрешением.
В `Microsoft.HDInsight/clusters/configurations/*` дальнейшем для доступа к этим секретам потребуется разрешение, означающее, что пользователи больше не могут быть доступны пользователям с ролью читателя. Секреты определяются как значения, которые можно использовать для получения более повышения уровня доступа, чем разрешено для роли пользователя. К ним относятся такие значения, как учетные данные HTTP для шлюза кластера, ключи учетной записи хранения и учетные данные базы данных.

Мы также представляем новую роль [оператора кластера HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) , которая сможет получать секреты без предоставления административных разрешений участнику или владельцу. Итог:

| Role                                  | Ранее                                                                                       | Переход вперед       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Читатель                                | — Доступ на чтение, включая секреты                                                                   | — Доступ на чтение, **исключение** секретов |           |   |   |
| Оператор кластера HDInsight<br>(Новая роль) | Н/Д                                                                                              | — Доступ на чтение и запись, включая секреты         |   |   |
| Участник                           | — Доступ на чтение и запись, включая секреты<br>— Создание всех типов ресурсов Azure и управление ими.     | Без изменений |
| имя владельца;                                 | — Доступ на чтение и запись, включая секреты<br>— Полный доступ ко всем ресурсам<br>-Делегирование доступа другим пользователям | Без изменений |

Сведения о том, как добавить назначение роли оператора кластера HDInsight пользователю, чтобы предоставить им доступ для чтения и записи к секретам кластера, см. в разделе ниже, [добавьте назначение роли "оператор кластера hdinsight" пользователю](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Я затронул эти изменения?

Затрагиваются следующие сущности и сценарии:

- [API](#api). Пользователи, `/configurations` использующие конечные точки или `/configurations/{configurationName}` .
- [Средства Azure HDInsight для Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) версии 1.1.1 или ниже.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) версии 3.20.0 или ниже.
- [Средства Azure Data Lake и Stream Analytics для Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) ниже версии 2.3.9000.1.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) версии 3.15.0 или ниже.
- [Пакет SDK для .NET](#sdk-for-net)
    - [версии 1. x или 2. x](#versions-1x-and-2x): Пользователи `GetClusterConfigurations`, использующие методы `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` или`DisableHttp` из класса конфигуратионсоператионсекстенсионс.
    - [версии 3. x и выше](#versions-3x-and-up): Пользователи `Get`, использующие методы `Update`, `EnableHttp`, или `DisableHttp` из `ConfigurationsOperationsExtensions` класса.
- [Пакет SDK для Python](#sdk-for-python): Пользователи, `get` использующие методы `update` или из `ConfigurationsOperations` класса.
- [Пакет SDK для Java](#sdk-for-java): Пользователи, `update` использующие методы `get` или из `ConfigurationsInner` класса.
- [Пакет SDK для Go](#sdk-for-go): Пользователи, `Get` использующие методы `Update` или из `ConfigurationsClient` структуры.
- [AZ. HDInsight PowerShell](#azhdinsight-powershell) ниже версии 2.0.0.
См. разделы ниже (или перейдите по ссылкам выше), чтобы ознакомиться с этапами миграции для своего сценария.

### <a name="api"></a>API

Следующие API будут изменены или устарели:

- [**Получить/конфигуратионс/{конфигуратионнаме}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (конфиденциальная информация удалена)
    - Ранее использовался для получения индивидуальных типов конфигурации (включая секреты).
    - Этот вызов API теперь будет возвращать отдельные типы конфигурации с опущенными секретами. Чтобы получить все конфигурации, включая секреты, используйте новый вызов POST/конфигуратионс. Чтобы получить только параметры шлюза, используйте новый вызов POST/Жетгатевайсеттингс.
- [**Получить/конфигуратионс**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) не рекомендуется
    - Ранее использовалось для получения всех конфигураций (включая секреты)
    - Этот вызов API больше не будет поддерживаться. Чтобы получить все конфигурации, пересылаемые вперед, используйте новый вызов POST/конфигуратионс. Чтобы получить конфигурации с опущенными конфиденциальными параметрами, используйте вызов GET/Конфигуратионс/{конфигуратионнаме}.
- [**Опубликовать/конфигуратионс/{конфигуратионнаме}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) не рекомендуется
    - Ранее использовалось для обновления учетных данных шлюза.
    - Этот вызов API будет устаревшим и больше не поддерживается. Вместо этого используйте новый POST/Упдатегатевайсеттингс.

Добавлены следующие API-интерфейсы замены:</span>

- [**ОПУБЛИКОВАТЬ/конфигуратионс**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Используйте этот API для получения всех конфигураций, включая секреты.
- [**ОПУБЛИКОВАТЬ/Жетгатевайсеттингс**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Используйте этот API для получения параметров шлюза.
- [**ОПУБЛИКОВАТЬ/Упдатегатевайсеттингс**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Используйте этот API для обновления параметров шлюза (имя пользователя и/или пароль).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Средства Azure HDInsight для Visual Studio Code

Если вы используете версию 1.1.1 или ниже, обновите до [последней версии средств Azure HDInsight для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) , чтобы избежать перерывов.

### <a name="azure-toolkit-for-intellij"></a>Набор средств Azure для IntelliJ

Если вы используете версию 3.20.0 или ниже, обновите [последнюю версию подключаемого модуля Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) , чтобы избежать перерывов.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Средства Azure Data Lake и Stream Analytics для Visual Studio

Обновите [Azure Data Lake и средства Stream Analytics для Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) до версии 2.3.9000.1 или более поздней, чтобы избежать перерывов в работе.  Справку по обновлению см. в нашей документации, а также в [статье обновление средств Data Lake для Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Набор средств Azure для Eclipse

Если вы используете версию 3.15.0 или ниже, обновите ее до [последней версии Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) во избежание перерывов.

### <a name="sdk-for-net"></a>Пакет SDK для .NET

#### <a name="versions-1x-and-2x"></a>Версии 1. x и 2. x

Обновите [версию 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) SDK HDInsight для .NET. При использовании метода, затрагиваемого этими изменениями, могут потребоваться минимальные изменения в коде:

- `ClusterOperationsExtensions.GetClusterConfigurations`**больше не будет возвращать конфиденциальные параметры** , такие как ключи хранилища (основной сайт) или учетные данные HTTP (шлюз).
    - Для получения всех конфигураций, включая конфиденциальные параметры `ClusterOperationsExtensions.ListConfigurations` , используйте переход вперед.  Обратите внимание, что пользователи с ролью "читатель" не смогут использовать этот метод. Это позволяет детально контролировать, какие пользователи могут получать доступ к конфиденциальной информации для кластера.
    - Чтобы получить только учетные данные шлюза `ClusterOperationsExtensions.GetGatewaySettings`HTTP, используйте.

- `ClusterOperationsExtensions.GetConnectivitySettings`теперь является нерекомендуемым и заменено на `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings`теперь является нерекомендуемым и заменено на `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp`и `DisableHttp` теперь являются устаревшими. Протокол HTTP теперь всегда включен, поэтому эти методы больше не нужны.

#### <a name="versions-3x-and-up"></a>Версии 3. x и выше

Обновите пакет SDK HDInsight для .NET до [версии 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) или более поздней. При использовании метода, затрагиваемого этими изменениями, могут потребоваться минимальные изменения в коде:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**больше не будет возвращать конфиденциальные параметры** , такие как ключи хранилища (основной сайт) или учетные данные HTTP (шлюз).
    - Для получения всех конфигураций, включая конфиденциальные параметры [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) , используйте переход вперед.  Обратите внимание, что пользователи с ролью "читатель" не смогут использовать этот метод. Это позволяет детально контролировать, какие пользователи могут получать доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet)HTTP, используйте. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)теперь является нерекомендуемым и заменено на [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)и [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) теперь являются устаревшими. Протокол HTTP теперь всегда включен, поэтому эти методы больше не нужны.

### <a name="sdk-for-python"></a>Пакет SDK для Python

Обновление до [версии 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) или более поздней версии пакета SDK для HDInsight для Python. При использовании метода, затрагиваемого этими изменениями, могут потребоваться минимальные изменения в коде:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**больше не будет возвращать конфиденциальные параметры** , такие как ключи хранилища (основной сайт) или учетные данные HTTP (шлюз).
    - Для получения всех конфигураций, включая конфиденциальные параметры [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) , используйте переход вперед.  Обратите внимание, что пользователи с ролью "читатель" не смогут использовать этот метод. Это позволяет детально контролировать, какие пользователи могут получать доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)HTTP, используйте.
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)теперь является нерекомендуемым и заменено на [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>Пакет SDK для Java

Обновление до [версии 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) или более поздней версии пакета SDK для HDInsight для Java. При использовании метода, затрагиваемого этими изменениями, могут потребоваться минимальные изменения в коде:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)**больше не будет возвращать конфиденциальные параметры** , такие как ключи хранилища (основной сайт) или учетные данные HTTP (шлюз).
    - Для получения всех конфигураций, включая конфиденциальные параметры [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) , используйте переход вперед.  Обратите внимание, что пользователи с ролью "читатель" не смогут использовать этот метод. Это позволяет детально контролировать, какие пользователи могут получать доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза [`ClustersInner.getGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable)HTTP, используйте.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)теперь является нерекомендуемым и заменено на [`ClustersInner.updateGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>Пакет SDK для Go

Обновление до [версии 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) или более поздней версии пакета SDK HDInsight для Go. При использовании метода, затрагиваемого этими изменениями, могут потребоваться минимальные изменения в коде:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)**больше не будет возвращать конфиденциальные параметры** , такие как ключи хранилища (основной сайт) или учетные данные HTTP (шлюз).
    - Для получения всех конфигураций, включая конфиденциальные параметры [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) , используйте переход вперед.  Обратите внимание, что пользователи с ролью "читатель" не смогут использовать этот метод. Это позволяет детально контролировать, какие пользователи могут получать доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)HTTP, используйте.
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)теперь является нерекомендуемым и заменено на [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>AZ. HDInsight PowerShell
Обновите команду [AZ PowerShell версии 2.0.0](https://www.powershellgallery.com/packages/Az) или более поздней, чтобы избежать перерывов.  При использовании метода, затрагиваемого этими изменениями, могут потребоваться минимальные изменения в коде.
- `Grant-AzHDInsightHttpServicesAccess`теперь является нерекомендуемым и заменено новым `Set-AzHDInsightGatewayCredential` командлетом.
- `Get-AzHDInsightJobOutput`был обновлен для поддержки детализированного доступа к ключу хранилища на основе ролей.
    - На пользователей с ролями оператора, участника или владельца кластера HDInsight это не повлияет.
    - Пользователям с ролью читателя потребуется явно указать `DefaultStorageAccountKey` параметр.
- `Revoke-AzHDInsightHttpServicesAccess`теперь является устаревшим. Протокол HTTP теперь всегда включен, поэтому этот командлет больше не нужен.
 См [. раздел AZ. ](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight)Дополнительные сведения см. в разделе с руководством по миграции HDInsight.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Добавление назначения роли оператора кластера HDInsight пользователю

Пользователь с ролью " [участник](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) " или " [владелец](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) " может назначить роль " [оператор кластера hdinsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) " пользователям, которым требуется доступ на чтение и запись к конфиденциальным значениям конфигурации кластера hdinsight (например, учетные данные шлюза кластера). и ключи учетной записи хранения).

### <a name="using-the-azure-cli"></a>Использование Azure CLI

Самый простой способ добавить это назначение роли — использовать `az role assignment create` команду в Azure CLI.

> [!NOTE]
> Эта команда должна выполняться пользователем с ролью участника или владельца, так как только они могут предоставлять эти разрешения. `--assignee` — Это адрес электронной почты пользователя, которому необходимо назначить роль оператора кластера HDInsight.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Предоставление роли на уровне ресурсов (кластера)

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

Можно также использовать портал Azure для добавления назначения роли оператора кластера HDInsight пользователю. См. документацию, [Управление доступом к ресурсам Azure с помощью RBAC и портал Azure — Добавление назначения ролей](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
