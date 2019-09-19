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
ms.date: 09/18/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 97a9b6c60539191850e8205eed4387565b79f6db
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129880"
---
# <a name="install-and-run-computer-vision-containers"></a>Установка и запуск контейнеров Компьютерное зрение

Контейнеры позволяют запускать Компьютерное зрение API в собственной среде. Контейнеры отлично подходят для конкретных требований к безопасности и управлению данными. В этой статье вы узнаете, как скачать, установить и запустить контейнер Компьютерное зрение.

Для Компьютерное зрение доступны два контейнера docker: *Распознавание текста* и *Чтение*. Контейнер *Распознавание текста* позволяет обнаруживать и извлекать *печатный текст* из изображений различных объектов с разными поверхностями и фонами, например с помощью чеков, афиш и визитных карточек. Однако контейнер *Read* ; также обнаруживает *рукописный текст* в изображениях и предоставляет поддержку PDF/TIFF/многостраничной поддержки. Дополнительные сведения см. в документации по [API чтения](concept-recognizing-text.md#read-api) .

> [!IMPORTANT]
> Контейнер Распознавание текста считается устаревшим, в пользу которого находится контейнер Read. Контейнер чтения — это надмножество предшественника Распознавание текста контейнера, и потребители должны перейти на использование контейнера Read. Оба контейнера работают только на английском языке.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Перед использованием контейнеров необходимо выполнить следующие предварительные требования:

|Обязательное значение|Цель|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс Компьютерное зрение |Для использования контейнера необходимо следующее:<br><br>Ресурс Azure **компьютерное зрение** и соответствующий ключ API для конечной точки. Оба значения доступны на страницах обзора и ключей для ресурса и необходимы для запуска контейнера.<br><br>**{API_KEY}** : Один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}** : Конечная точка, указанная на странице **обзора**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

# <a name="readtabread"></a>[чтение](#tab/read)

Доступны образы контейнеров для чтения.

| Контейнер | Реестр контейнеров, имя репозитория или образа |
|-----------|------------|
| прочтены | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

# <a name="recognize-texttabrecognize-text"></a>[Распознавание текста](#tab/recognize-text)

Доступны образы контейнеров для Распознавания текста.

| Контейнер | Реестр контейнеров, имя репозитория или образа |
|-----------|------------|
| Распознавание текста | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

***

Воспользуйтесь командой [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера.

# <a name="readtabread"></a>[чтение](#tab/read)

### <a name="docker-pull-for-the-read-container"></a>Извлечение DOCKER для контейнера чтения

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

# <a name="recognize-texttabrecognize-text"></a>[Распознавание текста](#tab/recognize-text)

### <a name="docker-pull-for-the-recognize-text-container"></a>docker pull для контейнера Распознавания текста

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](computer-vision-resource-container-config.md) команды `docker run`. 
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Дополнительные сведения о том, как получить значения и `{API_KEY}` , `{ENDPOINT_URI}` см. в разделе [сбор обязательных параметров](#gathering-required-parameters) .

[Доступны примеры](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` команд.

# <a name="readtabread"></a>[чтение](#tab/read)

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

# <a name="recognize-texttabrecognize-text"></a>[Распознавание текста](#tab/recognize-text)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер Распознавание текста из образа контейнера.
* Выделяет 8 ядер ЦП и 16 гигабайт (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

***

Доступны дополнительные [примеры](./computer-vision-resource-container-config.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> Для выполнения контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. 

Используйте узел `http://localhost:5000` для API контейнера.

# <a name="readtabread"></a>[чтение](#tab/read)

### <a name="asynchronous-read"></a>Асинхронное чтение

Операции `POST /vision/v2.0/read/core/asyncBatchAnalyze` и`GET /vision/v2.0/read/operations/{operationId}` можно использовать совместно для асинхронного чтения образа, аналогично тому, как служба компьютерное зрение использует соответствующие операции RESTful. Асинхронный метод POST возвратит объект `operationId` , который используется в качестве идентификатора для HTTP-запроса GET.

В пользовательском интерфейсе Swagger выберите, `asyncBatchAnalyze` чтобы развернуть его в браузере. Нажмите кнопку **попробовать** > **выбрать файл**. В этом примере мы будем использовать следующее изображение:

![символы табуляции и пробелы](media/tabs-vs-spaces.png)

После успешного выполнения асинхронной отправки возвращается код состояния **HTTP 202** . В качестве части ответа имеется `operation-location` заголовок, содержащий конечную точку результата для запроса.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location` — Это полный URL-адрес, доступ к которому осуществляется через HTTP GET. Ниже приведен ответ JSON, посвященный `operation-location` выполнению URL-адреса из предыдущего изображения:

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

Для синхронного считывания изображения можно использовать `POST /vision/v2.0/read/core/Analyze` операцию. Когда изображение считывается целиком, тогда API возвращает ответ JSON. Единственное исключение — при возникновении ошибки. При возникновении ошибки возвращается следующий код JSON:

```json
{
    status: "Failed"
}
```

Объект ответа JSON имеет тот же граф объектов, что и асинхронная версия. Если вы являетесь пользователем JavaScript и хотите обеспечить безопасность типов, можно использовать следующие типы для приведения ответа JSON в качестве `AnalyzeResult` объекта.

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

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: string | null;
}
```

В качестве примера варианта использования ознакомьтесь с [песочницей TypeScript](https://aka.ms/ts-read-api-types) и выберите "выполнить", чтобы визуализировать простоту использования.

# <a name="recognize-texttabrecognize-text"></a>[Распознавание текста](#tab/recognize-text)

### <a name="asynchronous-text-recognition"></a>Асинхронное распознавание текста

Вы можете использовать операции `POST /vision/v2.0/recognizeText` и `GET /vision/v2.0/textOperations/*{id}*` для асинхронного распознавания печатного текста на изображении подобно тому, как служба "Компьютерное зрение" использует соответствующие операции REST. В настоящее время контейнер распознавания текста определяет только печатный текст и не распознает рукописный, поэтому параметр `mode`, обычно указываемый для операции службы "Компьютерное зрение", игнорируется контейнером распознавания текста.

### <a name="synchronous-text-recognition"></a>Синхронное распознавание текста

С помощью операции `POST /vision/v2.0/recognizeTextDirect` можно синхронно распознавать печатный текст на изображении. Так как эта операция является синхронной, текст запроса для этой операции `POST /vision/v2.0/recognizeText` совпадает с текстом операции, но текст ответа для этой операции совпадает с текстом, возвращенным `GET /vision/v2.0/textOperations/*{id}*` операцией.

***

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](./computer-vision-resource-container-config.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера. 

## <a name="billing"></a>Выставление счетов

Контейнеры Cognitive Services отправляют сведения о выставлении счетов в Azure, используя соответствующий ресурс в учетной записи Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

Из этой статьи вы узнали основные понятия и рабочий процесс, позволяющий скачивать, устанавливать и запускать контейнеры компьютерного зрения. В разделе "Сводка" сделайте следующее.

* Компьютерное зрение предоставляет контейнер Linux для DOCKER, который инкапсулирует как Распознавание текста, так и чтение.
* Образы контейнеров загружаются из реестра контейнеров "предварительный просмотр контейнера" в Azure.
* Образы контейнеров выполняются в Docker.
* Вы можете использовать REST API или пакет SDK для вызова операций в Распознавание текста или чтения контейнеров, указав универсальный код ресурса (URI) узла контейнера.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например анализируемые изображения или тексты).

## <a name="next-steps"></a>Следующие шаги

* Ознакомьтесь со статьей о [конфигурации контейнеров](computer-vision-resource-container-config.md).
* Дополнительные сведения о распознавании печатного и рукописного текста см. в статье [Обзор компьютерного зрения](Home.md).
* Дополнительные сведения о методах, поддерживаемых контейнером, см. в статье [API компьютерного зрения](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).
* Чтобы разрешать проблемы, связанные с функциональностью Компьютерного зрения, ознакомьтесь с [часто задаваемыми вопросами](FAQ.md).
* [Поддержка контейнеров в Azure Cognitive Services](../cognitive-services-container-support.md)
