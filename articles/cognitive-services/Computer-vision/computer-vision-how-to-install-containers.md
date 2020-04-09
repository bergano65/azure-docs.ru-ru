---
title: Установка и запуск контейнеров — API компьютерного зрения
titleSuffix: Azure Cognitive Services
description: В этом пошаговом руководстве показано, как скачивать, устанавливать и выполнять контейнеры компьютерного зрения.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 5f36c429041a8182551d1f077f0a1229f520e8c1
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879349"
---
# <a name="install-and-run-read-containers-preview"></a>Установка и запуск контейнеров чтения (Предварительный просмотр)

Контейнеры позволяют запускать AA-приложения компьютерного зрения в вашей собственной среде. Контейнеры отлично подходят для конкретных требований к безопасности и управлению данными. В этой статье вы узнаете, как загружать, устанавливать и запускать контейнер Computer Vision.

Один контейнер Docker, *Читать*, доступен для компьютерного зрения. Контейнер *Read* позволяет обнаруживать и извлекать *печатный текст* из изображений различных объектов с различными поверхностями и фонами, таких как квитанции, плакаты и визитные карточки. Кроме того, контейнер *Read* обнаруживает *рукописный текст* в изображениях и обеспечивает поддержку ФАЙЛОВ PDF, TIFF и многостраничного файла. Для получения дополнительной информации смотрите документацию [Read](concept-recognizing-text.md#read-api) API.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Перед использованием контейнеров необходимо выполнить следующие предпосылки:

|Обязательно|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду с Docker для [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). См. [общие сведения о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс компьютерного зрения |Для использования контейнера необходимо следующее:<br><br>Ресурс Azure **Computer Vision** и связанный с ним API ключ к конечным точкам URI. Оба значения доступны на страницах «Обзор» и «Ключи» для ресурса и необходимы для запуска контейнера.<br><br>**«API_KEY:** Один из двух доступных ключей ресурса на странице **Ключей**<br><br>**(ENDPOINT_URI:** конечная точка, представленная на странице **Обзор**|

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Расширенная поддержка расширения векторного расширения

**Компьютер-хоста** — это компьютер, который управляет контейнером докера. Хозяин *должен поддерживать* [Расширенные расширения вектор](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Вы можете проверить поддержку AVX2 на хостах Linux со следующей командой:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Компьютер-хозяин *необходим* для поддержки AVX2. Контейнер *не будет* функционировать правильно без поддержки AVX2.

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Доступны изображения контейнеров для чтения.

| Контейнер | Реестр контейнеров / Репозиторий / Имя изображения |
|-----------|------------|
| Чтение | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду для загрузки изображения контейнера.

### <a name="docker-pull-for-the-read-container"></a>Докер тянуть для чтения контейнера

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](computer-vision-resource-container-config.md) команды `docker run`. 
1. [Запрос конечная точка прогнозирования контейнера.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Обратитесь к [сбору необходимых параметров](#gathering-required-parameters) `{ENDPOINT_URI}` для `{API_KEY}` получения подробной информации о том, как получить и значения.

[Приведены примеры](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` команды.

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер Read с изображения контейнера.
* Выделяет 8 процессоров ядра и 16 гигабайт (ГБ) памяти.
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

### <a name="asynchronous-read"></a>Асинхронное чтение

Вы можете `POST /vision/v2.0/read/core/asyncBatchAnalyze` использовать `GET /vision/v2.0/read/operations/{operationId}` и операции в согласии, чтобы асинхронно читать изображение, подобно тому, как служба Компьютерного Видения использует эти соответствующие операции REST. Асинхронный метод POST `operationId` вернет метод, используемый в качестве идентификатора, в запрос HTTP GET.

Из куража uI выберите, `asyncBatchAnalyze` чтобы расширить его в браузере. Затем выберите **Попробуйте выберите** > **файл.** В этом примере мы будем использовать следующее изображение:

![вкладки против пробелов](media/tabs-vs-spaces.png)

Когда асинхронный POST успешно работает, он возвращает код состояния **HTTP 202.** В ответе имеется `operation-location` заголовок, вмещает конечную точку результата для запроса.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

Является `operation-location` полностью квалифицированным URL и доступен через HTTP GET. Вот ответ JSON от выполнения `operation-location` URL из предыдущего изображения:

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
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
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

Операцию `POST /vision/v2.0/read/core/Analyze` можно использовать для синхронного чтения изображения. Когда изображение читается в полном объеме, то и только тогда API возвращает ответ JSON. Единственным исключением является, если ошибка происходит. При возникновении ошибки возвращается следующее возвращение JSON:

```json
{
    status: "Failed"
}
```

Объект ответа JSON имеет тот же график объекта, что и асинхронная версия. Если вы являетесь пользователем JavaScript и хотите сохранить тип, следующие типы `AnalyzeResult` могут быть использованы для отбрасывая ответ JSON в качестве объекта.

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

Для примера примера примера использования, см. <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">песочницу TypeScript здесь <span class="docon docon-navigate-external x-hidden-focus"></span> </a> и выберите **Run,** чтобы визуализировать свою простоту использования.

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](./computer-vision-resource-container-config.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

Контейнеры Cognitive Services отправляют платежную информацию в Azure, используя соответствующий ресурс в учетной записи Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

Из этой статьи вы узнали основные понятия и рабочий процесс, позволяющий скачивать, устанавливать и запускать контейнеры компьютерного зрения. В разделе "Сводка" сделайте следующее.

* Computer Vision предоставляет Linux контейнер для Docker, инкапсулируя Read.
* Изображения контейнеров загружаются из реестра контейнеров «Контейнерный предварительный просмотр» в Azure.
* Образы контейнеров выполняются в Docker.
* Вы можете использовать либо REST API, либо SDK для вызова операций в контейнерах Read, указав упор URI контейнера.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например анализируемые изображения или тексты).

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [конфигурации контейнеров](computer-vision-resource-container-config.md).
* Дополнительные сведения о распознавании печатного и рукописного текста см. в статье [Обзор компьютерного зрения](Home.md).
* Дополнительные сведения о методах, поддерживаемых контейнером, см. в статье [API компьютерного зрения](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).
* Чтобы разрешать проблемы, связанные с функциональностью Компьютерного зрения, ознакомьтесь с [часто задаваемыми вопросами](FAQ.md).
* Используйте больше [контейнеров когнитивных услуг](../cognitive-services-container-support.md)
