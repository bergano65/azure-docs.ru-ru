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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72511515"
---
Шифрование azure Disk может быть включено и управляется через [Azure CLI](/cli/azure) и [Azure PowerShell.](/powershell/azure/new-azureps-module-az) Для этого необходимо установить инструменты локально и подключиться к подписке Azure.

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) — это интерфейс командной строки для управления ресурсами Azure. Этот интерфейс обеспечивает гибкие функции подачи запросов, выполнение длительных операций без блокировки и простое создание скриптов. Вы можете установить его локально, выяснив следующие шаги в [установке Azure CLI.](/cli/azure/install-azure-cli?view=azure-cli-latest)

Чтобы [войти в учетную запись Azure с помощью Azure CLI,](/cli/azure/authenticate-azure-cli)используйте команду [входа в az.](/cli/azure/reference-index?view=azure-cli-latest#az-login)

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
[Модуль Azure PowerShell az](/powershell/azure/new-azureps-module-az) предоставляет набор cmdlets, который использует модель управления [ресурсами Azure](/azure/azure-resource-manager/resource-group-overview) для управления ресурсами Azure. Вы можете использовать его в браузере с [Azure Cloud Shell,](/azure/cloud-shell/overview)или вы можете установить его на локальной машине, используя инструкции в [модуле Установить Azure PowerShell.](/powershell/azure/install-az-ps) 

Если вы уже установили его локально, убедитесь, что вы используете последнюю версию версии пакета SDK для Azure PowerShell для настройки шифрования дисков Azure. Скачайте последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Чтобы [войти в учетную запись Azure в Azure PowerShell,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)используйте cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```powershell
Connect-AzAccount
```

Если у вас несколько подписок и вы хотите указать одну из них, используйте cmdlet [Get-AzSubscription,](/powershell/module/Az.Accounts/Get-AzSubscription) чтобы перечислить их, а затем Смдлет [Set-AzContext:](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Запуск cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) позволит проверить, выбрана ли была выбрана правильная подписка.

Для подтверждения установки cmdlets шифрования дисков Azure, используйте [Cmdlet Get-command:](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6)
     
```powershell
Get-command *diskencryption*
```
Для получения дополнительной [Getting started with Azure PowerShell](/powershell/azure/get-started-azureps)информации см. 