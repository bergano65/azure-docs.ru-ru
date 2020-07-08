---
title: Анализ видеотрансляции с помощью собственной модели — Azure
description: В этом кратком руководстве вы примените компьютерное зрение для анализа потока видеотрансляции из IP-камеры (имитированной).
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: e19c5bd6b44f9a4dd225f6a849ee9aaf62fa6273
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027670"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-model"></a>Краткое руководство. Анализ видеотрансляции с помощью собственной модели

В этом кратком руководстве показано, как использовать Аналитику видеотрансляций в службе IoT Edge для анализа потока видеотрансляции из (имитированной) IP-камеры. Вы узнаете, как применить модель компьютерного зрения для обнаружения объектов. Подмножество кадров в потоке видео в реальном времени отправляется в службу вывода. Результаты отправляются в центр IoT Edge. 

В этом кратком руководстве используется имитация видеотрансляции, а в качестве устройства IoT Edge используется виртуальная машина Azure. Оно основывается на образце кода, написанном на C# для краткого руководства [Создание событий при обнаружении движения](detect-motion-emit-events-quickstart.md). 

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. Если у вас еще нет учетной записи, [ создайте ее бесплатно](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) со следующими расширениями:
    * [Средства Интернета вещей Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Пакет SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Если вы не прошли краткое руководство [Создание событий при обнаружении движения](detect-motion-emit-events-quickstart.md), обязательно [настройте ресурсы Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> Во время установки расширения Azure IoT Tools может появиться запрос на установку Docker. На него можно не обращать внимания.

## <a name="review-the-sample-video"></a>Просмотр примера видео
При настройке ресурсов Azure на виртуальную машину Linux в Azure, используемую в качестве устройства IoT Edge, копируется короткое видео дорожного потока автомобилей. В этом кратком руководстве для имитации потока в реальном времени используется видеофайл.

Откройте приложение, например [проигрыватель мультимедиа VLC](https://www.videolan.org/vlc/). Нажмите сочетание клавиш CTRL+N, а затем вставьте ссылку на [видео](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv), чтобы начать воспроизведение. Вы увидите видеозапись множества транспортных средств, движущихся в потоке.

В этом кратком руководстве вы будете использовать Аналитику видеотрансляций в IoT Edge для обнаружения таких объектов, как транспортные средства и лица. Вы будете публиковать связанные события вывода в центре IoT Edge.

## <a name="overview"></a>Обзор

![Обзор](./media/quickstarts/overview-qs5.png)

На схеме показан порядок передачи сигналов в этом кратком руководстве. [Пограничный модуль](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) имитирует IP-камеру, на которой находится RTSP-сервер. Узел [источника RTSP](media-graph-concept.md#rtsp-source) извлекает видеопоток с этого сервера и отправляет видеокадры на узел [обработчика фильтра частоты кадров](media-graph-concept.md#frame-rate-filter-processor). Этот обработчик ограничивает частоту кадров видеопотока при достижении узла [обработчика расширения HTTP](media-graph-concept.md#http-extension-processor). 

Узел расширения HTTP играет роль прокси-сервера. Он преобразует видеокадры в изображения указанного типа. Затем с использованием REST он передает изображение на другой пограничный модуль, выполняющий модель искусственного интеллекта на конечной точке HTTP. В этом примере этот пограничный модуль строится с помощью модели [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), которая может обнаруживать объекты множества типов. Узел обработчика расширений HTTP собирает результаты обнаружения и публикует события в узле [приемника Центра Интернета вещей](media-graph-concept.md#iot-hub-message-sink). Затем узел отправляет эти события в [центр IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

В этом кратком руководстве вы выполните указанные ниже задачи.

1. Создание и развертывание графа мультимедиа.
1. Интерпретация результатов.
1. Очистка ресурсов.



## <a name="create-and-deploy-the-media-graph"></a>Создание и развертывание графа мультимедиа
    
### <a name="examine-and-edit-the-sample-files"></a>Изучение и изменение образцов файлов

Согласно предварительным требованиям вы загрузили в папку пример кода. Выполните указания ниже, чтобы изучить и изменить файлы примеров.

1. В Visual Studio Code перейдите в папку *src/edge*. Здесь находится созданный нами *ENV*-файл и несколько файлов шаблона развертывания.

    Шаблон развертывания содержит манифест развертывания для пограничного устройства. Он содержит некоторые значения-заполнители. В *ENV*-файле указаны значения таких переменных.

1. Теперь перейдите в папку *src/cloud-to-device-console-app*. Здесь вы увидите файл *appSettings.json* и несколько других файлов:

    * ***c2d-console-app.csproj*** — файл проекта Visual Studio Code.
    * ***operations.json*** — список операций, которые должна запускать программа.
    * ***Program.cs*** — пример кода программы. Этот код выполняет следующие действия:

        * загружает параметры приложения;
        * вызывает прямые методы, которые представляются модулем Аналитики видеотрансляций в IoT Edge (с помощью модуля можно анализировать потоки видеотрансляции, вызывая его [прямые методы](direct-methods.md));
        * приостанавливает выполнение, позволяя проанализировать выходные данные программы в окне **ТЕРМИНАЛ** и проверить сгенерированные модулем события в окне **ВЫХОДНЫЕ ДАННЫЕ**;
        * вызывает прямые методы для очистки ресурсов.


1. Внесите правки в файл *operations.json*:
    * Измените ссылку на топологию графа:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`

    * В разделе `GraphInstanceSet` измените имя топологии графа, чтобы оно совпадало со значением в приведенной выше ссылке:

      `"topologyName" : "InferencingWithHttpExtension"`

    * В разделе `GraphTopologyDelete` измените имя:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Создание и развертывание манифеста развертывания IoT Edge

1. Щелкните правой кнопкой мыши файл *src/edge/deployment.yolov3.template.json* и выберите **Generate IoT Edge Deployment Manifest** (Создать манифест развертывания IoT Edge).

    ![Создание файла манифеста развертывания IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    Файл манифеста *deployment.yolov3.amd64.json* создается в папке *src/edge/config*.

1. Если вы прошли краткое руководство [Создание событий при обнаружении движения](detect-motion-emit-events-quickstart.md), пропустите этот шаг. 

    В противном случае рядом с областью **Центр Интернета вещей Azure** в левом нижнем углу щелкните значок **Дополнительные действия**, а затем выберите **Установка строки подключения Центра Интернета вещей**. Строку можно скопировать из файла *appsettings.json*. Чтобы обеспечить правильность настройки центра Интернета вещей в Visual Studio Code, используйте команду [Выбрать центр Интернета вещей](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Установка строки подключения Центра Интернета вещей](./media/quickstarts/set-iotconnection-string.png)

1. Щелкните правой кнопкой мыши файл *src/edge/config/deployment.yolov3.amd64.json* и выберите **Create Deployment for Single Device** (Создать развертывание для одного устройства). 

    ![Создание развертывания для одного устройства](./media/quickstarts/create-deployment-single-device.png)

1. Когда появится запрос на выбор устройства Центра Интернета вещей, выберите **lva-sample-device**.
1. Примерно через 30 секунд обновите содержимое Центра Интернета вещей в левом нижнем углу окна. Теперь в пограничном устройстве должны отображаться следующие развернутые модули:

    * Модуль Аналитики видеотрансляций **lvaEdge**.
    * Модуль **rtspsim**, имитирующий RTSP-сервер, выступающий в качестве источника веб-канала видеотрансляции.
    * Модуль **yolov3**, являющийся моделью обнаружения объектов YOLOv3, которая применяет компьютерное зрение к изображениям и возвращает несколько классов типов объектов.
 
      ![Модули, развернутые на пограничном устройстве.](./media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Подготовка к мониторингу событий

Щелкните правой кнопкой мыши устройство Аналитики видеотрансляции и выберите **Запуск мониторинга встроенной конечной точки события**. Этот шаг необходим для мониторинга событий Центра Интернета вещей в окне **ВЫХОДНЫЕ ДАННЫЕ** в Visual Studio Code. 

![Запуск мониторинга](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Запуск примера программы

1. Чтобы начать сеанс отладки, нажмите клавишу F5. В окне **ТЕРМИНАЛ** отображаются выводимые сообщения.
1. Код *operations.json* начинается с вызовов прямых методов `GraphTopologyList` и `GraphInstanceList`. Если вы очистили ресурсы после работы с предыдущими краткими руководствами, тогда этот процесс возвратит пустые списки, а затем приостановится. Чтобы продолжить, нажмите клавишу ВВОД.

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

    В окне **ТЕРМИНАЛ** показывается следующий набор вызовов прямых методов:

     * Вызов `GraphTopologySet`, который использует использованный ранее `topologyUrl`.
     * Вызов `GraphInstanceSet`, который использует такой код:

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

     * Вызов `GraphInstanceActivate`, который активирует экземпляр графа и поток видео.
     * Второй вызов к `GraphInstanceList`, который показывает, что экземпляр графа находится в состоянии выполнения
1. Вывод данных в окне **ТЕРМИНАЛ** приостановится с появлением предложения `Press Enter to continue`. Не нажимайте клавишу ВВОД на этом этапе. Прокрутите экран вверх, чтобы увидеть полезные данные ответов JSON на вызванные нами прямые методы.
1. Перейдите в окно **ВЫХОДНЫЕ ДАННЫЕ** в Visual Studio Code. Вы увидите сообщения, которые модуль Аналитики видеотрансляций в IoT Edge передает в центр Интернета вещей. Эти сообщения рассматриваются в следующем разделе этого краткого руководства.
1. Граф мультимедиа продолжит работать и выводить результаты. Симулятор RTSP будет продолжать циклический перебор исходного видео. Чтобы остановить граф мультимедиа, вернитесь к окну **ТЕРМИНАЛ** и нажмите клавишу ВВОД. 

    Следующая серия вызовов очищает ресурсы:
      * Вызов `GraphInstanceDeactivate` деактивирует экземпляр графа.
      * Вызов `GraphInstanceDelete` удаляет этот экземпляр.
      * Вызов `GraphTopologyDelete` удаляет топологию.
      * Окончательный вызов `GraphTopologyList` показывает, что список теперь пуст.

## <a name="interpret-results"></a>Интерпретация результатов

При запуске графа мультимедиа результаты из узла обработчика расширения HTTP отправляются через узел-преемник Центра Интернета вещей в центр Интернета вещей. Сообщения, отображаемые в окне **ВЫХОДНЫЕ ДАННЫЕ**, содержат раздел `body` и раздел `applicationProperties`. Дополнительные сведения см. в статье [Создание и чтение сообщений Центра Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

В приведенных ниже сообщениях модуль Аналитики видеотрансляций определяет свойства приложения и содержимое раздела body. 

### <a name="mediasessionestablished-event"></a>Событие MediaSessionEstablished

После создания экземпляра графа мультимедиа узел источника RTSP пытается подключиться к RTSP-серверу, работающему в контейнере rtspsim-live555. Если соединение установлено, будет выводиться указанное ниже событие. Тип события — `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`.

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

В этом сообщении обратите внимание на следующие сведения:

* Сообщение является событием диагностики. `MediaSessionEstablished` указывает, что исходный узел RTSP (параметр subject) установил подключение к симулятору RTSP и начал прием потока данных (имитируемого).
* В `applicationProperties` `subject` указывает, что сообщение было создано из узла RTSP-источника в графе мультимедиа.
* В `applicationProperties` `eventType` указывает, что это событие является событием диагностики.
* `eventTime` указывает время события.
* `body` содержит данные о событии диагностики. В этом случае данные содержат сведения [протокола SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Событие вывода

Узел обработчика расширений HTTP получает результаты вывода из модуля yolov3. Затем он выдает результаты через узел приемника Центра Интернета вещей в качестве событий вывода. 

В этих событиях тип имеет значение `entity`, чтобы указать, что это сущность, например легковой автомобиль или грузовик. Значение `eventTime` — время в формате UTC, когда был обнаружен объект. 

В следующем примере два автомобиля были обнаружены в одном видеокадре с различными уровнями достоверности.

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

В сообщениях обратите внимание на следующие сведения:

* В `applicationProperties` `subject` — это узел в топологии графа, из которого было создано сообщение. 
* В `applicationProperties` `eventType` указывает, что это событие является событием аналитики.
* Значение `eventTime` — это время возникновения события.
* Раздел `body` содержит информацию об аналитическом событии. В этом случае событие является событием вывода, поэтому текст содержит данные `inferences`.
* Раздел `inferences` указывает, что `type` — это `entity`. Этот раздел содержит дополнительные данные о сущности.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете работать с другими краткими руководствами, сохраните созданные ресурсы. В противном случае на портале Azure перейдите к группам ресурсов, выберите группу ресурсов, с которой вы работали в этом кратком руководстве, и удалите все ресурсы.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с дополнительными задачами для опытных пользователей.

* Вместо симулятора RTSP используйте [IP-камеру](https://en.wikipedia.org/wiki/IP_camera) с поддержкой RTSP. IP-камеры, поддерживающие протокол RTSP, можно найти на странице [продуктов, соответствующих ONVIF](https://www.onvif.org/conformant-products/). Ищите устройства, которые соответствуют профилям G, S или T.
* Используйте устройство AMD64 или x64 на базе Linux вместо виртуальной машины Linux в Azure. Устройство должно находиться в той же сети, что и IP-камера. Вы можете выполнить инструкции из статьи [Install Azure IoT Edge runtime on Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) (Установка среды выполнения Azure IoT Edge в системах Linux на основе Debian). Чтобы зарегистрировать устройство в Центре Интернета вещей Azure, следуйте инструкциям краткого руководства [Развертывание первого модуля IoT Edge на виртуальном устройстве Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).

