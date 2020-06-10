---
title: Начало работы с Аналитикой видеотрансляции в IoT Edge — Azure
description: В этом кратком руководстве объясняется, как приступить к работе с Аналитикой видеотрансляций в IoT Edge и обнаруживать движение в потоке видеотрансляции.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261569"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Краткое руководство. Начало работы. Общие сведения об Аналитике видеотрансляции в IoT Edge

В этом руководстве содержатся пошаговые инструкции по началу работы с Аналитикой видеотрансляции в IoT Edge. В нем используется имитация видеотрансляции и виртуальная машина Azure в качестве устройства IoT Edge. После завершения процедуры настройки вы сможете запустить имитацию видеотрансляции с помощью графа мультимедиа, который обнаруживает движения в этой трансляции и сообщает о них. Ниже представлено схематическое изображение графа мультимедиа.

![Аналитика видеотрансляции на основе обнаружения движения](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
* Установите [Visual Studio Code](https://code.visualstudio.com/) на компьютере, на котором ведется разработка, с [расширением Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Сеть, к которой подключен ваш компьютер, должна разрешать протокол AMQP через порт 5671 (чтобы расширение Azure IoT Tools могло взаимодействовать с Центром Интернета вещей Azure).

> [!TIP]
> Во время установки расширения Azure IoT Tools может появится запрос на установку Docker. Вы можете проигнорировать его.

## <a name="set-up-azure-resources"></a>Настройка ресурсов Azure

Для выполнения инструкций из этого учебника потребуются перечисленные ниже ресурсы Azure.

* Центр Интернета вещей
* Учетная запись хранения
* Учетная запись Служб мультимедиа Azure
* Виртуальная машина Linux в Azure с установленной [средой выполнения IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

В целях этого краткого руководства мы рекомендуем использовать [сценарий подготовки ресурсов Аналитики видеотрансляции](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) для развертывания ресурсов Azure, указанных выше в подписке Azure. Для этого выполните описанные ниже действия.

1. Перейдите по адресу https://shell.azure.com.
1. Если вы впервые используете Cloud Shell, вам будет предложено выбрать подписку для создания учетной записи хранения и общей папки в службе "Файлы Microsoft Azure". Выберите "Создать новое хранилище", чтобы создать учетную запись для хранения сведений о сеансах Cloud Shell. Эта учетная запись хранения отличается от той, которая будет создана сценарием для использования с учетной записью Служб мультимедиа Azure.
1. Выберите "Bash" в качестве среды в раскрывающемся списке в левой части окна Shell.

    ![Выбор среды](./media/quickstarts/env-selector.png)

1. Выполните следующую команду.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
Если сценарий успешно завершает работу, в подписке должны отобразиться все указанные выше ресурсы. Среди выходных данных сценария будет таблица ресурсов, содержащая имя Центра Интернета вещей. Найдите тип ресурса **Microsoft.Devices/IotHubs** и запишите его имя. Оно потребуется для выполнения следующего шага. Сценарий также создаст несколько файлов конфигурации в каталоге ~/clouddrive/lva-sample/ directory. Они понадобятся вам позже в кратком руководстве.

## <a name="deploy-modules-on-your-edge-device"></a>Развертывание модулей на пограничном устройстве

Выполните следующую команду в Cloud Shell.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Указанная выше команда развернет следующие модули на пограничном устройстве (виртуальная машина Linux):

* Аналитика видеотрансляции в IoT Edge (название модуля — lvaEdge)
* Симулятор RTSP (название модуля — rtspsim)

Модуль симулятора RTSP имитирует видеотрансляцию с применением сохраненного видеофайла, который был скопирован на пограничное устройство при запуске [сценария подготовки ресурсов Аналитики видеотрансляции](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). На этом этапе модули уже развернуты, но нет активных графов мультимедиа.

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Настройка расширения Azure IoT Tools в Visual Studio Code

Запустите Visual Studio Code и следуйте приведенным ниже инструкциям, чтобы подключиться к Центру Интернета вещей Azure с помощью расширения Azure IoT Tools.

1. Перейдите на вкладку Explorer в Visual Studio Code, выбрав **Вид** > **Explorer**, или просто нажмите клавиши CTRL+Shift+E.
1. На вкладке Explorer выберите "Центр Интернета вещей Azure" в левом нижнем углу.
1. Щелкните значок "Дополнительные параметры", чтобы открыть контекстное меню, и выберите параметр "Установка строки подключения Центра Интернета вещей".
1. Появится поле ввода. После этого введите строку подключения Центра Интернета вещей. Вы можете получить строку подключения для Центра Интернета вещей из ~/clouddrive/lva-sample/appsettings.json в Cloud Shell.
1. Если подключение будет установлено, отобразится список пограничных устройств. Среди них должно быть по крайней мере одно устройство с именем lva-sample-device.
1. Теперь вы можете управлять устройствами IoT Edge и взаимодействовать с Центром Интернета вещей Azure с помощью контекстного меню.
1. Чтобы просмотреть модули, развернутые на пограничном устройстве, разверните узел модулей в разделе lva-sample-device.

    ![Узел lva-sample-device](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>Использование прямых методов

С помощью модуля можно анализировать потоки видеотрансляции, вызывая его прямые методы. Ознакомитесь со статьей [Прямые методы для Аналитики видеотрансляции в IoT Edge](direct-methods.md), чтобы узнать о всех прямых методах, обеспечиваемых модулем. 

### <a name="invoke-graphtopologylist"></a>Вызов GraphTopologyList
Перечисляются все [топологии графа](media-graph-concept.md#media-graph-topologies-and-instances) в модуле.

1. Щелкните модуль lvaEdge правой кнопкой мыши и выберите "Вызов прямого метода модуля" из контекстного меню.
1. Вы увидите всплывающее поле ввода в верхней средней части окна Visual Studio Code. Введите GraphTopologyList в поле ввода и нажмите клавишу ВВОД.
1. Затем скопируйте и вставьте приведенные ниже полезные данные JSON в поле редактирования и нажмите клавишу ВВОД.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    Через несколько секунд появится окно вывода во всплывающем окне Visual Studio Code со следующим ответом:

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    Должен появится указанный выше ответ, так как топологии графов не были созданы.
    

### <a name="invoke-graphtopologyset"></a>Вызов GraphTopologySet

С помощью шагов, описанных для вызова GraphTopologyList, можно вызвать GraphTopologySet, чтобы задать [топологию графа](media-graph-concept.md#media-graph-topologies-and-instances), используя в качестве полезных данных следующий формат JSON.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```


Указанные выше полезные данные JSON приводят к созданию топологии графа, которая определяет три параметра (для двух из которых установлены значения по умолчанию). Топология содержит один узел источника (RTSP Source), один узел обработчика (обработчик обнаружения движения) и один узел-приемник (приемник Центра Интернета вещей).

Через несколько секунд появится окно вывода со следующим ответом:

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Возвращенное состояние — 201, что означает создание новой топологии. В качестве следующего шага выполните такое действие:

* Снова вызовите GraphTopologySet и обратите внимание, что возвращен код состояния 200. Код состояния 200 указывает, что существующая топология успешно обновлена.
* Снова вызовите GraphTopologySet, но измените строку описания. Обратите внимание, что код состояния в ответе — 200, а описание обновлено до нового значения.
* Вызовите GraphTopologyList, как описано в предыдущем разделе, и обратите внимание, что теперь в возвращенных полезных данных отображается топология графа MotionDetection.

### <a name="invoke-graphtopologyget"></a>Вызов GraphTopologyGet

Теперь вызовите GraphTopologyGet со следующими полезными данными

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Через несколько секунд в окне вывода появится следующий ответ:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Обратите внимание на указанные ниже полезные данные ответа.

* Код состояния — 200, что означает успешное выполнение.
* Полезные данные имеют метки времени "created" и "lastModified".

### <a name="invoke-graphinstanceset"></a>Вызов GraphInstanceSet

Затем создайте экземпляр графа, который ссылается на указанную выше топологию графа. Как объясняется [здесь](media-graph-concept.md#media-graph-topologies-and-instances), экземпляры графа позволяют анализировать видеопотоки с нескольких камер с одинаковой топологией графа.

Вызовите прямой метод GraphInstanceSet с указанными ниже полезными данными.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Следует отметить следующее.

* В приведенных выше полезных данных указывается имя топологии (MotionDetection), для которой необходимо создать экземпляр.
* Полезные данные содержат значение для параметра rtspUrl, для которого отсутствует значение по умолчанию в полезных данных топологии графа.

Через несколько секунд в окне вывода отобразится следующий ответ:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Обратите внимание на указанные ниже полезные данные ответа.

* Код состояния — 201, т. е. был создан новый экземпляр.
* Состояние — Inactive (Неактивный), т. е экземпляр графа был создан, но не активирован. Дополнительные сведения см. в разделе с описанием [состояний графа мультимедиа](media-graph-concept.md).

В качестве следующего шага выполните такое действие:

* Снова вызовите GraphInstanceSet с теми же полезными данными и обратите внимание, что возвращенный код состояния теперь 200.
* Вызовите GraphInstanceSet еще раз, но с другим описанием, и обратите внимание на обновленное описание в полезных данных ответа, указывающее на то, что экземпляр графа успешно обновлен.
* Вызовите GraphInstanceSet, но измените имя на Sample-Graph-2 и просмотрите полезные данные ответа. Обратите внимание, что создан новый экземпляр графа (т. е. код состояния — 201).

### <a name="invoke-graphinstanceactivate"></a>Вызов GraphInstanceActivate

Теперь активируйте экземпляр графа, который запускает поток видеотрансляции через модуль. Вызовите прямой метод GraphInstanceActivate со следующими полезными данными.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Через несколько секунд в окне вывода появится следующий ответ:

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Код состояния 200 в полезных данных ответа указывает на то, что экземпляр графа успешно активирован.

### <a name="invoke-graphinstanceget"></a>Вызов GraphInstanceGet

Теперь вызовите прямой метод GraphInstanceGet с указанными ниже полезными данными.

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

Через несколько секунд в окне вывода появится следующий ответ:

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Обратите внимание на указанные ниже полезные данные ответа.

* Код состояния — 200, что означает успешное выполнение.
* Состояние — Active (активный), т. е. экземпляр графа теперь находится в активированном состоянии.

## <a name="observe-results"></a>Просмотр результатов

Экземпляр графа, созданный и активированный как описано выше, использует узел обработчика обнаружения движения для обнаружения движения во входящем потоке видеотрансляции и отправляет события в узел-приемник Центра Интернета вещей. Затем эти события передаются в центр IoT Edge, и их можно просмотреть. Для этого выполните следующие действия.

1. Откройте область обозревателя в Visual Studio Code и найдите Центр Интернета вещей Azure в левом нижнем углу.
2. Разверните узел Devices (Устройства).
3. Щелкните правой кнопкой мыши устройство lva-sample-device и выберите команду "Запуск мониторинга встроенной конечной точки события".

![Запуск мониторинга событий в Центре Интернета вещей](./media/quickstarts/start-monitoring-iothub-events.png)

В окне вывода отобразятся следующие сообщения:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Обратите внимание на следующее в приведенном выше сообщении

* Сообщение содержит раздел текста body и раздел свойств приложения applicationProperties. Сведения о назначении этих разделов см. в статье [Создание и чтение сообщений Центра Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).
* Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел в графе мультимедиа, сгенерировавший сообщение. В данном случае сообщение исходит от обработчика обнаружения движения.
* Параметр типа события eventType в разделе applicationProperties указывает, что событие является аналитическим.
* Параметр времени события eventTime указывает время события.
* Раздел body содержит информацию об аналитическом событии. В данном случае событие является событием вывода, поэтому раздел body содержит данные параметров timestamp и inferences.
* В разделе inferences указано, что у параметра type значение motion, он также содержит дополнительные данные о событии motion.

Если позволить графу мультимедиа MediaGraph выполняться достаточно долго, в окне вывода появится также следующее сообщение:

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Обратите внимание на следующее в приведенном выше сообщении

* Параметр субъекта subject в разделе свойств приложения applicationProperties указывает на то, что сообщение было создано из исходного узла в графе мультимедиа.
* Параметр типа события eventType в разделе applicationProperties указывает, что событие является диагностическим.
* Раздел текста body содержит данные о диагностическом событии. В этом случае событием является MediaSessionEstablished и, следовательно, body.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Вызов дополнительных прямых методов для очистки

Теперь вызовем прямые методы для деактивации и удаления экземпляра графа (в указанном порядке).

### <a name="invoke-graphinstancedeactivate"></a>Вызов GraphInstanceDeactivate

Вызовите прямой метод GraphInstanceDeactivate со следующими полезными данными.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Через несколько секунд в окне вывода появится следующий ответ:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Код состояния 200 указывает на то, что экземпляр графа успешно деактивирован.

В качестве следующего шага выполните такое действие:

* Вызовите GraphInstanceGet, как указано в предыдущих разделах, и обратите внимание на значение параметра состояния state.

### <a name="invoke-graphinstancedelete"></a>Вызов GraphInstanceDelete

Вызовите прямой метод GraphInstanceDelete со следующими полезными данными:

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Через несколько секунд в окне вывода появится следующий ответ:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Код состояния 200 в ответе указывает на то, что экземпляр графа был успешно удален.

### <a name="invoke-graphtopologydelete"></a>Вызов GraphTopologyDelete

Вызовите прямой метод GraphTopologyDelete со следующими полезными данными:

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Через несколько секунд в окне вывода появится следующий ответ:

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Код состояния 200 означает, что топология графа успешно удалена.

В качестве следующего шага выполните такое действие:

* Вызовите GraphTopologyList и убедитесь, что в модуле отсутствуют топологии графов.
* Вызовите GraphInstanceList с полезными данными, указанными в GraphTopologyList, и убедитесь, что экземпляры графа не перечислены.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не собираетесь использовать это приложение в дальнейшем, удалите ресурсы, созданные в данном кратком руководстве.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как записывать видео с помощью Аналитики видеотрансляции в IoT Edge
* Узнайте больше о диагностических сообщениях.
