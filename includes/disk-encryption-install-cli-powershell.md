---
title: включение файла
description: включение файла
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72511515"
---
Шифрование дисков Azure можно включить и управлять с помощью [Azure CLI](/cli/azure) и [Azure PowerShell](/powershell/azure/new-azureps-module-az). Для этого необходимо установить средства локально и подключиться к подписке Azure.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) — это интерфейс командной строки для управления ресурсами Azure. Этот интерфейс обеспечивает гибкие функции подачи запросов, выполнение длительных операций без блокировки и простое создание скриптов. Его можно установить локально, выполнив действия, описанные в разделе [установка Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Чтобы [войти в учетную запись Azure с помощью Azure CLI](/cli/azure/authenticate-azure-cli), используйте команду [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) .

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
[Модуль Azure PowerShell AZ](/powershell/azure/new-azureps-module-az) предоставляет набор командлетов, использующих модель [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure. Его можно использовать в браузере с [Azure Cloud Shell](/azure/cloud-shell/overview)или установить на локальном компьютере, выполнив инструкции из статьи [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps). 

Если вы уже установили его локально, убедитесь, что вы используете последнюю версию версии пакета SDK для Azure PowerShell для настройки шифрования дисков Azure. Скачайте последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Чтобы [войти в учетную запись Azure с помощью Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0), используйте командлет [Connect-азаккаунт](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```powershell
Connect-AzAccount
```

Если у вас несколько подписок и вы хотите указать одну из них, используйте командлет [Get-азсубскриптион](/powershell/module/Az.Accounts/Get-AzSubscription) , чтобы вывести их список, а затем командлет [Set-азконтекст](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

При выполнении командлета [Get-азконтекст](/powershell/module/Az.Accounts/Get-AzContext) будет проверена правильность выбранной подписки.

Чтобы убедиться, что командлеты шифрования дисков Azure установлены, используйте командлет [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) :
     
```powershell
Get-command *diskencryption*
```
Дополнительные сведения см. в статье [Приступая к работе с Azure PowerShell](/powershell/azure/get-started-azureps). 