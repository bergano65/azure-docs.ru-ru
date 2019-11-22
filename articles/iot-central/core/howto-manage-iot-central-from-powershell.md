---
title: Управление IoT Central в Azure PowerShell | Документация Майкрософт
description: В этой статье описывается создание приложений IoT Central и управление ими из Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 7c55d0568832fcefee6e0763810c5e1220480270
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278845"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Управление IoT Central в Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Вместо создания приложений IoT Central и управления ими на веб-сайте [диспетчера приложений Azure IOT Central](https://aka.ms/iotcentral) можно использовать [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) для управления приложениями.

## <a name="prerequisites"></a>предварительным требованиям

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы хотите запустить Azure PowerShell на локальном компьютере, ознакомьтесь с разделом об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). При локальном запуске Azure PowerShell используйте командлет **Connect-AzAccount**, чтобы войти в Azure, прежде чем использовать командлеты, представленные в этой статье.

## <a name="install-the-iot-central-module"></a>Установка модуля IoT Central

Выполните следующую команду, чтобы проверить, установлен ли [модуль IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) в среду PowerShell.

```powershell
Get-InstalledModule -name Az.I*
```

Если в списке установленных модулей нет **Az.IotCentral**, выполните следующую команду.

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Создание приложения

Используйте командлет [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp), чтобы создать приложение IoT Central в подписке Azure. Например,

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Сначала сценарий создает группу ресурсов в регионе Восточная часть США для приложения. В следующей таблице описаны параметры, используемые в команде **New-AzIotCentralApp**:

|Параметр         |ОПИСАНИЕ |
|------------------|------------|
|ResourceGroupName |Группа ресурсов, в которой содержится приложение. В подписке уже должна существовать эта группа ресурсов. |
|Место проведения |По умолчанию этот командлет использует расположение из группы ресурсов. В настоящее время можно создать IoT Centralное приложение в **США**, **Австралии**, **Азиатско-Тихоокеанский регион**или в **странах Европы** .  |
|имя              |Имя приложения на портале Azure. |
|Поддомен         |Поддомен в URL-адресе приложения. В примере URL-адрес приложения — https://mysubdomain.azureiotcentral.com. |
|Sku               |В настоящее время единственное значение — **S1** (уровень "Стандартный"). Ознакомьтесь с разделом [Цены на Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|шаблона          | Шаблон приложения для использования. Дополнительные сведения приведены в таблице ниже. |
|DisplayName       |Имя приложения, отображаемое на пользовательском интерфейсе. |

**Шаблоны приложения**

|Имя шаблона  |ОПИСАНИЕ |
|---------------|------------|
|iotc-default@1.0.0 |Создает пустое приложение, в которое необходимо добавить собственные шаблоны устройств и сами устройства. |
|iotc-demo@1.0.0    |Создает приложение, содержащее шаблон устройства, созданный для охлаждаемого торгового автомата. Используйте этот шаблон, чтобы приступить к работе в Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Создает приложение с готовыми шаблонами устройств, чтобы вы могли подключить устройство MXChip или Raspberry Pi. Используйте этот шаблон, если вы являетесь разработчиком устройства, работающим с одним из этих устройств. |

> [!NOTE]
> В настоящее время шаблон **предварительной версии приложения** доступен только в **Северной Европе** и в **центральных регионах США** .

## <a name="view-your-iot-central-applications"></a>Просмотр приложений IoT Central

Используйте командлет [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp), чтобы получить список приложений IoT Central и просмотреть метаданные.

## <a name="modify-an-application"></a>Изменение приложения

Используйте командлет [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) для обновления метаданных приложения IoT Central. Например, чтобы изменить отображаемое имя приложения:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Удаление приложения

Используйте командлет [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) для удаления приложения IoT Central. Например,

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как управлять приложениями Azure IoT Central в Azure PowerShell, а значит, вы готовы к следующему шагу:

> [!div class="nextstepaction"]
> [Администрирование приложения](howto-administer.md)
