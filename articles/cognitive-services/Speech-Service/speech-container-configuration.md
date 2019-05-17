---
title: Настройка контейнеров речи
titleSuffix: Azure Cognitive Services
description: Контейнер речи
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: diberry
ms.openlocfilehash: e2ed29bb61f553f68b9f9802884169361d5d983f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65797929"
---
# <a name="configure-speech-service-containers"></a>Настройка контейнеров службы распознавания речи

Контейнеры речи Предоставьте клиентам возможность создавать один архитектура приложения речи, оптимизированный для преимущества надежные облачные возможности и Населенный пункт edge. Два речи контейнеры, мы теперь поддерживаем — **речи в текст** и **преобразования текста в речь**. 

**Речи** среду выполнения контейнера настраивается с помощью `docker run` аргументы команды. Контейнер поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные параметры выставления счетов. 

# <a name="configuration-settings"></a>Параметры конфигурации

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Параметры [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) и [`Eula`](#eula-setting) используются совместно, и для всех трех параметров необходимо указать допустимые значения. В противном случае контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов для контейнера. Необходимо указать значение для ApiKey и оно должно быть допустимый ключ для _речи_ ресурс, заданный для [ `Billing` ](#billing-configuration-setting) параметр конфигурации.

Этот параметр можно найти в следующем месте.

* Портал Azure: **В речи** управления ресурсами в разделе **ключи**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

`Billing` Параметр указывает URI конечной точки из _речи_ ресурсов в Azure позволяет контролировать использование выставления счетов для контейнера. Необходимо указать значение для этого параметра конфигурации, а значение должно быть допустимым URI конечной точки для _речи_ ресурсов в Azure. Отчеты об использовании контейнера примерно каждые 10—15 минут.

Этот параметр можно найти в следующем месте.

* Портал Azure: **В речи** Обзор, с меткой `Endpoint`

|Обязательно для заполнения| ИМЯ | Тип данных | Описание |
|--|------|-----------|-------------|
|Да| `Billing` | Строка | URI конечной точки выставления счетов<br><br>Пример:<br>`Billing=https://westus.api.cognitive.microsoft.com/sts/v1.0` |

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

Контейнеры речи не используются входные данные или подключает выходных данных для хранения обучения или службы данных. 

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того,расположение подключения на [главном компьютере](speech-container-howto.md#the-host-computer) может оказаться недоступным из-за конфликта между разрешениями для учетной записи службы Docker и расположения подключения к узлу. 

|Необязательно| ИМЯ | Тип данных | Описание |
|-------|------|-----------|-------------|
|Не разрешено| `Input` | Строка | Контейнеры речи не следует использовать.|
|Необязательно| `Output` | Строка | Цель выходного подключения. По умолчанию используется значение `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run 

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](speech-container-howto.md#stop-the-container).

* **Символ продолжения строки**. В командах Docker в следующих разделах используется обратная косая черта (`\`) как символ продолжения строки. Замените или удалите ее в соответствии с требованиями вашей операционной системы. 
* **Порядок аргументов**. Не изменяйте порядок аргументов, если вы не являетесь уверенным пользователем контейнеров Docker.

Замените строку {_имя_аргумента_} собственными значениями.

| Местозаполнитель | Value | Формат или пример |
|-------------|-------|---|
|{BILLING_KEY} | Ключ конечной точки ресурса речи. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Количество выставленных счетов за конечную точку, включая регион.|`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing-configuration-setting).
> Значение ApiKey **ключ** на странице ключей Azure речи ресурсов. 

## <a name="speech-container-docker-examples"></a>Примеры Docker для контейнеров речи

В следующих примерах Docker предназначены для контейнера речи. 

### <a name="basic-example-for-speech-to-text"></a>Простой пример для распознавания речи в текст

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
```

### <a name="basic-example-for-text-to-speech"></a>Простой пример для преобразования текста в речь

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
```

### <a name="logging-example-for-speech-to-text"></a>Пример ведения журнала для распознавания речи в текст

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}   
  Logging:Console:LogLevel:Default=Information
```

### <a name="logging-example-for-text-to-speech"></a>Пример ведения журнала для преобразования текста в речь

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}  
  Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Дальнейшие действия

* Изучите статью об [установке и запуске контейнеров](speech-container-howto.md).