---
title: Устранение неполадок в Live Video Analytics на IoT Edge в Azure
description: В этой статье рассматриваются действия по устранению неполадок в службе Live Video Analytics на IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 05/24/2020
ms.openlocfilehash: dd55050521a1791a11f220cd5617d9df2fa2d160
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045588"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Устранение неполадок в Live Video Analytics на IoT Edge

В этой статье рассматриваются действия по устранению неполадок в службе Live Video Analytics на IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Устранение неполадок развертывания

### <a name="diagnostics"></a>Диагностика

В рамках развертывания Live Video Analytics вы настроите ресурсы Azure, такие как центр Интернета вещей и устройство IoT Edge. В качестве первого шага диагностики проблем всегда гарантируется, что ребро правильно настроено, выполнив следующие инструкции:

1. [Выполните команду "Check"](https://docs.microsoft.com/azure/iot-edge/troubleshoot#run-the-check-command)
1. [Проверка версии IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-iot-edge-version)
1. [Проверка состояния диспетчера безопасности IoT Edge и его журналов](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-the-status-of-the-iot-edge-security-manager-and-its-logs)
1. [Просмотр сообщений, идущих через центр IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot#view-the-messages-going-through-the-iot-edge-hub)
1. [Перезапуск контейнеров](https://docs.microsoft.com/azure/iot-edge/troubleshoot#restart-containers)
1. [Проверьте правила конфигурации брандмауэра и порта.](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-your-firewall-and-port-configuration-rules)

### <a name="pre-deployment-issues"></a>Проблемы перед развертыванием

Если инфраструктура пограничной инфраструктуры подойдет, можно найти проблемы с файлом манифеста развертывания. Чтобы развернуть Live Video Analytics в модуле IoT Edge на пограничном устройстве вместе с любыми другими модулями Интернета вещей, вы будете использовать манифест развертывания, содержащий концентратор пограничных устройств, агент пограничных агентов и другие модули со своими свойствами. Если формат JSON сформирован неправильно, можно получить следующее сообщение об ошибке: 

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```

Не удалось выполнить синтаксический анализ JSON из файла: " <deployment manifest.json> " для аргумента "Content" с исключением: "дополнительные данные: строка 101, столбец 1 (char 5325)"

При возникновении этой ошибки рекомендуется проверить JSON-файл на наличие недостающих квадратных скобок или других проблем со структурой файла. Для проверки структуры файлов можно использовать клиент, например [Notepad + + с подключаемым модулем средства просмотра JSON](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) или онлайн-инструмент, например https://jsonformatter.curiousconcept.com/ .

### <a name="deployment--diagnose-with-media-graph-direct-methods"></a>Развертывание — диагностика с помощью прямых методов Media Graph 

После того, как на пограничном устройстве правильно развернута интерактивная аналитика видео в модуле IoT Edge, можно создать и запустить граф мультимедиа, вызвав [прямые методы](direct-methods.md). С помощью портала можно запустить диагностику графа мультимедиа с помощью прямых методов:

1. С помощью портала перейдите в центр Интернета вещей, подключенный к устройству с пограничным устройством.
    1. В колонке центра Интернета вещей найдите автоматическое управление устройствами — >IoT Edge.
    1. Если щелкнуть IoT Edge, отобразится список пограничных устройств. Выберите устройство, которое нужно диагностировать.
         
        ![Пограничные устройства](./media/troubleshoot-how-to/lva-sample-device.png)
    1. Проверьте, имеет ли код ответа значение 200-ОК. Существуют и другие коды ответов для [среды выполнения IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime) , например:
        1. 400 — конфигурация развертывания имеет неправильный формат или недопустима.
        1. 417-у устройства нет набора конфигураций развертывания.
        1. 412 — версия схемы в конфигурации развертывания недопустима.
        1. 406 — устройство IoT Edge работает в автономном режиме или не отправляет отчеты о состоянии.
        1. 500 — в среде выполнения IoT Edge произошла ошибка.
    1. Если щелкнуть устройство, также отобразится список ожидаемых IoT Edgeных модулей, которые были развернуты, и их состояние.
    1. Если в столбцах "указано в развертывании" и "сообщается устройство" указано "Да", то можно вызвать прямые методы в реальном видео Analytics для IoT Edge модуля. Щелкните модуль, чтобы перейти к экрану, где можно проверить требуемые и сообщаемые свойства, а также вызвать прямые методы. 
        1. Проверка сообщаемых и требуемых свойств помогает понять, синхронизированы ли свойства модуля с развертыванием. Если это не так, можно перезапустить ребро 
        1. Воспользуйтесь руководством по [прямым методам](direct-methods.md) , чтобы вызвать несколько методов, особенно простых, например графтопологилист. Кроме того, в этом руководством указываются ожидаемые полезные данные запроса и ответа, а также коды ошибок. После успешного выполнения простых прямых методов можно гарантировать, что модуль Live Video Analytics пойдет в нормальном режиме.
        
        ![Прямой метод](./media/troubleshoot-how-to/direct-method.png) 
1. Если вы получаете код состояния 501, убедитесь, что имя прямого метода является точным. Если имя метода и полезные данные запроса являются точными, необходимо получить результаты вместе с кодом успешного выполнения = 200. Если полезные данные запроса неточны, вы получите состояние = 400 и полезные данные ответа, которые указывают код ошибки и сообщение, которое должно помочь при диагностике проблемы с помощью вызова прямого метода. 

### <a name="post-deployment--diagnose-logs-for-issues-during-run"></a>После развертывания — Диагностика журналов на наличие проблем во время выполнения 

Журналы контейнеров для нашего пограничной модуля должны иметь диагностику<!--<todo:add link to diagnostics doc>--> сведения, которые должны помочь в отладке проблем во время выполнения модуля. Вы можете [проверить журналы контейнеров на наличие проблем](https://docs.microsoft.com/azure/iot-edge/troubleshoot#check-container-logs-for-issues) и самостоятельно выполнить диагностику, но если все указанные выше проверки выполнены и проблемы по-прежнему возникают, соберите журналы с IOT Edge устройства [с помощью команды "support](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) ", которую можно проанализировать далее командой Azure. Вы можете [обратиться](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) к нам за поддержкой и отправить собранные журналы.

## <a name="common-error-resolutions"></a>Распространенные способы устранения ошибок

Функция Live Video Analytics развертывается как модуль IoT Edge на пограничном устройстве и работает совместно с агентом IoT Edge и модулями концентратора. Некоторые распространенные ошибки, которые вы будете использовать при развертывании службы "Анализ видео в реальном времени", будут вызваны проблемами с базовой инфраструктурой IoT. Ниже перечислены некоторые распространенные ошибки, которые могут быть IoT Edge агента и концентратора.

1. [Агент IOT Edge останавливается через минуту](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-stops-after-about-a-minute).
1. [Агент IOT EDGE не может получить доступ к образу модуля (403)](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-agent-cant-access-a-modules-image-403).
1. [Модуль агента пограничной модели сообщает о пустом файле конфигурации и не запускает модули на устройстве](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
1. [Не удается запустить центр IOT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-hub-fails-to-start).
1. [IOT Edge демон безопасности завершает работу с недопустимым именем узла](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-security-daemon-fails-with-an-invalid-hostname).
1. [Интерактивная аналитика видео или любой другой настраиваемый модуль IOT EDGE не может отправить сообщение в концентратор ребра с ошибкой 404](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
1. [После этого развертывание модуля IOT Edge успешно исчезает с устройства](https://docs.microsoft.com/azure/iot-edge/troubleshoot-common-errors#iot-edge-module-deploys-successfully-then-disappears-from-device).

### <a name="edge-set-up-script-issues"></a>Проблемы со сценариями настройки ребра

В рамках нашей документации мы предоставили [сценарий настройки](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) для развертывания пограничных и облачных ресурсов, чтобы приступить к работе с ребром Live Video Analytics. В этом разделе мы получили сообщения об ошибках, которые могут возникнуть при работе с сценарием и способах их отладки.

Сценарий запускает частичное создание нескольких ресурсов, но завершается со следующим сообщением:

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
1. Убедитесь, что установлены следующие расширения. На момент написания этого руководства версия расширений выглядит следующим образом:

    | Расширение | Версия |
    |---|---|
    |azure-cli   |      2.5.1|
    |Command-modules-нспкг         |   2.0.3|
    |core  |    2.5.1|
    |нспкг    | 3.0.4|
    |telemetry| 1.0.4|
    |Расширения.    ||
    |storage-preview          |     0.2.10|
    |azure-cli-iot-ext          |    0.8.9|
    |eventgrid| 0.4.9|
    |azure-iot                       | 0.9.2|
1. Если любое из расширений старше указанных номеров выпусков, обновите расширение до последней версии с помощью команды:

    ```
    az extension update --name <Extension name>
    ```

    Например: `az extension update --name azure-iot`

### <a name="sample-app-issues"></a>Примеры проблем приложения

В рамках нашего выпуска мы предоставили пример кода для .NET, который позволит вам загрузить сообщество разработчиков. В этом разделе мы получили сообщения об ошибках, которые могут возникнуть при выполнении примера кода и отладке таких ошибок.

1. Program.cs завершается со следующей ошибкой при вызове прямого метода:

    ```
    Unhandled exception. Microsoft.Azure.Devices.Common.Exceptions.UnauthorizedException: {"Message":"{\"errorCode\":401002,\"trackingId\":\"b1da85801b2e4faf951a2291a2c467c3-G:32-TimeStamp:04/06/2020 17:15:11\",\"message\":\"Unauthorized\",\"timestampUtc\":\"2020-04-06T17:15:11.6990676Z\"}","ExceptionMessage":""}
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpClient httpClient, HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`2 isMappedToException, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
    
        at Microsoft.Azure.Devices.HttpClientHelper.ExecuteAsync(HttpMethod httpMethod, Uri requestUri, Func`3 modifyRequestMessageAsync, Func`3 processResponseMessageAsync, IDictionary`2 errorMappingOverrides, CancellationToken cancellationToken)
        
        at Microsoft.Azure.Devices.HttpClientHelper.PostAsync[T,T2](Uri requestUri, T entity, TimeSpan operationTimeout, IDictionary`2 errorMappingOverrides, IDictionary`2 customHeaders, CancellationToken cancellationToken)…
    ```

    1. Убедитесь, что [средства Azure IOT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) установлены в среде VS Code и подключены к программе установки центра Интернета вещей. (Ctrl + Shift + P, а затем выберите пункт выбрать метод центра Интернета вещей для подключения к подписке и центру Интернета вещей)
1. Проверьте, можно ли вызвать прямой метод в модуле ребра с помощью VS Code (например, вызовите Графтоплогилист со следующими полезными данными {" @apiVersion ": "1,0"}), и вы должны вернуть следующий ответ. 

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

    ![Visual Studio Code](./media/troubleshoot-how-to/visual-studio-code1.png)
1. Если описанный выше сбой, попробуйте выполнить следующие действия.
    1. Перейдите в командную строку на пограничном устройстве и введите.
    
    ```
    sudo systemctl restart iotedge
    ```

    Это приведет к перезапуску пограничной устройства и всех модулей. Подождите несколько минут и выполните следующую команду, чтобы убедиться, что модули запущены, прежде чем пытаться снова использовать Директмесод.

    ```
    sudo iotedge list
    ```
    1. Если это не так, попробуйте перезагрузить ВИРТУАЛЬную машину или компьютер.
    1. Если все не удается, выполните следующую команду, чтобы получить ZIP-файл со всеми [соответствующими журналами](https://docs.microsoft.com/azure/iot-edge/troubleshoot#gather-debug-information-with-support-bundle-command) , подсоединяемыми в этот запрос в [службу поддержки](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ```
    sudo iotedge support-bundle --since 2h
    ```
1. Если вы получаете сообщение об ошибке 400, убедитесь, что полезные данные вызова метода правильно сформированы в соответствии с руководством [прямого метода](direct-methods.md) .
1. Если вы получаете код состояния 200, это означает, что концентратор работает правильно и развертывание модуля выполняется правильно и отвечает. Следующим шагом является проверка правильности конфигураций приложения. Конфигурация приложения состоит из следующих полей в appsettings.jsв файле. Проверьте правильность значений deviceId и moduleId. Простой способ проверить это можно с помощью раздела расширения центра Интернета вещей Azure в VSCode. Значения в appsettings.jsв файле и в центре Интернета вещей должны совпадать.
    
    ```
    {
        "IoThubConnectionString" : 
        "deviceId" : 
        "moduleId" : 
    }
    ```

    ![ЦЕНТР ИНТЕРНЕТА ВЕЩЕЙ](./media/troubleshoot-how-to/iot-hub.png)

1. Наконец, убедитесь, что в appsettings.jsпредоставлена строка подключения центра Интернета вещей, а не строка подключения устройства центра Интернета вещей, так как их [форматы](https://devblogs.microsoft.com/iotdev/understand-different-connection-strings-in-azure-iot-hub/) различаются.

### <a name="live-video-analytics-working-with-external-modules"></a>Интерактивная аналитика видео работает с внешними модулями

Интерактивная аналитика видео с помощью обработчика расширений HTTP может расширить граф мультимедиа для отправки и получения данных из других модулей IoT Edge по протоколу HTTP с помощью функции RESTFUL.  В качестве [конкретного примера](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension) граф мультимедиа может отправлять видеоматериалы в виде изображений во внешний модуль вывода, например Йоло v3, и получать результаты анализа на основе JSON. В такой топологии конечным местом назначения для событий является в основном центр Интернета вещей. В ситуациях, когда вы не видите события вывода в концентраторе, проверьте следующее:

1. Убедитесь, что концентратор, на котором граф мультимедиа публикуется в VS. то, что вы проверите, совпадает. Иногда при создании нескольких развертываний вы получите несколько концентраторов и можете по ошибке проверить неправильный центр для событий.
1. Если внешний модуль развернут и работает, проверьте Via VSCode. На рисунке примера ртспсим и ОПС являются IoT Edge модулями, работающими извне модуля в Лваедже.

    ![ЦЕНТР ИНТЕРНЕТА ВЕЩЕЙ](./media/troubleshoot-how-to/iot-hub.png)
1. Проверьте, отправляются ли события в правильную конечную точку URL-адреса. Внешний контейнер AI предоставляет URL-адрес и порт, который он получает, и возвращает данные из запросов POST. Этот URL-адрес указан как свойство Endpoint: URL для обработчика расширений HTTP. Как видно в [URL-адресе топологии](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json) , для параметра URL-адреса используется значение. Убедитесь, что значение по умолчанию для параметра ( http://yolov3/score) или переданное значение является точным, и вы можете проверить, работает ли оно с помощью функции «перелистывание».  
    1. Например, контейнер Йоло v3, выполняющийся на локальном компьютере, и IP-адрес для контейнера — 172.17.0.3 (используйте команду DOCKER для поиска IP-адреса).

    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Возвращенный результат:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```

1. При использовании одного или нескольких экземпляров графа, использующего обработчик расширений HTTP, необходимо иметь фильтр частоты кадров перед каждым обработчиком HTTP-расширений управлять кадрами в секунду (кадр/с) видеоканала. В некоторых ситуациях, когда ЦП и память на пограничном компьютере интенсивно используются, можно потерять определенные события вывода. Чтобы устранить эту необходимость, установите низкое значение для свойства Максимумфпс в фильтре частоты кадров. Вы можете задать для него значение 0,5 ("Максимумфпс": 0,5) на каждом экземпляре графа и повторно запустить, чтобы проверить наличие событий вывода в концентраторе.
    1. Кроме того, вы можете получить более мощный компьютер с более мощными ПРОЦЕССОРами и памятью.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Несколько прямых методов в Parallel — сбой времени ожидания 

Интерактивная аналитика видео на IoT Edge предоставляет прямую модель программирования на основе методов, которая позволяет настроить несколько топологий и несколько экземпляров графов. В рамках настройки топологии и графа вы будете вызывать несколько прямых вызовов методов в модуле ребра. При вызове этих нескольких вызовов методов, особенно при запуске и остановке графов, в параллельном режиме могут возникнуть ошибки времени ожидания, как показано ниже. 

Метод инициализации сборки Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.IniТиализеассемблясинк вызвал исключение. Microsoft. Azure. Devices. Common. Exceptions. Иосубексцептион: Microsoft. Azure. Devices. Common. Exceptions. Иосубексцептион:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

Не рекомендуется вызывать прямые методы параллельно, но делать это последовательным способом, т. е.  один прямой вызов метода только после завершения предыдущего.

### <a name="collecting-logs-for-submitting-a-support-ticket"></a>Сбор журналов для отправки запроса в службу поддержки

Если шаги самостоятельного устранения неполадок не устраняют проблемы, следует перейти на портал Azure и отправить запрос [в службу поддержки](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Выполните следующие действия, чтобы собрать соответствующие журналы, которые должны быть добавлены в билет. Вы сможете отправить файлы журналов на вкладке **сведения** запроса в службу поддержки.

### <a name="support-bundle"></a>Пакет поддержки

Если необходимо собрать журналы с IoT Edge устройства, проще всего использовать `support-bundle` команду. Эта команда собирает:

- Журналы модулей
- IoT Edge журналов диспетчера безопасности и модуля контейнера
- Iotedge проверка выходных данных JSON
- Полезные отладочные сведения

#### <a name="use-the-iot-edge-security-manager"></a>Использование диспетчера безопасности IoT Edge
 
Диспетчер IoT Edge безопасности отвечает за такие операции, как инициализация IoT Edgeной системы при запуске и подготовке устройств. Если IoT Edge не запускается, журналы диспетчера безопасности могут предоставить полезную информацию. Чтобы просмотреть более подробные журналы IoT Edge диспетчера безопасности:

1. Измените параметры управляющей программы IoT Edge на устройстве IoT погранично:

    ```
    sudo systemctl edit iotedge.service
    ```

1. Обновите следующие строки:

    ```
    [Service]
    Environment=IOTEDGE_LOG=edgelet=debug
    ```

1. Перезапустите управляющую программу IoT Edge безопасности, выполнив следующие команды:

    ```
    sudo systemctl cat iotedge.service
    sudo systemctl daemon-reload
    sudo systemctl restart iotedge
    ```

1. Выполните `support-bundle` команду с флагом--с, чтобы указать, сколько времени в прошлом требуется получить журналы. Например, 2H получит журналы с момента последних двух часов. Можно изменить значение этого флага, чтобы включить журналы в другой период.

    ```
    sudo iotedge support-bundle --since 2h
    ```

### <a name="lva-debug-logs"></a>Журналы отладки лва

Выполните следующие действия, чтобы настроить модуль лва on IoT Edge для создания журналов отладки:

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к своему Центру Интернета вещей.
1. В меню выберите **IoT Edge**.
1. Щелкните идентификатор целевого устройства в списке устройств.
1. Щелкните ссылку **Set modules (задать модули** ) в верхнем меню.

  ![Задание модулей для портала Azure](media/troubleshoot-how-to/set-modules.png)

5. В разделе модули IoT Edge найдите и щелкните **лваедже**.
1. Щелкните **Параметры создания контейнера**.
1. В разделе привязки добавьте следующую команду:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    Это привязывает папки журналов между граничным устройством и контейнером.

1. Нажмите кнопку " **Обновить** ".
1. Нажмите кнопку " **Просмотр и создание** " в нижней части страницы. Будет выполнена простая проверка и сообщение об успешной проверке находится под зеленым баннером.
1. Нажмите кнопку **Создать**.
1. Затем обновите **удостоверение модуля двойника** , чтобы указать параметр дебуглогсдиректори, указывающий на каталог, в котором будут собираться журналы:
    1. В таблице **modules (модули** ) выберите **лваедже** .
    1. Щелкните ссылку **модуля удостоверение двойника** . Это можно найти в верхней части страницы. Откроется Редактируемая панель.
    1. Добавьте следующую пару "ключ-значение" в **нужный ключ**:

        `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    1. Щелкните **Save**(Сохранить).

1. Воспроизведите проблему.
1. Подключитесь к виртуальной машине на странице центра Интернета вещей на портале.
1. Перейдите в `/var/local/mediaservices/logs` папку и заzip содержимое этой папки и поделитесь с нами. (Эти файлы журналов не предназначены для самостоятельной диагностики. Они предназначены для анализа проблем в Azure.)

1. Сбор журналов можно остановить, присвоив этому параметру в **удостоверении модуля двойника** значение *null* . Вернитесь на страницу **удостоверения модуля двойника** и измените следующий параметр на:

    `"DebugLogsDirectory": ""`

## <a name="next-steps"></a>Дальнейшие шаги

[Руководство по Запись видео в облако и его воспроизведение оттуда на основе событий](event-based-video-recording-tutorial.md)
