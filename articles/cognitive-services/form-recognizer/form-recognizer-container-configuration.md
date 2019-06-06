---
title: Настройка контейнера Распознавателя документов
titleSuffix: Azure Cognitive Services
description: Узнайте, как настраивать контейнер Распознавателя документов для синтаксического анализа данных форм и таблиц.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/31/2019
ms.author: pafarley
ms.openlocfilehash: 28acc2d1eafacb9e53fac3e3cce092738401f838
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475380"
---
# <a name="configure-form-recognizer-containers"></a>Настройка контейнера Распознавателя документов

Контейнеры Распознавателя документов позволяют клиентам выполнять сборку архитектуры приложения, оптимизированной для эффективной работы как с надежными возможностями облака, так и с пограничным расположением.

Среда выполнения контейнера **Распознавателя документов** настраивается с помощью аргументов команды `docker run`. Контейнер поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные параметры выставления счетов.

## <a name="configuration-settings"></a>Параметры конфигурации

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Параметры [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) и [`Eula`](#eula-setting) используются совместно, и для всех трех параметров необходимо указать допустимые значения. В противном случае контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов для контейнера. Значение ApiKey является обязательным и должно содержать допустимый ключ ресурса службы _Распознаватель документов_, который определяется в параметре конфигурации [`Billing`](#billing-configuration-setting).

Этот параметр можно найти в следующем месте.

* Портал Azure: Управление ресурсами **Распознавателя документов** в разделе **Ключи**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

Параметр `Billing` определяет URI конечной точки ресурса _Распознавателя документов_ в Azure. Этот параметр используется для контроля данных, связанных с контейнером, для выставления счетов. Для этого параметра конфигурации необходимо задать значение, которое должно быть допустимым URI конечной точки для ресурса _Распознавателя документов_ в Azure. Отчеты об использовании контейнера примерно каждые 10—15 минут.

Этот параметр можно найти в следующем месте.

* Портал Azure: Обзор **Распознавателя документов** с меткой `Endpoint`

|Обязательно| ИМЯ | Тип данных | ОПИСАНИЕ |
|--|------|-----------|-------------|
|Да| `Billing` | Строка | URI конечной точки выставления счетов<br><br>Пример:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

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

Для работы контейнера Распознавателя документов требуется входное и выходное подключение. Входное подключение может быть только для чтения и необходимо для доступа к данным, которые будут использоваться для обучения и оценки. Выходное подключение должно быть доступным для хранения и будет использоваться для хранения моделей и временных данных.

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того, расположение подключения на [главном компьютере](form-recognizer-container-howto.md#the-host-computer) может оказаться недоступным из-за конфликта между разрешениями для учетной записи службы Docker и расположением подключения к узлу.

|Необязательно| ИМЯ | Тип данных | ОПИСАНИЕ |
|-------|------|-----------|-------------|
|Обязательно| `Input` | Строка | Цель входного подключения. По умолчанию используется значение `/input`.    <br><br>Пример:<br>`--mount type=bind,src=c:\input,target=/input`|
|Обязательно| `Output` | Строка | Цель выходного подключения. По умолчанию используется значение `/output`.  <br><br>Пример:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](form-recognizer-container-howto.md#stop-the-container).

* **Символ продолжения строки**. В командах Docker в следующих разделах используется обратная косая черта (`\`) как символ продолжения строки. Замените или удалите ее в соответствии с требованиями вашей операционной системы.
* **Порядок аргументов**. Не изменяйте порядок аргументов, если вы не являетесь уверенным пользователем контейнеров Docker.

Замените строку {_имя_аргумента_} собственными значениями.

| Placeholder | Значение |
|-------------|-------|
|{BILLING_KEY} | Этот ключ используется для запуска контейнера и доступен на странице ключей Распознавателя документов на портале Azure.  |
|{BILLING_ENDPOINT_URI} | Значение URI конечной точки выставления счетов доступно на странице обзора Распознавателя документов на портале Azure.|
|{COMPUTER_VISION_API_KEY}| Ключ доступен на странице ключей API Компьютерного зрения на портале Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|Конечная точка выставления счетов. При использовании облачного ресурса Компьютерного зрения значение URI доступно на странице обзора API Компьютерного зрения на портале Azure. Если вы используете контейнер `cognitive-services-recognize-text`, используйте URL-адрес конечной точки выставления счетов, переданный контейнеру в команде `docker run`.|

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing-configuration-setting).
> Значение ApiKey — это **ключ** со страницы ключей ресурса Распознавателя документов Azure.

## <a name="form-recognizer-container-docker-examples"></a>Примеры контейнера Docker Распознавателя документов

Следующие примеры Docker предназначены для контейнера Распознавателя документов.

### <a name="basic-example-for-form-recognizer"></a>Простой пример для Распознавателя документов

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Пример записи в журнал для Распознавателя документов

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Дополнительная информация

* Изучите статью об [установке и запуске контейнеров](form-recognizer-container-howto.md).
