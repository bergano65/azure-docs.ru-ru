---
title: Установка Azure IoT Edge в Linux ARM32 | Документация Майкрософт
description: Инструкции по установке Azure IoT Edge в Linux на устройствах ARM32 на примере Raspberry Pi
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kgremban
ms.openlocfilehash: ad7e34110b0c6d047eb7454b7fac9f8c10df8be2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126381"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Установка среды выполнения Azure IoT Edge в Linux (ARM32v7/armhf)

Среда выполнения Azure IoT Edge превращает устройство в устройство IoT Edge. Среду выполнения можно развернуть на всех устройствах — от небольшого Raspberry Pi до технического сервера. Как только на устройстве будет настроена среда выполнения IoT Edge, вы можете начать развертывать в нее бизнес-логику из облака. 

Дополнительные сведения о работе среды выполнения IoT Edge и ее компонентах см. в статье [Общие сведения о среде выполнения Azure IoT Edge и ее архитектуре](iot-edge-runtime.md).

В этой статье перечислены действия по установке среды выполнения Azure IoT Edge на устройстве Linux ARM32v7/armhf IoT Edge. Например, эти действия будут работать для устройств Raspberry Pi. Список поддерживаемых операционных систем ARM32, см. в разделе [поддержки Azure IoT Edge](support.md#operating-systems). 

>[!NOTE]
>К пакетам в репозиториях программного обеспечения Linux применяются условия лицензии, которые можно найти в каждом пакете (/usr/share/doc/*имя-пакета*). Прежде чем использовать пакет, ознакомьтесь с условиями лицензии. Установка и использование пакета свидетельствуют о принятии этих условий. Если вы не согласны с условиями лицензии, не используйте этот пакет.

## <a name="install-the-container-runtime"></a>Установка среды выполнения контейнера

Служба Azure IoT Edge использует среду выполнения контейнера, [совместимую с OCI](https://www.opencontainers.org/). Для разработки в рабочей среде мы настоятельно рекомендуем использовать платформу [на основе Moby](https://mobyproject.org/), описанную ниже. Это единственная платформа контейнеров, которая официально поддерживается с Azure IoT Edge. Образы контейнеров Docker (Community Edition или Enterprise Edition) совместимы со средой выполнения на основе Moby.

Следующие команды устанавливают модуль на основе Кита и интерфейса командной строки (CLI). CLI применяется на стадии разработки, но при развертывании в рабочей среде он может не понадобится.

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

## <a name="install-the-iot-edge-security-daemon"></a>Установка управляющей программы безопасности IoT Edge

**Управляющей программы безопасности IoT Edge** обеспечивает и поддерживает стандарты безопасности на устройстве IoT Edge. Управляющая программа запускается при каждой загрузке устройства и перезагружает устройство, запуская остальные компоненты среды выполнения IoT Edge. 


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

## <a name="connect-your-device-to-an-iot-hub"></a>Подключение устройства к Центру Интернета вещей 

Настройте среду выполнения IoT Edge, чтобы связать свое физическое устройство с идентификатором, который существует в Центре Интернета вещей Azure. 

Управляющую программу можно настроить с помощью файла конфигурации `/etc/iotedge/config.yaml`. Файл защищен от записи по умолчанию, поэтому может понадобиться повышенным уровнем разрешений, чтобы изменить его.

Одно устройство IoT Edge можно подготовить к работе вручную, используя строку подключения устройства, предоставленную Центром Интернета вещей. Вы также можете использовать службу подготовки устройств, что удобно, если нужно автоматически подготовить несколько устройств. В зависимости от выбранного способа подготовки выберите соответствующий скрипт установки. 

### <a name="option-1-manual-provisioning"></a>Вариант 1. Подготовка вручную

Чтобы вручную подготовить устройства, необходимо указать его с [строки подключения устройства](how-to-register-device-portal.md) , можно создать путем регистрации нового устройства IoT Edge в центр Интернета вещей.

Откройте файл конфигурации. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Найдите раздел подготовки файла и раскомментируйте режим подготовки **вручную**. Замените значение **device_connection_string** строкой подключения для устройства IoT Edge.

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
# provisioning: 
#   source: "dps"
#   global_endpoint: "https://global.azure-devices-provisioning.net"
#   scope_id: "{scope_id}"
#   registration_id: "{registration_id}"
```

Сохраните и закройте файл. 

`CTRL + X`, `Y`, `Enter`

Когда введете сведения о подготовке в файл конфигурации, перезапустите управляющую программу:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Вариант 2. Автоматическая подготовка

Для автоматической подготовки устройства [настройте службу подготовки устройств и получите идентификатор регистрации устройства](how-to-auto-provision-simulated-device-linux.md). Автоматическая подготовка работает только с устройствами, которые содержат микросхему доверенного платформенного модуля (TPM). Например, устройства Raspberry Pi не поставляются с TPM по умолчанию. 

Откройте файл конфигурации. 

```bash
sudo nano /etc/iotedge/config.yaml
```

Найдите раздел подготовки файла и раскомментируйте режим подготовки **dps**. Замените значения **scope_id** и **registration_id** значениями для вашего экземпляра Службы подготовки устройств к добавлению в Центр Интернета вещей и устройства IoT Edge с доверенным платформенным модулем. 

```yaml
# provisioning:
#   source: "manual"
#   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
provisioning: 
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "{scope_id}"
  registration_id: "{registration_id}"
```

Сохраните и закройте файл. 

`CTRL + X`, `Y`, `Enter`

Когда введете сведения о подготовке в файл конфигурации, перезапустите управляющую программу:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Проверка установки

Если вы выполняли **настройку вручную**, как описано в предыдущем разделе, подготовленная среда выполнения IoT Edge должна работать на вашем устройстве. Или, если вы использовали **автоматической настройки** действия, то необходимо выполнить некоторые дополнительные действия, чтобы среда выполнения можно зарегистрировать устройства с центром Интернета вещей от вашего имени. Для получения дальнейших инструкций, см. в разделе [Создание и подготовка имитированного устройства доверенного платформенного МОДУЛЯ IoT Edge на виртуальной машине Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

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

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда подготовлено устройство IoT Edge и установлена среда выполнения, вы можете [развернуть модули IoT Edge](how-to-deploy-modules-portal.md).

Если возникают проблемы со средой выполнения IoT Edge, установка должным образом, см. раздел [Устранение неполадок](troubleshoot.md#stability-issues-on-resource-constrained-devices) страницы.

Чтобы обновить существующую установку до последней версии IoT Edge, см. раздел [Обновление управляющей программы безопасности и среды выполнения IoT Edge](how-to-update-iot-edge.md).
