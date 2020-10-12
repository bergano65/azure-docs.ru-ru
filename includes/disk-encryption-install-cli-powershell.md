---
title: включить файл
description: включить файл
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "72511515"
---
Шифрованием дисков Azure можно управлять с помощью [Azure CLI](/cli/azure) и [Azure PowerShell](/powershell/azure/new-azureps-module-az). Для этого необходимо установить средства локально и подключиться к подписке Azure.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) — это интерфейс командной строки для управления ресурсами Azure. Этот интерфейс обеспечивает гибкие функции подачи запросов, выполнение длительных операций без блокировки и простое создание скриптов. Его можно установить локально, выполнив действия, описанные в [Установке Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Для [входа в подписку Azure в Azure CLI](/cli/azure/authenticate-azure-cli) используйте команду [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login).

```azurecli
az login
```

Если вы хотите использовать для входа определенный клиент, выполните команду:
    
```azurecli
az login --tenant <tenant>
```

Если у вас есть несколько подписок и нужно указать конкретную, получите список подписок с помощью команды [az account list](/cli/azure/account#az-account-list) и задайте требуемую, используя команду [az account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Дополнительные сведения можно найти в документации по [началу работы с Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
В [Azure PowerShell az module](/powershell/azure/new-azureps-module-az) доступен набор командлетов, которые используют модель [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure. Его можно использовать в браузере с [Azure Cloud Shell](/azure/cloud-shell/overview), а также установить на локальном компьютере, следуя инструкциям в статье [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps). 

Если вы уже установили его локально, убедитесь, что вы используете последнюю версию версии пакета SDK для Azure PowerShell для настройки шифрования дисков Azure. Скачайте последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Чтобы [войти в учетную запись Azure с помощью Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), используйте командлет [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0).

```powershell
Connect-AzAccount
```

Если у вас есть несколько подписок и вы хотите указать одну из них, используйте командлет [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) для их перечисления, а затем выполните командлет [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0):

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

При выполнении командлета [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) будет проверена правильность выбранной подписки.

Чтобы убедиться, что командлеты шифрования дисков Azure установлены, используйте командлет [Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6):
     
```powershell
Get-command *diskencryption*
```
Дополнительные сведения см. в разделе [Приступая к работе с командлетами Azure PowerShell](/powershell/azure/get-started-azureps). 