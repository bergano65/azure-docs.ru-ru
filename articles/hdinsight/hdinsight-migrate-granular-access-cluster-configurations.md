---
title: Перенос в детализированный доступ на основе ролей для конфигурации кластера — Azure HDInsight
description: Дополнительные сведения об изменениях, необходимые для переноса в детализированный доступ на основе ролей для конфигурации кластера.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 0422d848ccdf9ba82e68813de64eec863ee4ad29
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60006239"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Перенос в детализированный доступ на основе ролей для конфигурации кластера

Мы представляем некоторые важные изменения для поддержки более точный контроль ролевого доступа для получения конфиденциальной информации. По мере изменения часть из них, некоторые **действие может потребоваться** при использовании одного из [затронутых сущностей и сценарии](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>Что изменяется?

Ранее, секреты может быть получен через HDInsight API пользователями кластера имеет владельца, участника или читателя [роли RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles).
В дальнейшем эти секреты больше не будут доступны пользователям с ролью читателя. Мы с радостью Представляем новую роль «Оператор создаст кластер», которая будет иметь возможность получать секреты без наличия административных разрешений участника или владельца. Итог:

| Роль                                  | Ранее                                                                                       | Now       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Читатель                                | -Доступ на чтение, включая секреты                                                                   | -Доступ на чтение, **за исключением** секретов |           |   |   |
| Оператор кластера HDInsight<br>(Новая роль) | Н/Д                                                                                              | — Чтение и запись, включая секреты         |   |   |
| Участник                           | — Чтение и запись, включая секреты<br>-Создание и управление ими все типы ресурсов Azure.     | Без изменения. |
| Владелец.                                 | — Доступ чтение и запись, включая секреты<br>— Полный доступ ко всем ресурсам<br>-Делегировать доступ другим пользователям | Без изменения. |

Сведения о том, как добавить назначение роли оператора кластера HDInsight пользователю, чтобы предоставлять им чтения и записи доступа к секретам кластера, см. в разделе под разделом, [добавить оператор кластера HDInsight назначение роли пользователю](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Затронуты этими изменениями?

Затрагиваются следующие сущности и сценариев:

- [API](#api). Пользователи, использующие `/configurations` или `/configurations/{configurationName}` конечные точки.
- [Средства Azure HDInsight для Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) ___ версии и ниже.
- [Набор средств Azure для IntelliJ](#azure-toolkit-for-intellij) версии __ и ниже.
- [Набор средств Azure для Eclipse](#azure-toolkit-for-eclipse) версии __ и ниже.
- [Пакет SDK для .NET](#sdk-for-net)
    - [версии 1.x или 2.x](#versions-1x-and-2x): Пользователи, использующие `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` или `DisableHttp` методы из класса ConfigurationsOperationsExtensions.
    - [версии 3.x и более](#versions-3x-and-up): Пользователи, использующие `EnableHttp`, `DisableHttp`, `Update`, или `Get` методы из `ConfigurationsOperationsExtensions` класса.
- [Пакет SDK для Python](#sdk-for-python): Пользователи, использующие `get` или `update` методы из класса ConfigurationsOperations.
- [Пакет SDK для Java](#sdk-for-java): Пользователи, использующие `update` или `get` методы из класса ConfigurationsInner.
- [Пакет SDK для Go](#sdk-for-go): Пользователи, использующие `Get` или `Update` методы из ConfigurationsClient структуры.

См. на ниже разделах (или воспользуйтесь указанным ниже ссылкам) для миграции см. в разделе шаги для вашего сценария.

### <a name="api"></a>API

Следующие API будет изменились или устарели:

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (конфиденциальные данные удалены) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - Ранее, используется для получения отдельной конфигурации типов (включая секретов).
    - Этот вызов API теперь возвращает типы отдельных конфигураций с секретными данными опущен. Для получения всех конфигураций, включая секреты, используйте новый [POST /configurations]() вызова. Чтобы получить только параметры шлюза, используйте новый [POST /getGatewaySettings]() вызова.
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) (устаревшая версия) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Ранее, используемый для получения всех конфигураций (в том числе секреты)
    - Этот вызов API, больше не будет поддерживаться. Для получения всех конфигураций, в дальнейшем используется новый [POST /configurations]() вызова. Для получения конфигураций с конфиденциальные параметры опущен, используется [/configurations/ GET {configurationName}]() вызова.
- [**POST /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (устаревшая версия) https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations/{configurationName}?api-version={api-version}
    - Ранее, используется для обновления учетных данных шлюза.
    - Этот вызов API устарело и больше не поддерживается. Используйте новый [POST /updateGatewaySettings]() вместо этого.

При замене следующие интерфейсы API были добавлены:</span>

- **POST /configurations** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/configurations?api-version={api-version}
    - Этот API можно используйте для получения всех конфигураций, включая секреты.
- **POST /getGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/getGatewaySettings?api-version={api-version}
    - Этот API можно используйте для получения параметров шлюза.
- **POST /updateGatewaySettings** https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.HDInsight/clusters/{clusterName}/updateGatewaySettings?api-version={api-version}
    - Этот API можно используйте для обновления параметров шлюза (имя пользователя или пароль).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Средства Azure HDInsight для Visual Studio Code

Если вы используете версии 1.1.1 или более ранней версии, выполните обновление до [последнюю версию средства Azure HDInsight для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) избежать прерывания работы.

### <a name="azure-toolkit-for-intellij"></a>Набор средств Azure для IntelliJ

Если вы используете версию 3.21.0 или более ранней версии, выполните обновление до [последнюю версию набора средств Azure для подключаемого модуля IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) избежать прерывания работы.

### <a name="azure-toolkit-for-eclipse"></a>Набор средств Azure для Eclipse

Если вы используете выпуск 2019-03-29 или более ранней версии, выполните обновление до последней версии набора средств Azure для Eclipse, чтобы избежать прерывания работы.

### <a name="sdk-for-net"></a>Пакет SDK для .NET

#### <a name="versions-1x-and-2x"></a>Версии 1.x и 2.x

Выполните обновление до [версии 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) пакета SDK HDInsight для .NET. Если вы используете метод затронуты этими изменениями, могут потребоваться изменения строк кода:

- `ClusterOperationsExtensions.GetClusterConfigurations` будет **больше не возвращает конфиденциальные параметры** , такие как ключи к хранилищу (core-site) или учетных данных HTTP (шлюз).
    - Получить все конфигурации, включая конфиденциальные параметры с помощью `ClusterOperationsExtensions.ListConfigurations` Забегая вперед.  Обратите внимание на то, что пользователи с ролью «Читатель» не смогут использовать этот метод. Это обеспечивает детальный контроль, по которому пользователи можно получить доступ к конфиденциальной информации для кластера.
    - Чтобы получить только учетные данные шлюза HTTP, используйте `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` Теперь считается устаревшим и будет заменен `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` Теперь считается устаревшим и будет заменен `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` и `DisableHttp` уже устарели. HTTP теперь всегда включена, поэтому эти методы больше не требуются.

#### <a name="versions-3x-and-up"></a>Версии 3.x и более

Выполните обновление до [версии 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) пакета SDK HDInsight для .NET. Если вы используете метод затронуты этими изменениями, могут потребоваться изменения строк кода:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) будет **больше не возвращает конфиденциальные параметры** , такие как ключи к хранилищу (core-site) или учетных данных HTTP (шлюз).
    - Получить все конфигурации, включая конфиденциальные параметры с помощью `ConfigurationOperationsExtensions.List` Забегая вперед.  Обратите внимание на то, что пользователи с ролью «Читатель» не смогут использовать этот метод. Это обеспечивает детальный контроль, по которому пользователи можно получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза HTTP, используйте `ClusterOperationsExtensions.GetGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) Теперь считается устаревшим и будет заменен `ClusterOperationsExtensions.UpdateGatewaySettings`. 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) и [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) уже устарели. HTTP теперь всегда включена, поэтому эти методы больше не требуются.

### <a name="sdk-for-python"></a>Пакет SDK для Python

Выполните обновление до [версии 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) пакета SDK HDInsight для Python. Если вы используете метод затронуты этими изменениями, могут потребоваться изменения строк кода:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) будет **больше не возвращает конфиденциальные параметры** , такие как ключи к хранилищу (core-site) или учетных данных HTTP (шлюз).
    - Получить все конфигурации, включая конфиденциальные параметры с помощью [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) в дальнейшем.  Обратите внимание на то, что пользователи с ролью «Читатель» не смогут использовать этот метод. Это обеспечивает детальный контроль, по которому пользователи можно получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза HTTP, используйте [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) Теперь считается устаревшим и будет заменен [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>Пакет SDK для Java

Выполните обновление до [версии 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) HDInsight пакета SDK для Java. Если вы используете метод затронуты этими изменениями, могут потребоваться изменения строк кода:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) будет **больше не возвращает конфиденциальные параметры** , такие как ключи к хранилищу (core-site) или учетных данных HTTP (шлюз).
    - Получить все конфигурации, включая конфиденциальные параметры с помощью `ConfigurationsInner.list` Забегая вперед.  Обратите внимание на то, что пользователи с ролью «Читатель» не смогут использовать этот метод. Это обеспечивает детальный контроль, по которому пользователи можно получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза HTTP, используйте `ConfigurationsOperations.get_gateway_settings`.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) Теперь считается устаревшим и будет заменен `ClusterOperationsExtensions.update_gateway_settings`.

### <a name="sdk-for-go"></a>Пакет SDK для Go

Выполните обновление до [версии 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) HDInsight пакета SDK для Go. Если вы используете метод затронуты этими изменениями, могут потребоваться изменения строк кода:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) будет **больше не возвращает конфиденциальные параметры** , такие как ключи к хранилищу (core-site) или учетных данных HTTP (шлюз).
    - Получить все конфигурации, включая конфиденциальные параметры с помощью [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) в дальнейшем.  Обратите внимание на то, что пользователи с ролью «Читатель» не смогут использовать этот метод. Это обеспечивает детальный контроль, по которому пользователи можно получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные шлюза HTTP, используйте [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) Теперь считается устаревшим и будет заменен [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Добавить оператор кластера HDInsight назначение роли пользователю

Пользователь с [участник](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) или [владельца](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) роли можно предоставить с ролью оператора кластера HDInsight, пользователям требуется иметь доступ на чтение и запись к секретам кластера HDInsight, как шлюз учетных данных кластера и ключи учетной записи хранения.

### <a name="using-the-azure-cli"></a>Использование Azure CLI

Чтобы добавить это назначение роли проще, используя следующую команду в Azure CLI:

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

> [!NOTE]
> Эта команда должны выполняться пользователем с ролями владельца или участника, как только они могут предоставлять эти разрешения. `--assignee` — Это адрес электронной почты пользователя, которому вы хотите назначить роль оператора кластера HDInsight.

Приведенная выше команда предоставляет этой роли на уровне подписки. Чтобы вместо этого просто предоставить этой роли на уровне группы ресурсов, можно изменить команду следующим образом:

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

### <a name="using-the-azure-portal"></a>Использование портала Azure

Также можно использовать портал Azure для добавления оператор кластера HDInsight назначение роли пользователю. См. в документации [управление доступом к ресурсам Azure с помощью RBAC и портала Azure: Добавление назначения роли](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
