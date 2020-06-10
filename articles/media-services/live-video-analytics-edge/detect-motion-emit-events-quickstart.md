---
title: Создание событий при обнаружении движения — Azure
description: В этом кратком руководстве показано, как использовать Аналитику видеотрансляции в IoT Edge для создания событий при обнаружении движения путем программного вызова прямых методов.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 4986ea13bec5382a8e0ef791e75442e4333e4356
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261591"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Краткое руководство. Создание событий при обнаружении движения

В этом руководстве изложены пошаговые инструкции по началу работы с Аналитикой видеотрансляции в IoT Edge. В нем используется имитация видеотрансляции и виртуальная машина Azure в качестве устройства IoT Edge. После завершения процедуры настройки вы сможете запустить имитацию видеотрансляции с помощью графа мультимедиа, который обнаруживает движения в этой трансляции и сообщает о них. Ниже представлено схематическое изображение графа мультимедиа.

![Аналитика видеотрансляции на основе обнаружения движения](./media/analyze-live-video/motion-detection.png) 

В основе этой статьи лежит [пример кода](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp), написанного на языке C#.

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. [Создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) бесплатно.
* [Visual Studio Code](https://code.visualstudio.com/) со следующими расширениями:
    1. [Средства Интернета вещей Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    2. [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* Пакет [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1), установленный в системе

> [!TIP]
> Во время установки расширения Azure IoT Tools может появится запрос на установку Docker. Вы можете проигнорировать его.

## <a name="set-up-azure-resources"></a>Настройка ресурсов Azure

Для выполнения инструкций из этого руководства потребуются перечисленные ниже ресурсы Azure.

* Центр Интернета вещей
* Учетная запись хранения
* Учетная запись Служб мультимедиа Azure
* Виртуальная машина Linux в Azure с установленной [средой выполнения IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

В целях этого краткого руководства мы рекомендуем использовать [сценарий подготовки ресурсов аналитики видеотрансляции](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) для развертывания ресурсов Azure, указанных выше в подписке Azure. Для этого выполните описанные ниже действия.

1. Перейдите по адресу https://shell.azure.com.
1. Если вы впервые используете Cloud Shell, вам будет предложено выбрать подписку для создания учетной записи хранения и общей папки для службы "Файлы Microsoft Azure". Выберите "Создать новое хранилище", чтобы создать учетную запись для хранения сведений о сеансах Cloud Shell. Эта учетная запись хранения отличается от той, которая будет создана сценарием для использования с учетной записью Служб мультимедиа Azure.
1. Выберите "Bash" в качестве среды в раскрывающемся списке в левой части окна Shell.

    ![Выбор среды](./media/quickstarts/env-selector.png)

1. Выполните следующую команду.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```

    Если сценарий успешно завершает работу, в подписке должны отобразиться все указанные выше ресурсы.

1. Когда сценарий завершит свою работу, щелкните фигурные скобки, чтобы раскрыть структуру папок. В каталоге ~/clouddrive/lva-sample будет создано несколько файлов. В этом кратком руководстве нас интересуют указанные ниже файлы.

     * ~/clouddrive/lva-sample/edge-deployment/.env — содержит свойства, которые Visual Studio Code использует для развертывания модулей на пограничном устройстве.
     * ~/clouddrive/lva-sample/appsetting.json — используется Visual Studio Code для запуска примера кода.
     
Позднее в этом кратком руководстве необходимо будет обновить эти файлы в Visual Studio Code. Пока мы рекомендуем сохранить локальные копии этих файлов.


 ![Параметры приложения](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

1. Выполните клонирование репозитория отсюда: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Запустите Visual Studio Code и откройте папку, в которую скачали репозиторий.
1. В Visual Studio Code перейдите в папку src/cloud-to-device-console-app и создайте файл под названием appsettings.json. Этот файл содержит параметры, необходимые для выполнения программы.
1. Скопируйте содержимое файла ~/clouddrive/lva-sample/appsettings.json, созданного в предыдущем разделе (см. шаг 5).

    Текст должен выглядеть так:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Затем перейдите в папку src/edge и создайте файл под названием .env.
1. Скопируйте содержимое файла /clouddrive/lva-sample/edge-deployment/.env. Текст должен выглядеть так:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Изучение примеров файлов

1. В Visual Studio Code перейдите в папку src/edge. Здесь находится созданный нами файл .env, а также несколько файлов шаблона развертывания.

    Шаблон развертывания — это манифест развертывания для пограничного устройства, где используются некоторые значения заполнителей. В файле .env указаны значения таких переменных.
1. Теперь перейдите в папку src/cloud-to-device-console-app. Здесь мы найдем файл appsettings.json, который создали, и еще несколько файлов:

    * c2d-console-app.csproj — файл проекта Visual Studio Code.
    * operations.json — файл со списком различных операций, которые необходимо выполнить программе.
    * Program.cs — образец кода программы, который выполняет следующие действия:
    
        * загружает параметры приложения;
        * вызывает прямые методы, представляемые модулем аналитики видеотрансляции в IoT Edge. (с помощью модуля можно анализировать потоки видеотрансляции, вызывая его [прямые методы](direct-methods.md)); 
        * приостанавливает выполнение, позволяя вам проанализировать выходные данные программы в окне терминала и сгенерированные модулем события в окне выходных данных;
        * вызывает прямые методы для очистки ресурсов.   

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Создание и развертывание манифеста развертывания IoT Edge

Манифест развертывания определяет, какие именно модули развертываются на пограничном устройстве, а также содержит параметры конфигурации для этих модулей. Выполните перечисленные ниже действия, чтобы создать такой манифест на базе файла шаблона, а затем развернуть его на пограничном устройстве.

1. Откройте Visual Studio Code.
1. Задайте строку подключения к Центру Интернета вещей. Для этого щелкните значок "Дополнительные действия" рядом с областью Центра Интернета вещей Azure в левом нижнем углу. Строку подключения можно скопировать из файла src/cloud-to-device-console-app/appsettings.json. 

    ![Установка строки подключения Центра Интернета вещей](./media/quickstarts/set-iotconnection-string.png)
1. Затем щелкните правой кнопкой мыши файл src/edge/deployment.template.json и выберите Generate IoT Edge Deployment Manifest (Создать манифест развертывания IoT Edge).
    ![Создание файла манифеста развертывания IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    В результате в папке src/edge/config должен быть создан файл манифеста под названием deployment.amd64.json.
1. Щелкните правой кнопкой мыши файл src/edge/config/deployment.amd64.json и выберите Create Deployment for Single Device (Создать развертывание для одного устройства), затем выберите имя пограничного устройства.

    ![Создание развертывания для одного устройства](./media/quickstarts/create-deployment-single-device.png)
1. Вам будет предложено выбрать устройство Центра Интернета вещей. Выберите устройство lva-sample-device из раскрывающегося списка.
1. Примерно через 30 секунд обновите содержимое Центра Интернета вещей в левом нижнем углу. Должно появиться пограничное устройство со следующими развернутыми модулями:

    * Аналитика видеотрансляции в IoT Edge (название модуля — lvaEdge)
    * Симулятор RTSP (название модуля — rtspsim)

Модуль симулятора RTSP имитирует видеотрансляцию с применением сохраненного видеофайла, который был скопирован на пограничное устройство при запуске [сценарий подготовки ресурсов аналитики видеотрансляции](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). На этом этапе модули уже развернуты, но нет активных графов мультимедиа.

## <a name="prepare-for-monitoring-events"></a>Подготовка к мониторингу событий

Для обнаружения движения во входящем потоке видеотрансляции и создания событий в Центре Интернета вещей будет использоваться модуль аналитики видеотрансляции в IoT Edge. Чтобы просмотреть эти события, выполните указанные ниже действия.

1. Откройте панель Explorer в Visual Studio Code и найдите Центр Интернета вещей Azure в левом нижнем углу.
1. Разверните узел Devices (Устройства).
1. Щелкните правой кнопкой мыши устройство lva-sample-device и выберите команду "Запуск мониторинга встроенной конечной точки события".

    ![Запуск мониторинга встроенной конечной точки события](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Запуск примера программы

Чтобы запустить пример кода, выполните следующие действия.
1. В Visual Studio Code перейдите к файлу src/cloud-to-device-console-app/operations.json.
1. В узле GraphTopologySet внесите следующие изменения:

    ` "topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Затем убедитесь, что в узлах GraphInstanceSet и GraphTopologyDelete значение topologyName соответствует значению свойства name в топологии вышеприведенного графа:

    `"topologyName" : "MotionDetection"`
    
1. Запустите сеанс отладки (нажмите клавишу F5). В окне терминала начнут появляться сообщения.
1. Файл operations.json начнет выполнение операций с вызова GraphTopologyList и GraphInstanceList. Если вы очистили ресурсы после выполнения действий, описанных в предыдущих кратких руководствах, будут возвращены пустые списки, а затем выполнение будет приостановлено, пока вы не нажмете клавишу ВВОД.

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
       "name": "Sample-Graph",
       "properties": {
         "topologyName": "MotionDetection",
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
1. Граф мультимедиа продолжит работать и выводить результаты: симулятор RTSP будет продолжать циклическое воспроизведение исходного видео. Чтобы остановить граф мультимедиа, вернитесь в окно терминала и нажмите клавишу ВВОД. Следующая серия вызовов производится для очистки ресурсов.
     * Вызов GraphInstanceDeactivate для деактивации экземпляра графа.
     * Вызов GraphInstanceDelete для удаления экземпляра.
     * Вызов GraphTopologyDelete для удаления топологии.
     * Окончательный вызов GraphTopologyList, чтобы продемонстрировать, что список пуст.

## <a name="interpret-results"></a>Интерпретация результатов

При запуске графа мультимедиа результаты из узла обработчика детектора движения отправляются через узел стока Центра Интернета вещей в Центр Интернета вещей. Эти сообщения, отображаемые в окне вывода данных Visual Studio Code, содержат разделы body (текст) и applicationProperties (свойства приложения). Чтобы понять назначение этих разделов, ознакомьтесь с [этой](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) статьей.

В приведенных ниже сообщениях свойства приложений и содержимое раздела body определяются модулем аналитики видеотрансляции.

## <a name="mediasession-established-event"></a>Событие MediaSessionEstablished

После создания экземпляра графа мультимедиа узел источника RTSP пытается подключиться к серверу TRSP, работающему в контейнере rtspsim-live555. В случае успеха выводится следующее событие:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

* Сообщение представляет собой диагностическое событие MediaSessionEstablished: оно говорит о том, что узлу источника RTSP (параметру subject) удалось установить подключение к симулятору RTSP и начать прием потока данных (имитируемого).
* Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел в топологии графа, сгенерировавший сообщение. В данном случае сообщение исходит от узла источника RTSP.
* Параметр типа события eventType в разделе applicationProperties указывает, что событие является диагностическим.
* Параметр времени события eventTime указывает время события.
* Раздел body содержит информацию о диагностическом событии: в данном случае это данные [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


## <a name="motion-detection-event"></a>Событие обнаружения движения

При обнаружении движения модуль аналитики видеотрансляции Edge отправляет событие вывода. В качестве типа устанавливается motion, чтобы показать, что событие создано обработчиком обнаружения движения. Параметр eventTime указывает время, когда возникло событие (в формате UTC). Ниже приведен пример:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

* Параметр субъекта subject в разделе свойств приложения applicationProperties обозначает узел в графе мультимедиа, сгенерировавший сообщение. В данном случае сообщение исходит от узла обработчика обнаружения движения.
* Параметр типа события eventType в разделе applicationProperties указывает, что событие является аналитическим.
* Параметр времени события eventTime указывает время события.
Раздел body содержит информацию об аналитическом событии. В данном случае событие является событием вывода, поэтому раздел body содержит данные параметров timestamp и inferences.
* Данные параметра inferences указывают, что у параметра type значение motion, а также дополнительные данные о событии motion.
* В разделе box содержатся координаты прямоугольника, ограничивающего перемещение объекта. Эти значения нормализованы по ширине и высоте видео в пикселях (например, ширине 1920 и высоте 1080).

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="cleanup-resources"></a>Очистка ресурсов

Если вы планируете поработать и с другими краткими руководствами, созданные ресурсы следует сохранить. В противном случае зайдите на портал Azure, откройте список групп ресурсов, выберите группу, в которой работали с этим кратким руководством, и удалите все ресурсы.

## <a name="next-steps"></a>Дальнейшие действия

Изучайте другие краткие руководства, например по обнаружению объекта в видеотрансляции.        
