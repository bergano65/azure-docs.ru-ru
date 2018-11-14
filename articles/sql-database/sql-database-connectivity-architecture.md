---
title: Архитектура подключений к базе данных SQL Azure | Документация Майкрософт
description: В этом документе описывается архитектура подключений к базе данных SQL Azure из Azure или извне Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: 11133a24f4446478dcc7f38ed50eb36de8843442
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978407"
---
# <a name="azure-sql-database-connectivity-architecture"></a>Архитектура подключений к базе данных SQL Azure

В этой статье описывается архитектура подключения к базе данных SQL Azure и объясняется, как функционируют различные компоненты при передаче трафика к вашему экземпляру базы данных SQL Azure. Эти компоненты подключения к базе данных SQL Azure направляют сетевой трафик к базе данных Azure с помощью клиентов, подключающихся из Azure, и клиентов, подключающихся извне Azure. В этой статье также приведены примеры сценариев, позволяющие изменить параметры подключения, а также рекомендации по изменению параметров подключения по умолчанию.

## <a name="connectivity-architecture"></a>Архитектура подключения

На следующей схеме показана общая архитектура подключений к базе данных SQL Azure.

![Общий вид архитектуры](./media/sql-database-connectivity-architecture/architecture-overview.png)

Ниже описывается, как устанавливается подключение к базе данных SQL Azure с помощью программного балансировщика нагрузки (SLB) и шлюза базы данных SQL Azure.

- Клиенты подключаются к SLB, который использует общедоступный IP-адрес и ожидает передачи данных через порт 1433.
- SLB направляет трафик в шлюз базы данных SQL Azure.
- Шлюз, в зависимости от политики действующие подключения, перенаправляет или проксирует трафик к правильному ПО промежуточного слоя.
- ПО промежуточного слоя прокси-сервера перенаправляет трафик в соответствующую базу данных SQL Azure.

> [!IMPORTANT]
> У каждого из этих компонентов есть встроенная защита от отказа в обслуживании (DDoS) на сетевом и прикладном уровнях.

## <a name="connection-policy"></a>Политика подключения

База данных SQL Azure поддерживает следующие три варианта для параметра политики подключения сервера базы данных SQL.

- **Перенаправление (рекомендуется).** Клиенты устанавливают подключения непосредственно к размещению узла базы данных. Чтобы разрешить возможность подключения, необходимо, чтобы клиенты разрешили правила брандмауэра для исходящего трафика для всех IP-адресов Azure в регионе (используйте группы безопасности сети (NSG) с [тегами службы](../virtual-network/security-overview.md#service-tags)), не только для IP-адресов шлюза базы данных SQL Azure. Поскольку пакеты переходят непосредственно к базе данных, задержка и пропускная способность увеличили производительность.
- **Прокси-сервер.** В этом режиме все подключения проксируют через шлюзы базы данных SQL Azure. Чтобы разрешить возможность подключения, клиентам необходимы правила брандмауэра для исходящего трафика, которые разрешают только адреса IP-адреса шлюза базы данных SQL Azure (два IP-адреса на регион). Этот режим может стать результатом большего времени задержки и меньшей пропускной способности, в зависимости от характера рабочей нагрузки. Чтобы задержка была минимальной, а пропускная способность высокой, настоятельно рекомендуется использовать политику перенаправления подключения через политику подключения прокси-сервера.
- **Значение по умолчанию.** Это политика подключения, которая влияет на все серверы после создания, пока вы явным образом не измените политику подключения на прокси-сервер или перенаправление. Действующая политика зависит от того, создается подключение из Azure (перенаправление) или за его пределами (прокси-сервер).

## <a name="connectivity-from-within-azure"></a>Подключение из Azure

Для подключений из Azure к серверу, созданному 10 ноября 2018 г., по умолчанию действует политика подключения **Перенаправление**. Политика подключения **Перенаправление** означает, что после установления сеанса TCP с базой данных SQL Azure сеанс клиента перенаправляется в ПО промежуточного уровня прокси-сервера с измененным виртуальным IP-адресом назначения, то есть вместо адреса шлюза базы данных SQL Azure указывается адрес ПО промежуточного уровня прокси-сервера. После этого все последующие пакеты передаются непосредственно через ПО промежуточного уровня прокси-сервера, минуя шлюз базы данных SQL Azure. Этот поток трафика представлен на схеме ниже.

![Общий вид архитектуры](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

> [!IMPORTANT]
> Если вы создали сервер базы данных SQL до 10 ноября 2018 года, политику подключения было задано в форме **прокси-сервера**. Чтобы повысить производительность, при использовании конечных точек служб мы настоятельно рекомендуем установить политику подключения **Перенаправление**. При изменении политики подключения на **Перенаправление** будет недостаточно разрешить вашей группе безопасности сети исходящие подключения к перечисленным ниже IP-адресам шлюза базы данных SQL Azure. Необходимо разрешить исходящие подключения ко всем IP-адресам базы данных SQL Azure. Это можно сделать с помощью тегов служб в группах безопасности сети. Дополнительные сведения см. в разделе [Теги служб](../virtual-network/security-overview.md#service-tags).

## <a name="connectivity-from-outside-of-azure"></a>Подключение извне Azure

Для подключений извне Azure по умолчанию действует политика подключения **Прокси-сервер**. Политика **Прокси-сервер** означает, что устанавливается сеанс TCP через шлюз базы данных SQL Azure и все последующие пакеты проходят через этот шлюз. Этот поток трафика представлен на схеме ниже.

![Общий вид архитектуры](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>IP-адреса шлюза базы данных SQL Azure

Чтобы иметь возможность подключения к базе данных SQL Azure из локальных ресурсов, необходимо разрешить исходящий сетевой трафик к шлюзу базы данных SQL Azure для своего региона Azure. При подключении в режиме прокси-сервера, который используется по умолчанию при подключении из локальных ресурсов, подключения проходят только через шлюз.

В следующей таблице перечислены основные и дополнительные IP-адреса шлюза базы данных SQL Azure для всех областей данных. В некоторых регионах существует два IP-адреса. В этих регионах основным IP-адресом является текущий IP-адрес шлюза, а второй IP-адрес — это IP-адрес отработки отказа. Адрес отработки отказа — это адрес, на который можно переместить сервер, чтобы сохранить высокий уровень доступности службы. Для этих регионов рекомендуется разрешить исходящий трафик для обоих IP-адресов. Второй IP-адрес принадлежит корпорации Майкрософт и не ожидает передачи данных от каких-либо служб, пока не будет активирован базой данных SQL Azure для приема подключений.

| Имя региона | Основной IP-адрес | Дополнительный IP-адрес |
| --- | --- |--- |
| Восточная часть Австралии | 191.238.66.109 | 13.75.149.87 |
| Юго-Восточная Австралия | 191.239.192.109 | 13.73.109.251 |
| Южная часть Бразилии | 104.41.11.5 | |
| Центральная Канада | 40.85.224.249 | |
| Восточная Канада | 40.86.226.166 | |
| Центральный регион США | 23.99.160.139 | 13.67.215.62 |
| Восточный Китай 1 | 139.219.130.35 | |
| Восточный Китай 2 | 40.73.82.1 | |
| Северный Китай 1 | 139.219.15.17 | |
| Северный Китай 2 | 40.73.50.0 | |
| Восточная Азия | 191.234.2.139 | 52.175.33.150 |
| Восточная часть США 1 | 191.238.6.43 | 40.121.158.30 |
| Восток США 2 | 191.239.224.107 | 40.79.84.180 * |
| Центральная Франция | 40.79.137.0 | 40.79.129.1 |
| Центральная Германия | 51.4.144.100 | |
| Северо-восточная Германия | 51.5.144.179 | |
| Центральная Индия | 104.211.96.159 | |
| Южная Индия | 104.211.224.146 | |
| Западная Индия | 104.211.160.80 | |
| Восточная часть Японии | 191.237.240.43 | 13.78.61.196 |
| Западная часть Японии | 191.238.68.11 | 104.214.148.156 |
| Центральная Корея | 52.231.32.42 | |
| Южная Корея | 52.231.200.86 |  |
| Центрально-северная часть США | 23.98.55.75 | 23.96.178.199 |
| Северная Европа | 191.235.193.75 | 40.113.93.91 |
| Центрально-южная часть США | 23.98.162.75 | 13.66.62.124 |
| Юго-Восточная Азия | 23.100.117.95 | 104.43.15.0 |
| Север Соединенного Королевства | 13.87.97.210 | |
| Южная часть Соединенного Королевства 1 | 51.140.184.11 | |
| Юг Соединенного Королевства 2 | 13.87.34.7 | |
| Западная часть Великобритании | 51.141.8.11 | |
| Западно-центральная часть США | 13.78.145.25 | |
| Западная Европа | 191.237.232.75 | 40.68.37.158 |
| Западная часть США 1 | 23.99.34.75 | 104.42.238.205 |
| Западный регион США 2 | 13.66.226.202 | |
||||

\***Примечание.** В регионе *Восточная часть США 2* также доступны третичные IP-адреса `52.167.104.0`.

## <a name="change-azure-sql-database-connection-policy"></a>Изменение политики подключения для базы данных SQL Azure

Чтобы изменить политику подключения базы данных SQL Azure для сервера базы данных SQL Azure, используйте команду [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

- Если задана политика подключения **Прокси-сервер**, то всех сетевые пакеты проходят через шлюз базы данных SQL Azure. Для этого режима необходимо разрешить исходящий трафик только через IP-адрес шлюза базы данных SQL Azure. В режиме **Прокси-сервер** задержка выше, чем в режиме **Перенаправление**.
- Если используется политика подключения **Перенаправление**, то все сетевые пакеты передаются непосредственно в ПО промежуточного уровня прокси-сервера. Для этого режима необходимо разрешить исходящий трафик для нескольких IP-адресов.

## <a name="script-to-change-connection-settings-via-powershell"></a>Сценарий для изменения параметров подключения через PowerShell

> [!IMPORTANT]
> Для работы этого сценария требуется [модуль Azure PowerShell](/powershell/azure/install-azurerm-ps).
>

Приведенный ниже сценарий PowerShell показывает, как изменить политику подключения.

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Сценарий изменения параметров подключения через Azure CLI

> [!IMPORTANT]
> Для работы этого сценария требуется [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

В приведенном ниже сценарии CLI показано, как изменить политику подключения.

```azurecli-interactive
<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy

</pre>
```

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о том, как изменить политику подключения базы данных SQL Azure для сервера базы данных SQL Azure, см. в статье о команде [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Сведения о поведении подключения к базе данных SQL Azure клиентов, использующих ADO.NET 4.5 или более поздней версии, см. в разделе [Порты для ADO.NET 4.5, отличные от порта 1433](sql-database-develop-direct-route-ports-adonet-v12.md).
- Общая информация о разработке приложений приведена в разделе [Обзор разработки приложений баз данных SQL](sql-database-develop-overview.md).
