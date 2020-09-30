---
title: Установка чтения с оптического распознавания контейнеров DOCKER из Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Используйте средства чтения ОПТИЧЕСКИх контейнеров DOCKER из Компьютерное зрение для извлечения текста из изображений и даументс в локальной среде.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: локальное, оптическое распознавание, Docker, контейнер
ms.openlocfilehash: acf6a391965dcba20a2dabc18648076b88c5e7c5
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536381"
---
# <a name="install-read-ocr-docker-containers-preview"></a>Установить чтение с оптического распознавания контейнеров DOCKER (Предварительная версия) 

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Контейнеры позволяют запускать API-интерфейсы Компьютерного зрения в вашей среде. Контейнеры соответствуют конкретным требованиям к безопасности и управлению данными. В этой статье вы узнаете, как загружать, устанавливать и запускать контейнеры Компьютерное зрение.

Контейнер для *чтения* и распознавания текста позволяет извлекать печатные и рукописные тексты из изображений и документов с поддержкой форматов JPEG, PNG, BMP, PDF и TIFF. Дополнительные сведения см. в [документации по API чтения](concept-recognizing-text.md#read-api).

## <a name="read-3x-containers"></a>Чтение контейнеров 3. x
В предварительной версии доступны две версии контейнеров 3. x. Обе версии предоставляют дополнительную точность и функции по сравнению с предыдущим контейнером.

Контейнер Read 3,0-Preview предоставляет следующие сведения:
* Новые модели для повышения точности.
* Поддержка нескольких языков в одном документе
* Поддержка: Голландский, английский, французский, немецкий, итальянский, португальский и испанский.
* Единая операция для документов и изображений.
* Поддержка больших документов и изображений.
* Показатели достоверности от 0 до 1.
* Поддержка документов с печатным и рукописным текстом

Контейнер Read 3,1-Preview предоставляет те же преимущества, что и версия 3.0-Preview, с дополнительными функциями:

* Поддержка упрощенного китайского и японского языков.
* оценки достоверности и метки для печатного и рукописного текста. 
* Возможность извлечения текста только из выбранных страниц в документе.

При рассмотрении используемой версии контейнера Обратите внимание, что версия 3.1-Preview находится в более раннем состоянии предварительной версии. Если вы используете "Read 2,0 Containers" сегодня, ознакомьтесь с [руководством по миграции](read-container-migration-guide.md) , чтобы узнать об изменениях в новых версиях.

## <a name="prerequisites"></a>Предварительные условия

Перед использованием контейнеров необходимо выполнить следующие предварительные требования:

|Обязательный|Цель|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду с Docker для [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс Компьютерное зрение |Для использования контейнера необходимо следующее:<br><br>Ресурс Azure **компьютерное зрение** и соответствующий ключ API для конечной точки. Оба значения доступны на страницах обзора и ключей для ресурса и необходимы для запуска контейнера.<br><br>**{API_KEY}**: один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}**: конечная точка, указанная на странице **обзора**|

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/), прежде чем начинать работу.

## <a name="request-approval-to-run-the-container"></a>Запросить утверждение для запуска контейнера

Заполните [форму запроса](https://aka.ms/csgate) и отправьте ее, чтобы запросить утверждение для запуска контейнера. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Поддержка расширенного векторного расширения

**Главный** компьютер — это компьютер, на котором выполняется контейнер DOCKER. Узел *должен поддерживать* [Расширенные векторные расширения](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Вы можете проверить поддержку AVX2 на узлах Linux с помощью следующей команды:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> Главный компьютер *необходим* для поддержки AVX2. Контейнер *не будет* работать правильно без поддержки AVX2.

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Доступны образы контейнеров для чтения.

| Контейнер | Реестр контейнеров, имя репозитория или образа |
|-----------|------------|
| Чтение 2,0-Preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Чтение 3,0-Preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview` |
| Чтение 3,1-Preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду, чтобы скачать образ контейнера.

### <a name="docker-pull-for-the-read-container"></a>Извлечение DOCKER для контейнера чтения

# <a name="version-31-preview"></a>[Версия 3,1-Preview](#tab/version-3-1)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview
```

# <a name="version-30-preview"></a>[Версия 3.0-preview](#tab/version-3)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview
```

# <a name="version-20-preview"></a>[Версия 2,0-Preview](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](computer-vision-resource-container-config.md) команды `docker run`. 
1. [Запросите конечную точку прогнозирования контейнера](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Дополнительные сведения о том, как получить значения и, см. в разделе [сбор обязательных параметров](#gathering-required-parameters) `{ENDPOINT_URI}` `{API_KEY}` .

Доступны [примеры](computer-vision-resource-container-config.md#example-docker-run-commands) `docker run` команд.

# <a name="version-31-preview"></a>[Версия 3,1-Preview](#tab/version-3-1)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер чтения из образа контейнера.
* Выделяет 8 ядер ЦП и 18 гигабайт (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

# <a name="version-30-preview"></a>[Версия 3.0-preview](#tab/version-3)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}

```

Эта команда:

* Запускает контейнер чтения из образа контейнера.
* Выделяет 8 ядер ЦП и 18 гигабайт (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

# <a name="version-20-preview"></a>[Версия 2,0-Preview](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер чтения из образа контейнера.
* Выделяет 8 ядер ЦП и 16 гигабайт (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

---


Доступны дополнительные [примеры](./computer-vision-resource-container-config.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

Если требуется более высокая пропускная способность (например, при обработке многостраничных файлов), рассмотрите возможность развертывания нескольких контейнеров v 3.0 или v 3.1 [в кластере Kubernetes](deploy-computer-vision-on-premises.md)с помощью службы [хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-account-create) и [очереди Azure](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction).

Если вы используете службу хранилища Azure для хранения образов для обработки, можно создать [строку подключения](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string) для использования при вызове контейнера.

Чтобы найти строку подключения, выполните следующие действия.

1. Перейдите к **учетным записям хранения** на портал Azure и найдите свою учетную запись.
2. Щелкните **ключи доступа** в левом списке навигации.
3. Строка подключения будет расположена под **строкой подключения**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет интерфейсы REST API конечной точки прогнозирования запросов. 

# <a name="version-31-preview"></a>[Версия 3,1-Preview](#tab/version-3-1)

Используйте узел `http://localhost:5000` для API контейнера. Путь к Swagger можно просмотреть по адресу: `http://localhost:5000/swagger/vision-v3.1-preview-read/swagger.json` .

# <a name="version-30-preview"></a>[Версия 3.0-preview](#tab/version-3)

Используйте узел `http://localhost:5000` для API контейнера. Путь к Swagger можно просмотреть по адресу: `http://localhost:5000/swagger/vision-v3.0-preview-read/swagger.json` .

# <a name="version-20-preview"></a>[Версия 2,0-Preview](#tab/version-2)

Используйте узел `http://localhost:5000` для API контейнера. Путь к Swagger можно просмотреть по адресу: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Асинхронное чтение


# <a name="version-31-preview"></a>[Версия 3,1-Preview](#tab/version-3-1)

Операции и можно использовать `POST /vision/v3.1/read/analyze` `GET /vision/v3.1/read/operations/{operationId}` совместно для асинхронного чтения образа, аналогично тому, как служба компьютерное зрение использует соответствующие операции RESTful. Асинхронный метод POST возвратит объект `operationId` , который используется в качестве идентификатора для HTTP-запроса GET.


В пользовательском интерфейсе Swagger выберите, `asyncBatchAnalyze` чтобы развернуть его в браузере. Нажмите кнопку **попробовать**  >  **выбрать файл**. В этом примере мы будем использовать следующее изображение:

![символы табуляции и пробелы](media/tabs-vs-spaces.png)

После успешного выполнения асинхронной отправки возвращается код состояния **HTTP 202** . В качестве части ответа имеется `operation-location` заголовок, содержащий конечную точку результата для запроса.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.1/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`— Это полный URL-адрес, доступ к которому осуществляется через HTTP GET. Ниже приведен ответ JSON, посвященный выполнению `operation-location` URL-адреса из предыдущего изображения:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:30:14Z",
  "lastUpdatedDateTime": "2020-09-02T10:30:15Z",
  "analyzeResult": {
    "version": "3.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "appearance": {
              "style": "handwriting",
              "styleConfidence": 0.999
            },
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "appearance": {
              "style": "print",
              "styleConfidence": 0.603
            },
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-30-preview"></a>[Версия 3.0-preview](#tab/version-3)

Операции и можно использовать `POST /vision/v3.0/read/analyze` `GET /vision/v3.0/read/operations/{operationId}` совместно для асинхронного чтения образа, аналогично тому, как служба компьютерное зрение использует соответствующие операции RESTful. Асинхронный метод POST возвратит объект `operationId` , который используется в качестве идентификатора для HTTP-запроса GET.

В пользовательском интерфейсе Swagger выберите, `asyncBatchAnalyze` чтобы развернуть его в браузере. Нажмите кнопку **попробовать**  >  **выбрать файл**. В этом примере мы будем использовать следующее изображение:

![символы табуляции и пробелы](media/tabs-vs-spaces.png)

После успешного выполнения асинхронной отправки возвращается код состояния **HTTP 202** . В качестве части ответа имеется `operation-location` заголовок, содержащий конечную точку результата для запроса.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`— Это полный URL-адрес, доступ к которому осуществляется через HTTP GET. Ниже приведен ответ JSON, посвященный выполнению `operation-location` URL-адреса из предыдущего изображения:

```json
{
  "status": "succeeded",
  "createdDateTime": "2020-09-02T10:24:49Z",
  "lastUpdatedDateTime": "2020-09-02T10:24:50Z",
  "analyzeResult": {
    "version": "3.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.12,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "language": "",
        "lines": [
          {
            "boundingBox": [58, 42, 314, 59, 311, 123, 56, 121],
            "text": "Tabs vs",
            "words": [
              {
                "boundingBox": [85, 45, 242, 62, 241, 122, 83, 123],
                "text": "Tabs",
                "confidence": 0.981
              },
              {
                "boundingBox": [258, 64, 314, 72, 314, 123, 256, 123],
                "text": "vs",
                "confidence": 0.958
              }
            ]
          },
          {
            "boundingBox": [286, 171, 415, 165, 417, 197, 287, 201],
            "text": "paces",
            "words": [
              {
                "boundingBox": [303, 175, 415, 167, 415, 198, 306, 199],
                "text": "paces",
                "confidence": 0.918
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Версия 2,0-Preview](#tab/version-2)

Операции и можно использовать `POST /vision/v2.0/read/core/asyncBatchAnalyze` `GET /vision/v2.0/read/operations/{operationId}` совместно для асинхронного чтения образа, аналогично тому, как служба компьютерное зрение использует соответствующие операции RESTful. Асинхронный метод POST возвратит объект `operationId` , который используется в качестве идентификатора для HTTP-запроса GET.

В пользовательском интерфейсе Swagger выберите, `asyncBatchAnalyze` чтобы развернуть его в браузере. Нажмите кнопку **попробовать**  >  **выбрать файл**. В этом примере мы будем использовать следующее изображение:

![символы табуляции и пробелы](media/tabs-vs-spaces.png)

После успешного выполнения асинхронной отправки возвращается код состояния **HTTP 202** . В качестве части ответа имеется `operation-location` заголовок, содержащий конечную точку результата для запроса.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location`— Это полный URL-адрес, доступ к которому осуществляется через HTTP GET. Ниже приведен ответ JSON, посвященный выполнению `operation-location` URL-адреса из предыдущего изображения:

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

---

> [!IMPORTANT]
> При развертывании нескольких контейнеров чтения за подсистемой балансировки нагрузки, например в разделе Docker Compose или Kubernetes, необходим внешний кэш. Так как контейнер обработки и контейнер запроса GET могут отличаться, внешний кэш сохраняет результаты и разделяет их между контейнерами. Дополнительные сведения о параметрах кэша см. в разделе [Configure компьютерное зрение Container DOCKER](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-resource-container-config).

### <a name="synchronous-read"></a>Синхронное чтение

Для синхронного чтения образа можно использовать следующую операцию. 

# <a name="version-31-preview"></a>[Версия 3,1-Preview](#tab/version-3-1)

`POST /vision/v3.1/read/syncAnalyze` 

# <a name="version-30-preview"></a>[Версия 3.0-preview](#tab/version-3)

`POST /vision/v3.0/read/syncAnalyze`

# <a name="version-20-preview"></a>[Версия 2,0-Preview](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Когда изображение считывается целиком, тогда API возвращает ответ JSON. Единственное исключение — при возникновении ошибки. При возникновении ошибки возвращается следующий код JSON:

```json
{
    "status": "Failed"
}
```

Объект ответа JSON имеет тот же граф объектов, что и асинхронная версия. Если вы являетесь пользователем JavaScript и хотите обеспечить безопасность типа, рассмотрите возможность использования TypeScript для приведения ответа JSON.

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

## <a name="summary"></a>Сводка

Из этой статьи вы узнали основные понятия и рабочий процесс, позволяющий скачивать, устанавливать и запускать контейнеры компьютерного зрения. В разделе "Сводка" сделайте следующее.

* Компьютерное зрение предоставляет контейнер Linux для DOCKER, инкапсулирующий чтение.
* Образы контейнеров загружаются из реестра контейнеров "предварительный просмотр контейнера" в Azure.
* Образы контейнеров выполняются в Docker.
* Вы можете использовать REST API или пакет SDK для вызова операций в контейнерах чтения, указав универсальный код ресурса (URI) узла контейнера.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например анализируемые изображения или тексты).

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [конфигурации контейнеров](computer-vision-resource-container-config.md).
* Дополнительные сведения о распознавании печатного и рукописного текста см. в статье [Обзор компьютерного зрения](overview.md).
* Дополнительные сведения о методах, поддерживаемых контейнером, см. в статье [API компьютерного зрения](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).
* Чтобы разрешать проблемы, связанные с функциональностью Компьютерного зрения, ознакомьтесь с [часто задаваемыми вопросами](FAQ.md).
* Использование большего числа [контейнеров Cognitive Services](../cognitive-services-container-support.md)
