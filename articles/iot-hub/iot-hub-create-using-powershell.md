---
title: Создание Центра Интернета вещей Azure с помощью командлета PowerShell | Документация Майкрософт
description: Узнайте, как использовать cmdlets PowerShell для создания группы ресурсов, а затем создать концентратор IoT в группе ресурсов. Также узнайте, как удалить концентратор.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890605"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>Создание концентратора IoT с помощью смдлета New-AzIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Вступление

Для создания Центров Интернета вещей и управления ими можно использовать командлеты Azure PowerShell. В этом руководстве показано, как создать Центр Интернета вещей с помощью PowerShell.

Для работы с этим руководством вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Подключение к подписке Azure

Если вы используете Cloud Shell, вы уже вошли в свою подписку. Если вместо этого вы используете PowerShell в локальной среде, введите следующую команду, чтобы войти в подписку Azure.

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Для развертывания Центра Интернета вещей необходима группа ресурсов. Вы можете выбрать существующую группу ресурсов или создать новую.

Чтобы создать группу ресурсов для концентратора IoT, используйте команду [New-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) В этом примере создается группа ресурсов с именем **MyIoTRG1**, размещенная в регионе **Восточная часть США**:

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Для создания концентратора IoT в группе ресурсов, созданной на предыдущем этапе, используйте команду [New-AzIotHub.](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) В этом примере создается центр категории **S1** с именем **MyTestIoTHub**, размещенный в регионе **Восточная часть США**:

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Имя Центра Интернета вещей должно быть глобально уникальным.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Вы можете перечислить все концентраторы IoT в подписке с помощью команды [Get-AzIotHub:](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub)

```azurepowershell-interactive
Get-AzIotHub
```

Это пример стандартного Центра Интернета вещей S1, который вы создали на предыдущем шаге.

Вы можете удалить концентратор IoT с помощью команды [Remove-AzIotHub:](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub)

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Кроме того, можно удалить группу ресурсов и все ресурсы, которые она содержит, с помощью команды [Удалить-AzResourceGroup:](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Дальнейшие действия

После развертывания Центра Интернета вещей с помощью командлета PowerShell вам могут понадобиться дополнительные сведения, с которыми можно ознакомиться в следующих статьях:

* [AzureRM.IotHub](https://docs.microsoft.com/powershell/module/az.iothub/).

* [Поставщик ресурсов IoT Hub REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Дополнительные сведения о разработке для Центра Интернета вещей см. в следующих статьях:

* [Пакет SDK для устройств Azure IoT для C](iot-hub-device-sdk-c-intro.md)

* [IoT Hub SDKs](iot-hub-devguide-sdks.md) (Пакеты SDK для Центра Интернета вещей)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)
