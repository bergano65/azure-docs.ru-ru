---
ms.openlocfilehash: e96975bce5e5d99db4ea78c80c027a95d4b662e3
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92038598"
---
![Обзор](../../../media/quickstarts/gRPC-extension.svg)

На схеме показан порядок передачи сигналов в этом кратком руководстве. [Пограничный модуль](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) имитирует IP-камеру, на которой находится RTSP-сервер. Узел [RTSP-источника](../../../media-graph-concept.md#rtsp-source) получает видеосигнал с этого сервера и передает видеокадры на узел [обработчика обнаружения движения](../../../media-graph-concept.md#motion-detection-processor). Этот процессор определяет движение и при обнаружении отправляет видеокадры в узел [процессора расширения gRPC](../../../media-graph-concept.md#grpc-extension-processor).

Узел расширения gRPC играет роль прокси-сервера. Он преобразует видеокадры в изображения указанного типа. Затем посредством gRPC он передает изображение на другой пограничный модуль, выполняющий модель искусственного интеллекта на конечной точке gRPC с использованием [общей памяти](https://en.wikipedia.org/wiki/Shared_memory). В этом примере этот пограничный модуль строится с помощью модели [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), которая может обнаруживать объекты множества типов. Узел обработчика расширений gRPC собирает результаты обнаружения и публикует события в узле [приемника Центра Интернета вещей](../../../media-graph-concept.md#iot-hub-message-sink). Затем узел отправляет эти события в [центр IoT Edge](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

В этом кратком руководстве вы выполните указанные ниже задачи.

1. Создание и развертывание графа мультимедиа.
1. Интерпретация результатов.
1. Очистка ресурсов.