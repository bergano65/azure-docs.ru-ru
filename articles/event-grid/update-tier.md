---
title: Обновление ценовой категории раздела или домена службы "Сетка событий Azure"
description: В этой статье описывается, как обновить ценовую категорию раздела или домена службы "Сетка событий Azure" (базовый для Premium, Premium — базовый) с помощью портал Azure, Azure CLI и Azure PowerShell.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300145"
---
# <a name="update-pricing-tier"></a>Обновить ценовую категорию 
В этой статье показано, как обновить ценовую категорию раздела или домена службы "Сетка событий Azure" с помощью портал Azure, Azure CLI и Azure PowerShell. 

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе показано, как изменить ценовую категорию раздела или домена в портал Azure. 

### <a name="overview-page"></a>Страница "Обзор"
Ценовую категорию раздела или домена можно изменить на странице **Обзор** . В следующем примере показано, как обновить раздел с уровня "базовый" до уровня "Премиум". Действия по переходу с уровня "Премиум" на уровень "базовый" выполняются аналогично.

1. В [портал Azure](https://portal.azure.com)перейдите на страницу раздела или домена. 
2. На странице **Обзор** выберите текущую ценовую категорию (в следующем примере это **базовый**уровень).
    
    ![Выбрать текущую ценовую категорию](./media/update-tier/select-tier.png)
3. На странице **ценовая** Категория измените уровень и нажмите кнопку **ОК**. 

    ![Обновление ценовой категории](./media/update-tier/change-tier.png)
4. Проверьте состояние операции на странице **уведомления** .

    ![Состояние обновления](./media/update-tier/status.png)    
5. Убедитесь, что на странице **Обзор** отображается обновленный уровень. 

    ![Состояние обновления](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Страница "Сетевые подключения"
Вы можете **Обновить** уровень "базовый" до уровня "Премиум" на странице " **сеть** ". Вы не сможете перейти с уровня "Премиум" на уровень "базовый" на этой странице. 

1. В [портал Azure](https://portal.azure.com)перейдите на страницу раздела или домена. 
2. На странице **сеть** перейдите на вкладку **подключения частной конечной точки (Предварительная версия)** . 
3. Если текущая ценовая категория — **Basic**, появится следующее сообщение. Выберите его. 

    ![Страница "уровни обновления на частных подключениях конечной точки"](./media/update-tier/private-endpoint-connections-page.png)
4. На странице **изменение ценовой категории Premium** выберите **Да**. 
    
    ![Подтверждение обновления](./media/update-tier/message-private-endpoint-connection.png)
5. Проверьте состояние операции на странице **уведомления** .

    ![Состояние обновления](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Использование Azure CLI
В этом разделе показано, как использовать команды Azure CLI для изменения ценовой категории раздела или домена. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Обновление раздела с Basic на Premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Переход на более раннюю версию статьи с уровня "Премиум" на "базовый"

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Обновление домена с базовой на Premium

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Понижение уровня домена с Premium на Basic

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Использование Azure PowerShell
В этом разделе показано, как использовать команды PowerShell для изменения ценовой категории раздела или домена. 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Обновление раздела с Basic на Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Переход на более раннюю версию статьи с уровня "Премиум" на "базовый"

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Обновление домена с базовой на Premium

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Понижение уровня домена с Premium на Basic

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>Дальнейшие действия
Для разделов и доменов уровня "Премиум" можно настроить частные конечные точки, чтобы ограничить доступ только из выбранных виртуальных сетей. Пошаговые инструкции см. в разделе [Настройка частных конечных точек](configure-private-endpoints.md).
