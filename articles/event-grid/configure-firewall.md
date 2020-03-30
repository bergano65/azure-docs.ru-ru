---
title: Нанастройка брандмауэра IP для тем или доменов Azure Event Grid (Предварительный просмотр)
description: В этой статье описывается, как настроить настройки брандмауэра для тем или доменов Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299872"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>Нанастройка брандмауэра IP для тем или доменов Azure Event Grid (Предварительный просмотр)
По умолчанию тема и домен доступны из Интернета до тех пор, пока запрос поставляется с проверкой подлинности и авторизации. С помощью IP-брандмауэра его можно ограничить только набором адресов IPv4 или диапазонов адресов IPv4 в обозначении [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Издатели, начиная с любого другого IP-адреса, будут отклонены и получат ответ 403 (Запрещенный). Для получения дополнительной информации об особенностях сетевой безопасности, поддерживаемых Event Grid, [см.](network-security.md)

В этой статье описывается, как настроить настройки брандмауэра IP для тем или доменов Azure Event Grid.

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе показано, как использовать портал Azure для создания входящих правил БРАНД-брандмауэра. Шаги, отображаемые в этом разделе, относятся к темам. Аналогичные действия можно использовать для создания входящих правил IP для **доменов.** 

1. На [портале Azure](https://portal.azure.com)перейдите к теме или домену сетки событий и переключитесь на вкладку **Сеть.**
2. Выберите **общественные сети,** чтобы все сети, включая Интернет, получить доступ к ресурсу. 

    Можно ограничить трафик с помощью правил БРАНДМС-брандмауэра на основе IP. Укажите один адрес IPv4 или ряд IP-адресов в обозначении Classless inter-domain routing (CIDR). 

    ![Страница общественных сетей](./media/configure-firewall/public-networks-page.png)
3. Выберите **частные конечные точки только** для того, чтобы доступны только частные конечные соединения для доступа к этому ресурсу. Используйте вкладку **приватных конечных точек** на этой странице для управления соединениями. 

    ![Страница общественных сетей](./media/configure-firewall/private-endpoints-page.png)
4. На панели инструментов щелкните **Сохранить**. 



## <a name="use-azure-cli"></a>Использование Azure CLI
В этом разделе показано, как использовать команды Azure CLI для создания тем с входящими правилами IP. Шаги, отображаемые в этом разделе, относятся к темам. Аналогичные действия можно использовать для создания входящих правил IP для **доменов.** 


### <a name="enable-public-network-access-for-an-existing-topic"></a>Включить доступ к общедоступной сети для существующей темы
По умолчанию общедоступный доступ к сети включен для тем и доменов. Можно ограничить трафик, нанастройки входящих правил БРАНД. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Отключить доступ к общедоступной сети для существующей темы
Когда доступ к общедоступной сети отключен для темы или домена, трафик через общедоступный Интернет не допускается. Доступ к этим ресурсам будет разрешен только частным соединениям конечных точек. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>Создание темы с помощью входящих правил ip
Следующий образец команды CLI создает тему сетки событий с входящими правилами IP в одном шаге. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>Сначала создайте тему, а затем добавьте входящие правила ip
Этот пример сначала создает тему сетки событий, а затем добавляет входящие правила IP для темы в отдельную команду. Он также обновляет входящие правила IP- иС, которые были установлены во второй команде. 

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
В этом разделе показано, как использовать команды Azure PowerShell для создания тем Azure Event Grid с входящими правилами брандмауэра IP. Шаги, отображаемые в этом разделе, относятся к темам. Аналогичные действия можно использовать для создания входящих правил IP для **доменов.** 

### <a name="prerequisite"></a>Предварительные требования
Следуйте инструкциям от [Как: Используйте портал для создания приложения Azure AD и принципала обслуживания, которое может получить доступ к ресурсам](../active-directory/develop/howto-create-service-principal-portal.md) для создания приложения Active Directory Azure и запишите следующие значения:

- Идентификатор каталога (клиента)
- Идентификатор приложения (клиента)
- Приложение (клиент) секрет

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Подготовка токенов и заголовков для вызовов REST API 
Запустите следующие предварительные команды, чтобы получить токен проверки подлинности для использования с вызовами REST API, а также авторизацией и другой информацией заголовка. 

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

### <a name="enable-public-network-access-for-an-existing-topic"></a>Включить доступ к общедоступной сети для существующей темы
По умолчанию общедоступный доступ к сети включен для тем и доменов. Можно ограничить трафик, нанастройки входящих правил БРАНД. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Отключить доступ к общедоступной сети для существующей темы
Когда доступ к общедоступной сети отключен для темы или домена, трафик через общедоступный Интернет не допускается. Доступ к этим ресурсам будет разрешен только частным соединениям конечных точек. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Создание темы сетки событий с входящими правилами в одном шаге

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


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Сначала создайте тему сетки событий, а затем добавьте входящие правила ip

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
