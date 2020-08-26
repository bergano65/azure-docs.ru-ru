---
ms.openlocfilehash: eff73888a449de20b2b460d519b36c0f03c4ea77
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691095"
---
При запуске графа мультимедиа результаты из узла обработчика детектора движения проходят через узел приемника Центра Интернета вещей в Центр Интернета вещей. Сообщения, отображаемые в окне **ВЫХОДНЫЕ ДАННЫЕ** в Visual Studio Code содержат раздел `body` и раздел `applicationProperties`. Дополнительные сведения см. в статье [Создание и чтение сообщений Центра Интернета вещей](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

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
