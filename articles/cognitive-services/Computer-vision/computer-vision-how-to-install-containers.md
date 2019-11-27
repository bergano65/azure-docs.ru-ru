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
# <a name="install-and-run-read-containers-preview"></a>Установка и запуск контейнеров чтения (Предварительная версия)

Контейнеры позволяют запускать Компьютерное зрение API в собственной среде. Контейнеры отлично подходят для конкретных требований к безопасности и управлению данными. В этой статье вы узнаете, как скачать, установить и запустить контейнер Компьютерное зрение.

Для Компьютерное зрение доступен один контейнер DOCKER, доступный для *чтения*. Контейнер *Read* позволяет обнаруживать и извлекать *печатный текст* из изображений различных объектов с разными поверхностями и фонами, такими как уведомления, плакаты и визитные карточки. Кроме того, контейнер *Read (чтение* ) обнаруживает *рукописный текст* в изображениях и предоставляет поддержку файлов PDF, TIFF и нескольких страниц. Дополнительные сведения см. в документации по [API чтения](concept-recognizing-text.md#read-api) .

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

Перед использованием контейнеров необходимо выполнить следующие предварительные требования:

|обязательные|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). См. [общие сведения о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс Компьютерное зрение |Для использования контейнера необходимо следующее:<br><br>Ресурс Azure **компьютерное зрение** и соответствующий ключ API для конечной точки. Оба значения доступны на страницах обзора и ключей для ресурса и необходимы для запуска контейнера.<br><br>**{API_KEY}** : один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}** : конечная точка, указанная на странице **обзора**|

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Доступны образы контейнеров для чтения.

| Контейнер | Реестр контейнеров, имя репозитория или образа |
|-----------|------------|
| чтение | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

Воспользуйтесь командой [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера.

### <a name="docker-pull-for-the-read-container"></a>Извлечение DOCKER для контейнера чтения

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](computer-vision-resource-container-config.md) команды `docker run`. 
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Сведения о том, как получить значения `{ENDPOINT_URI}` и `{API_KEY}`, см. в разделе [сбор обязательных параметров](#gathering-required-parameters) .

Доступны [примеры](computer-vision-resource-container-config.md#example-docker-run-commands) команды `docker run`.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер чтения из образа контейнера.
* Выделяет 8 ядер ЦП и 16 гигабайт (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

Доступны дополнительные [примеры](./computer-vision-resource-container-config.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> Для выполнения контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. 

Используйте узел `http://localhost:5000` для API контейнера.

### <a name="asynchronous-read"></a>Асинхронное чтение

Операции `POST /vision/v2.0/read/core/asyncBatchAnalyze` и `GET /vision/v2.0/read/operations/{operationId}` можно использовать совместно для асинхронного чтения образа, аналогично тому, как служба Компьютерное зрение использует соответствующие операции RESTFUL. Асинхронный метод POST возвратит `operationId`, который используется в качестве идентификатора для HTTP-запроса GET.

В пользовательском интерфейсе Swagger выберите `asyncBatchAnalyze`, чтобы развернуть его в браузере. Затем выберите **испытать** > **выберите файл**. В этом примере мы будем использовать следующее изображение:

![символы табуляции и пробелы](media/tabs-vs-spaces.png)

После успешного выполнения асинхронной отправки возвращается код состояния **HTTP 202** . В качестве части ответа имеется заголовок `operation-location`, содержащий конечную точку результата для запроса.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location` является полным URL-адресом, доступ к которому осуществляется через HTTP GET. Ниже приведен ответ JSON, посвященный выполнению URL-адреса `operation-location` из предыдущего образа:

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

### <a name="synchronous-read"></a>Синхронное чтение

Для синхронного чтения изображения можно использовать операцию `POST /vision/v2.0/read/core/Analyze`. Когда изображение считывается целиком, тогда API возвращает ответ JSON. Единственное исключение — при возникновении ошибки. При возникновении ошибки возвращается следующий код JSON:

```json
{
    status: "Failed"
}
```

Объект ответа JSON имеет тот же граф объектов, что и асинхронная версия. Если вы являетесь пользователем JavaScript и хотите обеспечить безопасность типов, можно использовать следующие типы для приведения ответа JSON в качестве объекта `AnalyzeResult`.

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

В качестве примера варианта использования ознакомьтесь с <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">песочницей <span class="docon docon-navigate-external x-hidden-focus"></span> TypeScript</a> и выберите Run ( **запустить** ), чтобы визуализировать простоту использования.

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](./computer-vision-resource-container-config.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

Контейнеры Cognitive Services отправляют сведения о выставлении счетов в Azure, используя соответствующий ресурс в учетной записи Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>summary

Из этой статьи вы узнали основные понятия и рабочий процесс, позволяющий скачивать, устанавливать и запускать контейнеры компьютерного зрения. Краткая сводка.

* Компьютерное зрение предоставляет контейнер Linux для DOCKER, инкапсулирующий чтение.
* Образы контейнеров загружаются из реестра контейнеров "предварительный просмотр контейнера" в Azure.
* Образы контейнеров, которые выполняются в Docker.
* Вы можете использовать REST API или пакет SDK для вызова операций в контейнерах чтения, указав универсальный код ресурса (URI) узла контейнера.
* При создании контейнера необходимо указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например анализируемые изображения или тексты).

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со статьей о [параметрах конфигурации](computer-vision-resource-container-config.md).
* Дополнительные сведения о распознавании печатного и рукописного текста см. в статье [Обзор компьютерного зрения](Home.md).
* Дополнительные сведения о методах, поддерживаемых контейнером, см. в статье [API компьютерного зрения](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).
* Чтобы разрешать проблемы, связанные с функциональностью Компьютерного зрения, ознакомьтесь с [часто задаваемыми вопросами](FAQ.md).
* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../cognitive-services-container-support.md)
