---
title: Установка и запуск контейнеров — Анализ текста
titleSuffix: Azure Cognitive Services
description: В этом пошаговом руководстве показано, как скачивать, устанавливать и выполнять контейнеры для Анализа текста.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dapine
ms.openlocfilehash: f658e8d0f820ccec513b5665fc1ce94c083c3b3e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703524"
---
# <a name="install-and-run-text-analytics-containers"></a>Установка и запуск контейнеров API анализа текста

Контейнеры позволяют запускать API анализа текста в собственной среде и прекрасно подходят для конкретных требований к безопасности и управлению данными. Контейнеры Анализ текста обеспечивают расширенную обработку естественного языка по необработанному тексту и включают три основные функции: анализ тональностиности, извлечение ключевых фраз и определение языка. Связывание сущностей в настоящее время не поддерживается в контейнере.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительные требования

Чтобы запустить любой из Анализ текста контейнеров, необходимо иметь главный компьютер и среды контейнеров.

## <a name="preparation"></a>Подготовка

Прежде чем использовать контейнеры Анализа текста, необходимо выполнить следующие условия:

|Обязательное значение|Цель|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс API анализа текста |Для использования контейнера необходимо следующее:<br><br>Ресурс Azure [анализ текста](../../cognitive-services-apis-create-account.md) для получения соответствующего ключа API и URI конечной точки. Оба значения доступны на страницах "Обзор" и "Ключи" API анализа текста на портале Azure и необходимы для запуска контейнера.<br><br>**{API_KEY}** : Один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}** : Конечная точка, указанная на странице **обзора**|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальные и рекомендуемые требования к ЦП (минимум 2,6 ГГц или быстрее) и памяти, в ГБ, для выделения для каждого контейнера Анализа текста.

| Контейнер | Минимум | Рекомендуется | ПЛАТЫ<br>(Минимум, максимум)|
|-----------|---------|-------------|--|
|Извлечение ключевых фраз | 1 ядро, 2 ГБ памяти | 1 ядро, 4 ГБ памяти |15, 30|
|Распознавание языка | 1 ядро, 2 ГБ памяти | 1 ядро, 4 ГБ памяти |15, 30|
|Анализ тональности 2. x | 1 ядро, 2 ГБ памяти | 1 ядро, 4 ГБ памяти |15, 30|
|Анализ тональности 3. x | 1 ядро, 2 ГБ памяти | 4 ядра, 4 ГБ памяти |15, 30|

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* TPS — транзакций в секунду.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Образы контейнеров для Анализа текста доступны в Реестре контейнеров Майкрософт.

| Контейнер | Репозиторий |
|-----------|------------|
|Извлечение ключевых фраз | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Распознавание языка | `mcr.microsoft.com/azure-cognitive-services/language` |
|Анализ тональности 2. x| `mcr.microsoft.com/azure-cognitive-services/sentiment` |
|Анализ тональности 3. x| `containerpreview.azurecr.io/microsoft/cognitive-services-sentiment-v3.0` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Используйте команду, чтобы скачать образ контейнера из реестра контейнеров Майкрософт.

Полное описание доступных тегов для контейнеров Анализа текста см. в следующих контейнерах в центре Docker:

* [Извлечение ключевых фраз](https://go.microsoft.com/fwlink/?linkid=2018757).
* [Распознавание языка](https://go.microsoft.com/fwlink/?linkid=2018759).
* [Анализ тональности](https://go.microsoft.com/fwlink/?linkid=2018654).

Воспользуйтесь командой [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>docker pull для контейнера извлечения ключевой фразы

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>docker pull для контейнера определения языка

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-2x-container"></a>Извлечение DOCKER для контейнера тональности 2. x

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

### <a name="docker-pull-for-the-sentiment-3x-container"></a>Извлечение DOCKER для контейнера тональности 3. x

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-sentiment-v3.0:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](../text-analytics-resource-container-config.md#example-docker-run-commands) команды `docker run`.
1. Запросите конечную точку прогнозирования контейнера для [v2](#query-the-containers-v2-prediction-endpoint) или [v3](#query-the-containers-v3-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска любого из трех контейнеров. Дополнительные сведения о том, как получить значения и `{API_Key}` , `{Endpoint_URI}` см. в разделе [сбор обязательных параметров](#gathering-required-parameters) .

[Доступны примеры](../text-analytics-resource-container-config.md#example-docker-run-commands) `docker run` команд.

### <a name="run-v2-container-example-of-docker-run-command"></a>Пример выполнения команды для контейнера "выполнение" в командной строки DOCKER

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* запускает контейнер ключевой фразы из образа контейнера;
* выделяет одно ядро ЦП и 4 ГБ памяти;
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

### <a name="run-v3-container-example-of-docker-run-command"></a>Пример выполнения команды "V3 Container" в команде DOCKER Run

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-sentiment-v3.0 \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Эта команда:

* запускает контейнер ключевой фразы из образа контейнера;
* Выделяет 4 ядра ЦП и 4 гигабайт (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-v2-prediction-endpoint"></a>Запрос конечной точки прогнозирования версии 2 контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST.

Используйте узел `https://localhost:5000` для API контейнера.

## <a name="query-the-containers-v3-prediction-endpoint"></a>Запрос конечной точки прогнозирования v3 в контейнере

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST.

Используйте узел `https://localhost:5000` для API контейнера.

### <a name="v3-api-request-post-body"></a>Текст запроса POST API V3

Следующий код JSON является примером сообщения POST запроса API V3:

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Hello world. This is some input text that I love."
    },
    {
      "language": "en",
      "id": "2",
      "text": "It's incredibly sunny outside! I'm so happy."
    }
  ]
}
```

### <a name="v3-api-response-body"></a>Текст ответа API V3

Следующий код JSON является примером сообщения POST запроса API V3:

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](../text-analytics-resource-container-config.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера.

## <a name="billing"></a>Выставление счетов

Контейнеры API анализа текста отправляют данные для выставления счетов в Azure с помощью ресурса _API анализа текста_ в учетной записи Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье вы узнали основные понятия и рабочий процесс для скачивания, установки и выполнения контейнеров Анализа текста. В разделе "Сводка" сделайте следующее.

* Анализ текста предоставляет три контейнера Linux для Docker, инкапсулируя извлечение ключевых фраз, определение языка и анализ тональности.
* Образы контейнеров скачиваются из Реестра контейнеров (Майкрософт) в Azure.
* Образы контейнеров выполняются в Docker.
* Указав узел URI контейнера, пакет SDK или REST API можно использовать для вызова операций в контейнерах Анализа текста.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например, анализируемые изображения или тексты).

## <a name="next-steps"></a>Следующие шаги

* Ознакомьтесь со статьей о [конфигурации контейнеров](../text-analytics-resource-container-config.md).
* Чтобы решить проблемы, связанные с функциональностью, ознакомьтесь с [часто задаваемыми вопросами](../text-analytics-resource-faq.md).
