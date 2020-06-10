---
title: 'Руководство: непрерывная запись видео в облако и его воспроизведение оттуда — Azure'
description: Из этого руководства мы узнаем, как с помощью службы аналитики видеотрансляции (Live Video Analytics) на платформе IoT Edge осуществлять непрерывную запись видео в облако и транслировать произвольные фрагменты этого видео в режиме потоковой передачи с помощью Служб мультимедиа Azure.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a69d3f5db9dd8cbe25bbf79f44921f26258005cc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259991"
---
# <a name="tutorial-continuous-video-recording-to-cloud-and-playback-from-cloud"></a>Руководство. Непрерывная запись видео в облако и его воспроизведение оттуда  

Из этого руководства мы узнаем, как с помощью службы аналитики видеотрансляции (Live Video Analytics) на платформе IoT Edge осуществлять [непрерывную запись видео](continuous-video-recording-concept.md) в облако и транслировать произвольные фрагменты этого видео в режиме потоковой передачи с помощью Служб мультимедиа. Такая схема может пригодиться в системах безопасности, для обеспечения нормативного соответствия и в других ситуациях, когда необходимо хранить архив записей с видеокамеры в течение нескольких дней или недель.

> [!div class="checklist"]
> * Подготовка необходимых ресурсов
> * Проверка кода, который выполняет непрерывную запись видео
> * Запуск примера кода
> * Проверка результатов и просмотр видео

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Рекомендуемые материалы для предварительного ознакомления  

Рекомендуем вам ознакомиться со следующей документацией:

* [Аналитика видеотрансляции в IoT Edge: обзор](overview.md)
* [Аналитика видеотрансляции в IoT Edge: терминология](terminology.md)
* [Граф мультимедиа: основные понятия](media-graph-concept.md) 
* [Сценарии непрерывной записи видео](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Предварительные требования

Ниже перечислены предварительные условия для начала работы с этим руководством.

* Установите [Visual Studio Code](https://code.visualstudio.com/) на компьютере, на котором ведется разработка, с расширениями [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) и [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > Если появится запрос на установку Docker, его можно проигнорировать.
* Установите [SDK .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) на компьютере, на котором ведется разработка.
* Выполните [скрипт подготовки ресурсов аналитики видеотрансляции](https://github.com/Azure/live-video-analytics/tree/master/edge/setup).

После выполнения описанных выше действий в вашей подписке Azure будет развернут набор ресурсов Azure, включая перечисленные ниже.

* Центр Интернета вещей
* Учетная запись хранения
* Учетная запись Служб мультимедиа Azure
* Виртуальная машина Linux в Azure с установленной [средой выполнения IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

## <a name="concepts"></a>Основные понятия

Как объясняется [здесь](media-graph-concept.md), с помощью графа мультимедиа можно указать, откуда нужно записывать мультимедийные данные, как их необходимо обрабатывать и куда следует направлять результаты. Процесс непрерывной записи видео заключается в захвате видео с камеры с поддержкой протокола RTSP и его непрерывной записи в [актив Служб мультимедиа Azure](terminology.md#asset). Ниже представлено схематическое изображение графа мультимедиа.

![Граф мультимедиа](./media/continuous-video-recording-tutorial/continuous-video-recording-overview.png)

В этом руководстве для эмуляции RTSP-камеры мы будем использовать модуль Edge, созданный с помощью [мультимедийного сервера Live555](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555). В графе мультимедиа мы используем узел [источника RTSP](media-graph-concept.md#rtsp-source) для приема трансляции из канала и отправляем полученное видео на [узел приемника](media-graph-concept.md#asset-sink), который записывает его в актив.

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

Прежде чем приступать к работе, убедитесь, что выполнен пункт 3 из раздела [предварительных требований](#prerequisites). Когда скрипт подготовки ресурсов завершит свою работу, щелкните фигурные скобки, чтобы раскрыть структуру папок. В каталоге ~/clouddrive/lva-sample будет создано несколько файлов.

![Параметры приложения](./media/quickstarts/clouddrive.png)

Ниже перечислены файлы, интересующие нас в данном сценарии.

     * ~/clouddrive/lva-sample/edge-deployment/.env  - contains properties that Visual Studio Code uses to deploy modules to an edge device
     * ~/clouddrive/lva-sample/appsettings.json - used by Visual Studio Code for running the sample code

Эти файлы потребуются нам на описанных ниже этапах.

1. Выполните клонирование репозитория отсюда: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Запустите Visual Studio Code и откройте папку, в которую скачали репозиторий.
1. В Visual Studio Code перейдите в папку src/cloud-to-device-console-app и создайте файл под названием appsettings.json. Этот файл содержит параметры, необходимые для выполнения программы.
1. Скопируйте содержимое файла ~/clouddrive/lva-sample/appsettings.json. Текст должен выглядеть так:
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    Строка подключения к Центру Интернета вещей позволяет использовать Visual Studio Code для отправки команд модулям Edge через Центр Интернета вещей Azure.
    
1. Затем перейдите в папку src/edge и создайте файл под названием .env.
1. Скопируйте содержимое файла ~/clouddrive/lva-sample/.env. Текст должен выглядеть так:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Изучение примеров файлов

Откройте в Visual Studio Code файл src/edge/deployment.template.json. Этот шаблон определяет, какие модули Edge мы будем развертывать на пограничном устройстве (виртуальной машине Azure Linux). Обратите внимание, что в разделе modules есть две записи со следующими названиями:

* lvaEdge — модуль аналитики видеотрансляции в IoT Edge
* rtspsim — симулятор RTSP

Теперь перейдите в папку src/cloud-to-device-console-app. Здесь мы найдем файл appsettings.json, который создали, и еще несколько файлов:

* c2d-console-app.csproj — файл проекта Visual Studio Code.
* operations.json — файл со списком различных операций, которые будут выполняться.
* Program.cs — образец кода программы, который выполняет следующие действия:
    * загружает параметры приложения;
    * вызывает прямые методы, представляемые модулем аналитики видеотрансляции в IoT Edge (с помощью модуля можно анализировать потоки видеотрансляции, вызывая его [прямые методы](direct-methods.md));
    * приостанавливает выполнение, позволяя вам проанализировать выходные данные программы в окне терминала и сгенерированные модулем события в окне выходных данных;
    * вызывает прямые методы для очистки ресурсов.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Создание и развертывание манифеста развертывания IoT Edge 

Манифест развертывания определяет, какие именно модули развертываются на пограничном устройстве, а также содержит параметры конфигурации для этих модулей. Выполните перечисленные ниже действия, чтобы создать такой манифест на базе файла шаблона, а затем развернуть его на пограничном устройстве.

1. Запустите Visual Studio Code.
1. Задайте строку подключения к Центру Интернета вещей. Для этого щелкните значок More actions ("Дополнительные действия") рядом с областью Центра Интернета вещей Azure в левом нижнем углу. Строку подключения можно скопировать из файла src/cloud-to-device-console-app/appsettings.json. 

    ![Установка строки подключения Центра Интернета вещей](./media/quickstarts/set-iotconnection-string.png)
1. Затем щелкните правой кнопкой мыши файл src/edge/deployment.template.json и выберите Generate IoT Edge Deployment Manifest ("Создать манифест развертывания IoT Edge"). Visual Studio Code заменяет переменные в шаблоне развертывания значениями из файла .env. В результате в папке src/edge/config должен быть создан файл манифеста под названием deployment.amd64.json.

   ![Создание файла манифеста развертывания IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Щелкните правой кнопкой мыши файл src/edge/config/deployment.amd64.json и выберите Create Deployment for Single Device ("Создать развертывание для одного устройства").

   ![Создание развертывания для одного устройства](./media/quickstarts/create-deployment-single-device.png)
1. Вам будет предложено выбрать устройство Центра Интернета вещей. Выберите устройство lva-sample-device из раскрывающегося списка.
1. Примерно через 30 секунд обновите содержимое Центра Интернета вещей в левом нижнем углу. Должно появиться пограничное устройство со следующими развернутыми модулями:
    * Аналитика видеотрансляции в IoT Edge (название модуля — lvaEdge)
    * Симулятор RTSP (название модуля — rtspsim)
 
    ![Центр Интернета вещей](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Подготовка к мониторингу модулей 

Во время записи потоковой видеотрансляции с помощью модуля аналитики видеотрансляции в IoT Edge в Центр Интернета вещей отправляются события. Чтобы просмотреть их, выполните указанные ниже действия.

1. Откройте панель Explorer в Visual Studio Code и найдите Центр Интернета вещей Azure в левом нижнем углу.
1. Разверните узел Devices ("Устройства").
1. Щелкните правой кнопкой мыши устройство lva-sample-device и выберите команду Start Monitoring Built-in Event Endpoint ("Запустить мониторинг встроенной конечной точки события").

    ![Запуск мониторинга встроенной конечной точки события](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Запуск программы 

1. В Visual Studio Code перейдите к файлу src/cloud-to-device-console-app/operations.json.
1. В узле GraphTopologySet внесите следующие изменения:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. Затем убедитесь, что в узлах GraphInstanceSet и GraphTopologyDelete значение topologyName соответствует значению свойства name в топологии вышеприведенного графа:

    `"topologyName" : "CVRToAMSAsset"`  
1. Откройте [топологию](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json) в браузере и найдите параметр assetNamePattern. Чтобы название актива было уникальным, можно изменить название экземпляра графа в файле operations.json (по умолчанию он называется Sample-Graph-1).

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Запустите сеанс отладки (нажмите клавишу F5). В окне терминала начнут появляться сообщения.
1. Файл operations.json начнет выполнение операций с вызова GraphTopologyList и GraphInstanceList. Если вы очистили ресурсы после выполнения действий, описанных в предыдущих кратких руководствах или учебниках, будут возвращены пустые списки, а затем выполнение будет приостановлено, пока вы не нажмете клавишу ВВОД, как показано ниже:

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
1. После нажатия клавиши ВВОД в окне терминала будет вызвана следующая серия прямых методов.
     * Вызов GraphTopologySet с использованием приведенной выше топологии.
     * Вызов GraphInstanceSet с использованием приведенного ниже кода body.
     
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
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
     * Вызов GraphInstanceActivate для запуска экземпляра графа и видеопотока.
     * Второй вызов GraphInstanceList для демонстрации того факта, что экземпляр графа находится в состоянии выполнения.  
1. Вывод данных в окне терминала будет приостановлен с предложением нажать клавишу ВВОД для продолжения. Не нажимайте ВВОД на данном этапе. Прокрутите экран вверх, чтобы увидеть полезные данные ответов JSON на вызванные нами прямые методы.
1. Если теперь переключиться в окно выходных данных в Visual Studio Code, мы увидим сообщения, отправляемые в Центр Интернета вещей модулем аналитики видеотрансляции в IoT Edge.

     * Эти сообщения рассматриваются в следующем разделе.
1. Экземпляр графа продолжит работать и записывать видео: симулятор RTSP будет продолжать циклическое воспроизведение исходного видео. Чтобы остановить запись, вернитесь в окно терминала и нажмите клавишу ВВОД. Следующая серия вызовов производится для очистки ресурсов.

     * Вызов GraphInstanceDeactivate для деактивации экземпляра графа.
     * Вызов GraphInstanceDelete для удаления экземпляра.
     * Вызов GraphTopologyDelete для удаления топологии.
     * Окончательный вызов GraphTopologyList, чтобы продемонстрировать, что список пуст.

## <a name="interpret-the-results"></a>Интерпретация результатов 

После запуска графа мультимедиа модуль аналитики видеотрансляции в IoT Edge отправляет в концентратор IoT Edge определенные диагностические и операционные события. Эти события представляют собой сообщения, отображаемые в окне вывода данных Visual Studio Code: они содержат разделы body (тело) и applicationProperties (свойства приложения). Чтобы понять назначение этих разделов, ознакомьтесь с [этой](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) статьей.

В приведенных ниже сообщениях свойства приложений и содержимое раздела body определяются модулем аналитики видеотрансляции.

## <a name="diagnostic-events"></a>Диагностические события 

### <a name="mediasession-established-event"></a>Событие MediaSessionEstablished

После активации экземпляра графа узел источника RTSP пытается подключиться к серверу TRSP, работающему в модуле rtspsim. В случае успеха на печать выводится следующее событие:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* Сообщение представляет собой диагностическое событие MediaSessionEstablished: оно говорит о том, что узлу источника RTSP (субъекту subject) удалось установить подключение к симулятору RTSP и начать прием потока данных (эмулируемого).
* Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел в топологии графа, сгенерировавший сообщение. В данном случае сообщение исходит от узла источника RTSP.
* Параметр типа события eventType в разделе applicationProperties указывает, что событие является диагностическим.
* Параметр времени события eventTime обозначает время события.
* Раздел body содержит информацию о диагностическом событии: в данном случае это данные [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="operational-events"></a>Операционные события 

### <a name="recordingstarted-event"></a>Событие RecordingStarted

Когда узел приемника актива начинает записывать видео, он генерирует событие типа Microsoft.Media.Graph.Operational.RecordingStarted.

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел приемника актива в графе, сгенерировавший данное сообщение.

Раздел body содержит сведения о месте вывода данных: в нашем случае это название актива Службы мультимедиа Azure, в который осуществляется запись видео. Запишите это значение.

### <a name="recordingavailable-event"></a>Событие RecordingAvailable

Когда начинается запись видео, отправляется событие RecordingStarted, однако при этом видеоданные могут быть еще не загружены в актив. После того как узел приемника актива загружает видеоданные в актив, он генерирует событие типа Microsoft.Media.Graph.Operational.RecordingAvailable.

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Это событие означает, что в актив записано уже достаточно данных для того, чтобы можно было начать воспроизведение видео в проигрывателе или на клиенте.

Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел приемника актива в графе, сгенерировавший данное сообщение.

Раздел body содержит сведения о месте вывода данных: в нашем случае это название актива Службы мультимедиа Azure, в который осуществляется запись видео.

### <a name="recordingstopped-event"></a>Событие RecordingStopped

Когда экземпляр графа деактивируется, узел приемника актива останавливает запись видео в актив и генерирует событие типа Microsoft.Media.Graph.Operational.RecordingStopped.

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Это событие означает, что запись прекращена.

Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел приемника актива в графе, сгенерировавший данное сообщение.

Раздел body содержит сведения о месте вывода данных: в нашем случае это название актива Службы мультимедиа Azure, в который осуществляется запись видео.

## <a name="media-services-asset"></a>Актив Служб мультимедиа  

Чтобы проверить актив Служб мультимедиа, созданный графом мультимедиа, войдите на портал Azure и просмотрите видео.

1. В браузере откройте [портал Azure](https://portal.azure.com/). Введите свои учетные данные для входа на портал. Панель мониторинга службы является представлением по умолчанию.
1. Найдите свою учетную запись Служб мультимедиа среди ресурсов в составе вашей подписки и откройте ее колонку.
1. Щелкните Assets ("Активы") в списке Служб мультимедиа.

    ![Активы](./media/continuous-video-recording-tutorial/assets.png)
1. Вы увидите актив под названием sampleAsset-CVRToAMSAsset-Sample-Graph-1 — такая схема именования выбрана в файле топологии графа.
1. Щелкните актив.
1. На странице подробных сведений об активе щелкните **Create new** ("Создать") под текстовым полем URL-адреса потоковой передачи.

    ![Новый актив](./media/continuous-video-recording-tutorial/new-asset.png)

1. В открывшемся мастере примите параметры по умолчанию и нажмите Add ("Добавить"). Дополнительные сведения см. в статье о [воспроизведении видео](video-playback-concept.md).

    > [!TIP]
    > Убедитесь, что [конечная точка потоковой передачи запущена](../latest/streaming-endpoint-concept.md).
1. Видео должно загрузиться в проигрыватель, и должна активироваться кнопка **воспроизведения**>** для его просмотра.

> [!NOTE]
> Поскольку источник видео представлял собой контейнер, эмулирующий поток данных с камеры, метки времени в видео обозначают моменты активации и деактивации экземпляра графа. Ознакомьтесь с [этим](playback-multi-day-recordings-tutorial.md) руководством, чтобы узнать, как открыть запись за несколько дней и просмотреть отдельные фрагменты соответствующего архива. В этом руководстве также можно увидеть метки времени в видео на экране.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете поработать и с другими руководствами, созданные ресурсы следует сохранить. В противном случае зайдите на портал Azure, откройте список групп ресурсов, выберите группу, в которой работали с этим руководством, и удалите ее.

## <a name="next-steps"></a>Дальнейшие действия

* Вместо симулятора RTSP используйте [IP-камеру](https://en.wikipedia.org/wiki/IP_camera) с поддержкой RTSP. Подобрать RTSP-совместимую IP-камеру можно на странице [продуктов, соответствующих спецификации ONVIF](https://www.onvif.org/conformant-products/): ищите устройства, совместимые с профилем G, S или T.
* Используйте устройство AMD64 или X64 на базе Linux (вместо виртуальной машины Linux в Azure). Устройство должно находиться в той же сети, что и IP-камера. Вы можете сначала воспользоваться инструкциями по [установке среды выполнения Azure IoT Edge на Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux), а затем — инструкциями из краткого руководства [Развертывание первого модуля IoT Edge на виртуальном устройстве Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux), чтобы зарегистрировать устройство в Центре Интернета вещей Azure.
