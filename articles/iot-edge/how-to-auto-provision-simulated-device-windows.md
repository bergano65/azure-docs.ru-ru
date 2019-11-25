---
title: Automatically provision Windows devices with DPS - Azure IoT Edge | Microsoft Docs
description: Использование имитированного устройства на компьютере Windows для тестирования автоматической подготовки устройств для Azure IoT Edge с помощью Службы подготовки устройств к добавлению в Центр Интернета вещей
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ad92d4cf0d5b61c778b87114d4be6c23557f8e26
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457136"
---
# <a name="create-and-provision-a-simulated-iot-edge-device-with-a-virtual-tpm-on-windows"></a>Create and provision a simulated IoT Edge device with a virtual TPM on Windows

Устройства Azure IoT Edge можно подготовить автоматически с помощью [Службы подготовки устройств к добавлению в Центр Интернета вещей](../iot-dps/index.yml) точно так же, как и другие устройства. При необходимости ознакомьтесь с [процессом автоматической подготовки](../iot-dps/concepts-auto-provisioning.md), прежде чем продолжить.

DPS supports symmetric key attestation for IoT Edge devices in both individual enrollment and group enrollment. For group enrollment, if you check “is IoT Edge device” option to be true in symmetric key attestation, all the devices that are registered under that enrollment group will be marked as IoT Edge devices. 

This article shows you how to test auto-provisioning on a simulated IoT Edge device with the following steps:

* Создание экземпляра Службы подготовки устройств к добавлению в Центр Интернета вещей.
* Создание имитированного устройства на компьютере Windows с имитированным доверенным платформенным модулем (TPM) для обеспечения безопасности оборудования.
* Создание отдельной регистрации устройства.
* Установка среды выполнения IoT Edge и подключение устройства к Центру Интернета вещей.

> [!NOTE]
> TPM 2.0 is required when using TPM attestation with DPS and can only be used to create individual, not group, enrollments.

> [!TIP]
> This article describes testing auto-provisioning by using TPM attestation on virtual devices, but much of it applies when using physical TPM hardware as well.

## <a name="prerequisites"></a>Технические условия

* Компьютер для разработки Windows. В этой статье используется Windows 10.
* Действующий Центр Интернета вещей.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Настройка Службы подготовки устройств к добавлению в Центр Интернета вещей

Создайте экземпляр Службы подготовки устройств к добавлению в Центр Интернета вещей в Azure и свяжите его со своим Центром Интернета вещей. Следуйте инструкциям по [настройке Службы подготовки устройств к добавлению в Центр Интернета вещей на портале Azure](../iot-dps/quick-setup-auto-provision.md).

После запуска Службы подготовки устройств к добавлению в Центр Интернета вещей скопируйте значение **Область идентификатора** на странице обзора. Это значение используется при настройке среды выполнения IoT Edge.

> [!TIP]
> If you're using a physical TPM device, you need to determine the **Endorsement key**, which is unique to each TPM chip and is obtained from the TPM chip manufacturer associated with it. You can derive a unique **Registration ID** for your TPM device by, for example, creating an SHA-256 hash of the endorsement key.
>
> Follow the instructions in the article [How to manage device enrollments with Azure Portal](../iot-dps/how-to-manage-enrollments.md) to create your enrollment in DPS and then proceed with the [Install the IoT Edge runtime](#install-the-iot-edge-runtime) section in this article to continue.

## <a name="simulate-a-tpm-device"></a>Имитация устройства TPM

Создайте имитированное устройства доверенного платформенного модуля на компьютере Windows для разработки. Retrieve the **Registration ID** and **Endorsement key** for your device, and use them to create an individual enrollment entry in DPS.

При регистрации в Службе подготовки устройств к добавлению в Центр Интернета вещей есть возможность объявить **Первоначальное состояние двойника устройства**. В двойнике устройства можно задать теги для группировки устройств по любой требуемой для решения метрике, например по региону, среде, расположению или типу устройства. Эти теги используются для создания [автоматических развертываний](how-to-deploy-monitor.md).

Выберите язык пакета SDK для создания имитации устройства, а затем создайте отдельную регистрацию.

When you create the individual enrollment, select **True** to declare that the simulated TPM device on your Windows development machine is an **IoT Edge device**.

Руководства по использованию виртуальных устройств и отдельной регистрации:

* [C](../iot-dps/quick-create-simulated-device.md)
* [Java](../iot-dps/quick-create-simulated-device-tpm-java.md)
* [C#](../iot-dps/quick-create-simulated-device-tpm-csharp.md)
* [Node.js](../iot-dps/quick-create-simulated-device-tpm-node.md)
* [Python](../iot-dps/quick-create-simulated-device-tpm-python.md)

Создав отдельную регистрацию, сохраните значение **идентификатора регистрации**. Это значение используется при настройке среды выполнения IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Установка среды выполнения IoT Edge

Среда выполнения IoT Edge развертывается на всех устройствах IoT Edge. Ее компоненты выполняются в контейнерах и позволяют развертывать дополнительные контейнеры на устройстве, чтобы обеспечить возможность выполнения кода в граничной системе.

You'll need the following information when provisioning your device:

* The DPS **ID Scope** value
* The device **Registration ID** you created

Install the IoT Edge runtime on the device that is running the simulated TPM. You'll configure the IoT Edge runtime for automatic, not manual, provisioning.

> [!TIP]
> Не закрывайте окно с запущенным симулятором TPM во время установки и тестирования.

For more detailed information about installing IoT Edge on Windows, including prerequisites and instructions for tasks like managing containers and updating IoT Edge, see [Install the Azure IoT Edge runtime on Windows](how-to-install-iot-edge-windows.md).

1. Откройте окно PowerShell с правами администратора. Be sure to use an AMD64 session of PowerShell when installing IoT Edge, not PowerShell (x86).

1. The **Deploy-IoTEdge** command checks that your Windows machine is on a supported version, turns on the containers feature, and then downloads the moby runtime and the IoT Edge runtime. The command defaults to using Windows containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. At this point, IoT Core devices may restart automatically. Other Windows 10 or Windows Server devices may prompt you to restart. If so, restart your device now. Once your device is ready, run PowerShell as an administrator again.

1. Команда **Initialize-IoTEdge** настраивает среду выполнения IoT Edge на вашем компьютере. По умолчанию при выполнении команды применяется подготовка вручную с помощью контейнеров Windows. Use the `-Dps` flag to use the Device Provisioning Service instead of manual provisioning.

   Replace the placeholder values for `{scope_id}` and `{registration_id}` with the data you collected earlier.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

## <a name="verify-successful-installation"></a>Проверка успешного выполнения установки

Если среда выполнения запущена успешно, можете перейти в Центр Интернета вещей и начать развертывание модулей IoT Edge на устройстве. Чтобы убедиться, что среда выполнения установлена и запущена успешно, используйте следующие команды на устройстве:  

Проверьте состояние службы IoT Edge.

```powershell
Get-Service iotedge
```

Просмотрите журналы службы за последние пять минут.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Просмотрите список запущенных модулей.

```powershell
iotedge list
```

## <a name="next-steps"></a>Дальнейшие действия

Процесс регистрации Службы подготовки устройств к добавлению в Центр Интернета вещей позволяет задать идентификатор устройства и теги двойников устройств параллельно с подготовкой нового устройства. Эти значения можно использовать для указания отдельных устройств или групп устройств с помощью автоматического управления устройствами. См. дополнительные сведения о развертывании и мониторинге модулей IoT Edge с поддержкой масштабирования с помощью [портала Azure](how-to-deploy-monitor.md) или [Azure CLI](how-to-deploy-monitor-cli.md).
