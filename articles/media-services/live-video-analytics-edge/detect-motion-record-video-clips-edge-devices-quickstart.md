---
title: Запись видео на пограничные устройства при обнаружении движения — Azure
description: В этом кратком руководстве описано, как использовать Аналитику видеотрансляции в IoT Edge для анализа видеопотока, поступающего с (имитированной ) IP-камеры, определять наличие движений и записывать видеоролики MP4 в локальную файловую систему на пограничном устройстве.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 796def7cad3632dd50184bea751dc9f348569216
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067727"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Краткое руководство. Запись видео на пограничные устройства при обнаружении движения
 
В этом кратком руководстве показано, как использовать Аналитику видеотрансляций в службе IoT Edge для анализа потока видеотрансляции из (имитированной) IP-камеры. Здесь показано, как определять наличие движения, и если так, записывать MP4-видеофайл в локальную файловую систему пограничного устройства. В этом кратком руководстве используется имитация видеотрансляции, а в качестве устройства IoT Edge используется виртуальная машина Azure. 

В основе этой статьи лежит пример кода, написанного на языке C#. Это руководство основано на кратком руководстве [Создание событий при обнаружении движения](detect-motion-emit-events-quickstart.md). 

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись Azure с активной подпиской. Если у вас еще нет учетной записи, [ создайте ее бесплатно](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) со следующими расширениями:
    * [Средства Интернета вещей Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Пакет SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Если вы еще не ознакомились с кратким руководством [Создание событий при обнаружении движения](detect-motion-emit-events-quickstart.md), выполните следующие действия:
     1. [Настройка ресурсов Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [Настройка среды разработки](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [Создание и развертывание манифеста развертывания IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [Подготовка к мониторингу событий](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> Во время установки расширения Azure IoT Tools может появиться запрос на установку Docker. Вы можете проигнорировать его.

## <a name="review-the-sample-video"></a>Просмотр примера видео
Когда вы настроите ресурсы Azure для этого краткого руководства, видео с изображением автостоянки будет скопировано на виртуальную машину Linux в Azure, используемую в качестве устройства IoT Edge. Этот видеофайл будет использован для имитации прямой трансляции во время работы с этим руководством.

Откройте приложение, например [проигрыватель VLC](https://www.videolan.org/vlc/), нажмите сочетание клавиш Ctrl+N и вставьте [эту ссылку](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) на видео с автостоянкой, чтобы начать воспроизведение. Примерно на 5-секундной отметке белый автомобиль движется по стоянке.

Выполняя указанные действия, чтобы использовать Аналитику видеотрансляций в IoT Edge для обнаружения перемещения автомобиля и записи видео, начиная с 5-секундной отметки.

## <a name="overview"></a>Обзор

![общие сведения](./media/quickstarts/overview-qs4.png)

На предыдущей схеме показан порядок передачи сигналов в этом кратком руководстве. [Пограничный модуль](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) имитирует IP-камеру, на которой находится RTSP-сервер. Узел [RTSP-источника](media-graph-concept.md#rtsp-source) получает видеосигнал с этого сервера и передает видеокадры на узел [обработчика обнаружения движения](media-graph-concept.md#motion-detection-processor). RTSP-источник отправляет эти же видеокадры на узел [шлюза обработчика сигнала](media-graph-concept.md#signal-gate-processor), который остается закрытым до тех пор, пока его не активирует событие.

Когда обработчик обнаружения движения определяет движение в видео, он отправляет событие на узел шлюза обработчика сигнала, тем самым активируя его. Шлюз открывается и остается открытым в течение заданного времени, отправляя видеокадры на узел [приемника файлов](media-graph-concept.md#file-sink). Этот узел приемника записывает видео в формате MP4 в локальную файловую систему пограничного устройства. Файл сохраняется в заданном расположении.

В этом кратком руководстве вы выполните указанные ниже задачи.

1. Создание и развертывание графа мультимедиа.
1. Интерпретация результатов.
1. Очистка ресурсов.

## <a name="examine-and-edit-the-sample-files"></a>Изучение и изменение образцов файлов
Согласно предварительным требованиям этого краткого руководства вы загрузили пример кода в папку. Выполните указания ниже, чтобы изучить и изменить пример кода.

1. В Visual Studio Code перейдите в папку *src/edge*. Здесь находится созданный нами *ENV*-файл и несколько файлов шаблона развертывания.

    Шаблон развертывания — это манифест развертывания для пограничного устройства, где для некоторых параметров используются переменные. В *ENV*-файле указаны значения таких переменных.
1. Теперь перейдите в папку *src/cloud-to-device-console-app*. Здесь вы увидите файл *appsettings.json* и несколько других файлов:
    * ***c2d-console-app.csproj*** — файл проекта Visual Studio Code.
    * ***operations.json*** — список операций, которые должна запускать программа.
    * ***Program.cs*** — пример кода программы. Этот код выполняет следующие действия:

        * загружает параметры приложения;
        * вызывает прямые методы, которые представляются модулем Аналитики видеотрансляций в IoT Edge (с помощью модуля можно анализировать потоки видеотрансляции, вызывая его [прямые методы](direct-methods.md)); 
        * приостанавливает выполнение, позволяя проанализировать выходные данные программы в окне **ТЕРМИНАЛ** и проверить сгенерированные модулем события в окне **ВЫХОДНЫЕ ДАННЫЕ**;
        * вызывает прямые методы для очистки ресурсов.

1. Внесите правки в файл *operations.json*:
    * Измените ссылку на топологию графа:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * В разделе `GraphInstanceSet` измените имя топологии графа, чтобы оно совпадало со значением в приведенной выше ссылке:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`

    * Измените URL-адрес RTSP, чтобы он указывал на видеофайл:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`

    * В разделе `GraphTopologyDelete` измените имя:

        `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-the-modules-status"></a>Проверка. Оценка состояния модулей

На шаге [Создание и развертывание манифеста развертывания IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) в Visual Studio Code, разверните узел **lva-sample-device** в **Центр Интернета вещей Azure** (в разделе слева внизу). Вы должны увидеть развертывание следующих модулей:

* Модуль Аналитики видеотрансляций `lvaEdge`.
* Модуль `rtspsim`, имитирующий RTSP-сервер, выступающий в качестве источника веб-канала видеотрансляции

  ![Модули](./media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Если вы используете собственное пограничное устройство вместо того, которое было подготовлено с помощью нашего скрипта установки, перейдите на свое пограничное устройство и выполните следующие команды с **правами администратора**, чтобы извлечь и сохранить пример видеофайла, используемый для этого краткого руководства.  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```

## <a name="review---prepare-for-monitoring-events"></a>Проверка. Подготовка к мониторингу событий
Убедитесь, что вы выполнили шаги для [Подготовки к мониторингу событий](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events).

![Запуск мониторинга встроенной конечной точки события](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Запуск примера программы

1. Чтобы запустить сеанс отладки, нажмите клавишу F5. В окне **ТЕРМИНАЛ** отображаются некоторые сообщения.
1. Код *operations.json* вызывает прямые методы `GraphTopologyList` и `GraphInstanceList`. Если вы очистили ресурсы после работы с предыдущими краткими руководствами, тогда этот процесс возвратит пустые списки, а затем приостановится. Нажмите на клавишу ВВОД.

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
  * Вызов `GraphTopologySet` который использует `topologyUrl` 
  * Вызов `GraphInstanceSet`, который использует такой код:

```
{
  "@apiVersion": "1.0",
  "name": "Sample-Graph",
  "properties": {
    "topologyName": "EVRToFilesOnMotionDetection",
    "description": "Sample graph description",
    "parameters": [
      {
        "name": "rtspUrl",
        "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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

3. Вывод данных в окне **ТЕРМИНАЛ** приостановится с появлением предложения `Press Enter to continue`. Не нажимайте клавишу ВВОД на этом этапе. Прокрутите экран вверх, чтобы увидеть полезные данные ответов JSON для вызванных прямых методов.
1. Перейдите в окно **ВЫХОДНЫЕ ДАННЫЕ** в Visual Studio Code. Вы увидите сообщения, которые модуль Аналитики видеотрансляций в IoT Edge передает в центр Интернета вещей. Эти сообщения рассматриваются в следующем разделе этого краткого руководства.

1. Граф мультимедиа продолжит работать и выводить результаты. Симулятор RTSP будет продолжать циклический перебор исходного видео. Чтобы остановить граф мультимедиа, вернитесь к окну **ТЕРМИНАЛ** и нажмите клавишу ВВОД. 

    Следующий набор вызовов очищает ресурсы:
     * Вызов `GraphInstanceDeactivate` деактивирует экземпляр графа.
     * Вызов `GraphInstanceDelete` удаляет этот экземпляр.
     * Вызов `GraphTopologyDelete` удаляет топологию.
     * Последний вызов к `GraphTopologyList` показывает, что список теперь пуст.

## <a name="interpret-results"></a>Интерпретация результатов 
При запуске графа мультимедиа результаты из узла обработчика детектора движения проходят через узел приемника Центра Интернета вещей в Центр Интернета вещей. Сообщения, отображаемые в окне **ВЫХОДНЫЕ ДАННЫЕ** в Visual Studio Code содержат раздел `body` и раздел `applicationProperties`. Дополнительные сведения см. в статье [Создание и чтение сообщений Центра Интернета вещей](../../iot-hub/iot-hub-devguide-messages-construct.md).

В приведенных ниже сообщениях модуль Аналитики видеотрансляций определяет свойства приложения и содержимое раздела body.

### <a name="mediasessionestablished-event"></a>Событие MediaSessionEstablished

После создания экземпляра графа мультимедиа узел источника RTSP пытается подключиться к RTSP-серверу, работающему в контейнере rtspsim-live555. Если соединение установлено, будет выводиться следующее событие.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

В приведенных выше выходных данных: 

* Сообщение является событием диагностики `MediaSessionEstablished`. Он указывает, что исходный узел RTSP (субъект) установил подключение к симулятору RTSP и начал получать (имитировать) канал трансляции.
* В `applicationProperties` `subject` — это узел в топологии графа, из которого было создано сообщение. В этом случае сообщение исходит от узла источника RTSP.
* В `applicationProperties` `eventType` указывает, что это событие является событием диагностики.
* Значение `eventTime` — это время возникновения события.
* В разделе `body` содержатся данные о событии диагностики. В этом случае данные содержат сведения [протокола SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="recordingstarted-event"></a>Событие RecordingStarted

При обнаружении движения активируется узел шлюза обработчика сигнала, а узел приемника файлов в графе мультимедиа начинает записывать MP4-файл. Узел приемника файлов отправляет операционное событие. Для `type` установлено значение `motion`, чтобы указать, что это результат, полученный от обработчика обнаружения движения. Значение `eventTime` — это время в формате UTC, когда произошло движение. Дополнительные сведения об этом процессе см. в разделе [Обзор](#overview) этого краткого руководства.

Ниже приведен пример этого сообщения:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

В предыдущем сообщении: 

* В `applicationProperties` `subject` — это узел в графе мультимедиа, из которого было создано сообщение. В этом случае сообщение исходит из узла приемника файлов.
* В `applicationProperties` `eventType` указывает, что это событие является операционным.
* Значение `eventTime` — это время возникновения события. Это время составляет от 5 до 6 секунд после `MediaSessionEstablished` и после начала видеотрансляции. Это время соответствует отметке 5–6 секунд, когда [автомобиль начал двигаться](#review-the-sample-video) по стоянке.
* В разделе `body` содержатся данные об операционном событии. В этом случае данные содержат `outputType` и `outputLocation`.
* Переменная `outputType` указывает, что эта информация относится к пути файла
* Значение `outputLocation` — это расположение MP4-файла в пограничном модуле.

### <a name="recordingstopped-and-recordingavailable-events"></a>События RecordingStopped и RecordingAvailable

Если вы посмотрите на свойства узла шлюза обработчика сигнала в [топологии графа](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), вы увидите, что время активации составляет 5 секунд. Примерно через 5 секунд после получения события `RecordingStarted` вы получаете следующее:

* событие `RecordingStopped`, указывающее, что запись остановлена;
* событие `RecordingAvailable`, указывающее, что теперь MP4-файл можно использовать для просмотра.

Эти два события обычно происходят одно за другим с разницей в несколько секунд.

## <a name="play-the-mp4-clip"></a>Воспроизведение MP4-файла

MP4-файлы записываются в каталог на пограничном устройстве, указанном в файле *.env* с помощью ключа OUTPUT_VIDEO_FOLDER_ON_DEVICE. Если использовано значение по умолчанию, результаты должны находиться в папке */var/media/* .

Чтобы воспроизвести MP4-файл:

1. Перейдите к группе ресурсов, найдите виртуальную машину, а затем подключитесь с помощью Бастиона Azure.

    ![Группа ресурсов](./media/quickstarts/resource-group.png)
    
    ![ВМ](./media/quickstarts/virtual-machine.png)

1. Войдите, используя учетные данные, созданные при [настройке ресурсов Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. В командной строке перейдите в соответствующий каталог. Расположение по умолчанию — */var/media*. В каталоге должны отобразиться файлы MP4.

    ![Выходные данные](./media/quickstarts/samples-output.png) 

1. Используйте [безопасное копирование (SCP)](../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) для копирования файлов на локальный компьютер. 
1. Воспроизведите файлы с помощью [проигрывателя мультимедиа VLC](https://www.videolan.org/vlc/) или любого другого проигрывателя, поддерживающего формат MP4.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете работать с другими краткими руководствами, сохраните созданные ресурсы. В противном случае на портале Azure перейдите к группам ресурсов, выберите группу ресурсов, с которой вы работали в этом кратком руководстве, а затем удалите все ресурсы.

## <a name="next-steps"></a>Дальнейшие действия

* Следуйте краткому руководству [Запуск Аналитики видеотрансляции с помощью собственной модели](use-your-model-quickstart.md), чтобы узнать, как применить ИИ к каналам видеотрансляций.
* Ознакомьтесь с дополнительными задачами для опытных пользователей.

    * Используйте [IP-камеру](https://en.wikipedia.org/wiki/IP_camera), которая поддерживает протокол RTSP вместо симулятора RTSP. IP-камеры, поддерживающие протокол RTSP, можно найти на странице [продуктов, соответствующих ONVIF](https://www.onvif.org/conformant-products). Ищите устройства, которые соответствуют профилям G, S или T.
    * Используйте устройство с архитектурой AMD64 или x64 вместо использования виртуальной машины Linux в Azure. Устройство должно находиться в той же сети, что и IP-камера. Выполните инструкции из статьи [Install Azure IoT Edge runtime on Linux](../../iot-edge/how-to-install-iot-edge-linux.md) (Установка среды выполнения Azure IoT Edge в системах Linux на основе Debian). Потом следуйте инструкциям краткого руководства [Развертывание первого модуля IoT Edge на виртуальном устройстве Linux](../../iot-edge/quickstart-linux.md), чтобы зарегистрировать устройство в Центре Интернета вещей Azure.
