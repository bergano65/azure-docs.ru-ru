---
title: Настройка частных конечных точек для тем или доменов Azure Event Grid
description: В этой статье описывается, как настроить частные конечные точки для тем или домена Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: d08afe00c13a3f96b9526c3cb29804cfad688ddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299911"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains-preview"></a>Настройка частных конечных точек для тем или доменов Azure Event Grid (Предварительный просмотр)
Вы можете использовать [частные конечные точки,](../private-link/private-endpoint-overview.md) чтобы позволить проникновение событий непосредственно из виртуальной сети в ваши темы и домены надежно по [частной ссылке,](../private-link/private-link-overview.md) не проходя через общественный Интернет. Частная конечная точка использует IP-адрес из адресного пространства VNet для вашей темы или домена. Для получения дополнительной концептуальной [информации см.](network-security.md)

В этой статье описывается, как настроить частные конечные точки для тем или доменов.

> [!IMPORTANT]
> Функция частных конечных точек доступна для тематических и доменов только в премиум-уровне. Чтобы перейти от базового уровня к премиум-уровню, [см.](update-tier.md) 

## <a name="use-azure-portal"></a>Использование портала Azure 
В этом разделе показан способ использования портала Azure для создания частной точки для темы или домена.

> [!NOTE]
> Шаги, показанные в этом разделе, в основном для тем. Аналогичные действия можно использовать для создания частных конечных точек для **доменов.** 

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к теме или домену.
2. Переключитесь на вкладку **Сети** на странице темы. Выберите **частную конечную точку** на панели инструментов.

    ![Добавление частной конечной точки](./media/configure-private-endpoints/add-button.png)
2. Одна страница **Основы,** выполните следующие шаги: 
    1. Выберите **подписку Azure,** в которой требуется создать частную конечную точку. 
    2. Выберите **группу ресурсов Azure** для частной конечных точек. 
    3. Введите **имя** для конечной точки. 
    4. Выберите **область** для конечных точек. Ваша частная конечная точка должна находиться в том же регионе, что и виртуальная сеть, но может находиться в другом регионе от ресурса частной ссылки (в данном примере тема сетки событий). 
    5. Затем выберите **кнопку «Следующий: >ресурсов»** в нижней части страницы. 

      ![Частная конечная точка - страница основы](./media/configure-private-endpoints/basics-page.png)
3. На странице **Ресурса** выполните следующие действия: 
    1. Для метода подключения, если вы выберете **ресурс «Подключение к ресурсу Azure» в моем каталоге,** выполните следующие действия. В этом примере показано, как подключиться к ресурсу Azure в каталоге. 
        1. Выберите **подписку Azure,** в которой существует **тема/домен.** 
        1. Для **типа ресурсов**, Выберите **Microsoft.EventGrid /темы** или **Microsoft.EventGrid /домены** для **типа ресурса**.
        2. Для **ресурса**выберите тему/домен из списка выпадающих. 
        3. Подтвердите, что **целевой субресурс** настроен на **тему** или **домен** (на основе выбранного типа ресурсов).    
        4. Выберите **следующую: Конфигурация >** кнопку в нижней части страницы. 

            ![Частная конечная точка - страница ресурса](./media/configure-private-endpoints/resource-page.png)
    2. При **выборе подключения к ресурсу с использованием идентификатора ресурсов или псевдонима**выполните следующие действия:
        1. Введите идентификатор ресурса. Например: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Для **ресурса,** введите **тему** или **домен**. 
        3. (необязательно) Добавьте сообщение запроса. 
        4. Выберите **следующую: Конфигурация >** кнопку в нижней части страницы. 

            ![Частная конечная точка - страница ресурса](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. На странице **Configuration** вы выбираете подсеть в виртуальной сети, где требуется развернуть частную конечную точку. 
    1. Выберите **виртуальную сеть.** В списке выпадающих списков перечислены только виртуальные сети в выбранной в настоящее время подписке и местоположении. 
    2. Выберите **подсеть** в выбранной вами виртуальной сети. 
    3. Выберите **далее: Теги >кнопку** в нижней части страницы. 

    ![Частная конечная точка - страница конфигурации](./media/configure-private-endpoints/configuration-page.png)
5. На странице **теги** создайте любые теги (имена и значения), которые необходимо связать с частным ресурсом конечных точек. Затем выберите **Кнопку «Обзор» и создайте** кнопку в нижней части страницы. 
6. В **обзоре создайте,** просмотрите все настройки и выберите **Создать** для создания частной конечной точки. 

    ![Частная конечная точка - обзор & создать страницу](./media/configure-private-endpoints/review-create-page.png)
    

## <a name="manage-private-link-connection"></a>Управление подключением Приватного канала

При создании частной конечной точки подключение должно быть утверждено. Если ресурс, для которого вы создаете частную конечную точку, находится в каталоге, вы можете утвердить запрос на подключение при условии наличия достаточных разрешений. Если вы подключаетесь к ресурсу Azure в другом каталоге, необходимо дождаться, когда владелец этого ресурса утвердит запрос на подключение.

Существует четыре состояния подготовки:

| Действия по обслуживанию | Состояние частной конечной точки объекта-получателя службы | Описание |
|--|--|--|
| None | Ожидает | Соединение создается вручную и ожидает утверждения от частного владельца ресурса Link. |
| Утверждение | Approved | Подключение утверждено автоматически или вручную и готово к использованию. |
| Reject | Отклонено | Подключение отклонил владелец ресурса Приватного канала. |
| Удалить | Отключено | Подключение удалил владелец ресурса Приватного канала. Частная конечная точка станет информативной и подлежит удалению для очистки. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Как управлять частным соединением конечных точек
Следующие разделы показывают, как утвердить или отклонить частное соединение конечных точек. 

1. Войдите на [портал Azure](https://portal.azure.com).
1. В панели поиска введите **темы Event Grid** или **домены Event Grid.**
1. Выберите **тему** или **домен,** которым вы хотите управлять.
1. Выберите вкладку **Сеть.**
1. Если есть какие-либо соединения, которые находятся на рассмотрении, вы увидите соединение, указанное в **положении ожидания** в состоянии подготовки. 

### <a name="to-approve-a-private-endpoint"></a>Утвердить частную конечную точку
Можно утвердить частную конечную точку, находясь в состоянии ожидания. Чтобы утвердить, выполните следующие действия: 

> [!NOTE]
> Шаги, показанные в этом разделе, в основном для тем. Аналогичные действия можно использовать для утверждения частных конечных точек для **доменов.** 

1. Выберите **частную конечную точку,** которая вы хотите утвердить, и выберите **Утверждение** на панели инструментов.

    ![Частная конечная точка - состояние в ожидании](./media/configure-private-endpoints/pending.png)
1. В поле диалога **«Утвердить соединение»** введите комментарий (по желанию) и выберите **«Да».** 

    ![Частная конечная точка - утвердить](./media/configure-private-endpoints/approve.png)
1. Подтвердите, что статус конечной точки **утвержден.** 

    ![Частная конечная точка - утвержденное состояние](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Отклонить частную конечную точку
Можно отклонить частную конечную точку, находясь в состоянии ожидания или утвержденном состоянии. Чтобы отклонить, выполните следующие действия: 

> [!NOTE]
> Шаги, отображаемые в этом разделе, относятся к темам. Аналогичные действия можно использовать для **отклонения частных конечных**точек для доменов. 

1. Выберите **частную конечную точку,** от которых вы хотите отказаться, и выберите **Отклонить** на панели инструментов.

    ![Частная конечная точка - отклонить](./media/configure-private-endpoints/reject-button.png)
1. На поле диалогов **соединения Отклонить,** введите комментарий (необязательно), и выберите **Да**. 

    ![Частная конечная точка - отклонить](./media/configure-private-endpoints/reject.png)
1. Подтвердите, что статус конечной точки **отклонен.** 

    ![Частная конечная точка - отклоненное состояние](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Вы не можете утвердить частную конечную точку на портале Azure после того, как она будет отклонена. 


## <a name="use-azure-cli"></a>Использование Azure CLI
Для создания частной конечной точки используйте метод [создания частной точки сети az,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) показанный в следующем примере:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Для описания параметров, используемых в примере, см. [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) Несколько моментов, которые следует отметить в этом примере: 

- Для, `private-connection-resource-id`указать идентификатор ресурса **темы** или **домена**. В предыдущем примере используется тип: тема.
- для, `group-ids` `topic` указать или `domain`. В предыдущем примере `topic` используется. 

Чтобы удалить частную конечную точку, используйте метод [удаления частной точки сети az,](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) показанный в следующем примере:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Шаги, отображаемые в этом разделе, относятся к темам. Аналогичные действия можно использовать для создания частных конечных точек для **доменов.** 

### <a name="create-a-private-endpoint"></a>Создание частной конечной точки
Вот пример сценария, который создает следующие ресурсы Azure:

- Группа ресурсов
- Виртуальная сеть
- Подсеть в виртуальной сети
- Тема Azure Event Grid (премиум-уровень)
- Частная конечная точка для темы

> [!NOTE]
> Шаги, отображаемые в этом разделе, относятся к темам. Аналогичные действия можно использовать для создания частных конечных точек для доменов.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# URI for the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicUri="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>?api-version=2020-04-01-preview"

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az rest --method put \
    --uri $topicUri \
    --body "{\""location\"":\""LOCATION\"", \""sku\"": {\""name\"": \""premium\""}, \""properties\"": {\""publicNetworkAccess\"":\""Disabled\""}}"

# verify that the topic was created.
az rest --method get \
    --uri $topicUri

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az rest --method get \
    --uri $topicUri

```

### <a name="approve-a-private-endpoint-connection"></a>Утвердить приватное соединение конечных точек
Следующий пример фрагмента CLI показывает, как утвердить частное соединение конечных точек. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""approved\"",\""description\"":\""connection approved\"", \""actionsRequired\"": \""none\""}}}"
```


### <a name="reject-a-private-endpoint-connection"></a>Отклонить частное соединение конечных точек
Следующий пример фрагмента CLI показывает, как отклонить частное соединение конечных точек. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""rejected\"",\""description\"":\""connection rejected\"", \""actionsRequired\"": \""none\""}}}"
```


## <a name="use-powershell"></a>Использование PowerShell
В этом разделе показано, как создать частную конечную точку для темы или домена с помощью PowerShell. 

### <a name="prerequisite"></a>Предварительные требования
Следуйте инструкциям от [Как: Используйте портал для создания приложения Azure AD и принципала обслуживания, которое может получить доступ к ресурсам](../active-directory/develop/howto-create-service-principal-portal.md) для создания приложения Azure Active Directory и запишите значения для **идентификатора каталога (арендатора),** **идентификатора приложения (клиента)** и секретного приложения **(клиента).** 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Подготовка токенов и заголовков для вызовов REST API 
Запустите следующие предварительные команды, чтобы получить токен проверки подлинности для использования с вызовами REST API и авторизацией и другой информацией заголовка. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Создание подсети с отключенными политиками сети конечных точек

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Создание темы сетки событий с помощью частной конечной точки

> [!NOTE]
> Шаги, отображаемые в этом разделе, относятся к темам. Аналогичные действия можно использовать для создания частных конечных точек для **доменов.** 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="premium"}; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-04-01-preview"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

При проверке создания конечной точки следует увидеть результат, аналогичный следующему:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Утвердить приватное соединение конечных точек
Следующий пример фрагмента PowerShell показывает, как утвердить частную конечную точку. 

> [!NOTE]
> Шаги, отображаемые в этом разделе, относятся к темам. Аналогичные действия можно использовать для утверждения частных конечных точек для **доменов.** 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Отклонить частное соединение конечных точек
Ниже приводится следующий пример, как отклонить частную конечную точку с помощью PowerShell. Вы можете получить GUID для частной конечной точки из результата предыдущей команды GET. 

> [!NOTE]
> Шаги, отображаемые в этом разделе, относятся к темам. Аналогичные действия можно использовать для **отклонения частных конечных**точек для доменов. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" ` 
    -Headers $Headers
```

Вы можете утвердить соединение даже после того, как оно отклонено через API. Если вы используете портал Azure, вы не можете утвердить конечную точку, которая была отклонена. 

## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать о том, как настроить настройки БРАНДМН, [см.](configure-firewall.md)