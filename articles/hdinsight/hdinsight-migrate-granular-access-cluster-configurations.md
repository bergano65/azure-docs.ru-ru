---
title: Детальный ролевой доступ к кластерным конфигурациям Azure HDInsight
description: Узнайте об изменениях, необходимых для миграции в детальный ролевой доступ для конфигураций кластеров HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: f1fdb9dffbe06430ea7e3eb9339e23f5239e4e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310838"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Переход на детализированный доступ на основе ролей для конфигураций кластера

Мы вносим некоторые важные изменения, чтобы поддержать более мелкозернистый ролевой доступ для получения конфиденциальной информации. В рамках этих изменений некоторые действия могут потребоваться **до 3 сентября 2019 года,** если вы используете один из [затронутых сущностей/сценариев.](#am-i-affected-by-these-changes)

## <a name="what-is-changing"></a>Что изменяется?

Ранее секреты можно было получить через HDInsight API пользователями кластеров, обладающими ролями Владельца, Участника `*/read` или Читателя [RBAC,](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)поскольку они были доступны любому с разрешения. Секреты определяются как значения, которые могут быть использованы для получения более высокого доступа, чем должна позволить роль пользователя. К ним относятся такие значения, как учетные данные кластерного шлюза HTTP, ключи учетной записи хранилища и учетные данные базы данных.

Начиная с 3 сентября 2019 года, доступ `Microsoft.HDInsight/clusters/configurations/action` к этим секретам потребует разрешения, то есть они больше не могут быть доступны пользователям с ролью Reader. Роли, которые имеют это разрешение, являются Вкладчиком, Владельцем и новой ролью оператора кластера HDInsight (подробнее об этом ниже).

Мы также представляем новую роль [кластерного оператора HDInsight,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) которая сможет получать секреты без предоставления административных разрешений вкладчика или владельца. Итог:

| Роль                                  | Раньше назывался .                                                                                       | Идти вперед       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Читатель                                | - Читать доступ, в том числе секреты                                                                   | - Читать доступ, **за исключением** секретов |           |   |   |
| Оператор кластера HDInsight<br>(Новая роль) | Недоступно                                                                                              | - Читать / писать доступ, в том числе секреты         |   |   |
| Участник                           | - Читать / писать доступ, в том числе секреты<br>- Создавайте и управляйте всеми типами ресурсов Azure.     | Без изменения. |
| Владелец                                 | - Читать / писать доступ, включая секреты<br>- Полный доступ ко всем ресурсам<br>- Доступ делегатов к другим | Без изменения. |

Для получения информации о том, как добавить назначение роли оператора кластера HDInsight для пользователя, чтобы предоставить им доступ к кластерным секретам, см. ниже раздел, [Добавьте назначение роли оператора кластера HDInsight для пользователя.](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)

## <a name="am-i-affected-by-these-changes"></a>Влияют ли на меня эти изменения?

Затронуты следующие сущности и сценарии:

- [API](#api): Пользователи, использующие или `/configurations` `/configurations/{configurationName}` конечные точки.
- [Инструменты Azure HDInsight для](#azure-hdinsight-tools-for-visual-studio-code) визуальной версии кода студии 1.1.1 или ниже.
- [Azure Toolkit для версии IntelliJ](#azure-toolkit-for-intellij) 3.20.0 или ниже.
- [Azure Data Lake и Stream Analytics Tools for Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) ниже версии 2.3.9000.1.
- [Лазурный инструментдля версии Eclipse](#azure-toolkit-for-eclipse) 3.15.0 или ниже.
- [Пакет SDK для .NET](#sdk-for-net)
    - [версии 1.x или 2.x](#versions-1x-and-2x) `GetConnectivitySettings`: `ConfigureHttpSettings` `EnableHttp` Пользователи, использующие, `DisableHttp` `GetClusterConfigurations`, , или методы из класса ConfigurationsOperationsExtensions.
    - [версии 3.x и](#versions-3x-and-up)вверх `Get` `Update`: `EnableHttp`Пользователи, использующие, , или `DisableHttp` методы из `ConfigurationsOperationsExtensions` класса.
- [SDK для Python](#sdk-for-python): `get` `update` Пользователи, `ConfigurationsOperations` использующие или методы из класса.
- [SDK для Java](#sdk-for-java): `update` `get` Пользователи, `ConfigurationsInner` использующие или методы из класса.
- [SDK для Go](#sdk-for-go): `Get` `Update` Пользователи, `ConfigurationsClient` использующие или методы из структуры.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) ниже версии 2.0.0.
Смотрите ниже разделы (или используйте вышеуказанные ссылки), чтобы увидеть шаги миграции для вашего сценария.

### <a name="api"></a>API

Следующие AA будут изменены или обесточены:

- [**GET /конфигурации / «конфигурация»**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (удалена конфиденциальная информация)
    - Ранее использовался для получения отдельных типов конфигураций (включая секреты).
    - Начиная с 3 сентября 2019 года, этот вызов API теперь будет возвращать отдельные типы конфигураций с опущенными секретами. Чтобы получить все конфигурации, включая секреты, используйте новый вызов POST/configurations. Чтобы получить только настройки шлюза, используйте новый вызов POST /getGatewaySettings.
- [**GET /конфигурации**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (унипраченные)
    - Ранее использовались для получения всех конфигураций (включая секреты)
    - Начиная с 3 сентября 2019 года, этот вызов API будет обесточен и больше не будет поддерживаться. Чтобы получить все конфигурации в будущем, используйте новый вызов POST /configurations. Для получения конфигураций с опущенными чувствительными параметрами используйте вызов GET/configurations/'configurationName.
- [**POST /конфигурации/ «конфигурация»**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (унипраженные)
    - Ранее использовался для обновления учетных данных шлюза.
    - Начиная с 3 сентября 2019 года, этот вызов API будет обесточен и больше не поддерживается. Вместо этого используйте новый POST /updateGatewaySettings.

Добавлены следующие заменяющие AI:</span>

- [**POST /конфигурации**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Используйте этот API для получения всех конфигураций, включая секреты.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Используйте этот API для получения настроек шлюза.
- [**POST /UpdateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Используйте этот API для обновления настроек шлюза (имя пользователя и/или пароль).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Инструменты Azure HDInsight для визуального кода студии

Если вы используете версию 1.1.1 или ниже, обновите [последнюю версию Azure HDInsight Tools for Visual Studio Code,](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) чтобы избежать перерывов.

### <a name="azure-toolkit-for-intellij"></a>Набор средств Azure для IntelliJ

Если вы используете версию 3.20.0 или ниже, обновите [последнюю версию azure Toolkit для плагина IntelliJ,](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) чтобы избежать перерывов.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Лазурные данные озера и поток Analytics Инструменты для визуальной студии

Обновление до версии 2.3.9000.1 или позже [Azure Data Lake и Stream Analytics Tools для Visual Studio,](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) чтобы избежать перерывов.  Для получения помощи в обновлении, см нашей документации, [Обновление данных озера Инструменты для визуальной студии](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Набор средств Azure для Eclipse

Если вы используете версию 3.15.0 или ниже, обновите [последнюю версию инструментария Azure для Eclipse,](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) чтобы избежать перерывов.

### <a name="sdk-for-net"></a>Пакет SDK для .NET

#### <a name="versions-1x-and-2x"></a>Версии 1.x и 2.x

Обновление до [версии 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) HDInsight SDK для .NET. Минимальные изменения кода могут потребоваться, если вы используете метод, затронутый этими изменениями:

- `ClusterOperationsExtensions.GetClusterConfigurations`больше не будет **возвращать чувствительные параметры,** такие как ключи хранения (основной сайт) или учетные данные HTTP (шлюз).
    - Для получения всех конфигураций, включая `ClusterOperationsExtensions.ListConfigurations` чувствительные параметры, используйте в будущем.  Обратите внимание, что пользователи с ролью "Reader" не смогут использовать этот метод. Это позволяет осуществлять детальный контроль, над которым пользователи могут получить доступ к конфиденциальной информации для кластера.
    - Чтобы получить только учетные данные http шлюза, используйте. `ClusterOperationsExtensions.GetGatewaySettings`

- `ClusterOperationsExtensions.GetConnectivitySettings`в настоящее время амортизативе и был заменен `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings`в настоящее время амортизативе и был заменен `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp`и `DisableHttp` в настоящее время обесцениваются. HTTP всегда включен, поэтому эти методы больше не нужны.

#### <a name="versions-3x-and-up"></a>Версии 3.x и вверх

Обновление до [версии 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) или позже HDInsight SDK для .NET. Минимальные изменения кода могут потребоваться, если вы используете метод, затронутый этими изменениями:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)больше не будет **возвращать чувствительные параметры,** такие как ключи хранения (основной сайт) или учетные данные HTTP (шлюз).
    - Для получения всех конфигураций, включая [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) чувствительные параметры, используйте в будущем.Обратите внимание, что пользователи с ролью "Reader" не смогут использовать этот метод. Это позволяет осуществлять детальный контроль, над которым пользователи могут получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные http шлюза, используйте. [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet) 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)в настоящее время амортизативе и был заменен [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)и [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) в настоящее время обесцениваются. HTTP всегда включен, поэтому эти методы больше не нужны.

### <a name="sdk-for-python"></a>Пакет SDK для Python

Обновление до [версии 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) или позже HDInsight SDK для Python. Минимальные изменения кода могут потребоваться, если вы используете метод, затронутый этими изменениями:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)больше не будет **возвращать чувствительные параметры,** такие как ключи хранения (основной сайт) или учетные данные HTTP (шлюз).
    - Для получения всех конфигураций, включая [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) чувствительные параметры, используйте в будущем.Обратите внимание, что пользователи с ролью "Reader" не смогут использовать этот метод. Это позволяет осуществлять детальный контроль, над которым пользователи могут получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные http шлюза, используйте. [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-)
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)в настоящее время амортизативе и был заменен [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK для Java

Обновление до [версии 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) или позже HDInsight SDK для Java. Минимальные изменения кода могут потребоваться, если вы используете метод, затронутый этими изменениями:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)больше не будет **возвращать чувствительные параметры,** такие как ключи хранения (основной сайт) или учетные данные HTTP (шлюз).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)в настоящее время обесценился.

### <a name="sdk-for-go"></a>SDK для идти

Обновление до [версии 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) или позже HDInsight SDK для Go. Минимальные изменения кода могут потребоваться, если вы используете метод, затронутый этими изменениями:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)больше не будет **возвращать чувствительные параметры,** такие как ключи хранения (основной сайт) или учетные данные HTTP (шлюз).
    - Для получения всех конфигураций, включая [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) чувствительные параметры, используйте в будущем.Обратите внимание, что пользователи с ролью "Reader" не смогут использовать этот метод. Это позволяет осуществлять детальный контроль, над которым пользователи могут получить доступ к конфиденциальной информации для кластера. 
    - Чтобы получить только учетные данные http шлюза, используйте. [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings)
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)в настоящее время амортизативе и был заменен [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Обновление до [версии Az PowerShell 2.0.0](https://www.powershellgallery.com/packages/Az) или позже, чтобы избежать перерывов.  Минимальные изменения кода могут потребоваться, если вы используете метод, затронутый этими изменениями.
- `Grant-AzHDInsightHttpServicesAccess`в настоящее время амортизированы `Set-AzHDInsightGatewayCredential` и был заменен на новый cmdlet.
- `Get-AzHDInsightJobOutput`был обновлен для поддержки детального ролевой доступности ключа хранения.
    - На пользователей с ролями оператора, участника или владельца кластера HDInsight это не повлияет.
    - Пользователям, у которых есть только `DefaultStorageAccountKey` роль Читателя, необходимо будет четко указать параметр.
- `Revoke-AzHDInsightHttpServicesAccess`в настоящее время обесценился. HTTP теперь всегда включен, так что этот cmdlet больше не нужен.
 Смотрите [АЗ. Руководство hdInsight по миграции](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) для получения более подробной информации.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Добавьте назначение роли оператора кластера HDInsight пользователю

Пользователь с ролью [владельца](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) может назначить пользователям роль [оператора кластера HDInsight,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) которую вы хотели бы прочитать/написать доступ к чувствительным значениям конфигурации кластера HDInsight (например, учетные данные шлюза кластера и ключи учетной записи хранилища).

### <a name="using-the-azure-cli"></a>Использование Azure CLI

Самый простой способ добавить это `az role assignment create` назначение роли — использовать команду в Azure CLI.

> [!NOTE]
> Эта команда должна управляться пользователем с ролью Владельца, так как только они могут предоставить эти разрешения. Это `--assignee` имя директора службы или адрес электронной почты пользователя, которому вы хотите назначить роль оператора кластера HDInsight. Если вы получили ошибку с недостаточным разрешением, смотрите часто задаваемые вопросы ниже.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Роль гранта на уровне ресурса (кластера)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Роль гранта на уровне ресурсной группы

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Роль гранта на уровне подписки

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Использование портала Azure

Вы можете использовать портал Azure для добавления назначения роли оператора кластера HDInsight для пользователя. Просмотрите документацию, [Управление доступом к ресурсам Azure с помощью RBAC и портала Azure - Добавить назначение ролей.](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment)

## <a name="faq"></a>часто задаваемые вопросы

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Почему после обновления запросов API и (или) средства отображается ответ 403 Forbidden (запрещено)?

Конфигурации кластеров теперь отстают от детального `Microsoft.HDInsight/clusters/configurations/*` управления доступом на основе ролей и требуют разрешения на доступ к ним. Чтобы получить это разрешение, назначайте роль оператора кластера HDInsight, участника или владельца пользователю или руководителю службы, пытаясь получить доступ к конфигурациям.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Почему при запуске команды Azure CLI при запуске команды Azure CLI присваивают роль оператора кластера HDInsight другому пользователю или главному обслуживанию?

Помимо роли владельца, пользователь или главный поставщик службы, исполняющий команду, должен иметь достаточное количество разрешений AAD для поиска идентиверев объектов назначенного. Это сообщение указывает на недостаточное количество разрешений AAD. Попробуйте заменить `-–assignee` `–assignee-object-id` аргумент и увядите идентификатор объекта назначенного в качестве параметра вместо имени (или основного идентификатора в случае управляемого удостоверения). Для получения дополнительной информации ознакомьтесь с разделом дополнительных параметров [назначения роли az, чтобы получить дополнительную](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) информацию.

Если это все еще не работает, обратитесь к вашему aAD-адуну, чтобы получить правильные разрешения.

### <a name="what-will-happen-if-i-take-no-action"></a>Что произойдет, если я не предприму никаких действий?

Начиная с 3 сентября 2019 года, `GET /configurations` звонки `POST /configurations/gateway` больше `GET /configurations/{configurationName}` не будут возвращать любую информацию, и вызов больше не будет возвращать чувствительные параметры, такие как ключи учетной записи хранилища или кластерный пароль. То же самое относится и к соответствующим методам SDK и Смдлетс PowerShell.

Если вы используете старую версию одного из инструментов для Visual Studio, VSCode, IntelliJ или Eclipse, упомянутых выше, они больше не будут функционировать до тех пор, пока вы не обновите.

Для получения более подробной информации смотрите соответствующий раздел этого документа для вашего сценария.
