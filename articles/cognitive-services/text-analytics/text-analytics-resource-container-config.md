---
title: Настройка контейнеров - Текстовая аналитика
titleSuffix: Azure Cognitive Services
description: Анализ текста предоставляет контейнер с общей платформой конфигурации, что позволяет легко настроить хранилище, ведение журнала, данные телеметрии и параметры безопасности для контейнеров, а также управлять ими.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 8a39327275dca43ddb6ce0e46a3e3bb51ec4555b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795310"
---
# <a name="configure-text-analytics-docker-containers"></a>Настройка контейнеров Docker API анализа текста

Анализ текста предоставляет контейнер с общей платформой конфигурации, что позволяет легко настроить хранилище, ведение журнала, данные телеметрии и параметры безопасности для контейнеров, а также управлять ими.

## <a name="configuration-settings"></a>Параметры конфигурации

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> , [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)и [`Eula`](#eula-setting) настройки используются вместе, и вы должны предоставить действительные значения для всех трех из них; в противном случае ваш контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](how-tos/text-analytics-how-to-install-containers.md#billing).

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов для контейнера. Значение ApiKey является обязательным и должно содержать допустимый ключ ресурса службы _Анализ текста_, который определяется в параметре конфигурации [`Billing`](#billing-configuration-setting).

Этот параметр можно найти в следующем месте.

* Портал Azure: Управление **ресурсами text Analytics** под **руководством ключей**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

Параметр `Billing` определяет URI конечной точки ресурса _Анализа текста_ на Azure, используемый, чтобы измерять данные для выставления счетов для контейнера. Для этого параметра конфигурации необходимо задать значение, которое должно быть допустимым URI конечной точки для ресурса _Анализа текста_ в Azure. Отчеты об использовании контейнера примерно каждые 10—15 минут.

Этот параметр можно найти в следующем месте.

* Портал Azure: **Обзор анализа текста,** помеченный`Endpoint`

|Обязательно| name | Тип данных | Описание |
|--|------|-----------|-------------|
|Да| `Billing` | Строка | Биллинг endpoint URI. Для получения дополнительной информации о получении биллинга URI [см.](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) Дополнительные сведения и полный список региональных конечных точек см. в статье [Custom subdomain names for Cognitive Services](../cognitive-services-custom-subdomains.md) (Пользовательские имена поддоменов для Cognitive Services). |

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

|Необязательный| name | Тип данных | Описание |
|-------|------|-----------|-------------|
|Не разрешено| `Input` | Строка | Контейнеры API анализа текста не используют этот элемент.|
|Необязательный| `Output` | Строка | Цель выходного подключения. Значение по умолчанию — `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run 

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](how-tos/text-analytics-how-to-install-containers.md#stop-the-container).

* **Характер продолжения линии**: Команды докера в следующих `\`разделах используют заднюю слэш, в качестве символа продолжения линии. Замените или удалите ее в соответствии с требованиями вашей операционной системы. 
* **Порядок аргумента**: Не изменяйте порядок аргументов, если вы не очень хорошо знакомы с контейнерами докеров.

Замените строку {_имя_аргумента_} собственными значениями.

| Заполнитель | Значение | Формат или пример |
|-------------|-------|---|
| **(API_KEY)** | Ключ конечной точки `Text Analytics` ресурса доступен `Text Analytics` на странице Azure Keys. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **(ENDPOINT_URI)** | Значение конечных точек выставления `Text Analytics` счетов доступно на странице Azure Overview.| [Ознакомьтесь со сбором необходимых параметров](how-tos/text-analytics-how-to-install-containers.md#gathering-required-parameters) для явных примеров. |

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](how-tos/text-analytics-how-to-install-containers.md#billing).
> Значение ApiKey — это **ключ** `Text Analytics` со страницы ключей ресурса Azure. 

#### <a name="key-phrase-extraction"></a>[Извлечение ключевых фраз](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-docker-examples](includes/key-phrase-extraction-docker-examples.md)]

#### <a name="language-detection"></a>[Распознавание языка](#tab/language)

[!INCLUDE [language-detection-docker-examples](includes/language-detection-docker-examples.md)]

#### <a name="sentiment-analysis"></a>[Анализ настроений](#tab/sentiment)

[!INCLUDE [sentiment-analysis-docker-examples](includes/sentiment-analysis-docker-examples.md)]

***

## <a name="next-steps"></a>Дальнейшие действия

* Изучите статью об [установке и запуске контейнеров](how-tos/text-analytics-how-to-install-containers.md).
* Используйте больше [контейнеров когнитивных услуг](../cognitive-services-container-support.md)
