---
title: Установка Azure IoT Edge для Linux на Windows (ru) Документы Майкрософт
description: Инструкции по установке Azure IoT Edge для контейнеров Linux на Windows 10, Windows Server и Ядро IoT Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.openlocfilehash: 8a4579e092bbc4fd58954f1ce1f1dad3a8ddbbba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133163"
---
# <a name="use-iot-edge-on-windows-to-run-linux-containers"></a>Используйте IoT Edge на Windows для запуска linux-контейнеров

ТестIoмые модули IoT Edge для устройств Linux с помощью машины Windows.

В производственном сценарии устройства Windows должны запускать только контейнеры Windows. Тем не менее, общий сценарий разработки заключается в использовании компьютера Windows для создания модулей IoT Edge для устройств Linux. Время выполнения IoT Edge для Windows позволяет запускать контейнеры Linux для **тестирования и разработки.**

В этой статье перечислены шаги по установке времени выполнения Azure IoT Edge с использованием контейнеров Linux в системе Windows x64 (AMD/Intel). Чтобы узнать больше об установке времени выполнения IoT Edge, включая [Install the Azure IoT Edge runtime on Windows](how-to-install-iot-edge-windows.md)подробную информацию обо всех параметрах установки, см.

Для получения информации о том, что включено [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases)в последнюю версию IoT Edge, см.

## <a name="prerequisites"></a>Предварительные требования

В этом разделе можно посмотреть, поддерживает ли устройство Windows IoT Edge, а также подготовить его для подсистемы контейнеров перед установкой.

### <a name="supported-windows-versions"></a>Поддерживаемые версии Windows

Azure IoT Edge с контейнерами Linux может работать на любой версии Windows, которая отвечает [требованиям Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install)

Если вы хотите установить IoT Edge на виртуальную машину, включите вложенную виртуализацию и выделите не менее 2 ГБ памяти. Способ виртуализации вложенных способов отличается в зависимости от гипервизора использования. Для Hyper-V виртуальные машины поколения 2 вложены в виртуализацию, включенную по умолчанию. Для VMWare есть переключатель, чтобы включить функцию на вашей виртуальной машине.

### <a name="prepare-the-container-engine"></a>Подготовка контейнерного двигателя

Служба Azure IoT Edge использует подсистему контейнеров, [совместимую с OCI](https://www.opencontainers.org/). Самая большая разница в конфигурации между запуском Windows и Linux контейнеров на машине Windows является то, что установка IoT Edge включает в себя время выполнения контейнера Windows, но вам нужно предоставить свое собственное время выполнения для контейнеров Linux перед установкой IoT Edge.

Для настройки машины Windows для разработки и тестирования контейнеров для устройств Linux можно использовать [Docker Desktop](https://www.docker.com/docker-windows) в качестве контейнерного движка. Вам нужно установить Docker и настроить его [на использование контейнеров Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers) перед установкой IoT Edge.  

Если устройство IoT Edge является компьютером под управлением Windows, убедитесь, что оно соответствует [системным требованиям](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) для использования Hyper-V.

## <a name="install-iot-edge-on-a-new-device"></a>Установка IoT Edge на новое устройство

>[!NOTE]
>Использование программных пакетов Azure IoT Edge регулируется условиями лицензии, содержащейся в самих пакетах (в каталоге LICENSE). Прежде чем использовать пакет, ознакомьтесь с условиями лицензии. Установка и использование пакета означают, что вы принимаете эти условия. Если вы не согласны с условиями лицензии, не используйте пакет.

Сценарий PowerShell загружает и устанавливает управляющую программу безопасности Azure IoT Edge. Управляющая программа безопасности запускает первый из двух модулей среды выполнения, агент IoT Edge, который обеспечивает удаленные развертывания других модулей.

При установке среды выполнения IoT Edge в первый раз на устройстве нужно подготовить устройство с помощью удостоверения из центра Интернета вещей. Одно устройство IoT Edge можно сделать вручную с помощью строки подключения к устройству, предоставляемой концентратором IoT. Вы также можете использовать службу подготовки устройств, что удобно, если нужно автоматически подготовить несколько устройств.

Подробнее о различных вариантах установки и параметрах вы можете прочитать в статье [Установить время выполнения Azure IoT Edge на Windows.](how-to-install-iot-edge-windows.md) После установки docker Desktop и настройки для контейнеров Linux основное различие в установке заключается в объявлении Linux с параметром **-ContainerOs.** Пример:

1. Если вы еще не сделали этого, зарегистрируйте новое устройство IoT Edge и извилите строку соединения устройства. Копирование строки соединения для использования позже в этом разделе. Вы можете завершить этот шаг с помощью следующих инструментов:

   * [Портал Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [Лазурный CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Откройте сеанс PowerShell от имени администратора.

   >[!NOTE]
   >Для установки IoT Edge используйте сеанс AMD64 PowerShell, а не PowerShell (x86). Чтобы узнать, какой тип сеанса используется, выполните следующую команду:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Команда **Deploy-IoTEdge** проверяет, что ваша машина Windows находится на поддерживаемой версии, включает функцию контейнеров, а затем загружает время выполнения moby (которое не используется для контейнеров Linux) и время выполнения IoT Edge. Команда по умолчанию по умолчанию windows контейнеров, так объявить Linux в качестве желаемой операционной системы контейнера.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge -ContainerOs Linux
   ```

4. На этом этапе устройства IoT Core могут перезапускаться автоматически. Другие устройства Windows 10 или Windows Server могут побудить вас к перезагрузке. Если да, то перезапустите устройство сейчас. Как только устройство будет готово, снова запустите PowerShell в качестве администратора.

5. Команда **Initialize-IoTEdge** настраивает среду выполнения IoT Edge на вашем компьютере. Команда по умолчанию выполняет ручную подготовку со строкой соединения устройства. Объявите Linux в качестве желаемой операционной системы контейнера снова.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ContainerOs Linux
   ```

6. При запросе укажите строку подключения устройства, которую вы извлекли в шаге 1. Строка подключения устройства связывает физическое устройство с идентификатором устройства в Концентраторе IoT.

   Строка соединения устройства принимает следующий формат и не должна включать кавычки:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

## <a name="verify-successful-installation"></a>Проверка установки

Проверьте состояние службы IoT Edge:

```powershell
Get-Service iotedge
```

Изучите журналы услуг за последние 5 минут:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Выполнить автоматизированную проверку наиболее распространенных ошибок конфигурации и сетей:

```powershell
iotedge check
```

Просмотрите список запущенных модулей. После новой установки единственным модулем, который вы должны увидеть, является **edgeAgent.** После [развертывания модулей IoT Edge](how-to-deploy-modules-portal.md) в первый раз, другой модуль системы, **edgeHub**, начнется на устройстве тоже.

```powershell
iotedge list
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда подготовлено устройство IoT Edge и установлена среда выполнения, вы можете [развернуть модули IoT Edge](how-to-deploy-modules-portal.md).

Если вам не удается установить IoT Edge, перейдите на страницу со сведениями об [устранении неполадок](troubleshoot.md).

Чтобы обновить существующую установку до последней версии IoT Edge, см. раздел [Обновление управляющей программы безопасности и среды выполнения IoT Edge](how-to-update-iot-edge.md).
