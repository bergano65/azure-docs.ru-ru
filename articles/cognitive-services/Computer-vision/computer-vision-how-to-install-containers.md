---
title: Установка и запуск контейнеров — API компьютерного зрения
titleSuffix: Azure Cognitive Services
description: В этом пошаговом руководстве показано, как скачивать, устанавливать и выполнять контейнеры компьютерного зрения.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: c65ed214747fd6a3729c2e9acff5489f5fa1b9d7
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323629"
---
# <a name="install-and-run-read-containers-preview"></a>Install and run Read containers (Preview)

Containers enable you to run the Computer Vision APIs in your own environment. Containers are great for specific security and data governance requirements. In this article you'll learn how to download, install, and run a Computer Vision container.

A single Docker container, *Read*, is available for Computer Vision. The *Read* container allows you to detect and extract *printed text* from images of various objects with different surfaces and backgrounds, such as receipts, posters, and business cards. Additionally, the *Read* container detects *handwritten text* in images and provides PDF, TIFF, and multi-page file support. For more information, see the [Read API](concept-recognizing-text.md#read-api) documentation.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

You must meet the following prerequisites before using the containers:

|Обязательно для заполнения|Цель|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Computer Vision resource |Для использования контейнера необходимо следующее:<br><br>An Azure **Computer Vision** resource and the associated API key the endpoint URI. Both values are available on the Overview and Keys pages for the resource and are required to start the container.<br><br>**{API_KEY}** : One of the two available resource keys on the **Keys** page<br><br>**{ENDPOINT_URI}** : The endpoint as provided on the **Overview** page|

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Container images for Read are available.

| Контейнер | Container Registry / Repository / Image Name |
|-----------|------------|
| Чтение | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

Воспользуйтесь командой [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера.

### <a name="docker-pull-for-the-read-container"></a>Docker pull for the Read container

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](computer-vision-resource-container-config.md) команды `docker run`. 
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Refer to [gathering required parameters](#gathering-required-parameters) for details on how to get the `{ENDPOINT_URI}` and `{API_KEY}` values.

[Examples](computer-vision-resource-container-config.md#example-docker-run-commands) of the `docker run` command are available.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Runs the Read container from the container image.
* Allocates 8 CPU core and 16 gigabytes (GB) of memory.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

Доступны дополнительные [примеры](./computer-vision-resource-container-config.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. 

Используйте узел `http://localhost:5000` для API контейнера.

### <a name="asynchronous-read"></a>Asynchronous read

You can use the `POST /vision/v2.0/read/core/asyncBatchAnalyze` and `GET /vision/v2.0/read/operations/{operationId}` operations in concert to asynchronously read an image, similar to how the Computer Vision service uses those corresponding REST operations. The asynchronous POST method will return an `operationId` that is used as the identifer to the HTTP GET request.

From the swagger UI, select the `asyncBatchAnalyze` to expand it in the browser. Then select **Try it out** > **Choose file**. In this example, we'll use the following image:

![tabs vs spaces](media/tabs-vs-spaces.png)

When the asynchronous POST has run successfully, it returns an **HTTP 202** status code. As part of the response, there is an `operation-location` header that holds the result endpoint for the request.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

The `operation-location` is the fully qualified URL and is accessed via an HTTP GET. Here is the JSON response from executing the `operation-location` URL from the preceding image:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [
            56,
            39,
            317,
            50,
            313,
            134,
            53,
            123
          ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [
                90,
                43,
                243,
                53,
                243,
                123,
                94,
                125
              ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [
                259,
                55,
                313,
                62,
                313,
                122,
                259,
                123
              ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [
            221,
            148,
            417,
            146,
            417,
            206,
            227,
            218
          ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [
                230,
                148,
                416,
                141,
                419,
                211,
                232,
                218
              ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>Synchronous read

You can use the `POST /vision/v2.0/read/core/Analyze` operation to synchronously read an image. When the image is read in its entirety, then and only then does the API return a JSON response. The only exception to this is if an error occurs. When an error occurs the following JSON is returned:

```json
{
    status: "Failed"
}
```

The JSON response object has the same object graph as the asynchronous version. If you're a JavaScript user and want type safety, the following types could be used to cast the JSON response as an `AnalyzeResult` object.

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

For an example use-case, see the <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">TypeScript sandbox here <span class="docon docon-navigate-external x-hidden-focus"></span></a> and select **Run** to visualize its ease-of-use.

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неисправностей

Если контейнер запускается с выходным [подключением](./computer-vision-resource-container-config.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

The Cognitive Services containers send billing information to Azure, using the corresponding resource on your Azure account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в разделе [Настройка контейнеров](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Резюме

Из этой статьи вы узнали основные понятия и рабочий процесс, позволяющий скачивать, устанавливать и запускать контейнеры компьютерного зрения. В разделе "Сводка" сделайте следующее.

* Computer Vision provides a Linux container for Docker, encapsulating Read.
* Container images are downloaded from the "Container Preview" container registry in Azure.
* Образы контейнеров выполняются в Docker.
* You can use either the REST API or SDK to call operations in Read containers by specifying the host URI of the container.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например анализируемые изображения или тексты).

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [конфигурации контейнеров](computer-vision-resource-container-config.md).
* Дополнительные сведения о распознавании печатного и рукописного текста см. в статье [Обзор компьютерного зрения](Home.md).
* Дополнительные сведения о методах, поддерживаемых контейнером, см. в статье [API компьютерного зрения](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).
* Чтобы разрешать проблемы, связанные с функциональностью Компьютерного зрения, ознакомьтесь с [часто задаваемыми вопросами](FAQ.md).
* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../cognitive-services-container-support.md)
