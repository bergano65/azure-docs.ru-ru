---
title: Установка Azure IoT Edge в Linux ARM32 | Документация Майкрософт
description: Инструкции по установке Azure IoT Edge в Linux на устройствах ARM32 на примере Raspberry Pi
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.openlocfilehash: 72349597a2831a64cb11ff66b30e6948f34cc921
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68224699"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Установка среды выполнения Azure IoT Edge в Linux (ARM32v7/armhf)

Среда выполнения Azure IoT Edge превращает устройство в устройство IoT Edge. Среду выполнения можно развернуть на всех устройствах — от небольшого Raspberry Pi до технического сервера. Как только на устройстве будет настроена среда выполнения IoT Edge, вы можете начать развертывать в нее бизнес-логику из облака. 

Дополнительные сведения о работе среды выполнения IoT Edge и ее компонентах см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре](iot-edge-runtime.md).

В этой статье перечислены действия по установке среды выполнения Azure IoT Edge на устройстве IoT Edge Linux ARM32v7/армхф. Например, эти действия будут работать для устройств Raspberry Pi. Список поддерживаемых операционных систем ARM32 см. в разделе [Azure IOT Edge Поддерживаемые системы](support.md#operating-systems). 

>[!NOTE]
>К пакетам в репозиториях программного обеспечения Linux применяются условия лицензии, которые можно найти в каждом пакете (/usr/share/doc/*имя-пакета*). Прежде чем использовать пакет, ознакомьтесь с условиями лицензии. Установка и использование пакета свидетельствуют о принятии этих условий. Если вы не согласны с условиями лицензии, не используйте пакет.

## <a name="install-the-latest-version"></a>Установка последней версии

Используйте следующие разделы для установки последней версии службы Azure IoT Edge на устройствах Linux ARM. 

### <a name="install-the-container-runtime"></a>Установка среды выполнения контейнера

Служба Azure IoT Edge использует среду выполнения контейнера, [совместимую с OCI](https://www.opencontainers.org/). Для разработки в рабочей среде мы настоятельно рекомендуем использовать платформу [на основе Moby](https://mobyproject.org/), описанную ниже. Это единственная платформа контейнеров, которая официально поддерживается с Azure IoT Edge. Образы контейнеров Docker (Community Edition или Enterprise Edition) совместимы со средой выполнения на основе Moby.

Следующие команды устанавливают модуль на основе значок Кита и интерфейс командной строки (CLI). CLI применяется на стадии разработки, но при развертывании в рабочей среде он может не понадобится.

```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f
```

### <a name="install-the-iot-edge-security-daemon"></a>Установка управляющей программы безопасности IoT Edge

Управляющая программа **IOT Edge безопасности** предоставляет и поддерживает стандарты безопасности на устройстве IOT Edge. Управляющая программа запускается при каждой загрузке устройства и перезагружает устройство, запуская остальные компоненты среды выполнения IoT Edge. 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

После успешной установки IoT Edge в выходных данных будет предложено обновить файл конфигурации. Выполните действия, описанные в разделе [Настройка управляющей программы Azure IOT Edge Security](#configure-the-azure-iot-edge-security-daemon) , чтобы завершить подготовку устройства. 

## <a name="install-a-specific-version"></a>Установка определенной версии

Если вы хотите установить определенную версию Azure IoT Edge, можно ориентироваться на файлы компонентов непосредственно из репозитория IoT Edge GitHub. Используйте те же `curl` команды, перечисленные в предыдущих разделах, чтобы получить все компоненты IOT EDGE на устройстве: подсистема значок Кита и интерфейс командной строки, либиоссм и, наконец, управляющая программа IOT Edge безопасности. Единственное отличие заключается в том, что URL-адреса **AKA.MS** заменяются ссылками, указывающими на версию каждого компонента, который вы хотите использовать.

Перейдите к [Azure IOT Edge](https://github.com/Azure/azure-iotedge/releases)выпускам и найдите целевую версию для выпуска. Разверните раздел **Assets** для версии и выберите файлы, соответствующие архитектуре устройства IOT Edge. Каждый IoT Edge выпуск содержит файлы **iotedge** и **либиоссм** . Не все выпуски включают **значок Кита-Engine** или **значок Кита-CLI**. Если вы еще не установили подсистему контейнеров значок Кита, просмотрите более старые выпуски, пока не найдете ту, которая содержит компоненты значок Кита. 

После успешной установки IoT Edge в выходных данных будет предложено обновить файл конфигурации. Выполните действия, описанные в следующем разделе, чтобы завершить подготовку устройства. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Настройка управляющей программы Azure IoT Edge Security

Настройте среду выполнения IoT Edge, чтобы связать свое физическое устройство с идентификатором, который существует в Центре Интернета вещей Azure. 

Управляющую программу можно настроить с помощью файла конфигурации `/etc/iotedge/config.yaml`. Файл защищен от записи по умолчанию, поэтому для его редактирования могут потребоваться повышенные разрешения.

Одно устройство IoT Edge можно подготовить к работе вручную, используя строку подключения устройства, предоставленную Центром Интернета вещей. Вы также можете использовать службу подготовки устройств, что удобно, если нужно автоматически подготовить несколько устройств. В зависимости от выбранного способа подготовки выберите соответствующий скрипт установки. 

### <a name="option-1-manual-provisioning"></a>Вариант 1. Подготовка вручную

Чтобы вручную предоставить устройство, необходимо указать [строку подключения устройства](how-to-register-device-portal.md) , которую можно создать, зарегистрировав новое устройство IOT EDGE в центре Интернета вещей.

Откройте файл конфигурации. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Найдите конфигурации подготовки файла и раскомментируйте раздел **Настройка подготовки вручную** . Замените значение **device_connection_string** строкой подключения для устройства IoT Edge. Убедитесь, что все остальные разделы подготовки включены в комментарий.

```yaml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
# DPS TPM provisioning configuration
# provisioning:
#   source: "dps"
#   global_endpoint: "https://global.azure-devices-provisioning.net"
#   scope_id: "{scope_id}"
#   attestation:
#     method: "tpm"
#     registration_id: "{registration_id}"
```

Сохраните и закройте файл. 

`CTRL + X`, `Y`, `Enter`

Когда введете сведения о подготовке в файл конфигурации, перезапустите управляющую программу:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Вариант 2. Автоматическая подготовка

Для автоматической подготовки устройства [настройте службу подготовки устройств и получите идентификатор регистрации устройства](how-to-auto-provision-simulated-device-linux.md). Существует несколько механизмов аттестации, поддерживаемых IoT Edge при использовании автоматической подготовки, но требования к оборудованию также влияют на выбор. Например, по умолчанию устройства Raspberry Pi не поставляются с микросхемой доверенный платформенный модуль (TPM) (TPM).

Откройте файл конфигурации. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Найдите конфигурации подготовки файла и раскомментируйте раздел, соответствующий вашему механизму аттестации. Например, при использовании аттестации доверенного платформенного модуля необходимо обновить значения **scope_id** и **registration_id** , указав значения из службы подготовки устройств центра интернета вещей и устройства IOT Edge с доверенным платформенным модулем соответственно.

```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
```

Сохраните и закройте файл. 

`CTRL + X`, `Y`, `Enter`

Когда введете сведения о подготовке в файл конфигурации, перезапустите управляющую программу:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Проверка установки

Если вы выполняли **настройку вручную**, как описано в предыдущем разделе, подготовленная среда выполнения IoT Edge должна работать на вашем устройстве. Если вы использовали шаги **автоматической настройки** , вам потребуется выполнить некоторые дополнительные действия, чтобы среда выполнения могла зарегистрировать устройство в центре Интернета вещей от вашего имени. Дальнейшие действия см. в статье [Создание и инициализация имитации устройства IOT Edge TPM на виртуальной машине Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Вы можете проверить состояние управляющей программы IoT Edge, используя следующий код:

```bash
systemctl status iotedge
```

Чтобы просмотреть журналы управляющей программы, используйте следующий код:

```bash
journalctl -u iotedge --no-pager --no-full
```

Чтобы получить список запущенных модулей, используйте следующий код.

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Советы и рекомендации

Для запуска команд `iotedge` требуется более высокий уровень привилегий. После установки среды выполнения выйдите из системы компьютера, а затем снова войдите для автоматического обновления разрешений. До тех пор используйте перед любой командой `iotedge` префикс **sudo**.

В устройствах с ограниченными ресурсами настоятельно рекомендуется присвоить переменной среды *OptimizeForPerformance* значение *false* согласно инструкциям в [руководстве по устранению неполадок](troubleshoot.md#stability-issues-on-resource-constrained-devices).

Если в вашей сети используется прокси-сервер, выполните действия, описанные в статье [Настройка устройства IoT Edge для обмена данными через прокси-сервер](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Удаление IoT Edge

Если вы хотите удалить установку IoT Edge с устройства Linux, используйте следующие команды из командной строки. 

Удалите среду выполнения IoT Edge. 

```bash
sudo apt-get remove --purge iotedge
```

При удалении среды выполнения IoT Edge созданные с ее помощью контейнеры остановятся, но будут по-прежнему храниться на устройстве. Просмотрите все контейнеры, чтобы увидеть, какие из них остаются. 

```bash
sudo docker ps -a
```

Удалите контейнеры с устройства, включая два контейнера в среде выполнения. 

```bash
sudo docker rm -f <container name>
```

Наконец, удалите среду выполнения контейнера с устройства. 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Следующие шаги

Теперь, когда подготовлено устройство IoT Edge и установлена среда выполнения, вы можете [развернуть модули IoT Edge](how-to-deploy-modules-portal.md).

Если при правильной установке среды выполнения IoT Edge возникли проблемы, см. страницу [устранения неполадок](troubleshoot.md#stability-issues-on-resource-constrained-devices) .

Чтобы обновить существующую установку до последней версии IoT Edge, см. раздел [Обновление управляющей программы безопасности и среды выполнения IoT Edge](how-to-update-iot-edge.md).
