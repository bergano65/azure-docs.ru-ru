---
title: Создание событий при обнаружении движения — Azure
description: В этом кратком руководстве показано, как использовать Аналитику видеотрансляции в IoT Edge для создания событий при обнаружении движения путем программного вызова прямых методов.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: fdc80c4d734902309e8b6dc5a6bfee38514fcdb7
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067806"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Краткое руководство. Создание событий при обнаружении движения

В этом руководстве изложены пошаговые инструкции по началу работы с Аналитикой видеотрансляции в IoT Edge. В нем используется имитация видеотрансляции и виртуальная машина Azure в качестве устройства IoT Edge. После завершения процедуры настройки вы сможете запустить имитацию видеотрансляции с помощью графа мультимедиа, который обнаруживает движения в этой трансляции и сообщает о них. На следующей схеме представлено изображение графа мультимедиа:

![Аналитика видеотрансляции на основе обнаружения движения](./media/analyze-live-video/motion-detection.png) 

В основе этой статьи лежит [пример кода](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp), написанного на языке C#.

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. Если у вас еще нет учетной записи, [ создайте ее бесплатно](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) со следующими расширениями:
    * [Средства Интернета вещей Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Пакет SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

> [!TIP]
> Во время установки расширения Azure IoT Tools может появиться запрос на установку Docker. На него можно не обращать внимания.

## <a name="set-up-azure-resources"></a>Настройка ресурсов Azure

Для работы с этим учебником требуются следующие ресурсы Azure:

* Центр Интернета вещей
* Учетная запись хранения
* Учетная запись Служб мультимедиа Azure
* Виртуальная машина Linux в Azure с установленной [средой выполнения IoT Edge](../../iot-edge/how-to-install-iot-edge-linux.md)

В целях этого краткого руководства мы рекомендуем использовать [сценарий подготовки ресурсов Аналитики видеотрансляций](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) для развертывания необходимых ресурсов в подписке Azure. Для этого выполните следующие действия.

1. Откройте [Azure Cloud Shell](https://shell.azure.com).
1. Если вы используете Cloud Shell впервые, вам будет предложено выбрать подписку для создания учетной записи хранения и общей папки для службы "Файлы Microsoft Azure". Выберите **Create storage** (Создать хранилище), чтобы создать учетную запись для хранения сведений о сеансах Cloud Shell. Эта учетная запись хранения отличается от учетной записи, которая будет создана сценарием для использования с учетной записью Служб мультимедиа Azure.
1. В раскрывающемся меню в левой части окна Cloud Shell выберите в качестве среды **bash**.

    ![Выбор среды](./media/quickstarts/env-selector.png)

1. Выполните следующую команду.

```
bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
```

Если скрипт завершит работу успешно, вы увидите все необходимые ресурсы в своей подписке.

1. Когда сценарий завершит свою работу, щелкните фигурные скобки, чтобы раскрыть структуру папок. В каталоге *~/clouddrive/lva-sample* вы увидите несколько файлов. В этом кратком руководстве нас интересуют указанные ниже файлы.

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** — файл, содержащий свойства, которые Visual Studio Code использует для развертывания модулей на пограничном устройстве.
     * ***~/clouddrive/lva-sample/appsetting.json*** — файл, используемый Visual Studio Code для запуска примера кода.
     
Эти файлы понадобятся вам при настройке среды разработки в Visual Studio Code в следующем разделе. Пока мы рекомендуем сохранить локальные копии этих файлов.

 ![Параметры приложения](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

1. Клонируйте репозиторий из этого расположения: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. В Visual Studio Code откройте папку, в которую вы скачали репозиторий.
1. В Visual Studio Code перейдите в папку *src/cloud-to-device-console-app*. Создайте там файл и назовите его *appsettings.json*. Этот файл содержит параметры, необходимые для выполнения программы.
1. Скопируйте содержимое из созданного ранее файла *~ / clouddrive / lva-sample / appsettings.json*.

    Текст должен выглядеть как следующий вывод:

```
{  
    "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
    "deviceId" : "lva-sample-device",  
    "moduleId" : "lvaEdge"  
}
```
5. Перейдите в папку *src/edge* и создайте файл с именем *.env*.
1. Скопируйте содержимое файла */clouddrive/lva-sample/edge-deployment/.env*. Текст должен выглядеть как следующий код:

```
SUBSCRIPTION_ID="<Subscription ID>"  
RESOURCE_GROUP="<Resource Group>"  
AMS_ACCOUNT="<AMS Account ID>"  
IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
AAD_TENANT_ID="<AAD Tenant ID>"  
AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
```

## <a name="examine-the-sample-files"></a>Изучение примеров файлов

1. В Visual Studio Code перейдите в папку *src/edge*. Здесь находится созданный нами *ENV*-файл и несколько файлов шаблона развертывания.

    Шаблон развертывания — это манифест развертывания для пограничного устройства, где для некоторых параметров используются переменные. Файл *.env* содержит значения таких переменных.
1. Теперь перейдите в папку *src/cloud-to-device-console-app*. Здесь вы увидите файл *appsettings.json* и несколько других файлов:

    * ***c2d-console-app.csproj*** — файл проекта Visual Studio Code.
    * ***operations.json*** — список операций, которые должна запускать программа.
    * ***Program.cs*** — пример кода программы. Этот код выполняет следующие действия:
    
      * загружает параметры приложения;
      * вызывает прямые методы, представляемые модулем Аналитики видеотрансляции в IoT Edge (с помощью модуля можно анализировать потоки видеотрансляции, вызывая его [прямые методы](direct-methods.md));
      * приостанавливает выполнение, позволяя проанализировать выходные данные программы в окне **ТЕРМИНАЛ** и проверить сгенерированные модулем события в окне **ВЫХОДНЫЕ ДАННЫЕ**;
      * вызывает прямые методы для очистки ресурсов.   

## <a name="generate-and-deploy-the-deployment-manifest"></a>Создание и развертывание манифеста развертывания

Манифест развертывания определяет, какие именно модули развертываются на пограничном устройстве. Кроме того, он определяет параметры конфигурации для этих модулей. 

Выполните перечисленные ниже действия, чтобы создать манифест на базе файла шаблона, а затем развернуть его на пограничном устройстве.

1. Откройте Visual Studio Code.
1. Рядом с областью **ЦЕНТР ИНТЕРНЕТА ВЕЩЕЙ AZURE** выберите значок **Дополнительные действия**, чтобы задать строку подключения к Центру Интернета вещей. Строку подключения можно скопировать из файла *src/cloud-to-device-console-app/appsettings.json*. 

    ![Установка строки подключения Центра Интернета вещей](./media/quickstarts/set-iotconnection-string.png)

1. Щелкните правой кнопкой мыши **src/edge/deployment.template.json** и выберите **Generate IoT Edge Deployment Manifest** (Создать манифест развертывания IoT Edge).

    ![Создание файла манифеста развертывания IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    В результате этого действия в папке *src/edge/config* должен быть создан файл манифеста под названием *deployment.amd64.json*.
1. Щелкните правой кнопкой мыши файл **src/edge/config/deployment.amd64.json**, выберите **Create Deployment for Single Device** (Создать развертывание для одного устройства), а затем выберите — имя пограничного устройства.

    ![Создание развертывания для одного устройства](./media/quickstarts/create-deployment-single-device.png)

1. Вам будет предложено выбрать устройство Центра Интернета вещей. Выберите устройство **lva-sample-device** из раскрывающегося меню.
1. Примерно через 30 секунд обновите содержимое Центра Интернета вещей в левом нижнем углу окна. Теперь в пограничном устройстве должны отображаться следующие развернутые модули:

    * Аналитика видеотрансляций в IoT Edge (модуль `lvaEdge`).
    * Симулятор протокола RTSP (модуль `rtspsim`).

Модуль симулятора RTSP имитирует видеотрансляцию с применением видеофайла, который был скопирован на пограничное устройство при запуске [сценария подготовки ресурсов Аналитики видеотрансляций](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Если вы используете собственное пограничное устройство вместо того, которое было подготовлено с помощью нашего скрипта установки, перейдите на свое пограничное устройство и выполните следующие команды с **правами администратора**, чтобы извлечь и сохранить пример видеофайла, используемый для этого краткого руководства.  

```
mkdir /home/lvaadmin/samples      
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
На этом этапе модули развернуты, но графы мультимедиа неактивны.

## <a name="prepare-to-monitor-events"></a>Подготовка к мониторингу событий

Для обнаружения движения во входящем потоке видеотрансляции и создания событий в Центре Интернета вещей будет использоваться модуль аналитики видеотрансляции в IoT Edge. Чтобы просмотреть эти события, выполните указанные ниже действия.

1. Откройте область обозревателя в Visual Studio Code и найдите Центр Интернета вещей Azure в левом нижнем углу.
1. Разверните узел **Devices** (Устройства).
1. Щелкните правой кнопкой мыши файл **lva-sample-device** и выберите **Запуск мониторинга встроенной конечной точки события**.

    ![Запуск мониторинга встроенной конечной точки события](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Запуск примера программы

Чтобы запустить пример кода, выполните следующие действия:

1. В Visual Studio Code перейдите к файлу *src/cloud-to-device-console-app/operations.json*.
1. Проверьте, отображается ли в узле **GraphTopologySet** следующее значение:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Убедитесь, что в узлах **GraphInstanceSet** и **GraphTopologyDelete** значение `topologyName` соответствует значению свойства `name` в топологии графа:

    `"topologyName" : "MotionDetection"`
    
1. Чтобы запустить сеанс отладки, нажмите клавишу F5. В окне **ТЕРМИНАЛ** отобразятся некоторые сообщения.
1. Файл *operations.json* начнет выполнение операций с вызова `GraphTopologyList` и `GraphInstanceList`. Если вы очистили ресурсы после работы с предыдущими краткими руководствами, тогда этот процесс возвратит пустые списки, а затем приостановится. Чтобы продолжить, нажмите клавишу ВВОД.

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
     
 * Вызов `GraphInstanceActivate`, который активирует экземпляр графа и поток видео.
 * Второй вызов к `GraphInstanceList`, который показывает, что экземпляр графа находится в состоянии выполнения
6. Вывод данных в окне **ТЕРМИНАЛ** приостановится с появлением предложения `Press Enter to continue`. Не нажимайте клавишу ВВОД на этом этапе. Прокрутите экран вверх, чтобы увидеть полезные данные ответов JSON на вызванные нами прямые методы.
1. Перейдите в окно **ВЫХОДНЫЕ ДАННЫЕ** в Visual Studio Code. Вы увидите сообщения, которые модуль Аналитики видеотрансляций в IoT Edge передает в центр Интернета вещей. Эти сообщения рассматриваются в следующем разделе этого краткого руководства.
1. Граф мультимедиа продолжит работать и выводить результаты. Симулятор RTSP будет продолжать циклический перебор исходного видео. Чтобы остановить граф мультимедиа, вернитесь к окну **ТЕРМИНАЛ** и нажмите клавишу ВВОД. 

    Следующая серия вызовов очищает ресурсы:
     * Вызов `GraphInstanceDeactivate` деактивирует экземпляр графа.
     * Вызов `GraphInstanceDelete` удаляет этот экземпляр.
     * Вызов `GraphTopologyDelete` удаляет топологию.
     * Окончательный вызов `GraphTopologyList` показывает, что список теперь пуст.

## <a name="interpret-results"></a>Интерпретация результатов

При запуске графа мультимедиа результаты из узла обработчика детектора движения проходят через узел приемника Центра Интернета вещей в Центр Интернета вещей. Сообщения, отображаемые в окне **ВЫХОДНЫЕ ДАННЫЕ** в Visual Studio Code содержат раздел `body` и раздел `applicationProperties`. Дополнительные сведения см. в статье [Создание и чтение сообщений Центра Интернета вещей](../../iot-hub/iot-hub-devguide-messages-construct.md).

В приведенных ниже сообщениях модуль Аналитики видеотрансляций определяет свойства приложения и содержимое раздела body.

### <a name="mediasessionestablished-event"></a>Событие MediaSessionEstablished

После создания экземпляра графа мультимедиа узел источника RTSP пытается подключиться к RTSP-серверу, работающему в контейнере rtspsim-live555. Если соединение установлено, будет выводиться указанное ниже событие.

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

В приведенных выше выходных данных: 
* Сообщение является событием диагностики `MediaSessionEstablished`. Оно указывает, что исходный узел RTSP (параметр subject) установил подключение к симулятору RTSP и начал прием потока данных (имитируемого).
* В `applicationProperties` `subject` — это узел в топологии графа, из которого было создано сообщение. В этом случае сообщение исходит от узла источника RTSP.
* В `applicationProperties` `eventType` указывает, что это событие является событием диагностики.
* Значение `eventTime` указывает время события.
* В разделе `body` содержатся данные о событии диагностики. В этом случае данные содержат сведения [протокола SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


### <a name="motiondetection-event"></a>Событие обнаружения движения

При обнаружении движения модуль аналитики видеотрансляции IoT Edge отправляет событие вывода. Для `type` установлено значение `motion`, чтобы указать, что это результат, полученный от обработчика обнаружения движения. Значение `eventTime` указывает время, когда произошло движение (в формате UTC). 

Ниже приведен пример этого сообщения:

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

В данном примере: 

* В `applicationProperties` `subject` — это узел в графе мультимедиа, из которого было создано сообщение. В данном случае сообщение исходит от узла обработчика обнаружения движения.
* В `applicationProperties` `eventType` указывает, что это событие является событием аналитики.
* Значение `eventTime` — это время возникновения события.
* Значение `body` содержит информацию об аналитическом событии. В этом случае событие является событием вывода, поэтому текст содержит данные `timestamp` и `inferences`.
* Данные `inferences` указывают, что `type` — это `motion`. В этом разделе содержатся дополнительные данные о событии `motion`.
* В разделе `box` содержатся координаты прямоугольника, ограничивающего перемещение объекта. Эти значения нормализованы по ширине и высоте видео в пикселях, например ширине 1920 и высоте 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете работать с другими краткими руководствами, сохраните созданные ресурсы. В противном случае на портале Azure перейдите к группам ресурсов, выберите группу ресурсов, с которой вы работали в этом кратком руководстве, а затем удалите все ресурсы.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с другими краткими руководствами, например по обнаружению объекта в видеотрансляции.        
