---
title: Анализ видеотрансляции с помощью собственной модели — Azure
description: В этом кратком руководстве вы примените компьютерное зрение для анализа потока видеотрансляции из IP-камеры (имитированной).
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261492"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>Краткое руководство. Анализ видеотрансляции с помощью собственной модели

В этом кратком руководстве показано, как использовать Аналитику видеотрансляции в службе IoT Edge для анализа потока видеотрансляции из (имитированной) IP-камеры с применением модели компьютерного зрения для обнаружения объектов. Подмножество кадров в видеопотоке отправляется в службу вывода, а результаты отправляются в центр IoT Edge. В нем используется имитация видеотрансляции и виртуальная машина Azure в качестве устройства IoT Edge. В основе этой статьи лежит пример кода, написанного на языке C#.

Перед изучением этой статьи ознакомитесь с [этим](detect-motion-emit-events-quickstart.md) кратким руководством. 

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
* [Visual Studio Code](https://code.visualstudio.com/) на вашем компьютере со следующими расширениями:
    * [Средства Интернета вещей Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Пакет [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1), установленный в системе
* Если вы ранее не выполняли инструкции из [этого](detect-motion-emit-events-quickstart.md) краткого руководства, выполните указанные ниже шаги.
     * [Настройка ресурсов Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> Во время установки расширения Azure IoT Tools может появится запрос на установку Docker. Вы можете проигнорировать его.

## <a name="review-the-sample-video"></a>Просмотр примера видео
В ходе выполнения описанных выше действий по настройке ресурсов Azure (короткое) видео с изображением дорожного движения на шоссе будет скопировано на виртуальную машину Linux в Azure, используемую в качестве устройства IoT Edge. Этот видеофайл будет использован для имитации прямой трансляции во время работы с этим руководством.

Можно использовать приложение, например [проигрыватель VLC](https://www.videolan.org/vlc/): запустите его, нажмите клавиши Control+N и вставьте [эту](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) ссылку на видео, чтобы начать воспроизведение. Вы увидите, что в этом материале показано дорожное движение на шоссе, по которому едет много транспортных средств.

При выполнении описанных ниже действия вы будете использовать Аналитику видеотрансляции в службе IoT Edge для обнаружения таких объектов, как транспортные средства, люди и т. д., а также для публикации связанных событий вывода в центр IoT Edge.

## <a name="overview"></a>Обзор

![Обзор](./media/quickstarts/overview-qs5.png)

На схеме выше показан порядок передачи сигналов в этом кратком руководстве. Модуль Edge (подробные сведения [здесь](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) имитирует IP-камеру, на которой размещен сервер RTSP. Узел [источника RTSP](media-graph-concept.md#rtsp-source) извлекает видеопоток с этого сервера и отправляет видеокадры на узел [обработчика фильтра частоты кадров](media-graph-concept.md#frame-rate-filter-processor). Этот обработчик ограничивает частоту кадров видеопотока при достижении узла [обработчика расширения HTTP](media-graph-concept.md#http-extension-processor). 

Узел расширения HTTP играет роль прокси-сервера, преобразуя видеокадры в указанный тип изображения и ретранслируя полученное изображение с помощью REST на другой модуль Edge, на котором запущена модель искусственного интеллекта для конечной точки HTTP. В данном примере этот модуль Edge построен на основе модели [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), которая может обнаруживать множество типов объектов. Узел обработчика расширений HTTP собирает результаты обнаружения и публикует события в [узле-приемнике Центра Интернета вещей](media-graph-concept.md#iot-hub-message-sink ), который затем отправляет эти события в [центр IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

В этом кратком руководстве вы выполните указанные ниже задачи.

1. Создание и развертывание графа мультимедиа.
1. Интерпретация результатов.
1. Очистка ресурсов.



## <a name="create-and-deploy-the-media-graph"></a>Создание и развертывание графа мультимедиа
    
### <a name="examine-and-edit-the-sample-files"></a>Изучение и изменение образцов файлов

В ходе выполнения предварительных требований вы загрузили пример кода в папку. Запустите Visual Studio Code и откройте эту папку.

1. В Visual Studio Code перейдите в папку src/edge. Здесь находится созданный вами файл .env, а также несколько файлов шаблона развертывания.

    * Шаблон развертывания — это манифест развертывания для пограничного устройства, где используются некоторые значения заполнителя. В файле .env указаны значения таких переменных.
1. Теперь перейдите в папку src/cloud-to-device-console-app. Здесь мы найдем файл appsettings.json, который вы создали, и еще несколько файлов:

    * c2d-console-app.csproj — файл проекта Visual Studio Code;
    * operations.json — файл со списком различных операций, которые необходимо выполнить программе;
    * Program.cs — образец кода программы, который выполняет следующие действия:

        * загружает параметры приложения;
        *  вызывает прямые методы, представляемые модулем Аналитики видеотрансляции в IoT Edge (с помощью модуля можно анализировать потоки видеотрансляции, вызывая его [прямые методы](direct-methods.md)); 
        * приостанавливает выполнение, позволяя вам проанализировать выходные данные программы в окне терминала и сгенерированные модулем события в окне выходных данных;
        * вызывает прямые методы для очистки ресурсов.   


1. Внесите следующие изменения в файл operations.json
    * Измените ссылку на топологию графа: `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * В разделе GraphInstanceSet измените имя топологии графа, чтобы оно совпадало со значением в приведенной выше ссылке `"topologyName" : "InferencingWithHttpExtension"`
    * В разделе GraphTopologyDelete измените имя `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Создание и развертывание манифеста развертывания IoT Edge

1. Щелкните правой кнопкой мыши файл src/edge/deployment.yolov3.template.json и выберите Generate IoT Edge Deployment Manifest (Создать манифест развертывания IoT Edge).

    ![Создание файла манифеста развертывания IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. В результате в папке src/edge/config должен быть создан файл манифеста под названием deployment.yolov3.amd64.json.
1. Если вы уже прошли [краткое руководство](detect-motion-emit-events-quickstart.md), пропустите этот шаг. В противном случае задайте строку подключения к Центру Интернета вещей. Для этого щелкните значок "Дополнительные действия" рядом с областью Центра Интернета вещей Azure в левом нижнем углу. Строку можно скопировать из файла appsettings.json. (Есть еще один рекомендуемый подход, позволяющий обеспечивать правильность настройки Центра Интернета вещей в Visual Studio Code. Он предполагает использование [команды выбора Центра Интернета вещей](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).
    
    ![Строка подключения Центра Интернета вещей](./media/quickstarts/set-iotconnection-string.png)
1. Затем щелкните правой кнопкой мыши файл src/edge/config/deployment.yolov3.amd64.json и выберите Create Deployment for Single Device (Создать развертывание для одного устройства). 

    ![Создание развертывания для одного устройства](./media/quickstarts/create-deployment-single-device.png)
1. Вам будет предложено выбрать устройство Центра Интернета вещей. Выберите устройство lva-sample-device из раскрывающегося списка.
1. Примерно через 30 секунд обновите содержимое Центра Интернета вещей в левом нижнем углу. Должно появиться пограничное устройство со следующими развернутыми модулями:

    1. Модуль Аналитики видеотрансляции с именем lvaEdge.
    1. Модуль с именем rtspsim, имитирующий сервер RTSP и выступающий в качестве источника сигнала видеопотока.
    1. Модуль yolov3, как следует из его имени, — это модель обнаружения объектов YOLOv3, которая применяет компьютерное зрение к изображениям и возвращает несколько классов типов объектов.
 
        ![Модель обнаружения объектов YOLOv3](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>Подготовка к мониторингу событий

Щелкните правой кнопкой мыши устройство Аналитики видеотрансляции и выберите "Запуск мониторинга встроенной конечной точки события". Этот шаг необходим для отслеживания событий Центра Интернета вещей и их просмотра в окне вывода Visual Studio Code. 

![Запуск мониторинга](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Запуск примера программы

1. Запустите сеанс отладки (нажмите клавишу F5). В окне терминала начнут появляться сообщения.
1. Файл operations.json начнет выполнение операций с вызова прямых методов GraphTopologyList и GraphInstanceList. Если вы очистили ресурсы после выполнения действий, описанных в предыдущих кратких руководствах, будут возвращены пустые списки, а затем выполнение будет приостановлено, пока вы не нажмете клавишу ВВОД.
   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

1. После нажатия клавиши ВВОД в окне терминала будет вызвана следующая серия прямых методов
     * Вызов GraphTopologySet с использованием приведенной выше топологии topologyUrl
     * Вызов GraphInstanceSet с использованием приведенного ниже кода body
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
         "description": "Sample graph description",
         "parameters": [
           {
             "name": "rtspUrl",
             "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
           },
           {
             "name": "rtspUserName",
             "value": "testuser"
           },
           {
             "name": "rtspPassword",
             "value": "testpassword"
           }
         ]
       }
     }
     ```
     * Вызов GraphInstanceActivate для запуска экземпляра графа и видеопотока
     * Второй вызов GraphInstanceList для демонстрации того факта, что экземпляр графа находится в состоянии выполнения
1. Вывод данных в окне терминала будет приостановлен с предложением нажать клавишу ВВОД для продолжения. Не нажимайте ВВОД на этом этапе. Прокрутите экран вверх, чтобы увидеть полезные данные ответов JSON на вызванные нами прямые методы.
1. Если теперь переключиться в окно выходных данных в Visual Studio Code, мы увидим сообщения, отправляемые в Центр Интернета вещей модулем Аналитики видеотрансляции в IoT Edge.
     * Эти сообщения рассматриваются в следующем разделе.
1. Граф мультимедиа продолжит работать и выводить результаты: симулятор RTSP будет продолжать циклическое воспроизведение исходного видео. Чтобы остановить граф мультимедиа, вернитесь в окно терминала и нажмите клавишу ВВОД. Следующая серия вызовов производится для очистки ресурсов
     * Вызов GraphInstanceDeactivate для деактивации экземпляра графа
     * Вызов GraphInstanceDelete для удаления экземпляра
     * Вызов GraphTopologyDelete для удаления топологии
     * Окончательный вызов GraphTopologyList, чтобы продемонстрировать, что список пуст

## <a name="interpret-results"></a>Интерпретация результатов

При запуске графа мультимедиа результаты из узла обработчика расширения Http отправляются через узел-преемник Центра Интернета вещей в Центр Интернета вещей. Эти сообщения, отображаемые в окне вывода данных Visual Studio Code, содержат разделы body (текст) и applicationProperties (свойства приложения). Чтобы понять назначение этих разделов, ознакомьтесь с [этой](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) статьей.

В приведенных ниже сообщениях свойства приложений и содержимое раздела body определяются модулем Аналитики видеотрансляции. 



### <a name="mediasession-established-event"></a>Событие MediaSession Established

После создания экземпляра графа мультимедиа узел источника RTSP пытается подключиться к серверу TRSP, работающему в контейнере rtspsim-live55. В случае успеха на печать выводится это событие. Тип данного события — Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

Обратите внимание на следующее в приведенном выше сообщении:
* Сообщение представляет собой диагностическое событие MediaSessionEstablished: оно говорит о том, что узлу источника RTSP (параметру subject) удалось установить подключение к симулятору RTSP и начать прием потока данных (эмулируемого).
* Параметр субъекта subject в разделе свойств приложения applicationProperties указывает на то, что сообщение было создано из исходного узла в графе мультимедиа.
* Параметр типа события eventType в разделе applicationProperties указывает, что событие является диагностическим.
* Параметр времени события eventTime указывает время события.
* Раздел body содержит информацию о диагностическом событии: в этом случае это данные [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Событие вывода

Узел обработчика расширений HTTP получает результаты вывода из модуля yolov3 и отправляет их через узел-приемник Центра Интернета вещей как события вывода. В этих событиях тип имеет значение entity, указывая на сущность, например автомобиль или грузовик, а eventTime сообщает, в какое время (UTC) был обнаружен объект. Ниже приведен пример, в котором обнаружены два автомобиля с различными уровнями достоверности в одном видеокадре.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

Обратите внимание на следующее в приведенных выше сообщениях:

* Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел в топологии графа, сгенерировавший сообщение. 
* Параметр типа события eventType в разделе applicationProperties указывает, что событие является аналитическим.
* Параметр времени события eventTime указывает время события.
* Раздел body содержит информацию об аналитическом событии. В данном случае событие является событием вывода, поэтому раздел body содержит данные параметра inferences.
* В разделе inferences указано, что у параметра type значение entity, он также содержит дополнительные данные о событии entity.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете поработать и с другими краткими руководствами, созданные ресурсы следует сохранить. В противном случае зайдите на портал Azure, откройте список групп ресурсов, выберите группу, в которой работали с этим кратким руководством, и удалите все ресурсы.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с дополнительными задачами для опытных пользователей.

* Вместо симулятора RTSP используйте [IP-камеру](https://en.wikipedia.org/wiki/IP_camera) с поддержкой RTSP. Подобрать RTSP-совместимую IP-камеру можно на странице [продуктов, соответствующих спецификации ONVIF](https://www.onvif.org/conformant-products/): ищите устройства, совместимые с профилем G, S или T.
* Используйте устройство AMD64 или X64 на базе Linux (вместо виртуальной машины Linux в Azure). Устройство должно находиться в той же сети, что и IP-камера. Вы можете сначала воспользоваться инструкциями по [установке среды выполнения Azure IoT Edge на Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux), а затем — инструкциями из краткого руководства [Развертывание первого модуля IoT Edge на виртуальном устройстве Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux), чтобы зарегистрировать устройство в Центре Интернета вещей Azure.

