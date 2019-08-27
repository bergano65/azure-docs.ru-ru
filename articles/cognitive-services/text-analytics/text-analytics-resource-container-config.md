---
title: Настройка контейнеров — Анализ текста
titleSuffix: Azure Cognitive Services
description: Анализ текста предоставляет контейнер с общей платформой конфигурации, что позволяет легко настроить хранилище, ведение журнала, данные телеметрии и параметры безопасности для контейнеров, а также управлять ими.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: f1c42002343de1dd3b3ef6b9c9e35f458db925f4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051130"
---
# <a name="configure-text-analytics-docker-containers"></a>Настройка контейнеров Docker API анализа текста

Анализ текста предоставляет контейнер с общей платформой конфигурации, что позволяет легко настроить хранилище, ведение журнала, данные телеметрии и параметры безопасности для контейнеров, а также управлять ими.

## <a name="configuration-settings"></a>Параметры конфигурации

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Параметры [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) и [`Eula`](#eula-setting) используются совместно, и для всех трех параметров необходимо указать допустимые значения. В противном случае контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов для контейнера. Значение ApiKey является обязательным и должно содержать допустимый ключ ресурса службы _Анализ текста_, который определяется в параметре конфигурации [`Billing`](#billing-configuration-setting).

Этот параметр можно найти в следующем месте.

* Портал Azure: **Анализ текста** управление ресурсами в разделе **ключи**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

Параметр `Billing` определяет URI конечной точки ресурса _Анализа текста_ на Azure, используемый, чтобы измерять данные для выставления счетов для контейнера. Для этого параметра конфигурации необходимо задать значение, которое должно быть допустимым URI конечной точки для ресурса _Анализа текста_ в Azure. Отчеты об использовании контейнера примерно каждые 10—15 минут.

Этот параметр можно найти в следующем месте.

* Портал Azure: **Анализ текста** Общие сведения, помеченные`Endpoint`

|Обязательное значение| Название | Тип данных | Описание |
|--|------|-----------|-------------|
|Да| `Billing` | Строковое | Требуемый URI конечной точки выставления счетов |

## <a name="eula-setting"></a>Параметр Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Параметры Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Параметры учетных данных прокси-сервера HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Параметры ведения журнала
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Параметры подключения

Используйте подключения привязок для чтения данных из контейнера и записи в него. Вы можете указать входное или выходное подключение, указав параметр `--mount` в команде [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Контейнеры API анализа текста не используют входные или выходные подключения для хранения учебных данных или данных службы. 

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того,расположение подключения на [главном компьютере](how-tos/text-analytics-how-to-install-containers.md#the-host-computer) может оказаться недоступным из-за конфликта между разрешениями для учетной записи службы Docker и расположения подключения к узлу. 

|Необязательный| Название | Тип данных | Описание |
|-------|------|-----------|-------------|
|Не разрешено| `Input` | Строковое | Контейнеры API анализа текста не используют этот элемент.|
|Необязательный| `Output` | Строковое | Цель выходного подключения. Значение по умолчанию — `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run 

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](how-tos/text-analytics-how-to-install-containers.md#stop-the-container).

* **Символ продолжения строки**. В командах Docker в следующих разделах используется обратная косая черта (`\`) как символ продолжения строки. Замените или удалите ее в соответствии с требованиями вашей операционной системы. 
* **Порядок аргументов**. Не изменяйте порядок аргументов, если вы еще не очень хорошо знакомы с контейнерами Docker.

Замените строку {_имя_аргумента_} собственными значениями.

| Местозаполнитель | Значение | Формат или пример |
|-------------|-------|---|
| **{API_KEY}** | Ключ `Text Analytics` конечной точки ресурса, доступный на странице ключей Azure `Text Analytics` . |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Значение конечной точки выставления счетов доступно на странице `Text Analytics` обзора Azure.| См. раздел [сбор обязательных параметров](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) для явных примеров. |

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](how-tos/text-analytics-how-to-install-containers.md#billing).
> Значение apiKey является **ключом** на странице ключей ресурсов Azure `Text Analytics` . 

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Извлечение ключевых фраз](#tab/keyphrase).

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detectiontablanguage"></a>[Распознавание языка](#tab/language).

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Анализ тональности](#tab/sentiment).

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Следующие шаги

* Изучите статью об [установке и запуске контейнеров](how-tos/text-analytics-how-to-install-containers.md).
* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../cognitive-services-container-support.md)
