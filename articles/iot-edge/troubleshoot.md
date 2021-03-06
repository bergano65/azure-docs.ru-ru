---
title: Устранение неполадок в Azure IoT Edge | Документация Майкрософт
description: В этой статье описаны стандартные навыки диагностики для Azure IoT Edge, такие как получение состояния компонента и журналов, а также решение распространенных проблем
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 279b09250aaa3a87e066ade81fc63719adaf07b5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429389"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Распространенные проблемы и их решения для Azure IoT Edge

Если при запуске Azure IoT Edge в вашей среде возникают проблемы, используйте эту статью как руководство для устранения неполадок.

## <a name="run-the-iotedge-check-command"></a>Выполните команду "Check" iotedge

Первым шагом при устранении неполадок IoT Edge должно быть использование команды `check`, которая выполняет набор конфигураций и тестов подключения для распространенных проблем. Команда `check` доступна в [выпуске 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) и более поздних версиях.

Можно выполнить команду `check`, как показано ниже, или включить флаг `--help`, чтобы просмотреть полный список параметров:

* В Linux

  ```bash
  sudo iotedge check
  ```

* В Windows:

  ```powershell
  iotedge check
  ```

Типы проверок, выполняемых средством, можно классифицировать следующим образом:

* Проверки конфигурации: проверяет сведения, которые могут препятствовать подключению пограничных устройств к облаку, включая проблемы с *config. YAML* и подсистемой контейнеров.
* Проверки подключения. проверяет, имеет ли среда выполнения IoT Edge доступ к портам на главном устройстве, а все компоненты IoT Edge могут подключаться к центру Интернета вещей.
* Проверки готовности к производству. Поиск рекомендуемых рекомендаций по производству, таких как состояние сертификатов центра сертификации устройств (ЦС) и конфигурация файла журнала модуля.

Полный список диагностических проверок см. в разделе [встроенные функции устранения неполадок](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Стандартные действия диагностики

Если возникла ошибка, см. Дополнительные сведения о состоянии устройства IoT Edge, просмотрев журналы контейнеров и сообщения, которые передаются на устройство и с него. Для сбора сведений используйте команды и инструменты, приведенные в этом разделе.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Проверка состояния диспетчера безопасности IoT Edge и его журналов

В Linux

* Чтобы просмотреть состояние диспетчера безопасности IoT Edge, используйте следующую команду:

   ```bash
   sudo systemctl status iotedge
   ```

* Чтобы просмотреть журналы диспетчера безопасности IoT Edge, используйте следующую команду:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Чтобы просмотреть более подробные сведения журналов диспетчера безопасности IoT Edge, сделайте следующее.

  * Измените параметры управляющей программы IoT Edge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Обновите следующие строки:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Перезапустите управляющую программу безопасности IoT Edge:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

В Windows:

* Чтобы просмотреть состояние диспетчера безопасности IoT Edge, используйте следующую команду:

   ```powershell
   Get-Service iotedge
   ```

* Чтобы просмотреть журналы диспетчера безопасности IoT Edge, используйте следующую команду:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Если диспетчер безопасности IoT Edge не запущен, проверьте YAML-файл конфигурации.

> [!WARNING]
> Файлы YAML не могут содержать знаки табуляции в качестве отступа. Вместо этого используйте двойные пробелы.

В Linux

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

В Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Проверка журналов контейнеров на наличие ошибок

После запуска управляющей программы безопасности IoT Edge проверьте журналы контейнеров на наличие ошибок. Начните с развернутых контейнеров, а затем просмотрите контейнеры, составляющие среду выполнения IoT Edge: edgeAgent и edgeHub. Журналы IoT Edge агентов обычно предоставляют сведения о жизненном цикле каждого контейнера. Журналы центра IoT Edge содержат сведения об обмене сообщениями и маршрутизации. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Просмотр сообщений, идущих через центр IoT Edge

Вы можете просмотреть сообщения, проходящие через центр IoT Edge, и собрать аналитические сведения из подробных журналов из контейнеров среды выполнения. Чтобы включить ведение подробных журналов в этих контейнерах, задайте значение `RuntimeLogLevel` в файле конфигурации YAML. Чтобы открыть этот файл, выполните следующее.

В Linux

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

В Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

По умолчанию элемент `agent` будет выглядеть, как показано в примере ниже:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Вместо `env: {}` вставьте следующий фрагмент:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML-файлы не могут содержать отступы в виде табуляции. Вместо этого используйте двойные пробелы.

Сохраните файл и перезапустите диспетчер безопасности IoT Edge.

Можно также проверить сообщения, отправленные между Центром Интернета вещей и устройствами IoT Edge. Просмотрите эти сообщения с помощью расширения [Набор средств для Центра Интернета вещей Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (прежнее название — "Набор средств для Интернета вещей Azure") для Visual Studio Code. Дополнительные сведения см. в записи блога об [удобном средстве при разработке с помощью Центра Интернета вещей Azure](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Перезапуск контейнеров

После изучения журналов и сообщений можно попытаться перезапустить контейнеры:

```cmd
iotedge restart <container name>
```

Перезапустите контейнеры среды выполнения IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Перезапустите диспетчер безопасности IoT Edge:

Если проблема не исчезла, попытайтесь перезапустить диспетчер безопасности IoT Edge.

В Linux

   ```cmd
   sudo systemctl restart iotedge
   ```

В Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Агент IoT Edge останавливается через минуту

Модуль edgeAgent запускается и выполняется успешно в течение минуты, а затем останавливается. Журналы указывают, что агент IoT Edge пытается подключиться к центру Интернета вещей через AMQP, а затем пытается подключиться с помощью AMQP через WebSocket. В случае сбоя агент IoT Edge завершает работу.

Примеры журналов edgeAgent:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Основная причина**

Сетевая конфигурация в сети узла не позволяет агенту IoT Edge достичь сети. Сначала агент пытается подключиться через AMQP (порт 5671). Если это не удается, он пытается подключиться через WebSocket (порт 443).

Среда выполнения IoT Edge настраивает сеть для всех модулей, с которыми нужно взаимодействовать. В Linux этой сетью является мостовая сеть. В Windows используется NAT. Эта проблема чаще всего встречается на устройствах с Windows, использующих контейнеры Windows с сетью NAT.

**Способы устранения:**

Должен быть маршрут к Интернету для IP-адресов, назначенных этой мостовой сети или сети NAT. Иногда конфигурация VPN на узле переопределяет сеть IoT Edge.

## <a name="iot-edge-hub-fails-to-start"></a>Не удается запустить центр IoT Edge

Не удается запустить модуль edgeHub и в журналы выводится следующее сообщение:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Основная причина**

На сервере связанный порт 443 используется для другого процесса. Центр IoT Edge сопоставляет порты 5671 и 443 для использования в сценариях шлюза. Если с этим портом связан другой процесс, такое сопоставление портов завершается сбоем.

**Способы устранения:**

Найдите и остановите процесс, который использует порт 443. Таким процессом обычно является веб-сервер.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>Агенту IoT Edge не удается получить доступ к образу модуля (403)

Не удается запустить контейнер, и в журналах edgeAgent отображается ошибка 403.

**Основная причина**

У агента IOT ребра нет разрешений на доступ к образу модуля.

**Способы устранения:**

Убедитесь, что в манифесте развертывания правильно указаны учетные данные реестра.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Сбой управляющей программы безопасности IoT Edge с недопустимым именем узла

Команда `sudo journalctl -u iotedge` завершается ошибкой и выводит следующее сообщение:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Основная причина**

Среда выполнения IoT Edge поддерживает только имена узлов, которые короче 64 символов. Физические компьютеры обычно не имеют длинных имен узлов. Эта ошибка более типична для виртуальных машин. Автоматически создаваемые имена узлов для виртуальных машин Windows, размещенных в Azure, часто бывают длинными. 

**Способы устранения:**

Эту ошибку можно устранить, настроив DNS-имя виртуальной машины, а затем задав DNS-имя в качестве имени узла в команде установки.

1. На портале Azure перейдите к странице обзора виртуальной машины.
2. Выберите **Настроить** под DNS-именем. Если для виртуальной машины уже настроено DNS-имя, настраивать новое не нужно.

   ![Настройка DNS-имени виртуальной машины](./media/troubleshoot/configure-dns.png)

3. Укажите значение в поле **Метка DNS-имени** и выберите **Сохранить**.
4. Скопируйте новое DNS-имя, которое должно быть в формате **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**.
5. В виртуальной машине используйте следующую команду для настройки среды выполнения IoT Edge с DNS-именем:

   * В Linux

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * В Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Проблемы с надежностью на устройствах с ограниченными ресурсами

Вы можете столкнуться с нестабильной работой некоторых устройств с ограниченными ресурсами, таких как Raspberry Pi, особенно если они используются в качестве шлюза. Для такой ситуации характерны исключения с сообщением о нехватке памяти на модуле центра IoT Edge, проблемы с подключением подчиненных устройств и (или) прекращение потока телеметрии после нескольких часов работы устройства.

**Основная причина**

Центр IoT Edge, который является частью среды выполнения IoT Edge, оптимизирован для повышения производительности по умолчанию и пытается выделить большие блоки памяти. Это не идеальный вариант для устройств IoT Edge с ограниченными ресурсами, где он может привести к нестабильной работе.

**Способы устранения:**

Для центра IoT Edge задайте для переменной среды **оптимизефорперформанце** **значение false**. Это можно осуществить двумя путями:

На портале Azure выполните следующие действия.

В центре Интернета вещей выберите устройство IoT Edge и на странице сведения об устройстве выберите **задать модули** > **Параметры среды выполнения**. Создайте переменную среды для модуля концентратора ребра с именем *оптимизефорперформанце* , для которого задано *значение false*.

![Задание значения false для имени OptimizeForPerformance](./media/troubleshoot/optimizeforperformance-false.png)

**OR**

Через манифест развертывания.

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Не удается получить журналы управляющей программы IoT Edge в Windows

Если при использовании команды `Get-WinEvent` в Windows возвращается исключение EventLogException, проверьте записи реестра.

**Основная причина**

Используя запись реестра, команда PowerShell `Get-WinEvent` ищет журналы по определенному имени поставщика (`ProviderName`).

**Способы устранения:**

Задайте запись реестра управляющей программы IoT Edge. Создайте файл **iotedge.reg** с указанным ниже содержимым и импортируйте его в реестр Windows. Для этого дважды щелкните этот файл или используйте команду `reg import iotedge.reg`.

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Модулю IoT Edge не удается отправить сообщение edgeHub, и возникает ошибка 404

Настраиваемому модулю IoT Edge не удается отправить сообщение в edgeHub, и возникает ошибка 404 `Module not found`. Управляющая программа IoT Edge выводит в журналы следующее сообщение: 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

**Основная причина**

Управляющая программа IoT Edge по соображениям безопасности принудительно применяет идентификацию процесса для всех модулей, подключающихся к edgeHub. Таким образом проверяется, что все сообщения, которые отправляются модулем, поступают с идентификатора основного процесса модуля. Если сообщение отправляется модулем с идентификатора процесса, отличного от первоначально установленного, такие сообщения будут отклоняться управляющей программой с ошибкой 404.

**Способы устранения:**

Начиная с версии 1.0.7, всем процессам модуля разрешено подключаться. Если обновление до 1.0.7 невозможно, выполните следующие действия. Дополнительные сведения см. в [журнале изменений выпуска 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Убедитесь, что для отправки сообщений в edgeHub пользовательский модуль IoT Edge всегда использует один и тот же идентификатор процесса. Например, убедитесь, что в файле DOCKER `ENTRYPOINT` вместо команды `CMD`, так как `CMD` приведет к одному ИДЕНТИФИКАТОРу процесса для модуля и другому ИДЕНТИФИКАТОРу процесса для команды bash, запускающей основную программу, в то время как `ENTRYPOINT` приведет к одному ИДЕНТИФИКАТОРу процесса.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Правила конфигурации брандмауэра и порта для развертывания IoT Edge

Azure IoT Edge допускает обмен данными между локальным сервером и облаком Azure с помощью поддерживаемых протоколов центра Интернета вещей, см. раздел [Выбор протокола связи](../iot-hub/iot-hub-devguide-protocols.md). В целях дополнительной безопасности коммуникационные каналы между Azure IoT Edge и Центром Интернета вещей всегда настроены как исходящие. Эта конфигурация основана на [модели связи с поддержкой служб](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), которая позволяет снизить контактную зону, изучаемую вредоносным объектом. Входящий трафик требуется только для конкретных сценариев, в которых Центр Интернета вещей отправляет сообщения на устройство Azure IoT Edge. Сообщения из облака на устройство защищаются с помощью безопасных каналов TLS и могут иметь дополнительную защиту при использовании сертификатов X.509 и модулей устройства TPM. Сведения об установке этой связи с помощью диспетчера безопасности Azure IoT Edge см. в [этой статье](../iot-edge/iot-edge-security-manager.md).

Хотя IoT Edge предоставляет улучшенную конфигурацию для обеспечения безопасности среды выполнения IoT Edge и развернутых модулей, при этом многое по-прежнему зависит от базовой конфигурации компьютера и сети. Следовательно, необходимо обеспечить правильную настройку правил сети и брандмауэра для обеспечения безопасной связи с облаком. Следующая таблица может использоваться в качестве рекомендации при настройке правил брандмауэра для базовых серверов, где размещена Azure IoT Edge среда выполнения:

|Протокол|Port|Входящие|Исходящие|Руководство|
|--|--|--|--|--|
|MQTT;|8883|ЗАБЛОКИРОВАННЫЕ (по умолчанию)|ЗАБЛОКИРОВАННЫЕ (по умолчанию)|<ul> <li>Исходящие подключения должны быть открыты при использовании MQTT в качестве протокола связи.<li>IoT Edge не поддерживает порт 1883 для MQTT. <li>Входящие подключения должны быть заблокированы.</ul>|
|AMQP;|5671|ЗАБЛОКИРОВАННЫЕ (по умолчанию)|ОТКРЫТЫЕ (по умолчанию)|<ul> <li>Протокол связи по умолчанию для IoT Edge. <li> Должен быть открыт, если Azure IoT Edge не настроен для других поддерживаемых протоколов или если AMQP является требуемым протоколом связи.<li>IoT Edge не поддерживает порт 5672 для AMQP.<li>Заблокируйте этот порт, если Azure IoT Edge использует другой протокол, поддерживаемый Центром Интернета вещей.<li>Входящие подключения должны быть заблокированы.</ul></ul>|
|HTTPS|443|ЗАБЛОКИРОВАННЫЕ (по умолчанию)|ОТКРЫТЫЕ (по умолчанию)|<ul> <li>Исходящие подключения должны быть открыты на порту 443 для подготовки IoT Edge. Эта конфигурация необходима при использовании созданных вручную сценариев или Службы подготовки устройств к добавлению в Центр Интернета вещей Azure. <li>Входящие подключения должны быть открытыми только для указанных сценариев: <ul> <li>  Если у вас есть прозрачный шлюз с конечными устройствами, которые могут отправлять запросы метода. В этом случае порт 443 не должен быть открыт во внешних сетях для подключения к Центру Интернета вещей или предоставления служб Центра Интернета вещей через Azure IoT Edge. Таким образом, правило для входящего трафика может разрешать открытие только входящего подключения из внутренней сети. <li> Для сценариев передачи данных из облака на устройство (C2D).</ul><li>IoT Edge не поддерживает порт 80 для HTTP.<li>Если на предприятии невозможно настроить протоколы, отличные от HTTP (например, AMQP или MQTT), сообщения могут отправляться через протоколы WebSocket. В этом случае для подключения по WebSocket будет использоваться порт 443.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Модуль агента пограничной модели постоянно сообщает о пустом файле конфигурации и не запускает модули на устройстве

Устройству не удается запустить модули, определенные в развертывании. Только edgeAgent работает, но постоянно сообщает о пустом файле конфигурации....

**Основная причина**

По умолчанию IoT Edge запускает модули в собственной изолированной сети контейнеров. Возможно, у устройства возникли проблемы с разрешением DNS-имен в этой частной сети.

**Способы устранения:**

**Вариант 1. Настройка DNS-сервера в параметрах модуля контейнера**

Укажите DNS-сервер для среды в параметрах модуля контейнера, которые будут применяться ко всем модулям контейнеров, запущенным ядром. Создайте файл с именем `daemon.json` указав DNS-сервер для использования. Пример.

```json
{
    "dns": ["1.1.1.1"]
}
```

В приведенном выше примере DNS-сервер задается для общедоступной службы DNS. Если пограничному устройству не удается получить доступ к этому IP-адресу из своей среды, замените его на доступный для сервера DNS-сервер.

Разместите `daemon.json` в правильном расположении для своей платформы: 

| Платформа | Расположение |
| --------- | -------- |
| Linux | `/etc/docker` |
| Узел Windows с контейнерами Windows | `C:\ProgramData\iotedge-moby\config` |

Если расположение уже содержит файл `daemon.json`, добавьте к нему ключ **DNS** и сохраните файл.

*Перезапустите модуль контейнера, чтобы обновления вступили в силу.*

| Платформа | Get-Help |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (административная оболочка PowerShell) | `Restart-Service iotedge-moby -Force` |

**Вариант 2. Настройка DNS-сервера при развертывании IoT Edge на модуль**

Вы можете задать DNS-сервер для каждого *креатеоптионс* модуля в развертывании IOT Edge. Пример.

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Обязательно задайте это значение для модулей *edgeAgent* и *edgeHub* .

## <a name="next-steps"></a>Дальнейшие действия

Считаете, что обнаружили ошибку в платформе IoT Edge? [Отправьте запрос](https://github.com/Azure/iotedge/issues), чтобы мы как можно скорее устранили неисправность. 

Если у вас есть другие вопросы, создайте [запрос в службу поддержки](https://portal.azure.com/#create/Microsoft.Support) для получения справки.
