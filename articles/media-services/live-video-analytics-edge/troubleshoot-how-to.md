---
title: Устранение неполадок в Live Video Analytics на IoT Edge в Azure
description: В этой статье рассматриваются действия по устранению неполадок в службе Live Video Analytics на IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: c297a189f3b13ca8e72daf4eef009bc28fac32bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823202"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Устранение неполадок в Live Video Analytics на IoT Edge

В этой статье рассматриваются действия по устранению неполадок в реальном видео-аналитике (лва) на Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Устранение неполадок развертывания

### <a name="diagnostics"></a>Диагностика

В рамках развертывания Live Video Analytics вы настраиваете ресурсы Azure, такие как центр Интернета вещей и устройства IoT Edge. В качестве первого шага для диагностики проблем всегда убедитесь, что Пограничное устройство правильно настроено, выполнив следующие инструкции:

1. [Выполните `check` команду](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Проверьте версию IOT Edge](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Проверьте состояние диспетчера безопасности IOT EDGE и его журналов](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Просмотр сообщений, передаваемых через центр IOT Edge](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Перезапустите контейнеры](../../iot-edge/troubleshoot.md#restart-containers).
1. [Проверьте правила конфигурации брандмауэра и порта](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Проблемы перед развертыванием

Если инфраструктура пограничной инфраструктуры подойдет, можно найти проблемы с файлом манифеста развертывания. Чтобы развернуть модуль Live Video Analytics в модуле IoT Edge на IoT Edge устройстве вместе с любыми другими модулями Интернета вещей, используйте манифест развертывания, который содержит центр IoT Edge, IoT Edge агент и другие модули и их свойства. Если код JSON сформирован неправильно, может появиться следующее сообщение об ошибке: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Не удалось выполнить синтаксический анализ JSON из файла: " <deployment manifest.json> " для аргумента "Content" с исключением: "дополнительные данные: строка 101, столбец 1 (char 5325)"

При возникновении этой ошибки рекомендуется проверить JSON на отсутствие квадратных скобок или другие проблемы со структурой файла. Чтобы проверить структуру файла, можно использовать клиент, например ["Блокнот" + + с подключаемым модулем средства просмотра JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) или онлайн-инструмент, например [модуль форматирования JSON & проверяющий элемент управления](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Во время развертывания: диагностика с помощью прямых методов Media Graph 

После того как модуль Live Video Analytics в модуле IoT Edge правильно развернут на устройстве IoT Edge, можно создать и запустить граф мультимедиа, вызвав [прямые методы](direct-methods.md). Вы можете использовать портал Azure для выполнения диагностики графа мультимедиа с помощью прямых методов:

1. В портал Azure перейдите в центр Интернета вещей, подключенный к устройству IoT Edge.

1. Найдите **Автоматическое управление устройствами**, а затем выберите **IOT Edge**.  

1. В списке пограничных устройств выберите устройство, которое требуется диагностировать.  
         
    ![Снимок экрана портал Azure отображения списка пограничных устройств](./media/troubleshoot-how-to/lva-sample-device.png)

1. Проверьте, имеет ли код ответа значение *200-ОК*. Ниже приведены другие коды ответов для [среды выполнения IOT Edge](../../iot-edge/iot-edge-runtime.md) .
    * 400 — конфигурация развертывания имеет неправильный формат или недопустима.
    * 417-у устройства нет набора конфигураций развертывания.
    * 412 — версия схемы в конфигурации развертывания недопустима.
    * 406 — устройство IoT Edge работает в автономном режиме или не отправляет отчеты о состоянии.
    * 500 — в среде выполнения IoT Edge произошла ошибка.

1. Если вы получаете код состояния 501, убедитесь, что имя прямого метода является точным. Если имя метода и полезные данные запроса являются точными, необходимо получить результаты вместе с кодом успешного выполнения = 200. Если полезные данные запроса неточны, вы получите состояние = 400 и полезные данные ответа, которые указывают код ошибки и сообщение, которое должно помочь при диагностике проблемы с помощью вызова прямого метода.
    * Проверка сообщаемых и требуемых свойств поможет понять, синхронизированы ли свойства модуля с развертыванием. Если это не так, можно перезапустить устройство IoT Edge. 
    * Воспользуйтесь руководством по [прямым методам](direct-methods.md) , чтобы вызвать несколько методов, особенно простых, например графтопологилист. Кроме того, в этом руководством указываются ожидаемые полезные данные запроса и ответа, а также коды ошибок. После успешного выполнения простых прямых методов можно быть уверенным в том, что модуль Live Video Analytics IoT Edge работает нормально.
        
       ![Снимок экрана: панель "прямой метод" для модуля IoT Edge.](./media/troubleshoot-how-to/direct-method.png) 

1. Если для столбцов, **указанных в** параметре развертывание и **сообщаемые по** столбцам устройства, указано *значение Да*, можно вызвать прямые методы в реальном видео Analytics для IOT Edge модуля. Выберите модуль, чтобы перейти на страницу, где можно проверить требуемые и сообщаемые свойства и вызвать прямые методы. Необходимо учитывать следующее: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>После развертывания: Диагностика журналов на наличие проблем во время выполнения 

Журналы контейнера для модуля IoT Edge должны содержать диагностические сведения, помогающие отладить проблемы во время выполнения модуля. Вы можете [проверить журналы контейнера на наличие проблем](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) и самостоятельно диагностировать проблему. 

Если вы выполнили все предыдущие проверки и все еще столкнулись с проблемами, соберите журналы с IoT Edge устройства [с помощью `support bundle` команды](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) для дальнейшего анализа командой Azure. Вы можете [связаться с нами](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) для получения поддержки и отправки собранных журналов.

## <a name="common-error-resolutions"></a>Распространенные способы устранения ошибок

Функция Live Video Analytics развертывается как модуль IoT Edge на устройстве IoT Edge и работает совместно с IoT Edge агентом и модулями концентратора. Некоторые распространенные ошибки, возникающие при развертывании в реальном времени, вызываются проблемами с базовой инфраструктурой IoT. К ошибкам относятся:

* [Агент IOT Edge останавливается через минуту](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [Агент IOT EDGE не может получить доступ к образу модуля (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [Модуль агента IOT Edge сообщает об пустом файле конфигурации и не запускает модули на устройстве](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Не удается запустить центр IOT Edge](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [Сбой управляющей программы IOT Edge безопасности с недопустимым именем узла](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [Интерактивная аналитика видео или любой другой настраиваемый модуль IOT EDGE не может отправить сообщение в концентратор ребра с ошибкой 404](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [Модуль IOT Edge развертывается успешно, а затем исчезает с устройства](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-setup-script-issues"></a>Проблемы с скриптом установки ребра

В рамках нашей документации мы предоставили [сценарий установки](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) для развертывания пограничных и облачных ресурсов, а также Приступая к работе с пограничными функциями Live Video Analytics. В этом разделе представлены некоторые ошибки сценариев, которые могут возникнуть, а также решения для их отладки.

Ошибка. сценарий выполняется, частично создавая несколько ресурсов, но завершается со следующим сообщением:

```
registering device...

Unable to load extension 'eventgrid: unrecognized kwargs: ['min_profile']'. Use --debug for more information.
The command failed with an unexpected error. Here is the traceback:

No module named 'azure.mgmt.iothub.iot_hub_client'
Traceback (most recent call last):
File "/opt/az/lib/python3.6/site-packages/knack/cli.py", line 215, in invoke
  cmd_result = self.invocation.execute(args)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 631, in execute
  raise ex
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 695, in _run_jobs_serially
  results.append(self._run_job(expanded_arg, cmd_copy))
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 688, in _run_job
  six.reraise(*sys.exc_info())
File "/opt/az/lib/python3.6/site-packages/six.py", line 693, in reraise
  raise value
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 665, in _run_job
  result = cmd_copy(params)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/commands/__init__.py", line 324, in __call__
  return self.handler(*args, **kwargs)
File "/opt/az/lib/python3.6/site-packages/azure/cli/core/__init__.py", line 574, in default_command_handler
  return op(**command_args)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 75, in iot_device_list
  result = iot_query(cmd, query, hub_name, top, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/operations/hub.py", line 45, in iot_query
  target = get_iot_hub_connection_string(cmd, hub_name, resource_group_name, login=login)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/common/_azure.py", line 112, in get_iot_hub_connection_string
  client = iot_hub_service_factory(cmd.cli_ctx)
File "/home/.azure/cliextensions/azure-cli-iot-ext/azext_iot/_factory.py", line 28, in iot_hub_service_factory
  from azure.mgmt.iothub.iot_hub_client import IotHubClient
ModuleNotFoundError: No module named 'azure.mgmt.iothub.iot_hub_client'
```
    
Устранение проблемы:

1. Выполните следующую команду:

    ```
    az --version
    ```
1. Убедитесь, что установлены следующие расширения. Начиная с публикации этой статьи, расширения и их версии:

    | Расширение | Версия |
    |---|---|
    |azure-cli   |      2.5.1|
    |Command-modules-нспкг         |   2.0.3|
    |core  |    2.5.1|
    |нспкг    | 3.0.4|
    |Данные телеметрии| 1.0.4|
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Если у вас установлено расширение, версия которого предшествует номеру выпуска, указанному здесь, обновите расширение с помощью следующей команды:

    ```
    az extension update --name <Extension name>
    ```

    Например, вы можете запустить `az extension update --name azure-iot` .

### <a name="sample-app-issues"></a>Примеры проблем приложения

В рамках нашего выпуска мы предоставили пример кода для .NET, который поможет вам приступить к начальной загрузке сообщества разработчиков. В этом разделе представлены некоторые ошибки, которые могут возникнуть при запуске примера кода, а также решения для их отладки.

Ошибка: Program.cs завершается со следующей ошибкой в вызове прямого метода:

```
Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
```

1. Убедитесь, что в среде Visual Studio Code установлены [средства Azure IOT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , и вы настроили подключение к центру Интернета вещей. Для этого нажмите клавиши CTRL + SHIFT + P, а затем выберите **пункт выбрать метод центра Интернета вещей**.

1. Проверьте, можно ли вызвать прямой метод в модуле IoT Edge с помощью Visual Studio Code. Например, вызовите графтопологилист со следующими полезными данными { &nbsp; " @apiVersion ": "1,0"}. Вы должны получить следующий ответ: 

    ```
    {
      "status": 200,
      "payload": {
        "values": [
          {…
    …}
          ]
        }
    }
    ```

    ![Снимок экрана ответа в Visual Studio Code.](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Если предыдущее решение завершается сбоем, попробуйте выполнить следующие действия.

    a. Перейдите в командную строку на устройстве IoT Edge и выполните следующую команду:
    
      ```
      sudo systemctl restart iotedge
      ```

      Эта команда перезапускает устройство IoT Edge и все модули. Подождите несколько минут, а затем, прежде чем пытаться снова использовать прямой метод, убедитесь, что модули работают, выполнив следующую команду:

      ```
      sudo iotedge list
      ```

    b. Если предыдущий подход также завершится сбоем, попробуйте перезагрузить виртуальную машину или компьютер.

    c. В случае сбоя всех подходов выполните следующую команду, чтобы получить сжатый ZIP-файл со всеми [соответствующими журналами](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command)и присоединить его к запросу в [службу поддержки](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```

1. Если вы получаете код ошибки *400* , убедитесь, что полезные данные вызова метода имеют правильный формат, в соответствии с руководством по [прямым методам](direct-methods.md) .
1. Если вы получаете код состояния *200* , это означает, что концентратор работает правильно и развертывание модуля выполняется правильно и отвечает. 

1. Проверьте, является ли конфигурация приложения точной. Конфигурация приложения состоит из следующих полей в *appsettings.js* в файле. Проверьте правильность значений deviceId и moduleId. Простой способ проверки — перейти к разделу расширение центра Интернета вещей Azure в Visual Studio Code. Значения в *appsettings.jsв* файле и в центре Интернета вещей должны совпадать.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

1. В *appsettings.js* файле убедитесь, что вы указали строку подключения центра Интернета вещей, а *не* строку подключения устройства центра Интернета вещей, так как [форматы строк подключения различаются](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/).

### <a name="live-video-analytics-working-with-external-modules"></a>Интерактивная аналитика видео работает с внешними модулями

Интерактивная аналитика видео с помощью обработчика расширений HTTP может расширить граф мультимедиа для отправки и получения данных из других модулей IoT Edge по протоколу HTTP с помощью функции RESTFUL. В качестве [конкретного примера](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)граф мультимедиа может отправлять видеоматериалы в виде изображений во внешний модуль вывода, например Йоло v3 и получать результаты анализа на основе JSON. В такой топологии назначением для событий является преимущественно центр Интернета вещей. В ситуациях, когда вы не видите события вывода в концентраторе, проверьте следующее:

* Убедитесь, что в концентраторе, на котором выполняется публикация медиа-графика, и что проверяемый центр является одним и тем же. При создании нескольких развертываний вы можете получить несколько центров и по ошибке проверить неправильный центр для событий.
* В Visual Studio Code проверьте, развернут и выполняется ли внешний модуль. В примере изображения ртспсим и ОПС являются IoT Edge модулями, работающими извне модуля Лваедже.

    ![Снимок экрана, на котором отображается состояние выполнения модулей в центре Интернета вещей Azure.](./media/troubleshoot-how-to/iot-hub.png)

* Проверьте, отправляются ли события в правильную конечную точку URL-адреса. Внешний контейнер AI предоставляет URL-адрес и порт, через который он получает и возвращает данные из запросов POST. Этот URL-адрес указан как `endpoint: url` свойство для обработчика РАСШИРЕНИЙ HTTP. Как видно в [URL-адресе топологии](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json), конечной точке присваивается параметр URL-адреса. Убедитесь, что значение по умолчанию для параметра или переданное значение является точным. Можно проверить, работает ли он с помощью URL-адреса клиента (в виде фигур).  

    Например, ниже приведен контейнер Йоло v3, который работает на локальном компьютере с IP-адресом 172.17.0.3. Для поиска IP-адреса используйте проверку DOCKER.

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Возвращенный результат:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

* Если вы используете один или несколько экземпляров графа, использующих обработчик расширений HTTP, необходимо иметь фильтр частоты кадров перед каждым обработчиком расширений HTTP для управления частотой кадров в секунду (кадров/с) в канале видео. 

   В некоторых ситуациях, где ЦП или память пограничных компьютеров сильно загружены, можно потерять определенные события вывода. Чтобы устранить эту ошибку, задайте низкое значение для свойства Максимумфпс в фильтре частоты кадров. Можно задать для него значение 0,5 ("Максимумфпс": 0,5) на каждом экземпляре графа, а затем повторно запустить экземпляр, чтобы проверить наличие событий вывода в концентраторе.

   Кроме того, вы можете получить более мощный компьютер с большим объемом ресурсов ЦП и памяти.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Несколько прямых методов в Parallel — сбой времени ожидания 

Интерактивная аналитика видео на IoT Edge предоставляет прямую модель программирования на основе методов, которая позволяет настроить несколько топологий и несколько экземпляров графов. В рамках настройки топологии и графа вы вызываете несколько прямых вызовов методов для модуля IoT Edge. При одновременном вызове этих нескольких вызовов методов, особенно тех, которые запускают и останавливают графы, может произойти сбой времени ожидания, как показано ниже: 

Метод инициализации сборки Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.IniТиализеассемблясинк вызвал исключение. Microsoft. Azure. Devices. Common. Exceptions. Иосубексцептион: Microsoft. Azure. Devices. Common. Exceptions. Иосубексцептион:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

*Не* рекомендуется вызывать прямые методы параллельно. Вызывайте их последовательно (т. е. Сделайте один прямой вызов метода только после завершения предыдущего).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Собирайте журналы для отправки запроса в службу поддержки

Если шаги самостоятельного устранения неполадок не помогли устранить проблему, перейдите на портал Azure и [откройте запрос в службу поддержки](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> Журналы могут содержать личные сведения (PII), такие как IP-адрес. Все локальные копии журналов будут удалены сразу после завершения анализа и закрытия запроса в службу поддержки.  

Чтобы собрать соответствующие журналы, которые необходимо добавить в билет, следуйте инструкциям в следующих разделах. Файлы журнала можно передать в области **сведений** запроса на поддержку.

### <a name="use-the-support-bundle-command"></a>Использование команды support-пучок

Если необходимо собрать журналы с IoT Edge устройства, проще всего использовать `support-bundle` команду. Эта команда собирает:

- Журналы модулей
- IoT Edge журналов диспетчера безопасности и модуля контейнера
- Iotedge проверка выходных данных JSON
- Полезные отладочные сведения

1. Выполните `support-bundle` команду с флагом *--с* , чтобы указать, сколько времени следует охватывать журналы. Например, 2H получит журналы за последние два часа. Можно изменить значение этого флага, чтобы включить журналы для различных периодов.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Эта команда создает файл с именем *support_bundle.zip* в каталоге, в котором была выполнена команда. 
   
1. Прикрепите файл *support_bundle.zip* к запросу в службу поддержки.

### <a name="live-video-analytics-debug-logs"></a>Журналы отладки для анализа видео в реальном времени

Чтобы настроить интерактивную аналитику видео в модуле IoT Edge для создания журналов отладки, выполните следующие действия.

1. Войдите в [портал Azure](https://portal.azure.com)и перейдите в центр Интернета вещей.
1. На левой панели выберите **IOT Edge**.
1. В списке устройств выберите идентификатор целевого устройства.
1. В верхней части панели выберите **задать модули**.

   ![Снимок экрана кнопки "задать модули" в портал Azure.](media/troubleshoot-how-to/set-modules.png)

1. В разделе **IOT Edge modules** найдите и выберите **лваедже**.
1. Выберите **Параметры создания контейнера**.
1. В разделе **привязки** добавьте следующую команду:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Эта команда привязывает папки журналов между граничным устройством и контейнером. Чтобы сохранить журналы в другом расположении, используйте следующую команду, заменив **$LOG _LOCATION_ON_EDGE_DEVICE** на расположение, которое вы хотите использовать. `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Выберите **Обновить**.
1. Выберите **Review + Create** (Просмотреть и создать). Сообщение об успешной проверке публикуется под зеленым баннером.
1. Нажмите кнопку **создания**.
1. Обновите **двойника удостоверений модуля** , чтобы он указывал на параметр дебуглогсдиректори, указывающий на каталог, в котором собираются журналы.

    a. В таблице **модули** выберите **лваедже**.  
    b. В верхней части панели выберите **модуль удостоверение двойника**. Откроется Редактируемая панель.  
    c. В разделе **требуемый ключ**добавьте следующую пару "ключ-значение":  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Эта команда привязывает папки журналов между граничным устройством и контейнером. Если вы хотите получить журналы в другом расположении на устройстве, сделайте следующее:
    > 1. Создайте привязку для расположения журнала отладки в разделе " **привязки** ", заменив **_LOG_LOCATION_ON_EDGE_DEVICE $DEBUG** и **$Debug _LOG_LOCATION** на нужное расположение: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Используйте следующую команду, заменив **$DEBUG _LOG_LOCATION** на расположение, используемое на предыдущем шаге:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Щелкните **Сохранить**.

1. Воспроизведите проблему.
1. Подключитесь к виртуальной машине на странице **центра Интернета вещей** на портале.
1. Заархивировать все файлы в папке *дебуглогс*

   > [!NOTE]
   > Эти файлы журналов не предназначены для самостоятельной диагностики. Они предназначены для анализа ваших проблем командой инженеров Azure.

   a. В следующей команде обязательно замените **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** расположением журналов отладки на пограничном устройстве, которое вы настроили ранее.  

   ```
   sudo apt install zip unzip  
   zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
   ```

   b. Прикрепите файл *debugLogs.zip* к запросу в службу поддержки.

1. Вы можете отключить сбор журналов, установив значение в поле **идентификатор модуля двойника** в значение *null*. Вернитесь на страницу **удостоверения модуля двойника** и измените следующий параметр на:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Дальнейшие шаги

[Руководство по Запись видео в облако и его воспроизведение оттуда на основе событий](event-based-video-recording-tutorial.md)
