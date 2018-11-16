---
title: Установка Azure IoT Edge в ОС Windows с контейнерами Linux | Документация Майкрософт
description: Инструкции по установке Azure IoT Edge в ОС Windows с контейнерами Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: 7bae81f2f8b9ef9f7da45aed3bf8d7b6e15c9d0b
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568273"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows-to-use-with-linux-containers"></a>Установка среды выполнения Azure IoT Edge в ОС Windows для использования с контейнерами Linux

Среда выполнения Azure IoT Edge превращает устройство в устройство IoT Edge. Среду выполнения можно развернуть на всех устройствах — от небольшого Raspberry Pi до технического сервера. Как только на устройстве будет настроена среда выполнения IoT Edge, вы можете начать развертывать в нее бизнес-логику из облака. 

Дополнительные сведения о работе среды выполнения IoT Edge и ее компонентах см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре](iot-edge-runtime.md).

В этой статье описаны этапы установки среды выполнения Azure IoT Edge с помощью контейнеров Linux на компьютере под управлением 64-разрядной ОС Windows (на базе процессора AMD или Intel). Сейчас поддержка Windows доступна в предварительной версии.

>[!NOTE]
Использование контейнеров Linux в системах Windows не является рекомендуемым или поддерживаемым производственной конфигурацией для Azure IoT Edge. Однако его можно использовать для целей разработки и тестирования.

## <a name="supported-windows-versions"></a>Поддерживаемые версии Windows
Azure IoT Edge может использоваться для разработки и тестирования в следующих версиях Windows при использовании контейнеров Linux.
  * Операционные системы Windows 10 или более поздней версии.
  * Операционные системы сервера Windows Server 2016 или новее.

Дополнительные сведения о том, какие операционные системы в настоящее время поддерживаются, см. на странице [поддержки Azure IoT Edge](support.md#operating-systems). 

## <a name="install-the-container-runtime"></a>Установка среды выполнения контейнера 

Служба Azure IoT Edge основана на среде выполнения контейнера, [совместимого с OCI](https://www.opencontainers.org/) (например, Docker). 

Для разработки и тестирования можно использовать [Docker для Windows](https://www.docker.com/docker-windows). Настройте клиент Docker для Windows [на использование контейнеров Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="install-the-azure-iot-edge-security-daemon"></a>Установка управляющей программы безопасности Azure IoT Edge

>[!NOTE]
>Использование программных пакетов Azure IoT Edge регулируется условиями лицензии, содержащейся в самих пакетах (в каталоге LICENSE). Прежде чем использовать пакет, ознакомьтесь с условиями лицензии. Установка и использование пакета означают, что вы принимаете эти условия. Если вы не согласны с условиями лицензии, не используйте пакет.

Одно устройство IoT Edge можно подготовить к работе вручную, используя строку подключения устройства, предоставленную Центром Интернета вещей. Вы также можете использовать службу подготовки устройств, что удобно, если нужно автоматически подготовить несколько устройств. В зависимости от выбранного способа подготовки выберите соответствующий скрипт установки. 

### <a name="option-1-install-and-manually-provision"></a>Вариант 1. Установка и подготовка вручную

1. Выполните действия, описанные в статье [Регистрация нового устройства Azure IoT Edge на портале Azure](how-to-register-device-portal.md), чтобы зарегистрировать устройство и получить для него строку подключения. 

2. На устройстве IoT Edge запустите PowerShell от имени администратора. 

3. Скачайте и установите среду выполнения IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

4. При появлении запроса на ввод строки **DeviceConnectionString** укажите строку подключения, полученную из Центра Интернета вещей. Не заключайте строку подключения в кавычки. 

### <a name="option-2-install-and-automatically-provision"></a>Вариант 2. Установка и автоматическая подготовка

1. Выполните действия, описанные в статье [Создание и подготовка имитированного устройства IoT Edge TPM в Windows](how-to-auto-provision-simulated-device-windows.md), чтобы установить Службу подготовки устройств и получить для нее **идентификатор области**. После этого сымитируйте устройство доверенного платформенного модуля (TPM) и получите для него **идентификатор регистрации**, а затем создайте отдельную регистрацию. Зарегистрировав устройство в Центре Интернета вещей, продолжите установку.  

   >[!TIP]
   >Не закрывайте окно с запущенным симулятором TPM во время установки и тестирования. 

2. На устройстве IoT Edge запустите PowerShell от имени администратора. 

3. Скачайте и установите среду выполнения IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Linux
   ```

4. При появлении запроса укажите **ScopeID** для службы подготовки и **RegistrationID** для устройства.

## <a name="verify-successful-installation"></a>Проверка установки

Чтобы проверить состояние службы IoT Edge, используйте следующий код. 

```powershell
Get-Service iotedge
```

Чтобы просмотреть журналы службы за последние пять минут, используйте следующий код.

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

Чтобы получить список запущенных модулей, используйте следующий код.

```powershell
iotedge list
```

## <a name="tips-and-suggestions"></a>Советы и рекомендации

Если в сети используется прокси-сервер, сведения об установке и запуске среды выполнения IoT Edge см. в [этой статье](how-to-configure-proxy-support.md).

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда подготовлено устройство IoT Edge и установлена среда выполнения, вы можете [развернуть модули IoT Edge](how-to-deploy-modules-portal.md).

Если вам не удается установить среду выполнения Edge, перейдите на страницу со сведениями об [устранении неполадок](troubleshoot.md).
