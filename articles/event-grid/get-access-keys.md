---
title: Получение ключа доступа для ресурса сетки событий
description: В этой статье описывается получение ключа доступа для раздела или домена сетки событий.
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: 2e258cebe1652178a67c292d0cccab3a151eddf7
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624841"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Получение ключей доступа для ресурсов сетки событий (разделы или домены)
Ключи доступа используются для проверки подлинности событий публикации приложения в ресурсах службы "Сетка событий Azure" (разделы и домены). Рекомендуется регулярно создавать ключи и хранить их в безопасном месте. Вы предоставляете два ключа доступа, которые позволяют поддерживать подключения с помощью одного ключа при повторном создании другого.

В этой статье описывается, как получить ключи доступа для ресурса сетки событий (раздела или домена) с помощью портал Azure, PowerShell или CLI. 

## <a name="azure-portal"></a>Портал Azure
В портал Azure перейдите на вкладку **ключи доступа** в **разделе сетки событий** или на странице **домена сетки событий** для раздела или домена.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Страница &quot;ключи доступа&quot;":::

## <a name="azure-powershell"></a>Azure PowerShell
Используйте команду [Get-азевентгридтопиккэй](/powershell/module/az.eventgrid/get-azeventgridtopickey) , чтобы получить ключи доступа для разделов. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Используйте команду [Get-азевентгриддомаинкэй](/powershell/module/az.eventgrid/get-azeventgriddomainkey) , чтобы получить ключи доступа для доменов. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Azure CLI
Для получения ключей доступа используйте раздел [AZ eventgrid Topic List](/cli/azure/eventgrid/topic/key?view=azure-cli-latest#az-eventgrid-topic-key-list) . 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Чтобы получить ключи доступа для доменов, используйте команду [AZ eventgrid Domain Key List](/cli/azure/eventgrid/domain/key?view=azure-cli-latest#az-eventgrid-domain-key-list) . 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Следующие шаги
См. следующую статью: [Проверка подлинности клиентов публикации](security-authenticate-publishing-clients.md). 
