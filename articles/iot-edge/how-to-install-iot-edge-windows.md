---
title: Установка Azure IoT Edge в Windows | Документация Майкрософт
description: Инструкции по установке Azure IoT Edge в Windows 10, Windows Server и Windows IoT Базовая
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: kgremban
ms.openlocfilehash: 401e988e41a25a999c047bf93b6794388d38461e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456804"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Установка среды выполнения Azure IoT Edge в Windows

Среда выполнения Azure IoT Edge превращает устройство в устройство IoT Edge. Среду выполнения можно развернуть на всех устройствах — от небольшого Raspberry Pi до технического сервера. Как только на устройстве будет настроена среда выполнения IoT Edge, вы можете начать развертывать в нее бизнес-логику из облака. 

Дополнительные сведения о среде выполнения IoT Edge см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре](iot-edge-runtime.md).

This article lists the steps to install the Azure IoT Edge runtime on your Windows x64 (AMD/Intel) system using Windows containers.

> [!NOTE]
> A known Windows operating system issue prevents transition to sleep and hibernate power states when IoT Edge modules (process-isolated Windows Nano Server containers) are running. This issue impacts battery life on the device.
>
> As a workaround, use the command `Stop-Service iotedge` to stop any running IoT Edge modules before using these power states. 

Использование контейнеров Linux в системах Windows не является рекомендуемым или поддерживаемым производственной конфигурацией для Azure IoT Edge. Однако его можно использовать для целей разработки и тестирования. To learn more, see [Use IoT Edge on Windows to run Linux containers](how-to-install-iot-edge-windows-with-linux.md).

For information about what's included in the latest version of IoT Edge, see [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Технические условия

В этом разделе можно посмотреть, поддерживает ли устройство Windows IoT Edge, а также подготовить его для подсистемы контейнеров перед установкой. 

### <a name="supported-windows-versions"></a>Поддерживаемые версии Windows

For development and test scenarios, Azure IoT Edge with Windows containers can be installed on any version of Windows 10 or Windows Server 2019 (build 17763) that supports the containers feature. For information about which operating systems are currently supported for production scenarios, see [Azure IoT Edge supported systems](support.md#operating-systems). 

IoT Core devices must include the IoT Core- Windows Containers optional feature to support the IoT Edge runtime. Use the following command in a [remote PowerShell session](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) to check that Windows containers are supported on your device: 

```powershell
Get-Service vmcompute
```

If the service is present, you should get a successful response with the service status listed as **running**. If the vmcompute service is not found, then your device does not meet the requirements for IoT Edge. Contact your hardware provider to ask about support for this feature. 

### <a name="prepare-for-a-container-engine"></a>Подготовка для подсистемы контейнеров 

Служба Azure IoT Edge использует подсистему контейнеров, [совместимую с OCI](https://www.opencontainers.org/). В рабочей среде используйте подсистему Moby, включенную в скрипт установки, для запуска контейнеров Windows на устройстве Windows. 

## <a name="install-iot-edge-on-a-new-device"></a>Установка IoT Edge на новое устройство

>[!NOTE]
>Использование программных пакетов Azure IoT Edge регулируется условиями лицензии, содержащейся в самих пакетах (в каталоге LICENSE). Прежде чем использовать пакет, ознакомьтесь с условиями лицензии. Установка и использование пакета свидетельствуют о принятии этих условий. Если вы не согласны с условиями лицензии, не используйте этот пакет.

Сценарий PowerShell загружает и устанавливает управляющую программу безопасности Azure IoT Edge. Управляющая программа безопасности запускает первый из двух модулей среды выполнения, агент IoT Edge, который обеспечивает удаленные развертывания других модулей. 

>[!TIP]
>For IoT Core devices, we recommend running the installation commands using a RemotePowerShell session. For more information, see [Using PowerShell for Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

При установке среды выполнения IoT Edge в первый раз на устройстве нужно подготовить устройство с помощью удостоверения из центра Интернета вещей. A single IoT Edge device can be provisioned manually using a device connection string provided by IoT Hub. Or, you can use the Device Provisioning Service (DPS) to automatically provision devices, which is helpful when you have many devices to set up. В зависимости от выбранного способа подготовки выберите соответствующий скрипт установки. 

Следующие разделы описывают распространенные варианты использования и параметров для скрипта установки IoT Edge на новое устройство. 

### <a name="option-1-install-and-manually-provision"></a>Вариант 1. Установка и подготовка вручную

In this first option, you provide a **device connection string** generated by IoT Hub to provision the device. 

This example demonstrates a manual installation with Windows containers:

1. If you haven't already, register a new IoT Edge device and retrieve the **device connection string**. Copy the connection string to use later in this section. You can complete this step using the following tools:

   * [Портал Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Код Visual Studio](how-to-register-device.md#register-with-visual-studio-code)

2. Откройте сеанс PowerShell от имени администратора.

   >[!NOTE]
   >Для установки IoT Edge используйте сеанс AMD64 PowerShell, а не PowerShell (x86). Чтобы узнать, какой тип сеанса используется, выполните следующую команду:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. The **Deploy-IoTEdge** command checks that your Windows machine is on a supported version, turns on the containers feature, and then downloads the moby runtime and the IoT Edge runtime. The command defaults to using Windows containers. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. At this point, IoT Core devices may restart automatically. Other Windows 10 or Windows Server devices may prompt you to restart. If so, restart your device now. Once your device is ready, run PowerShell as an administrator again.

5. Команда **Initialize-IoTEdge** настраивает среду выполнения IoT Edge на вашем компьютере. По умолчанию при выполнении команды применяется подготовка вручную с помощью контейнеров Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. When prompted, provide the device connection string that you retrieved in step 1. The device connection string associates the physical device with a device ID in IoT Hub. 

   The device connection string takes the following format, and should not include quotation marks: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Use the steps in [Verify successful installation](#verify-successful-installation) to check the status of IoT Edge on your device. 

Когда вы устанавливаете и подготавливаете устройство вручную, можно использовать дополнительные параметры для изменения установки, том числе сделать следующее.

* Направить трафик через прокси-сервер.
* Указать установщику автономный каталог.
* Объявить определенный образ контейнера агента и указать учетные данные, если он находится в частном реестре.

For more information about these installation options, skip ahead to learn about [all installation parameters](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Вариант 2. Установка и автоматическая подготовка

Во втором варианте подготовка устройства выполняется с помощью службы подготовки устройств к добавлению в Центр Интернета вещей. Provide the **Scope ID** from a Device Provisioning Service instance along with any other information specific to your preferred [attestation mechanism](../iot-dps/concepts-security.md#attestation-mechanism):

* [Create and provision a simulated IoT Edge device with a virtual TPM on Windows](how-to-auto-provision-simulated-device-windows.md)
* [Create and provision an IoT Edge device using symmetric key attestation](how-to-auto-provision-symmetric-keys.md)

When you install and provision a device automatically, you can use additional parameters to modify the installation including:

* Направить трафик через прокси-сервер.
* Указать установщику автономный каталог.
* Объявить определенный образ контейнера агента и указать учетные данные, если он находится в частном реестре.

Дополнительные сведения об этих параметрах установки вы найдете далее в этой статье. Вы можете пропустить этот раздел и ознакомиться со [всеми параметрами установки](#all-installation-parameters).

## <a name="offline-installation"></a>Автономная установка

During installation two files are downloaded:

* Microsoft Azure IoT Edge cab, which contains the IoT Edge security daemon (iotedged), Moby container engine, and Moby CLI.
* MSI-файл распространяемого пакета Visual C++ (среда выполнения VC).

You can download one or both of these files ahead of time to the device, then point the installation script at the directory that contains the files. Установщик сначала проверяет каталог и затем загружает только компоненты, которые не были найдены. If all the files are available offline, you can install with no internet connection. You can also use this feature to install a specific version of the components.  

For the latest IoT Edge installation files along with previous versions, see [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases).

To install with offline components, use the `-OfflineInstallationPath` parameter as part of the Deploy-IoTEdge command and provide the absolute path to the file directory. Например,

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

You can also use the offline installation path parameter with the Update-IoTEdge command, introduced later in this article. 

## <a name="verify-successful-installation"></a>Проверка успешного выполнения установки

Проверьте состояние службы IoT Edge. It should be listed as running.  

```powershell
Get-Service iotedge
```

Просмотрите журналы службы за последние пять минут. If you just finished installing the IoT Edge runtime, you may see a list of errors from the time between running **Deploy-IoTEdge** and **Initialize-IoTEdge**. These errors are expected, as the service is trying to start before being configured. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Run an automated check for the most common configuration and networking errors.

```powershell
iotedge check
```

Просмотрите список запущенных модулей. After a new installation, the only module you should see running is **edgeAgent**. After you [deploy IoT Edge modules](how-to-deploy-modules-portal.md) for the first time, the other system module, **edgeHub**, will start on the device too. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Manage module containers

The IoT Edge service requires a container engine running on your device. When you deploy a module to a device, the IoT Edge runtime uses the container engine to pull the container image from a registry in the cloud. The IoT Edge service enables you to interact with your modules and retrieve logs, but sometimes you may want to use the container engine to interact with the container itself. 

For more information about module concepts, see [Understand Azure IoT Edge modules](iot-edge-modules.md). 

If you're running Windows containers on your Windows IoT Edge device, then the IoT Edge installation included the Moby container engine. The Moby engine was based on the same standards as Docker, and was designed to run in parallel on the same machine as Docker Desktop. For that reason, if you want to target containers managed by the Moby engine, you have to specifically target that engine instead of Docker. 

For example, to list all Docker images, use the following command:

```powershell
docker images
```

To list all Moby images, modify the same command with a pointer to the Moby engine: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

The engine URI is listed in the output of the installation script, or you can find it in the container runtime settings section for the config.yaml file. 

![moby_runtime uri in config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

For more information about commands you can use to interact with containers and images running on your device, see [Docker command-line interfaces](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Обновление существующей установки

If you've already installed the IoT Edge runtime on a device before and provisioned it with an identity from IoT Hub, then you can update the runtime without having to reenter your device information. 

Дополнительные сведения см. в разделе [Обновление управляющей программы безопасности и среды выполнения IoT Edge](how-to-update-iot-edge.md).

В этом примере показана установка, которая указывает на существующий файл конфигурации и использует контейнеры Windows. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

When you update IoT Edge, you can use additional parameters to modify the update, including:

* Direct traffic to go through a proxy server, or
* Указать установщику автономный каталог. 
* Restarting without a prompt if necessary

You can't declare an IoT Edge agent container image with script parameters because that information is already set in the configuration file from the previous installation. Если вы хотите изменить образ контейнера агента, сделайте это в файле config.yaml. 

For more information about these update options, use the command `Get-Help Update-IoTEdge -full` or refer to [all installation parameters](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Удаление IoT Edge

Если вы хотите удалить установку IoT Edge с устройства Windows, используйте следующую команду в окне администратора PowerShell. Эта команда удаляет среду выполнения IoT Edge вместе с существующей конфигурацией и данными подсистемы Moby. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

The Uninstall-IoTEdge command does not work on Windows IoT Core. To remove IoT Edge from Windows IoT Core devices, you need to redeploy your Windows IoT Core image. 

For more information about uninstallation options, use the command `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Все параметры установки

В предыдущих разделах представлены общие сценарии установки с примерами того, как использовать параметры для изменения скрипта установки. This section provides reference tables of the common parameters used to install, update, or uninstall IoT Edge. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

The Deploy-IoTEdge command downloads and deploys the IoT Edge Security Daemon and its dependencies. The deployment command accepts these common parameters, among others. For the full list, use the command `Get-Help Deploy-IoTEdge -full`.  

| Параметр | Допустимые значения | Комментарии |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** или **Linux**. | If no container operating system is specified, Windows is the default value.<br><br>For Windows containers, IoT Edge uses the moby container engine included in the installation. Для контейнеров Linux необходимо установить подсистему контейнеров перед началом установки. |
| **Proxy** | URL-адрес прокси-сервера. | Включите этот параметр, если ваше устройство подключается к Интернету через прокси-сервер. Дополнительные сведения см. в статье [Настройка устройства IoT Edge для обмена данными через прокси-сервер](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Путь к каталогу. | If this parameter is included, the installer will check the listed directory for the IoT Edge cab and VC Runtime MSI files required for installation. Any files not found in the directory are downloaded. If both files are in the directory, you can install IoT Edge without an internet connection. You can also use this parameter to use a specific version. |
| **InvokeWebRequestParameters** | Хэш-таблица параметров и значений. | Во время установки выполняется несколько веб-запросов. Используйте это поле, чтобы задать параметры для этих веб-запросов. Этот параметр нужен, чтобы настроить учетные данные для прокси-серверов. Дополнительные сведения см. в статье [Настройка устройства IoT Edge для обмена данными через прокси-сервер](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | Нет | This flag allows the deployment script to restart the machine without prompting, if necessary. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

The Initialize-IoTEdge command configures IoT Edge with your device connection string and operational details. Much of the information generated by this command is then stored in the iotedge\config.yaml file. The initialization command accepts these common parameters, among others. For the full list, use the command `Get-Help Initialize-IoTEdge -full`. 

| Параметр | Допустимые значения | Комментарии |
| --------- | --------------- | -------- |
| **Вручную** | Нет | **Параметр-переключатель**. If no provisioning type is specified, manual is the default value.<br><br>Объявляет, что вы предоставите строку подключения устройства для подготовки устройства вручную. |
| **Dps** | Нет | **Параметр-переключатель**. If no provisioning type is specified, manual is the default value.<br><br>Объявляет, что вы предоставите идентификатор области службы подготовки устройств (DPS) и идентификатор регистрации устройства для подготовки через DPS.  |
| **DeviceConnectionString** | Строка подключения из устройства IoT Edge, зарегистрированного в Центре Интернета вещей, в одинарных кавычках. | **Необходима** для установки вручную. Если не указать строку подключения в параметрах скрипта, она будет запрошена во время установки. |
| **ScopeId** | Идентификатор области из экземпляра службы подготовки устройств, связанного с Центром Интернета вещей. | **Необходим** для установки через службу подготовки устройств. Если не указать идентификатор области в параметрах скрипта, он будет запрошен во время установки. |
| **RegistrationId** | Идентификатор регистрации, созданный вашим устройством. | **Required** for DPS installation if using TPM or symmetric key attestation. |
| **SymmetricKey** | The symmetric key used to provision the IoT Edge device identity when using DPS | **Required** for DPS installation if using symmetric key attestation. |
| **ContainerOs** | **Windows** или **Linux**. | If no container operating system is specified, Windows is the default value.<br><br>For Windows containers, IoT Edge uses the moby container engine included in the installation. Для контейнеров Linux необходимо установить подсистему контейнеров перед началом установки. |
| **InvokeWebRequestParameters** | Хэш-таблица параметров и значений. | Во время установки выполняется несколько веб-запросов. Используйте это поле, чтобы задать параметры для этих веб-запросов. Этот параметр нужен, чтобы настроить учетные данные для прокси-серверов. Дополнительные сведения см. в статье [Настройка устройства IoT Edge для обмена данными через прокси-сервер](how-to-configure-proxy-support.md). |
| **AgentImage** | URI образа агента IoT Edge. | По умолчанию новая установка IoT Edge использует последний тег для образа агента IoT Edge. Используйте этот параметр, чтобы задать определенный тег для версии образа, или предоставьте свой образ агента. Дополнительные сведения см. в статье [Основные сведения о тегах IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Имя пользователя** | Имя пользователя реестра контейнеров | Используйте этот параметр только в том случае, если вы задаете значение параметра -AgentImage для контейнера в частном реестре. Укажите имя пользователя с доступом к реестру. |
| **Пароль** | Строка надежного пароля. | Используйте этот параметр только в том случае, если вы задаете значение параметра -AgentImage для контейнера в частном реестре. Укажите пароль для доступа к реестру. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Параметр | Допустимые значения | Комментарии |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** или **Linux**. | If no container OS is specified, Windows is the default value. Для контейнеров Windows подсистема контейнеров будет включена в установку. Для контейнеров Linux необходимо установить подсистему контейнеров перед началом установки. |
| **Proxy** | URL-адрес прокси-сервера. | Включите этот параметр, если ваше устройство подключается к Интернету через прокси-сервер. Дополнительные сведения см. в статье [Настройка устройства IoT Edge для обмена данными через прокси-сервер](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Хэш-таблица параметров и значений. | Во время установки выполняется несколько веб-запросов. Используйте это поле, чтобы задать параметры для этих веб-запросов. Этот параметр нужен, чтобы настроить учетные данные для прокси-серверов. Дополнительные сведения см. в статье [Настройка устройства IoT Edge для обмена данными через прокси-сервер](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Путь к каталогу. | If this parameter is included, the installer will check the listed directory for the IoT Edge cab and VC Runtime MSI files required for installation. Any files not found in the directory are downloaded. If both files are in the directory, you can install IoT Edge without an internet connection. You can also use this parameter to use a specific version. |
| **RestartIfNeeded** | Нет | This flag allows the deployment script to restart the machine without prompting, if necessary. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Параметр | Допустимые значения | Комментарии |
| --------- | --------------- | -------- |
| **Force** | Нет | This flag forces the uninstallation in case the previous attempt to uninstall was unsuccessful. 
| **RestartIfNeeded** | Нет | This flag allows the uninstall script to restart the machine without prompting, if necessary. |

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда подготовлено устройство IoT Edge и установлена среда выполнения, вы можете [развернуть модули IoT Edge](how-to-deploy-modules-portal.md).

Если вам не удается установить IoT Edge, перейдите на страницу со сведениями об [устранении неполадок](troubleshoot.md).

Чтобы обновить существующую установку до последней версии IoT Edge, см. раздел [Обновление управляющей программы безопасности и среды выполнения IoT Edge](how-to-update-iot-edge.md).
