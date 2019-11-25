---
title: Обновления версии Azure IoT Edge на устройствах | Документация Майкрософт
description: Сведения об обновлении устройства IoT Edge для запуска последних версий управляющей программы безопасности и среды выполнения IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 58171d2fc8e7e1563bd83689d1cd91c55fea239a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456542"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Обновление управляющей программы безопасности и среды выполнения IoT Edge

As the IoT Edge service releases new versions, you'll want to update your IoT Edge devices for the latest features and security improvements. В этой статье представлены сведения об обновлении устройств IoT Edge при наличии новой версии. 

Если вы хотите перейти на новую версию, вам необходимо обновить два компонента устройства IoT Edge. Первый компонент — управляющая программа безопасности, которая выполняется на устройстве и запускает модули среды выполнения при запуске устройства. В настоящее время обновить управляющую программу безопасности можно только на самом устройстве. Второй компонент — это среда выполнения, состоящая из модулей центра и агента IoT Edge. В зависимости от структуры развертывания среду выполнения можно обновить как на устройстве, так и удаленно. 

Последнюю версию Azure IoT Edge можно найти [на сайте GitHub](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Обновление управляющей программы безопасности

Управляющая программа безопасности IoT Edge — это собственный компонент, который необходимо обновить с помощью диспетчера пакетов на устройстве IoT Edge. 

Проверьте версию управляющей программы безопасности, запущенной на вашем устройстве, с помощью команды `iotedge version`. 

### <a name="linux-devices"></a>Устройства Linux

On Linux x64 devices, use apt-get or your appropriate package manager to update the security daemon. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

On Linux ARM32 devices, use the steps in [Install Azure IoT Edge runtime on Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) to install the latest version of the security daemon. 

### <a name="windows-devices"></a>Устройства Windows

On Windows devices, use the PowerShell script to update the security daemon. The script automatically pulls the latest version of the security daemon. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Running the Update-IoTEdge command removes the security daemon from your device, along with the two runtime container images. The config.yaml file is kept on the device, as well as data from the Moby container engine (if you're using Windows containers). Keeping the configuration information means that you don't have to provide the connection string or Device Provisioning Service information for your device again during the update process. 

If you want to install a specific version of the security daemon, download the appropriate Microsoft-Azure-IoTEdge.cab file from [IoT Edge releases](https://github.com/Azure/azure-iotedge/releases). Затем используйте параметр `-OfflineInstallationPath`, чтобы указать местоположение файла. Дополнительные сведения см. в статье [Автономная установка](how-to-install-iot-edge-windows.md#offline-installation).

## <a name="update-the-runtime-containers"></a>Обновление контейнеров среды выполнения

The way that you update the IoT Edge agent and IoT Edge hub containers depends on whether you use rolling tags (like 1.0) or specific tags (like 1.0.7) in your deployment. 

Проверьте версию модулей агента и центра IoT Edge, используемую в настоящее время на вашем устройстве, с помощью команды `iotedge logs edgeAgent` или `iotedge logs edgeHub`. 

  ![Поиск версии контейнера в журналах](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Сведения о тегах IoT Edge

Образы агента и центра IoT Edge отмечены тегами версии IoT Edge, с которой они связаны. Существует два способа использования тегов с образами среды выполнения: 

* **Последовательные теги**: используются только первые два значения номера версии для получения последнего образа, соответствующего этим цифрам. Например, 1.0 обновляется всякий раз, когда появляется новый выпуск, указывающий на последнюю версию 1.0.x. Если среда выполнения контейнера на устройстве IoT Edge снова извлекает образ, модули среды выполнения обновляются до последней версии. Такой подход рекомендуется использовать для разработки. Для развертываний с портала Azure по умолчанию установлены последовательные теги. 
* **Конкретные теги**: используются все три значения номера версии для явной установки версии образа. For example, 1.0.7 won't change after its initial release. Вы можете объявить новый номер версии в манифесте развертывания, когда будете готовы к обновлению. Такой подход рекомендуется использовать для производственных целей.

### <a name="update-a-rolling-tag-image"></a>Обновление образа последовательного тега

Если вы используете последовательные теги в своем развертывании (например, mcr.microsoft.com/azureiotedge-hub:**1.0**), вам необходимо принудительно запустить среду выполнения контейнера на устройстве, чтобы извлечь последнюю версию образа. 

Удалите локальную версию образа с устройства IoT Edge. На компьютерах Windows удаление управляющей программы безопасности приводит к удалению образов среды выполнения, поэтому не нужно снова выполнять этот шаг. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Для удаления образов может потребоваться использовать флаг force `-f`. 

Служба IoT Edge извлечет последние версии образов среды выполнения и автоматически запустит их на вашем устройстве. 

### <a name="update-a-specific-tag-image"></a>Обновление образа конкретного тега

If you use specific tags in your deployment (for example, mcr.microsoft.com/azureiotedge-hub:**1.0.7**) then all you need to do is update the tag in your deployment manifest and apply the changes to your device. 

На портале Azure образы развертывания среды выполнения объявляются в разделе **Настройка дополнительных параметров среды выполнения Edge**. 

![Configure advanced edge runtime settings](./media/how-to-update-iot-edge/configure-runtime.png)

В манифесте развертывания JSON обновите образы модулей в разделе **systemModules**. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.7",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.7",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="update-to-a-release-candidate-version"></a>Update to a release candidate version

Azure IoT Edge regularly releases new versions of the IoT Edge service. Before each stable release, there is one or more release candidate (RC) versions. RC versions include all the planned features for the release, but are still going through the testing and validation processes required for a stable release. If you want to test a new feature early, you can install the RC version and provide feedback through GitHub. 

Release candidate versions follow the same numbering convention of releases, but have **-rc** plus an incremental number appended to the end. You can see the release candidates in the same list of [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) as the stable versions. For example, find **1.0.7-rc1** and **1.0.7-rc2**, the two release candidates that came before **1.0.7**. You can also see that RC versions are marked with **pre-release** labels. 

As previews, release candidate versions aren't included as the latest version that the regular installers target. Instead, you need to manually target the assets for the RC version that you want to test. Depending on your IoT Edge device operating system, use the following sections to update IoT Edge to a specific version:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-installation)

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с последними выпусками Azure IoT Edge [на сайте GitHub](https://github.com/Azure/azure-iotedge/releases).

Будьте в курсе последних обновлений и объявлений, просматривая блог, посвященный [Интернету вещей](https://azure.microsoft.com/blog/topics/internet-of-things/). 