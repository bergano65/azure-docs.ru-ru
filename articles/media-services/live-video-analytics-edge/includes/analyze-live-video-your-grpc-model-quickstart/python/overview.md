---
ms.openlocfilehash: 97c21ca300ee070b2cebaa01a585c1618899b1eb
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687362"
---

![Обзор](../../../media/quickstarts/overview-grpc.png)

На схеме показан порядок передачи сигналов в этом кратком руководстве. [Пограничный модуль](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) имитирует IP-камеру, на которой находится RTSP-сервер. Узел [RTSP-источника](../../../media-graph-concept.md#rtsp-source) получает видеосигнал с этого сервера и передает видеокадры на узел [обработчика обнаружения движения](../../../media-graph-concept.md#motion-detection-processor). Этот процессор определяет движение и при обнаружении отправляет видеокадры в узел [процессора расширения gRPC](../../../media-graph-concept.md#grpc-extension-processor).

Узел расширения gRPC играет роль прокси-сервера. Он преобразует видеокадры в изображения указанного типа. Затем посредством gRPC он передает изображение на другой пограничный модуль, выполняющий модель искусственного интеллекта на конечной точке gRPC с использованием [общей памяти](https://en.wikipedia.org/wiki/Shared_memory). В этом примере этот пограничный модуль строится с помощью модели [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), которая может обнаруживать объекты множества типов. Узел обработчика расширений gRPC собирает результаты обнаружения и публикует события в узле [приемника Центра Интернета вещей](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/media-graph-concept#iot-hub-message-sink). Затем узел отправляет эти события в [центр IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

В этом кратком руководстве вы выполните указанные ниже задачи.

1. Создание и развертывание графа мультимедиа.
1. Интерпретация результатов.
1. Очистка ресурсов.
