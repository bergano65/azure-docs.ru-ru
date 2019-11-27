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
ms.openlocfilehash: 506eb38a2844ed8e8eb9739b116d7647bc1810ec
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480282"
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

**Шаблоны приложений с общедоступными функциями**

| Имя шаблона            | ОПИСАНИЕ |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Создает пустое приложение, в которое необходимо добавить собственные шаблоны устройств и сами устройства. |
| iotc-demo@1.0.0          | Создает приложение, содержащее шаблон устройства, созданный для охлаждаемого торгового автомата. Используйте этот шаблон, чтобы приступить к работе в Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Создает приложение с готовыми шаблонами устройств, чтобы вы могли подключить устройство MXChip или Raspberry Pi. Используйте этот шаблон, если вы являетесь разработчиком устройства, работающим с одним из этих устройств. |


**Шаблоны приложений с функциями общедоступной предварительной версии**

| Имя шаблона            | ОПИСАНИЕ |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Создает пустое предварительное приложение Plug and Play для заполнения с помощью собственных шаблонов устройств и устройств. |
| iotc-condition@1.0.0     | Создает приложение с шаблоном мониторинга "аналитика в магазине". Этот шаблон используется для подключения и мониторинга среды хранения. |
| iotc-consumption@1.0.0   | Создает приложение с шаблоном мониторинга потребления воды. Этот шаблон используется для мониторинга потока воды и управления им. |
| iotc-distribution@1.0.0  | Создает приложение с шаблоном цифрового распространения. Используйте этот шаблон, чтобы повысить эффективность вывода данных в хранилище, дигитализинг ключевые активы и действия. |
| iotc-inventory@1.0.0     | Создает приложение с шаблоном управления интеллектуальным складом. Этот шаблон используется для автоматизации приема, перемещения продуктов, циклического подсчета и отслеживания датчиков. |
| iotc-logistics@1.0.0     | Создает приложение с подключенным шаблоном логистики. Этот шаблон используется для отслеживания отгрузки в реальном времени в сети, вода и земли с мониторингом расположения и условий. |
| iotc-meter@1.0.0         | Создает приложение с шаблоном интеллектуального мониторинга измерения. Этот шаблон используется для наблюдения за энергопотреблением, состоянием сети и выявления тенденций, повышающих эффективность поддержки клиентов и интеллектуального управления измерениями.  |
| iotc-patient@1.0.0       | Создание приложения с непрерывным шаблоном наблюдения за пациентами. Используйте этот шаблон, чтобы расширить здравоохранение, повторное распространение и управление заболеваниях. |
| iotc-power@1.0.0         | Создает приложение с шаблоном мониторинга солнечной панели. Этот шаблон используется для отслеживания состояния солнечной панели, тенденций создания энергии. |
| iotc-quality@1.0.0       | Создает приложение с шаблоном мониторинга качества воды. Используйте этот шаблон для цифрового мониторинга качества воды.|
| iotc-store@1.0.0         | Создает приложение с шаблоном "аналитика в магазине" — "извлечь". Этот шаблон используется для отслеживания потока извлечения в хранилище и управления им. |
| iotc-waste@1.0.0         | Создает приложение с подключенным шаблоном управления отходами. Этот шаблон используется для отслеживания расходных ячеек и операторов полей диспетчеризации. |

> [!NOTE]
> В настоящее время шаблоны приложений предварительной версии доступны только в **странах Европы** и **США** .

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
