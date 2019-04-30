---
title: Создание центра Интернета вещей Azure с помощью шаблона (PowerShell) | Документация Майкрософт
description: Как использовать шаблон Azure Resource Manager для создания центра Интернета вещей с помощью Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: eefa17b699a0599d2d3323409cafddd1d0984c61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440324"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Создание Центра Интернета вещей с помощью шаблона Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Узнайте, как использовать шаблон Azure Resource Manager для создания центра Интернета вещей и группой потребителей. Шаблоны Resource Manager — это JSON-файлы, которые определяют ресурсы, необходимые для развертывания решения. Дополнительные сведения о разработке шаблонов Resource Manager см. в разделе [документация по Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="create-an-iot-hub"></a>Создание Центра Интернета вещей

Шаблон Resource Manager, в этом кратком руководстве, доступен из [шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Ниже приведена копия шаблона:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Этот шаблон создает центр Интернета вещей Azure с тремя конечными точками (концентратора событий, cloud-to-device и обмена сообщениями) и группой потребителей. Дополнительные примеры шаблонов, см. в разделе [шаблоны быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Схема шаблона центра Интернета вещей можно найти [здесь](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Существует несколько способов для развертывания шаблона.  В этом руководстве используется Azure PowerShell.

Чтобы выполнить сценарий PowerShell, выберите **попробовать** чтобы открыть Azure Cloud shell. Вставьте сценарий, щелкните правой кнопкой мыши среду и затем выберите команду Вставить:

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

Как видно из сценария PowerShell, шаблон, используемый является из шаблонов быстрого запуска Azure. Чтобы использовать собственный, необходимо сначала передать файл шаблона в Cloud shell, а затем используйте `-TemplateFile` параметр, чтобы указать имя файла.  Например, см. в разделе [развертывания шаблона](../azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Дальнейшие действия

После развертывания центра Интернета вещей с помощью шаблона Azure Resource Manager, может потребоваться дальнейшего изучения:

* Ознакомьтесь с возможностями [REST API поставщика ресурсов Центра Интернета вещей][lnk-rest-api].
* Сведения о возможностях Azure Resource Manager см. в статье [Общие сведения об Azure Resource Manager][lnk-azure-rm-overview].
* Синтаксис JSON и используемые в шаблоне свойства см. в статье о [типах ресурсов Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Дополнительные сведения о разработке для Центра Интернета вещей см. в следующих статьях:

* [Знакомство с пакетом SDK для устройств Центра Интернета вещей Azure для C][lnk-c-sdk]
* [IoT Hub SDKs][lnk-sdks] (Пакеты SDK для Центра Интернета вещей)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Развертывание ИИ на пограничных устройствах с использованием Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
