---
title: Подготовка устройств и их развертывания в рабочей среде — Azure IoT Edge | Документация Майкрософт
description: Learn how to take your Azure IoT Edge solution from development to production, including setting up your devices with the appropriate certificates and making a deployment plan for future code updates.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1d8ba8452f5f2d4ab05083e1a97fa0b9ba75017f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457303"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Подготовка к развертыванию решения IoT Edge в рабочей среде

Когда вы будете готовы перейти от разработки решения IoT Edge к его развертыванию в рабочей среде, убедитесь, что оно настроено для непрерывной работы.

The information provided in this article isn't all equal. Чтобы помочь вам расставить приоритеты, каждый раздел начинается со списков, разделяющих работу на две части: **важные** задачи, которые следует выполнить перед переходом в рабочую среду, и **полезные** для вас сведения.

## <a name="device-configuration"></a>Конфигурация устройств

Устройством IoT Edge может быть что угодно — от Raspberry Pi до ноутбука или виртуальной машины, работающей на сервере. У вас может быть доступ к устройству (физически или через виртуальное подключение), либо оно может быть изолировано в течение продолжительного времени. Either way, you want to make sure that it's configured to work appropriately. 

* **Важно!**
    * Установка рабочих сертификатов
    * Составление плана управления устройствами
    * Использование Moby в качестве модуля контейнеров

* **Полезное**
    * Выбор вышестоящего протокола

### <a name="install-production-certificates"></a>Установка рабочих сертификатов

На каждом устройстве IoT Edge в рабочей среде необходимо установить сертификат от центра сертификации (ЦС). Затем этот сертификат ЦС объявляется в файле config.yaml для среды выполнения IoT Edge. Чтобы упростить разработку и тестирование, среда выполнения IoT Edge создает временные сертификаты, если они не объявлены в файле config.yaml. Однако срок действия этих сертификатов истекает спустя три месяца, и они недостаточно безопасны для рабочих сценариев. 

Сведения о роли сертификатов ЦС для устройств см. в статье [Сведения об использовании сертификатов Azure IoT Edge](iot-edge-certs.md).

Дополнительные сведения о том, как устанавливать сертификаты на устройстве IoT Edge и ссылаться на них из файла config.yaml, см. в статье [Настройка устройства IoT Edge в качестве прозрачного шлюза](how-to-create-transparent-gateway.md). Инструкции по настройке сертификатов не зависят от того, будет ли устройство использоваться в качестве шлюза. В этой статье представлены скрипты для создания образцов сертификатов исключительно в целях тестирования. Не используйте эти сертификаты в рабочей среде. 

### <a name="have-a-device-management-plan"></a>Составление плана управления устройствами

Прежде чем внедрять устройство в рабочую среду, следует определить, как вы будете управлять будущими обновлениями. В случае устройства IoT Edge могут обновляться перечисленные ниже компоненты.

* Встроенное ПО устройства
* Библиотеки операционной системы
* Модуль контейнеров (например, Moby)
* Управляющая программа IoT Edge
* Сертификаты ЦС

For more information, see [Update the IoT Edge runtime](how-to-update-iot-edge.md). Для применения современных методик обновления управляющей программы IoT Edge требуется доступ к устройству IoT Edge (физически или по протоколу SSH). If you have many devices to update, consider adding the update steps to a script or use an automation tool like Ansible.

### <a name="use-moby-as-the-container-engine"></a>Использование Moby в качестве модуля контейнеров

A container engine is a prerequisite for any IoT Edge device. В рабочей среде поддерживается только moby-engine. Другие модули контейнеров, например Docker, совместимы с IoT Edge, и их можно использовать при разработке. Модуль moby-engine можно распространять при использовании с Azure IoT Edge, а корпорация Майкрософт выполняет обслуживание этого модуля.

### <a name="choose-upstream-protocol"></a>Выбор вышестоящего протокола

The protocol (and therefore the port used) for upstream communication to IoT Hub can be configured for both the IoT Edge agent and the IoT Edge hub. По умолчанию используется протокол AMQP, но его можно изменить в зависимости от конфигурации сети. 

У двух модулей среды выполнения есть переменная среды **UpstreamProtocol**. Допустимые значения переменной: 

* MQTT;
* AMQP;
* MQTTWS
* AMQPWS

Configure the UpstreamProtocol variable for the IoT Edge agent in the config.yaml file on the device itself. For example, if your IoT Edge device is behind a proxy server that blocks AMQP ports, you may need to configure the IoT Edge agent to use AMQP over WebSocket (AMQPWS) to establish the initial connection to IoT Hub. 

После подключения устройства IoT Edge обязательно настраивайте переменную UpstreamProtocol для обоих модулей среды выполнения в будущих развертываниях. Пример этого процесса представлен в статье [Настройка устройства IoT Edge для обмена данными через прокси-сервер](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Развертывание.

* **Полезное**
    * Согласование с вышестоящим протоколом
    * Set up host storage for system modules
    * Reduce memory space used by the IoT Edge hub
    * Не используйте отладочные версии образов модулей

### <a name="be-consistent-with-upstream-protocol"></a>Согласование с вышестоящим протоколом

If you configured the IoT Edge agent on your IoT Edge device to use a different protocol than the default AMQP, then you should declare the same protocol in all future deployments. Например, если устройство IoT Edge защищено прокси-сервером, блокирующим порты AMQP, то оно наверняка настроено на подключение по протоколу AMQP через WebSocket (AMQPWS). When you deploy modules to the device, configure the same AMQPWS protocol for the IoT Edge agent and IoT Edge hub, or else the default AMQP will override the settings and prevent you from connecting again. 

You only have to configure the UpstreamProtocol environment variable for the IoT Edge agent and IoT Edge hub modules. Все остальные модули используют протокол, заданный в модулях среды выполнения. 

Пример этого процесса представлен в статье [Настройка устройства IoT Edge для обмена данными через прокси-сервер](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Set up host storage for system modules

The IoT Edge hub and agent modules use local storage to maintain state and enable messaging between modules, devices, and the cloud. For better reliability and performance, configure the system modules to use storage on the host filesystem.

For more information, see [Host storage for system modules](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Reduce memory space used by IoT Edge hub

If you're deploying constrained devices with limited memory available, you can configure IoT Edge hub to run in a more streamlined capacity and use less disk space. These configurations do limit the performance of the IoT Edge hub, however, so find the right balance that works for your solution. 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Не оптимизируйте устройства с ограниченными ресурсами для высокой производительности

The IoT Edge hub is optimized for performance by default, so it attempts to allocate large chunks of memory. Такая конфигурация может привести к проблемам со стабильностью на небольших устройствах, таких как Raspberry Pi. If you're deploying devices with constrained resources, you may want to set the **OptimizeForPerformance** environment variable to **false** on the IoT Edge hub. 

When **OptimizeForPerformance** is set to **true**, the MQTT protocol head uses the PooledByteBufferAllocator which has better performance but allocates more memory. The allocator does not work well on 32 bit operating systems or on devices with low memory. Additionally, when optimized for performance, RocksDb allocates more memory for its role as the local storage provider. 

Дополнительные сведения см. в разделе [Проблемы с надежностью на устройствах с ограниченными ресурсами](troubleshoot.md#stability-issues-on-resource-constrained-devices).

#### <a name="disable-unused-protocols"></a>Отключение неиспользуемых протоколов

Another way to optimize the performance of the IoT Edge hub and reduce its memory usage is to turn off the protocol heads for any protocols that you're not using in your solution. 

Protocol heads are configured by setting boolean environment variables for the IoT Edge hub module in your deployment manifests. Вот эти переменные:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Имена всех трех переменных содержат *два подчеркивания*, и для них можно задавать значение true или false. 

#### <a name="reduce-storage-time-for-messages"></a>Сокращение времени хранения сообщений

The IoT Edge hub module stores messages temporarily if they cannot be delivered to IoT Hub for any reason. You can configure how long the IoT Edge hub holds on to undelivered messages before letting them expire. If you have memory concerns on your device, you can lower the **timeToLiveSecs** value in the IoT Edge hub module twin. 

Для параметра timeToLiveSecs по умолчанию задано значение 7200 секунд, то есть два часа. 

### <a name="do-not-use-debug-versions-of-module-images"></a>Не используйте отладочные версии образов модулей

Переходя от разработки к рабочей среде, обязательно удалите конфигурации отладки из манифестов развертывания. Убедитесь, что имена образов модулей в манифестах развертывания не содержат суффикс **\.debug**. Если вы добавили параметры создания, чтобы открыть порты в модулях для отладки, удалите и эти параметры. 

## <a name="container-management"></a>Управление контейнерами

* **Важно!**
    * Управление доступом к реестру контейнеров
    * Использование тегов для управления версиями

### <a name="manage-access-to-your-container-registry"></a>Управление доступом к реестру контейнеров

Прежде чем развертывать модули на рабочих устройствах IoT Edge, убедитесь, что вы контролируете доступ к реестру контейнеров, чтобы посторонние не могли получать доступ к образам контейнеров и вносить в них изменения. Используйте частный, а не общедоступный, реестр контейнеров для управления их образами. 

В руководствах и другой документации говорится, что следует использовать на устройстве IoT Edge те же учетные данные реестра контейнеров, что и на компьютере для разработки. Эти инструкции призваны лишь помочь вам настраивать среды тестирования и разработки. Их не следует соблюдать в рабочей среде. Реестр контейнеров Azure рекомендует выполнять [проверку подлинности при помощи субъектов-служб](../container-registry/container-registry-auth-service-principal.md), если приложения или службы извлекают образы контейнеров автоматически или без участия пользователя, как устройства IoT Edge. Создайте субъект-службу с доступом только для чтения к реестру контейнеров и укажите эти имя пользователя и пароль в манифесте развертывания.

### <a name="use-tags-to-manage-versions"></a>Использование тегов для управления версиями

A tag is a docker concept that you can use to distinguish between versions of docker containers. Теги — это суффиксы (например, **1.0**), указываемые в конце имени репозитория контейнеров. Пример: **mcr.microsoft.com/azureiotedge-agent:1.0**. Теги можно в любой момент менять, чтобы указывать на другой контейнер, поэтому вашей команде следует договориться о правилах обновления образов модулей в будущем. 

Теги также помогают принудительно устанавливать обновления на устройствах IoT Edge. Отправляя обновленную версию модуля в реестр контейнеров, увеличивайте тег. Затем отправьте новое развертывание на устройства с увеличенным тегом. Модуль контейнеров распознает увеличенный тег как новую версию и извлечет последнюю версию модуля на устройство. 

Пример соглашения о тегах представлен в разделе [Сведения о тегах IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). Он поможет вам понять, как IoT Edge использует последовательные и конкретные теги для отслеживания версий. 

## <a name="networking"></a>Работа в сети

* **Полезное**
    * Просмотр конфигурации исходящих и входящих подключений
    * Allow connections from IoT Edge devices
    * Настройка связи через прокси-сервер

### <a name="review-outboundinbound-configuration"></a>Просмотр конфигурации исходящих и входящих подключений

Каналы связи между Центром Интернета вещей Azure и IoT Edge всегда настраиваются как исходящие. Для большинства сценариев IoT Edge достаточно трех подключений. Модуль контейнеров должен соединяться с реестрами контейнеров, в которых хранятся образы модулей. Среда выполнения IoT Edge должна подключиться к Центру Интернета вещей, чтобы получить сведения о конфигурации устройств, а также отправить сообщение и данные телеметрии. А если вы используете автоматическую подготовку, управляющая программа IoT Edge должна подключиться к службе подготовки устройств. Дополнительные сведения см. в разделе [Правила конфигурации брандмауэра и порта для развертывания IoT Edge](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment).

### <a name="allow-connections-from-iot-edge-devices"></a>Allow connections from IoT Edge devices

If your networking setup requires that you explicitly permit connections made from IoT Edge devices, review the following list of IoT Edge components:

* **Агент IoT Edge** открывает постоянное подключение AMQP/MQTT к Центру Интернета вещей (возможно, через объекты WebSocket). 
* **Центр IoT Edge** открывает одно постоянное подключение AMQP или несколько подключений MQTT к Центру Интернета вещей (возможно, через объекты WebSocket). 
* **Управляющая программа IoT Edge** совершает прерывистые HTTPS-вызовы к Центру Интернета вещей. 

Во всех трех случаях имя DNS соответствует шаблону \*.azure-devices.net. 

Кроме того, **модуль контейнеров** совершает вызовы реестров контейнеров по протоколу HTTPS. Чтобы получить образы контейнеров для среды выполнения IoT Edge, используйте имя DNS mcr.microsoft.com. Модуль контейнеров подключается к другим реестрам в соответствии с настройками развертывания. 

Этот контрольный список является отправной точкой для настройки правил брандмауэра:

   | URL-адрес (\* = подстановочный знак) | Исходящие порты TCP | Использование |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Реестр контейнеров Майкрософт |
   | global.azure-devices-provisioning.net  | 443 | Доступ к DPS (необязательно) |
   | \*.azurecr.io | 443 | Personal and third-party container registries |
   | \*.blob.core.windows.net | 443 | Download Azure Container Registry image deltas from blob storage  | 
   | \*.azure-devices.net | 5671, 8883, 443 | Доступ к Центру Интернета вещей |
   | \*.docker.io  | 443 | Docker Hub access (optional) |

Some of these firewall rules are inherited from Azure Container Registry. For more information, see [Configure rules to access an Azure container registry behind a firewall](../container-registry/container-registry-firewall-access-rules.md).

### <a name="configure-communication-through-a-proxy"></a>Настройка связи через прокси-сервер

Если устройства будут развернуты в сети, использующей прокси-сервер, они должны иметь возможность взаимодействовать через прокси-сервер, чтобы получать доступ к Центру Интернета вещей и реестрам контейнеров. Дополнительные сведения см. в статье [Настройка устройства IoT Edge для обмена данными через прокси-сервер](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Управление решениями

* **Полезное**
    * Настройка журналов и диагностики
    * Тесты и конвейеры CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Настройка журналов и диагностики

On Linux, the IoT Edge daemon uses journals as the default logging driver. Чтобы запрашивать журналы управляющей программы, можно использовать программу командной строки `journalctl`. В Windows управляющая программа IoT Edge использует диагностику PowerShell. Чтобы запрашивать журналы из управляющей программы, используйте команду `Get-IoTEdgeLog`. IoT Edge modules use the JSON driver for logging, which is the  default.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

При тестировании развертывания IoT Edge обычно можно получать доступ к своим устройствам для извлечения журналов и устранения неполадок. В случае развертывания эта возможность может отсутствовать. Подумайте, как вы будете собирать сведения об устройствах в рабочей среде. Один вариант — использовать модуль ведения журнала, который собирает сведения от других модулей и отправляет их в облако. К примерам модулей ведения журнала относится [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics). Вы также можете создать собственный модуль. 

### <a name="place-limits-on-log-size"></a>Place limits on log size

By default the Moby container engine does not set container log size limits. Over time this can lead to the device filling up with logs and running out of disk space. Consider the following options to prevent this:

**Option: Set global limits that apply to all container modules**

You can limit the size of all container logfiles in the container engine log options. The following example sets the log driver to `json-file` (recommended) with limits on size and number of files:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Add (or append) this information to a file named `daemon.json` and place it the right location for your device platform.

| платформа | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

The container engine must be restarted for the changes to take effect.

**Option: Adjust log settings for each container module**

You can do so in the **createOptions** of each module. Пример.

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

**Additional options on Linux systems**

* Configure the container engine to send logs to `systemd` [journal](https://docs.docker.com/config/containers/logging/journald/) by setting `journald` as the default logging driver. 

* Periodically remove old logs from your device by installing a logrotate tool. Используйте следующую спецификацию файла: 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Тесты и конвейеры CI/CD

Для максимально эффективного развертывания IoT Edge рекомендуем интегрировать рабочую среду с конвейерами CI/CD и тестирования. Azure IoT Edge поддерживает множество платформ CI/CD, включая Azure DevOps. Дополнительные сведения см. в статье [Непрерывная интеграция и непрерывное развертывание в Azure IoT Edge](how-to-ci-cd.md).

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения см. в статье об [автоматическом развертывании IoT Edge](module-deployment-monitoring.md).
* Узнайте о поддержке [непрерывных интеграции и развертывания](how-to-ci-cd.md) в IoT Edge.
