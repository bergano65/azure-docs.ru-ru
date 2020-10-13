---
title: Анализ видеотрансляций с помощью Аналитики видеотрансляций в IoT Edge и Пользовательского визуального распознавания Azure
description: Узнайте, как с помощью Пользовательского визуального распознавания создать контейнеризованную модель, которая сможет идентифицировать игрушечный грузовик, а также воспользоваться возможностью расширяемости ИИ Аналитики видеотрансляций в IoT Edge (LVA), чтобы развернуть модель на пограничном устройстве для обнаружения игрушечных грузовиков во время потоковой передачи видео.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 5da3186e64dd369dc57a0d5d1b635fc082158765
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91804152"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Руководство по анализу видеотрансляций с помощью Аналитики видеотрансляций в IoT Edge и Пользовательского визуального распознавания Azure

Из этого руководства вы узнаете, как с помощью [Пользовательского визуального распознавания](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) создать контейнеризованную модель, которая сможет идентифицировать игрушечный грузовик, а также воспользоваться [возможностью расширяемости ИИ](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) Аналитики видеотрансляций в IoT Edge, чтобы развернуть модель на пограничном устройстве для обнаружения игрушечных грузовиков во время потоковой передачи видео.

Мы покажем вам, как объединить преимущества Пользовательского визуального распознавания (которое позволяет каждому пользователю создавать и обучать модель компьютерного зрения, просто загрузив и пометив несколько изображений, не имея при этом никаких знаний в области компьютерного моделирования, ML или ИИ) и возможности Аналитики видеотрансляций, чтобы с легкостью развернуть пользовательскую модель в качестве пограничного контейнера и проанализировать смоделированный веб-канал видеотрансляции. В этом руководстве в качестве устройства IoT Edge используется Виртуальная машина Azure, основанная на образце кода, написанном на C# для краткого руководства [Создание событий при обнаружении движения](detect-motion-emit-events-quickstart.md).

В этом руководстве описаны следующие процедуры.

> [!div class="checklist"]
> * Настроите необходимые ресурсы.
> * Создание модели Пользовательского визуального распознавания в облаке, чтобы обнаруживать игрушечные грузовики и развертывать их на пограничном устройстве
> * Создание и развертывание графа мультимедиа с расширением HTTP для пользовательской модели компьютерного зрения
> * Запустите пример кода.
> * Изучите и интерпретируйте результаты.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Рекомендуемые материалы для предварительного ознакомления  

Перед началом работы рекомендуем ознакомиться со следующими статьями: 

* [Аналитика видеотрансляций в IoT Edge: обзор](overview.md)
* [Обзор Пользовательского визуального распознавания Azure](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)
* [Аналитика видеотрансляции в IoT Edge: терминология](terminology.md)
* [Граф мультимедиа: основные понятия](media-graph-concept.md)
* [Аналитика видеотрансляции без записи видео](analyze-live-video-concept.md)
* [Аналитика видеотрансляции с помощью собственной модели](use-your-model-quickstart.md)
* [Руководство. Разработка модуля IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Редактирование файла deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Предварительные требования

Обязательные компоненты для работы с этим руководством:

* [Visual Studio Code](https://code.visualstudio.com/) на компьютере для разработки с расширениями [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) и [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > Может появиться запрос на установку Docker. Проигнорируйте его.
* Установите [SDK .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) на компьютере, на котором ведется разработка.
* Убедитесь, что выполнены следующие действия:
    
    * [Настройка ресурсов Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Настройка среды разработки](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>Просмотр примера видео

В этом руководстве для моделирования прямой трансляции используется файл [видео вывода игрушечного автомобиля](https://lvamedia.blob.core.windows.net/public/t2.mkv/). Видео можно просмотреть с помощью таких приложений, как [проигрыватель мультимедиа VLC](https://www.videolan.org/vlc/). Нажмите сочетание клавиш CTRL+N, а затем вставьте ссылку на [видео вывода игрушечного автомобиля](https://lvamedia.blob.core.windows.net/public/t2.mkv), чтобы начать воспроизведение. Просматривая видео, обратите внимание на то, что игрушечный грузовик появляется на маркере, обозначающем 36-ю секунду. Пользовательская модель обучена так, чтобы обнаруживать этот конкретный игрушечный грузовик. В этом руководстве вы будете использовать Аналитику видеотрансляций в IoT Edge, чтобы обнаруживать такие игрушечные грузовики и публиковать соответствующие события вывода в центре IoT Edge.

## <a name="overview"></a>Обзор

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Обзор Пользовательского визуального распознавания":::

На схеме показан порядок передачи сигналов в этом руководстве. [Пограничный модуль](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) имитирует IP-камеру, на которой находится RTSP-сервер. Узел [источника RTSP](media-graph-concept.md#rtsp-source) извлекает видеопоток с этого сервера и отправляет видеокадры на узел [обработчика фильтра частоты кадров](media-graph-concept.md#frame-rate-filter-processor). Этот обработчик ограничивает частоту кадров видеопотока при достижении узла [обработчика расширения HTTP](media-graph-concept.md#http-extension-processor).
Узел расширения HTTP играет роль прокси-сервера. Он преобразует видеокадры в изображения указанного типа. Затем с использованием REST он передает изображение на другой пограничный модуль, выполняющий модель искусственного интеллекта на конечной точке HTTP. В этом примере пограничный модуль является моделью средства обнаружения игрушечного грузовика, созданной с помощью Пользовательского визуального распознавания. Узел обработчика расширений HTTP собирает результаты обнаружения и публикует события в узле [приемника Центра Интернета вещей](media-graph-concept.md#iot-hub-message-sink). Затем узел отправляет эти события в [центр IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Создание и развертывание модели обнаружения игрушки Пользовательского визуального распознавания 

Как можно понять из имени API — "Пользовательское визуальное распознавание", его можно использовать для создания собственного пользовательского средства для обнаружения объектов или классификатора в облаке. Это простой и понятный интерфейс для создания моделей пользовательского визуального распознавания, которые можно развернуть в облаке или на пограничном устройстве с помощью контейнеров. 

Чтобы получить сведения о создании средства обнаружения игрушечного грузовика, следуйте [статье краткого руководства](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector), посвященного Пользовательскому визуальному распознаванию, которая называется "Создание средства обнаружения объектов".

Дополнительные замечания
 
* Для работы с этим руководством не нужно использовать образы-примеры, указанные в разделе [предварительные требования](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#prerequisites) статьи краткого руководства. Вместо использования определенного набора образов для создания модели пользовательского визуального распознавания на основе средства обнаружения игрушек, мы рекомендуем воспользоваться [этими образцами](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip), когда в кратком руководстве будет предложено [выбрать образы обучения](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#choose-training-images).
* В разделе краткого руководства"Добавление тегов" следите за тем, чтобы добавить метки на изображения грузовика с тегом "грузовой автомобиль".

Как только модель будет соответствовать вашим требованиям, можете экспортировать ее в контейнер Docker с помощью кнопки "Экспорт" на вкладке "Производительность". Убедитесь, что в качестве типа платформы контейнера выбрана платформа Linux. Это платформа, на которой будет выполняться контейнер. Компьютер, на который вы загружаете контейнер, может работать как под управлением Windows, так и Linux. Приведенные ниже инструкции основаны на файле контейнера, загруженном на компьютер Windows.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Обзор Пользовательского визуального распознавания"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            С помощью этой команды можно проверить контейнер на локальном компьютере, и, если на изображении будет представлен тот же грузовой автомобиль, который использовался для обучения модели, то выходные данные должны выглядеть примерно так, как показано ниже. Вероятность того, что грузовой автомобиль обнаружен равняется 90,12 %.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Изучение примеров файлов

1. В VSCode перейдите в папку "src/edge". Здесь находится созданный вами файл .env, а также несколько файлов шаблона развертывания.

    Шаблон развертывания — это манифест развертывания для пограничного устройства, где используются некоторые значения заполнителя. В файле .env указаны значения таких переменных.
1. Теперь перейдите в папку src/cloud-to-device-console-app. Здесь мы найдем файл appsettings.json, который создали, и еще несколько файлов:

    * c2d-console-app.csproj — файл проекта VSCode.
    * operations.json — файл со списком различных операций, которые необходимо выполнить программе;
    * Program.cs — образец кода программы, который выполняет следующие действия:

        * загружает параметры приложения;
        * вызывает Аналитику видеотрансляций в прямых методах модуля IoT Edge, чтобы создать топологию, а также экземпляр графа и активировать граф;
        * приостанавливает выполнение графа в окне "ТЕРМИНАЛ" и событий, отправленных в Центр Интернета вещей в окне ВЫХОДНЫЕ ДАННЫЕ;
        * деактивирует и удаляет экземпляр графа, а также типологию графа.
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>Создание и развертывание манифеста развертывания

1. В VSCode перейдите к файлу "src/cloud-to-device-console-app/operations.json"

1. В GraphTopologySet проверьте наличие следующих условий:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. В GraphTopologySet проверьте: 
    1. "topologyName" : "InferencingWithHttpExtension"
    1. Добавьте следующий код в начало массива параметров — `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Измените значение параметра rtspUrl на "rtsp://rtspsim:554/media/t2.mkv"    
1. В разделе GraphTopologyDelete проверьте пункт "name": "InferencingWithHttpExtension"
1. Щелкните правой кнопкой мыши файл "src/edge/deployment.customvision.template.json" и выберите **Generate IoT Edge Deployment Manifest** (Создание манифеста развертывания IoT Edge).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Обзор Пользовательского визуального распознавания" рядом с областью Центра Интернета вещей Azure в левом нижнем углу. Строку можно скопировать из файла appsettings.json. (Есть еще один рекомендуемый подход, позволяющий обеспечивать правильность настройки Центра Интернета вещей в VSCode. Он предполагает использование [команды выбора Центра Интернета вещей](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Обзор Пользовательского визуального распознавания" и выберите **Create Deployment for Single Device** (Создать развертывание для одного устройства). 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Обзор Пользовательского визуального распознавания":::
1. Вам будет предложено выбрать устройство Центра Интернета вещей. Выберите устройство lva-sample-device из раскрывающегося списка.
1. Примерно через 30 секунд обновите содержимое Центра Интернета вещей в левом нижнем углу. Должно появиться пограничное устройство со следующими развернутыми модулями:

    * Аналитика видеотрансляций в модуле IoT Edge с именем lvaEdge.
    * Модуль с именем `rtspsim`, имитирующий сервер RTSP и выступающий в качестве источника сигнала видеопотока.
    * Модуль с именем `cv`, который, как предполагает название, является моделью обнаружения игрушечного грузовика Пользовательского визуального распознавания, которая применяет пользовательское распознавание к изображениям и возвращает несколько типов тегов. (Наша модель обучена с помощью одного тега — "грузовой автомобиль").

## <a name="prepare-for-monitoring-events"></a>Подготовка к мониторингу событий

Щелкните правой кнопкой мыши устройство Аналитики видеотрансляции и выберите **Запуск мониторинга встроенной конечной точки события**. Этот шаг необходим для мониторинга событий Центра Интернета вещей в окне ВЫХОДНЫЕ ДАННЫЕ в Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Обзор Пользовательского визуального распознавания":::

## <a name="run-the-sample-program"></a>Запуск примера программы

Если вы откроете топологию графа для этого учебника в браузере, то увидите, что для параметра inferencingUrl установлено значение http://cv:80/image. Это означает, что сервер вывода будет возвращать результаты после обнаружения игрушечных грузовиков (при их наличии) в видеотрансляции.

1. В Visual Studio Code откройте вкладку **Расширения** (или нажмите клавиши CTRL+SHIFT+X) и найдите центр Интернета вещей Azure.
1. Щелкните правой кнопкой мыши и выберите **Параметры расширения**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Обзор Пользовательского визуального распознавания" (Показывать подробное сообщение).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Обзор Пользовательского визуального распознавания"
              }
            ]
          }
        }
   ```
    
   * Вызов GraphInstanceActivate, который запускает экземпляр графа и видеопоток.
   * Второй вызов GraphInstanceList который указывает, что экземпляр графа находится в состоянии выполнения.
    
1. Вывод данных в окне Терминал будет приостановлен с предложением нажать клавишу ВВОД для продолжения. Не нажимайте клавишу ВВОД на этом этапе. Прокрутите экран вверх, чтобы увидеть полезные данные ответов JSON на вызванные нами прямые методы.
1. Перейдите в окно ВЫХОДНЫЕ ДАННЫЕ в Visual Studio Code. Вы увидите сообщения, которые модуль Аналитики видеотрансляций в IoT Edge передает в центр Интернета вещей. Эти сообщения рассматриваются в следующем разделе этого учебника.
1. Граф мультимедиа продолжит работать и выводить результаты. Симулятор RTSP будет продолжать циклический перебор исходного видео. Чтобы остановить граф мультимедиа, вернитесь к окну ТЕРМИНАЛ и нажмите клавишу ВВОД.
Следующая серия вызовов очищает ресурсы:
    
   * Вызов GraphInstanceDeactivate деактивирует экземпляр графа.
   * Вызов GraphInstanceDelete удаляет экземпляр.
   * Вызов GraphTopologyDelete удаляет топологию.
   * Окончательный вызов GraphTopologyList, показывает, что список пуст.
    
## <a name="interpret-the-results"></a>Интерпретация результатов

При запуске графа мультимедиа результаты из узла обработчика расширения HTTP отправляются через узел-преемник Центра Интернета вещей в центр Интернета вещей. Эти сообщения, отображаемые в окне Вывод содержат разделы body (текст) и applicationProperties (свойства приложения). Дополнительные сведения см. в статье [Создание и чтение сообщений Центра Интернета вещей](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

В приведенных ниже сообщениях модуль Аналитики видеотрансляций определяет свойства приложения и содержимое раздела body.

### <a name="mediasessionestablished-event"></a>Событие MediaSessionEstablished

После создания экземпляра графа мультимедиа узел источника RTSP пытается подключиться к RTSP-серверу, работающему в контейнере rtspsim-live555. Если соединение установлено, будет выводиться указанное ниже событие. Тип данного события — Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

В этом сообщении обратите внимание на следующие сведения:

* Сообщение является событием диагностики. MediaSessionEstablished указывает, что исходный узел RTSP (subject) установил подключение к симулятору RTSP и начал прием (имитируемого) потока данных.
* В applicationProperties параметр subject указывает на то, что сообщение было создано в исходном узле RTSP в графе мультимедиа.
* В разделе applicationProperties параметр eventType указывает на то, что событие является диагностическим.
* Параметр времени события eventTime указывает время события.
* Текст содержит данные о событии диагностики. В этом случае данные содержат сведения [протокола SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Событие вывода

Узел обработчика расширений HTTP получает результаты вывода из контейнера Пользовательского визуального распознавания и выдает результаты через узел приемника Центра Интернета вещей как события вывода.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Обратите внимание на следующее в приведенных выше сообщениях:

* Параметр subject в разделе свойств приложения applicationProperties обозначает узел в графе мультимедиа, сгенерировавший сообщение. В данном случае сообщение исходит от обработчика расширений HTTP.
* Параметр типа события eventType в разделе applicationProperties указывает на событие аналитического вывода.
* Параметр времени события eventTime указывает время события.
* Раздел body содержит информацию об аналитическом событии. В данном случае событие является событием вывода, поэтому код содержит массив выводов с именем "predictions".
* Раздел "predictions" содержит список предположений обнаружения игрушечного грузовика в кадре (метка=грузовой автомобиль). Как вы помните, грузовой автомобиль — пользовательская метка, предоставленная пользовательской обученной на грузовик модели, которая выводит и обнаруживает грузовик во входном видеосигнале с различными показателями вероятности.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете поработать с другими учебниками или краткими руководствами, созданные ресурсы следует сохранить. В противном случае перейдите на портал Azure, откройте список групп ресурсов, выберите группу, которую вы использовали для этого руководства, и удалите все ресурсы.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с дополнительными задачами для опытных пользователей.

* Вместо симулятора RTSP используйте [IP-камеру](https://en.wikipedia.org/wiki/IP_camera) с поддержкой RTSP. IP-камеры, поддерживающие протокол RTSP, можно найти на странице [продуктов, соответствующих ONVIF](https://www.onvif.org/conformant-products/). Ищите устройства, которые соответствуют профилям G, S или T.
* Используйте устройство AMD64 или x64 на базе Linux вместо виртуальной машины Linux в Azure. Устройство должно находиться в той же сети, что и IP-камера. Вы можете выполнить инструкции из статьи [Install Azure IoT Edge runtime on Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) (Установка среды выполнения Azure IoT Edge в системах Linux на основе Debian). 

Чтобы зарегистрировать устройство в Центре Интернета вещей Azure, следуйте инструкциям краткого руководства [Развертывание первого модуля IoT Edge на виртуальном устройстве Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).

