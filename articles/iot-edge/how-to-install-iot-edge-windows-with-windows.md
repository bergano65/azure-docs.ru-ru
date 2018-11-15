---
title: Установка Azure IoT Edge в Windows с контейнерами Windows | Документация Майкрософт
description: Инструкции по установке Azure IoT Edge в Windows с контейнерами Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: kgremban
ms.openlocfilehash: e6edc9d6e98c03b1a5847dc08bbaa3ad029aa673
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51565043"
---
# <a name="install-azure-iot-edge-runtime-on-windows-to-use-with-windows-containers"></a>Установка среды выполнения Azure IoT Edge в Windows для использования с контейнерами Windows

Среда выполнения Azure IoT Edge превращает устройство в устройство IoT Edge. Среду выполнения можно развернуть на всех устройствах — от небольшого Raspberry Pi до технического сервера. Как только на устройстве будет настроена среда выполнения IoT Edge, вы можете начать развертывать в нее бизнес-логику из облака. 

Дополнительные сведения о работе среды выполнения IoT Edge и ее компонентах см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре](iot-edge-runtime.md).

В этой статье описаны этапы установки среды выполнения Azure IoT Edge с помощью контейнеров Windows в системе Windows x64 (AMD/Intel). 

Сейчас поддержка Windows доступна в предварительной версии.

## <a name="supported-windows-versions"></a>Поддерживаемые версии Windows
Azure IoT Edge с контейнерами Windows можно использовать с такими версиями операционной системы:
  * "Windows 10 IoT Корпоративная" или "Windows 10 IoT Базовая" с обновлением за апрель 2018 года (сборка 17134);
  * Windows Server 1803.

Дополнительные сведения о том, какие операционные системы в настоящее время поддерживаются, см. на странице [поддержки Azure IoT Edge](support.md#operating-systems).

## <a name="install-the-container-runtime"></a>Установка среды выполнения контейнера 

>[!NOTE]
>Чтобы установить платформу контейнеров в ОС Windows IoT Core, следуйте инструкциям из статьи [Установка среды выполнения Azure IoT Edge в ОС Windows IoT Базовая (предварительная версия)](how-to-install-iot-core.md), а затем выполните инструкции, представленные ниже.

Служба Azure IoT Edge основана на среде выполнения контейнера, [совместимого с OCI](https://www.opencontainers.org/) (например, Docker). Для разработки и тестирования можно использовать [Docker для Windows](https://www.docker.com/docker-windows). 

Настройте клиент Docker для Windows [на использование контейнеров Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="install-the-azure-iot-edge-security-daemon"></a>Установка управляющей программы безопасности Azure IoT Edge

>[!NOTE]
>Использование программных пакетов Azure IoT Edge регулируется условиями лицензии, содержащейся в самих пакетах (в каталоге LICENSE). Прежде чем использовать пакет, ознакомьтесь с условиями лицензии. Установка и использование пакета означают, что вы принимаете эти условия. Если вы не согласны с условиями лицензии, не используйте пакет.

Одно устройство IoT Edge можно подготовить к работе вручную, используя строку подключения устройства, предоставленную Центром Интернета вещей. Вы также можете использовать службу подготовки устройств, что удобно, если нужно автоматически подготовить несколько устройств. В зависимости от выбранного способа подготовки выберите соответствующий скрипт установки. 

### <a name="install-and-manually-provision"></a>Установка и подготовка вручную

1. Выполните действия, описанные в статье [Регистрация нового устройства Azure IoT Edge на портале Azure](how-to-register-device-portal.md), чтобы зарегистрировать устройство и получить для него строку подключения. 

2. На устройстве IoT Edge запустите PowerShell от имени администратора. 

3. Скачайте и установите среду выполнения IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Windows
   ```

4. При появлении запроса на ввод строки **DeviceConnectionString** укажите строку подключения, полученную из Центра Интернета вещей. Не заключайте строку подключения в кавычки. 

### <a name="install-and-automatically-provision"></a>Установка и автоматическая подготовка

1. Выполните действия, описанные в статье [Создание и подготовка имитированного устройства IoT Edge TPM в Windows](how-to-auto-provision-simulated-device-windows.md), чтобы установить Службу подготовки устройств и получить для нее **идентификатор области**. После этого сымитируйте устройство доверенного платформенного модуля (TPM) и получите для него **идентификатор регистрации**, а затем создайте отдельную регистрацию. Зарегистрировав устройство в Центре Интернета вещей, продолжите установку.  

   >[!TIP]
   >Не закрывайте окно с запущенным симулятором TPM во время установки и тестирования. 

2. На устройстве IoT Edge запустите PowerShell от имени администратора. 

3. Скачайте и установите среду выполнения IoT Edge. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Dps -ContainerOs Windows
   ```

4. При появлении запроса укажите **ScopeID** для службы подготовки и **RegistrationID** для устройства. 

## <a name="verify-successful-installation"></a>Проверка установки

Чтобы проверить состояние службы IoT Edge, используйте следующий код: 

```powershell
Get-Service iotedge
```

Чтобы просмотреть журналы службы за последние пять минут, используйте следующий код:

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
