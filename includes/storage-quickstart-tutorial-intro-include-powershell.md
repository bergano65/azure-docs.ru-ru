---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 31ef8577a2304091fc4df1b394555c4b30fcf96e
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165570"
---
## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды `Connect-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

Если вы не знаете, какое расположение нужно использовать, можно получить список доступных расположений. Получив список, найдите расположение, которое нужно использовать. В этом примере используется **eastus**. Сохраните его в переменной и используйте ее. Так вы сможете изменить расположение в одном месте.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов Azure с помощью командлета [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. 

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

Создайте стандартную учетную запись хранения общего назначения с репликацией LRS, используя командлет [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), а затем извлеките контекст учетной записи хранения, который определяет используемую учетную запись хранения. Действуя в учетной записи хранения, ссылайтесь на контекст, вместо того чтобы многократно предоставлять учетные данные. В этом примере создается учетная запись хранения *mystorageaccount* с включенными по умолчанию локально избыточным хранилищем (LRS) и шифрованием больших двоичных объектов.

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```
