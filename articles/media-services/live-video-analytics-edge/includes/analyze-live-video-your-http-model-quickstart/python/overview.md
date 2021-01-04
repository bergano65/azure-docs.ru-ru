---
ms.openlocfilehash: 0e800668bea2f744f8862292b21be814858b0c16
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486771"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="Порядок передачи сигналов":::

На схеме показан порядок передачи сигналов в этом кратком руководстве. [Пограничный модуль](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) имитирует IP-камеру, на которой находится RTSP-сервер. Узел [RTSP-источника](../../../media-graph-concept.md#rtsp-source) получает видеосигнал с этого сервера и передает видеокадры на узел [обработчика расширений HTTP](../../../media-graph-concept.md#http-extension-processor). 

Узел расширения HTTP играет роль прокси-сервера. Он выбирает входящие видеокадры, заданные с помощью поля `samplingOptions`, а также преобразует видеокадры в изображения указанного типа. Затем с использованием REST он передает изображение на другой пограничный модуль, выполняющий модель искусственного интеллекта на конечной точке HTTP. В этом примере этот пограничный модуль строится с помощью модели [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), которая может обнаруживать объекты множества типов. Узел обработчика расширений HTTP собирает результаты обнаружения и публикует события в узле [приемника Центра Интернета вещей](../../../media-graph-concept.md#iot-hub-message-sink). Затем узел отправляет эти события в [центр IoT Edge](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

В этом кратком руководстве вы выполните указанные ниже задачи.

1. Создание и развертывание графа мультимедиа.
1. Интерпретация результатов.
1. Очистка ресурсов.
