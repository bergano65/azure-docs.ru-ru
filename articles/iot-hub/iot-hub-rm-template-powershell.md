---
title: Создание центра Интернета вещей Azure с помощью шаблона (PowerShell) | Документация Майкрософт
description: Как использовать шаблон менеджера ресурсов Azure для создания концентратора IoT с Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976621"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Создание Центра Интернета вещей с помощью шаблона Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Узнайте, как использовать шаблон Менеджера ресурсов Azure для создания концентратора IoT и группы потребителей. Шаблоны Resource Manager — это JSON-файлы, которые определяют ресурсы, необходимые для развертывания решения. Для получения дополнительной информации о разработке шаблонов управления ресурсами можно ознакомиться с [документацией менеджера ресурсов Azure.](https://docs.microsoft.com/azure/azure-resource-manager/)

Если у вас нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/) перед началом.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Шаблон менеджера ресурсов, используемый в этом быстром запуске, — это [шаблоны Azure quickstart.](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/) Ниже приведена копия шаблона:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Шаблон создает концентратор Azure Iot с тремя конечными точками (eventhub, облако множеся и обмен сообщениями) и группой потребителей. Для получения дополнительных примеров [шаблонов](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)см. Схема шаблона Iot Hub можно найти [здесь](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Существует несколько способов развертывания шаблона.  В этом учебнике используется Azure PowerShell.

Чтобы запустить сценарий PowerShell, выберите **его,** чтобы открыть оболочку облака Azure. Чтобы вставить сценарий, нажмите правой кнопкой оболочки, а затем выберите Паста:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Как вы можете видеть из сценария PowerShell, используемый шаблон используется из шаблонов Azure quickstart. Чтобы использовать свой собственный файл, необходимо сначала загрузить файл шаблона `-TemplateFile` в оболочку облака, а затем использовать переключатель, чтобы указать имя файла.  Например, [см. Развертывание шаблона](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы развернули концентратор IoT, используя шаблон Azure Resource Manager, вы можете изучить далее:

* Ознакомьтесь с возможностями [REST API поставщика ресурсов Центра Интернета вещей][lnk-rest-api].
* Сведения о возможностях Azure Resource Manager см. в статье [Общие сведения об Azure Resource Manager][lnk-azure-rm-overview].
* Синтаксис JSON и используемые в шаблоне свойства см. в статье о [типах ресурсов Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Дополнительные сведения о разработке для Центра Интернета вещей см. в следующих статьях:

* [Пакет SDK для устройств Azure IoT для C][lnk-c-sdk]
* [IoT Hub SDKs][lnk-sdks] (Пакеты SDK для Центра Интернета вещей)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
