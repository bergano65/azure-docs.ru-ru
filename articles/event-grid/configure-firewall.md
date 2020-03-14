---
title: Настройка брандмауэра IP для разделов или доменов службы "Сетка событий Azure" (Предварительная версия)
description: В этой статье описывается, как настроить параметры брандмауэра для разделов и доменов сетки событий.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299872"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>Настройка брандмауэра IP для разделов или доменов службы "Сетка событий Azure" (Предварительная версия)
По умолчанию раздел и домен доступны через Интернет, если запрос сопровождается действительной проверкой подлинности и авторизацией. С помощью IP-брандмауэра вы можете ограничить его более ограниченным набором адресов IPv4 или диапазонами IPv4-адресов в нотации [CIDR (без класса для маршрутизации между доменами)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Издатели, исходящие из любого другого IP-адреса, будут отклонены и получат ответ 403 (запрещено). Дополнительные сведения о функциях сетевой безопасности, поддерживаемых службой "Сетка событий", см. в разделе [Сетевая безопасность для сетки событий](network-security.md).

В этой статье описывается, как настроить параметры брандмауэра IP для разделов и доменов службы "Сетка событий Azure".

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе показано, как использовать портал Azure для создания правил брандмауэра для входящих IP-адресов. Действия, приведенные в этом разделе, предназначены для разделов. Аналогичные действия можно использовать для создания правил IP для входящего трафика для **доменов**. 

1. В [портал Azure](https://portal.azure.com)перейдите к разделу или домену сетки событий и перейдите на вкладку **Сетевые подключения** .
2. Выберите **общедоступные сети** , чтобы разрешить всем сетям, включая Интернет, доступ к ресурсу. 

    Трафик можно ограничить с помощью правил брандмауэра на основе IP-адресов. Укажите один адрес IPv4 или диапазон IP-адресов в нотации, зависящей от класса междоменной маршрутизации (CIDR). 

    ![Страница общедоступных сетей](./media/configure-firewall/public-networks-page.png)
3. Выберите **частные конечные точки только** , чтобы разрешить доступ к этому ресурсу только частным конечным точкам. Используйте вкладку **подключения к частной конечной точке** на этой странице для управления подключениями. 

    ![Страница общедоступных сетей](./media/configure-firewall/private-endpoints-page.png)
4. На панели инструментов щелкните **Сохранить**. 



## <a name="use-azure-cli"></a>Использование Azure CLI
В этом разделе показано, как использовать команды Azure CLI для создания разделов с правилами входящих IP-адресов. Действия, приведенные в этом разделе, предназначены для разделов. Аналогичные действия можно использовать для создания правил IP для входящего трафика для **доменов**. 


### <a name="enable-public-network-access-for-an-existing-topic"></a>Включить доступ к общедоступной сети для существующего раздела
По умолчанию доступ к общедоступной сети включен для разделов и доменов. Трафик можно ограничить, настроив правила брандмауэра для входящих IP-адресов. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Отключить доступ к общедоступной сети для существующего раздела
Если для раздела или домена отключен доступ к общедоступной сети, трафик через общедоступный Интернет не разрешается. Доступ к этим ресурсам будет разрешен только для подключений к частным конечным точкам. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>Создать раздел с правилами входящего трафика IP
Следующий пример команды CLI создает раздел сетки событий с правилами входящего трафика IP за один шаг. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>Сначала создайте раздел, а затем добавьте правила IP-адресов для входящего трафика.
В этом примере сначала создается таблица "Сетка событий", а затем в отдельной команде добавляются правила IP-адресов для входящего трафика для раздела. Он также обновляет правила IP-адресов входящего трафика, которые были заданы во второй команде. 

```azurecli-interactive

# create the event grid topic first
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\""}

# add inbound IP rules
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}

# later, update topic with additional ip rules or remove them. 
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""}, {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```


## <a name="use-powershell"></a>Использование PowerShell
В этом разделе показано, как использовать команды Azure PowerShell для создания разделов сетки событий Azure с правилами брандмауэра для входящих IP-адресов. Действия, приведенные в этом разделе, предназначены для разделов. Аналогичные действия можно использовать для создания правил IP для входящего трафика для **доменов**. 

### <a name="prerequisite"></a>Предварительные требования
Следуйте инструкциям из [руководства. Использование портала для создания приложения Azure AD и субъекта-службы, которые могут получать доступ к ресурсам](../active-directory/develop/howto-create-service-principal-portal.md) для создания Azure Active Directory приложения и заметок следующих значений:

- Идентификатор каталога (клиента)
- Идентификатор приложения (клиента)
- Секрет приложения (клиента)

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Подготовка токена и заголовков для вызовов REST API 
Выполните следующие предварительные команды, чтобы получить маркер проверки подлинности для использования с REST API вызовами, а также авторизацией и другими сведениями о заголовке. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>Включить доступ к общедоступной сети для существующего раздела
По умолчанию доступ к общедоступной сети включен для разделов и доменов. Трафик можно ограничить, настроив правила брандмауэра для входящих IP-адресов. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Отключить доступ к общедоступной сети для существующего раздела
Если для раздела или домена отключен доступ к общедоступной сети, трафик через общедоступный Интернет не разрешается. Доступ к этим ресурсам будет разрешен только для подключений к частным конечным точкам. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Создание раздела сетки событий с правилами входящего трафика за один шаг

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Сначала создайте сетку событий, а затем добавьте правила IP-адресов для входящего трафика.

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"}, @{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со сведениями о [мониторинге доставки сообщений в службе "Сетка событий"](monitor-event-delivery.md).
* Дополнительные сведения о ключе аутентификации см. в статье [Сетка событий: безопасность и проверка подлинности](security-authentication.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
