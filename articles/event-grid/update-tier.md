---
title: Обновление уровня ценообразования темы или домена Azure Event Grid
description: В этой статье описывается, как обновить уровень ценообразования темы или домена Azure Event Grid (основной для премиум, премиум-уровня к базовому) с помощью портала Azure, Azure CLI и Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300145"
---
# <a name="update-pricing-tier"></a>Обновление уровня ценообразования 
В этой статье показано, как обновить уровень ценообразования темы или домена Azure Event Grid с помощью портала Azure, Azure CLI и Azure PowerShell. 

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе показано, как изменить ценовой уровень темы или домена на портале Azure. 

### <a name="overview-page"></a>Страница "Обзор"
Вы можете изменить ценовой уровень темы или домена на странице **Обзор.** Ниже приводится следующий пример, как обновить тему с базового уровня на премиум-уровня. Шаги по понижению от премиум уровня до базового уровня аналогичны.

1. На [портале Azure](https://portal.azure.com)перейдите к теме или странице домена. 
2. На странице **Обзор** выберите текущий уровень ценообразования (в следующем примере он **базовый.)**
    
    ![Выберите текущий уровень ценообразования](./media/update-tier/select-tier.png)
3. На странице **уровня ценообразования** измените уровень и выберите **OK.** 

    ![Обновление ценового уровня](./media/update-tier/change-tier.png)
4. Проверьте состояние операции на странице **Уведомлений.**

    ![Состояние обновления](./media/update-tier/status.png)    
5. Подтвердите, что обновленный уровень можно увидеть на странице **«Обзор».** 

    ![Состояние обновления](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Страница "Сетевое взаимодействие"
Вы можете **перейти** от базового уровня до премиум-уровня на странице **Сети.** Вы не можете понизить от премиум уровня до базового уровня на этой странице, хотя. 

1. На [портале Azure](https://portal.azure.com)перейдите к теме или странице домена. 
2. На странице **Сети** переключитесь на вкладку **приватных конечных точек (предварительный просмотр).** 
3. Если текущий уровень ценообразования является **базовым,** вы видите следующее сообщение. Выберите его. 

    ![Обновление уровня на частной странице соединения конечных точек](./media/update-tier/private-endpoint-connections-page.png)
4. На странице **обновления до премиум-уровня ценообразования** выберите **Да**. 
    
    ![Подтвердить обновление](./media/update-tier/message-private-endpoint-connection.png)
5. Проверьте состояние операции на странице **Уведомлений.**

    ![Состояние обновления](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Использование Azure CLI
В этом разделе показаны, как использовать команды Azure CLI для изменения уровня ценообразования темы или домена. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Обновление темы с базовой на премиум

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Понизить тему с премиум-класса до базовой

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Обновление домена с базового на премиум

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Понизить домен с премиум-класса до базового

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Использование Azure PowerShell
В этом разделе показано, как использовать команды PowerShell для изменения ценового уровня темы или домена. 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Обновление темы с базовой на премиум

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Понизить тему с премиум-класса до базовой

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Обновление домена с базового на премиум

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Понизить домен с премиум-класса до базового

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Дальнейшие действия
Для тем и доменов премиум-уровня можно настроить частные конечные точки, чтобы ограничить доступ только из выбранных виртуальных сетей. Для пошаговых инструкций [см.](configure-private-endpoints.md)
