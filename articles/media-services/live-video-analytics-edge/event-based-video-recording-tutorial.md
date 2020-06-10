---
title: 'Руководство: запись видео в облако и его воспроизведение оттуда на основе событий — Azure'
description: Из этого руководства мы узнаем, как с помощью службы аналитики видеотрансляции (Live Video Analytics) на платформе IoT Edge осуществлять запись видео в облако и его воспроизведение оттуда на основе событий.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 7ff8502688e2b507b8a576c177948f29c2248be4
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456652"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Руководство по Запись видео в облако и его воспроизведение оттуда на основе событий

Из этого руководства мы узнаем, как с помощью службы аналитики видеотрансляции (Live Video Analytics) на платформе IoT Edge осуществлять выборочную запись фрагментов видеотрансляции из источника в Службы мультимедиа в облаке. Такой сценарий в этом руководстве называется [записью видео на основе событий](event-based-video-recording-concept.md). Мы будем использовать модель обнаружения объектов на базе ИИ для поиска объектов в видеопотоке и запуска видеозаписи только в случае обнаружения объекта некоторого типа. Мы также узнаем, как воспроизводить записанные видеоролики с помощью Служб мультимедиа. Такой сценарий возникает в ситуациях, когда необходимо вести архив определенных видеозаписей.

> [!div class="checklist"]
> * Подготовка необходимых ресурсов
> * Проверка кода, который выполняет запись видео на основе событий
> * Запуск примера кода
> * Проверка результатов и просмотр видео

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Рекомендуемые материалы для предварительного ознакомления  

Рекомендуем вам ознакомиться со следующей документацией:

* [Аналитика видеотрансляции в IoT Edge: обзор](overview.md)
* [Аналитика видеотрансляции в IoT Edge: терминология](terminology.md)
* [Граф мультимедиа: основные понятия](media-graph-concept.md) 
* [Запись видео на основе событий](event-based-video-recording-concept.md)
* [Учебник: разработка модуля IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Редактирование файла deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Раздел, посвященный [объявлению маршрутов в манифесте развертывания IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Предварительные требования

Ниже перечислены предварительные условия для начала работы с этим руководством.

* Установите [Visual Studio Code](https://code.visualstudio.com/) на компьютере, на котором ведется разработка, с расширениями [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) и [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > Если появится запрос на установку Docker, его можно проигнорировать.
* Установите [SDK .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) на компьютере, на котором ведется разработка.
* Выполните [скрипт подготовки ресурсов аналитики видеотрансляции](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) и [процедуру подготовки среды](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment).

После выполнения описанных выше действий в вашей подписке Azure будет развернут набор ресурсов Azure, включая перечисленные ниже.

* Центр Интернета вещей
* Учетная запись хранения
* Учетная запись Служб мультимедиа Azure
* Виртуальная машина Linux в Azure с установленной [средой выполнения IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

## <a name="concepts"></a>Основные понятия

Запись видео на основе событий — это процесс записи видео, инициируемый определенным событием. Это событие может возникать как в ходе обработки самого видеосигнала (например, при обнаружении на видео движущегося объекта), так и по сигналу от независимого источника (например, при открытии двери). Кроме того, запись может активироваться при обнаружении определенного события службой логического вывода.  В этом руководстве мы используем видео автомобилей, проезжающих по трассе, и будем записывать видео каждый раз при появлении грузовика.

![Граф мультимедиа](./media/event-based-video-recording-tutorial/overview.png)

Схема выше представляет собой изображение [графа мультимедиа](media-graph-concept.md) и дополнительных модулей, которые позволяют реализовать описанный сценарий. В нашей ситуации используются четыре перечисленных ниже модуля IoT Edge.

* Модуль аналитики видеотрансляции в IoT Edge.
* Модуль Edge с моделью ИИ, расположенный за конечной точкой HTTP. В этом модуле ИИ используется модель [YOLO v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), которая позволяет распознавать объекты различных видов.
* Пользовательский модуль, выполняющий подсчет и фильтрацию объектов (на схеме выше он обозначен как счетчик объектов), который мы создадим и развернем с помощью этого руководства.
* [Модуль симулятора RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555), эмулирующий работу RTSP-камеры.
    
    Как видно на схеме, мы используем узел [источника RTSP](media-graph-concept.md#rtsp-source) в графе мультимедиа для захвата эмулируемого видеопотока (движения автомобилей по шоссе) и отправляем это видео по двум маршрутам.

* Первый маршрут — узел [обработчика-фильтра частоты кадров](media-graph-concept.md#frame-rate-filter-processor), который выдает на выход кадры видео с определенной (сниженной) частотой. Эти видеокадры поступают на узел расширения HTTP, который затем передает их (в виде изображений) на модуль ИИ (YOLO v3, детектор объектов) и получает результаты — объекты (автомобили в потоке), обнаруженные моделью. Затем узел расширения HTTP публикует результаты через узел приемника сообщений Центра Интернета вещей в концентраторе IoT Edge.
* Модуль счетчика объектов настроен для приема сообщений от концентратора IoT Edge, которые включают результаты обнаружения объектов (автомобилей в потоке). Он проверяет эти сообщения на наличие объектов определенного типа (он задается в настройках). При обнаружении такого объекта модуль отправляет сообщение в концентратор IoT Edge. Эти сообщения об обнаружении объекта затем перенаправляются в узел источника Центра Интернета вещей графа мультимедиа. Получив сообщение, узел источника Центра Интернета вещей в графе мультимедиа активирует узел [обработчика шлюза сигналов](media-graph-concept.md#signal-gate-processor), в результате чего шлюз открывается на заданный промежуток времени. В течение этого периода видео поступает через шлюз на узел приемника актива. Соответствующий фрагмент транслируемого видеопотока записывается через узел [приемника актива](media-graph-concept.md#asset-sink) в [актив](terminology.md#asset) в учетной записи Службы мультимедиа Azure.

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

Прежде чем приступать к работе, убедитесь, что выполнен пункт 3 из раздела [предварительных требований](#prerequisites). Когда скрипт подготовки ресурсов завершит свою работу, щелкните фигурные скобки, чтобы раскрыть структуру папок. В каталоге ~/clouddrive/lva-sample будет создано несколько файлов.

![Параметры приложения](./media/quickstarts/clouddrive.png)

Ниже перечислены файлы, интересующие нас в данном сценарии.

* ~/clouddrive/lva-sample/edge-deployment/.env — содержит свойства, которые Visual Studio Code использует для развертывания модулей на пограничном устройстве.
* ~/clouddrive/lva-sample/appsetting.json — используется Visual Studio Code для запуска примера кода.

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

## <a name="examine-the-template-file"></a>Изучение примеров файлов 

На предыдущем этапе мы запустили Visual Studio Code и открыли папку, содержащую примеры программного кода.

В Visual Studio Code перейдите в папку src/edge. Здесь находится созданный нами файл .env, а также несколько файлов шаблона развертывания. Этот шаблон определяет, какие модули Edge мы будем развертывать на пограничном устройстве (виртуальной машине Azure Linux). Файл .env содержит значения для переменных, которые используются в этих шаблонах, например учетные данные Службы мультимедиа.

Откройте файл src/edge/deployment.objectCounter.template.json. Обратите внимание, что в разделе модулей modules есть четыре записи — по одной для каждого из перечисленных в разделе "Основные понятия" объектов:

* lvaEdge — модуль аналитики видеотрансляции в IoT Edge
* yolov3 — модуль ИИ, который использует модель YOLO v3
* rtspsim — симулятор RTSP
* objectCounter — модуль, который идет определенные объекты в результатах, полученных от модуля yolov3

Для модуля objectCounter важную роль играет строка (${MODULES.objectCounter}), которая связана со значением изображения (см. [руководство](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux) по разработке модуля IoT Edge). Visual Studio Code автоматически находит код модуля счетчика объектов в разделе src/edge/modules/objectCounter. 

Сведения о том, как объявлять маршруты в манифесте развертывания IoT Edge, а затем просматривать их в JSON-файле шаблона, приведены в [этом](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) разделе. Обратите внимание на следующие моменты:

* LVAToObjectCounter используется для отправки определенных событий на определенную конечную точку в модуле objectCounter.
* ObjectCounterToLVA используется для отправки события-триггера на определенную конечную точку (которая должна быть узлом источника Центра Интернета вещей) в модуле lvaEdge.
* objectCounterToIoTHub используется в качестве средства отладки и помогает просматривать выходные данные модуля objectCounter при запуске кода из этого руководства.

> [!NOTE]
> Проверьте необходимые свойства модуля objectCounter, которые настроены для поиска объектов, помеченных как грузовики, с уровнем достоверности не ниже 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Создание и развертывание манифеста развертывания IoT Edge 

Манифест развертывания определяет, какие именно модули развертываются на пограничном устройстве, а также содержит параметры конфигурации для этих модулей. Выполните перечисленные ниже действия, чтобы создать такой манифест на базе файла шаблона, а затем развернуть его на пограничном устройстве.

С помощью Visual Studio Code и [этих](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution) инструкций войдите в Docker, создайте и отправьте решение IoT Edge; используйте для этого этапа файл src/edge/deployment.objectCounter.template.json.

![Создание и отправка решения IoT Edge](./media/event-based-video-recording-tutorial/build-push.png)

В результате будет создан модуль objectCounter для подсчета объектов и отправки изображения в Реестр контейнеров Azure (ACR).

* Убедитесь, что в файле .env определены переменные среды CONTAINER_REGISTRY_USERNAME_myacr и CONTAINER_REGISTRY_PASSWORD_myacr.

На описанном выше шаге создается манифест развертывания IoT Edge в файле src/edge/config/deployment.objectCounter.amd64.json. Щелкните этот файл правой кнопкой и выберите Create Deployment for Single Device ("Создать развертывание для одного устройства").

![Создание развертывания для одного устройства](./media/quickstarts/create-deployment-single-device.png)

Если мы раньше не работали с руководствами по аналитике видеотрансляций в IoT Edge, среда Visual Studio Code предложит нам ввести строку подключения к Центру Интернета вещей. Ее можно скопировать из файла appsettings.json.

Далее Visual Studio Code предложит выбрать устройство Центра Интернета вещей. Выберите свое устройство IoT Edge (это должно быть устройство lva-sample-device).

На этом этапе начинается развертывание пограничных модулей на устройстве IoT Edge.
Примерно через 30 секунд обновите содержимое Центра Интернета вещей Azure в левом нижнем углу Visual Studio Code. Должны появиться четыре развернутых модуля (еще раз обратите внимание на их имена: lvaEdge, rtspsim, yolov3 и objectCounter).

![Четыре развернутых модуля](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Подготовка к мониторингу событий

Чтобы посмотреть события, поступающие от модуля счетчика объектов и от модуля аналитики видеотрансляции в IoT Edge, выполните указанные ниже действия.

1. Откройте панель Explorer в Visual Studio Code и найдите Центр Интернета вещей Azure в левом нижнем углу.
1. Разверните узел Devices ("Устройства").
1. Щелкните правой кнопкой мыши устройство lva-sample-device и выберите команду **Start Monitoring Built-in Event Endpoint** ("Запустить мониторинг встроенной конечной точки события").

![Запуск мониторинга встроенной конечной точки события](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Запуск программы

1. В Visual Studio Code перейдите к файлу src/cloud-to-device-console-app/operations.json.

1. В узле GraphTopologySet внесите следующие изменения:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Затем внесите изменения в узлах GraphInstanceSet и GraphTopologyDelete:

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
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
            "topologyName": "EVRtoAssetsOnObjDetect",
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
     * Второй вызов GraphInstanceList для демонстрации того факта, что экземпляр графа находится в состоянии выполнения
     
1. Вывод данных в окне терминала будет приостановлен с предложением нажать клавишу ВВОД для продолжения. Не нажимайте ВВОД на данном этапе. Прокрутите экран вверх, чтобы увидеть полезные данные ответов JSON на вызванные нами прямые методы.

1. Если теперь переключиться в окно выходных данных в Visual Studio Code, мы увидим сообщения, отправляемые в Центр Интернета вещей модулем аналитики видеотрансляции в IoT Edge.

     * Эти сообщения рассматриваются в следующем разделе.
     
1. Экземпляр графа продолжит работать и записывать видео: симулятор RTSP будет продолжать циклическое воспроизведение исходного видео. Просмотрите сообщения, как объясняется в следующем разделе. Затем, чтобы остановить экземпляр, вернитесь в окно терминала и нажмите клавишу ВВОД. Следующая серия вызовов производится для очистки ресурсов.

     * Вызов GraphInstanceDeactivate для деактивации экземпляра графа
     * Вызов GraphInstanceDelete для удаления экземпляра
     * Вызов GraphTopologyDelete для удаления топологии
     * Окончательный вызов GraphTopologyList, чтобы продемонстрировать, что список пуст.

## <a name="interpret-the-results"></a>Интерпретация результатов 

После запуска графа мультимедиа модуль аналитики видеотрансляции в IoT Edge отправляет в концентратор IoT Edge определенные диагностические и операционные события. Эти события представляют собой сообщения, отображаемые в окне вывода данных Visual Studio Code: они содержат разделы body (тело) и applicationProperties (свойства приложения). Сведения о назначении этих разделов см. в статье [Создание и чтение сообщений Центра Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

В приведенных ниже сообщениях свойства приложений и содержимое раздела body определяются модулем аналитики видеотрансляции.

## <a name="diagnostic-events"></a>Диагностические события

### <a name="mediasessionestablished-event"></a>Событие MediaSessionEstablished 

После создания экземпляра графа узел источника RTSP пытается подключиться к серверу TRSP, работающему в контейнере симулятора RTSP. В случае успеха на печать выводится это событие. Обратите внимание, что тип данного события — Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Сообщение представляет собой диагностическое событие MediaSessionEstablished: оно говорит о том, что узлу источника RTSP (субъекту subject) удалось установить подключение к симулятору RTSP и начать прием потока данных (эмулируемого).

* Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел в топологии графа, сгенерировавший сообщение. В этом случае сообщение исходит от узла источника RTSP.

* Параметр типа события eventType в разделе applicationProperties указывает, что событие является диагностическим.

* Параметр времени события eventTime обозначает время события: это время, когда видеозапись дорожного трафика (MKV-файл) начала поступать в модуль в режиме потоковой передачи.

* Раздел body содержит информацию о диагностическом событии: в данном случае это данные [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


## <a name="operational-events"></a>Операционные события

Через некоторое время после запуска графа мультимедиа поступит событие от модуля счетчика объектов. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

Объект свойств приложения applicationProperties содержит время события eventTime — оно обозначает момент, когда модуль счетчика объектов обнаружил, что результаты, поступающие от модуля YOLO v3, содержат интересующие нас объекты, т. е. грузовики.

По мере обнаружения на видео новых грузовиков будут возникать новые события этого типа.

### <a name="recordingstarted-event"></a>Событие RecordingStarted

Практически сразу после отправки счетчиком объектов события мы увидим событие типа Microsoft.Media.Graph.Operational.RecordingStarted.

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел приемника актива в графе, сгенерировавший данное сообщение. Раздел body содержит сведения о месте вывода данных: в нашем случае это название актива Службы мультимедиа Azure, в который осуществляется запись видео. Запишите это значение.

### <a name="recordingavailable-event"></a>Событие RecordingAvailable

После того как узел приемника актива загружает видео в актив, он генерирует событие типа Microsoft.Media.Graph.Operational.RecordingAvailable.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Это событие означает, что в актив записано уже достаточно данных для того, чтобы можно было начать воспроизведение видео в проигрывателе или на клиенте. Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел приемника актива в графе, сгенерировавший данное сообщение. Раздел body содержит сведения о месте вывода данных: в нашем случае это название актива Службы мультимедиа Azure, в который осуществляется запись видео.

### <a name="recordingstopped-event"></a>Событие RecordingStopped

Изучив параметры активации (maximumActivationTime) узла обработчика шлюза сигналов в [топологии](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), мы увидим, что шлюз закрывается, пропустив через себя видео продолжительностью 30 секунд. Таким образом, примерно через 30 секунд после события RecordingStarted мы должны увидеть событие типа Microsoft.Media.Graph.Operational.RecordingStopped, указывающее, что узел приемника актива остановил запись видео в данный актив.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Это событие означает, что запись прекращена. Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел приемника актива в графе, сгенерировавший данное сообщение. Раздел body содержит сведения о месте вывода данных: в нашем случае это название актива Службы мультимедиа Azure, в который осуществляется запись видео.

## <a name="media-services-asset"></a>Актив Служб мультимедиа  

Чтобы проверить актив Служб мультимедиа, созданный графом, войдите на портал Azure и просмотрите видео.

1. В браузере откройте [портал Azure](https://portal.azure.com/). Введите свои учетные данные для входа на портал. Панель мониторинга службы является представлением по умолчанию.
1. Найдите свою учетную запись Служб мультимедиа среди ресурсов в составе вашей подписки и откройте ее колонку.
1. Щелкните Assets ("Активы") в списке Служб мультимедиа.

    ![Активы](./media/continuous-video-recording-tutorial/assets.png)
1. Мы увидим актив под названием sampleAssetFromEVR-LVAEdge-{дата_и_время} — это имя указано в свойстве outputLocation события RecordingStarted. Алгоритм создания этого имени определяется свойством assetNamePattern топологии.
1. Щелкните актив.
1. На странице подробных сведений об активе щелкните **Create new** ("Создать") под текстовым полем URL-адреса потоковой передачи.

    ![Новый актив](./media/continuous-video-recording-tutorial/new-asset.png)

1. В открывшемся мастере примите параметры по умолчанию и нажмите Add ("Добавить"). Дополнительные сведения см. в статье о [воспроизведении видео](video-playback-concept.md).

    > [!TIP]
    > Убедитесь, что [конечная точка потоковой передачи запущена](../latest/streaming-endpoint-concept.md).
1. Видео должно загрузиться в проигрыватель, и должна активироваться кнопка **воспроизведения** для его просмотра.

> [!NOTE]
> Поскольку источник видео представлял собой контейнер, эмулирующий поток данных с камеры, метки времени в видео обозначают моменты активации и деактивации экземпляра графа. Если вы используете элементы управления воспроизведением, созданные с помощью руководства по [воспроизведению записей за несколько дней](playback-multi-day-recordings-tutorial.md), в видео на экране будут отображаться метки времени.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете поработать и с другими руководствами, созданные ресурсы следует сохранить. В противном случае зайдите на портал Azure, откройте список групп ресурсов, выберите группу, в которой работали с этим руководством, и удалите ее.

## <a name="next-steps"></a>Дальнейшие действия

* Вместо симулятора RTSP используйте [IP-камеру](https://en.wikipedia.org/wiki/IP_camera) с поддержкой RTSP. Подобрать RTSP-совместимую IP-камеру можно на странице [продуктов, соответствующих спецификации ONVIF](https://www.onvif.org/conformant-products/): ищите устройства, совместимые с профилем G, S или T.
* Используйте устройство AMD64 или X64 на базе Linux (вместо виртуальной машины Linux в Azure). Устройство должно находиться в той же сети, что и IP-камера. Вы можете сначала воспользоваться инструкциями по [установке среды выполнения Azure IoT Edge на Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux), а затем — инструкциями из краткого руководства [Развертывание первого модуля IoT Edge на виртуальном устройстве Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux), чтобы зарегистрировать устройство в Центре Интернета вещей Azure.
