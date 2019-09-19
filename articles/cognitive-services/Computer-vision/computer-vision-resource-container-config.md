---
title: Настройка контейнеров — компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Настройка различных параметров для контейнеров распознавания текста в функции компьютерного зрения.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 186f2f60aad15b336265114d7c85c757e0dd333f
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102288"
---
# <a name="configure-recognize-text-docker-containers"></a>Настройка контейнера Распознавания текста в Docker

Среда выполнения контейнера **Распознавание текста** настраивается с помощью аргументов команды `docker run`. Контейнер поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные параметры выставления счетов. 

## <a name="configuration-settings"></a>Параметры конфигурации

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Параметры [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) и [`Eula`](#eula-setting) используются совместно, и для всех трех параметров необходимо указать допустимые значения. В противном случае контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

Параметр указывает ключ ресурса Azure `Cognitive Services` , используемый для трассировки сведений о выставлении счетов для контейнера. `ApiKey` Необходимо указать значение для параметра apiKey, а значение должно быть допустимым ключом для ресурса _Cognitive Services_ , указанного для [`Billing`](#billing-configuration-setting) параметра конфигурации.

Этот параметр можно найти в следующем месте.

* Портал Azure: **Cognitive Services** Управление ресурсами в разделе **ключи**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

Параметр указывает URI конечной точки ресурса Cognitive Services в Azure, который используется для отслеживания сведений о выставлении счетов для контейнера. `Billing` Необходимо указать значение для этого параметра конфигурации, а значение должно быть допустимым URI конечной точки для ресурса _Cognitive Services_ в Azure. Отчеты об использовании контейнера примерно каждые 10—15 минут.

Этот параметр можно найти в следующем месте.

* Портал Azure: **Cognitive Services** Общие сведения, помеченные`Endpoint`

Не забудьте добавить `vision/v1.0` маршрут к универсальному коду ресурса (URI) конечной точки, как показано в следующей таблице. 

|Обязательное значение| Название | Тип данных | Описание |
|--|------|-----------|-------------|
|Да| `Billing` | Строковое | URI конечной точки выставления счетов<br><br>Пример:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

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

Контейнеры API компьютерного зрения не используют входные или выходные подключения для хранения учебных данных или данных службы. 

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того, расположение подключения на [главном компьютере](computer-vision-how-to-install-containers.md#the-host-computer) может оказаться недоступным из-за конфликта между разрешениями для учетной записи службы Docker и расположением подключения к узлу. 

|Необязательный| Название | Тип данных | Описание |
|-------|------|-----------|-------------|
|Не разрешено| `Input` | Строковое | Контейнеры API компьютерного зрения не используют этот элемент.|
|Необязательный| `Output` | Строковое | Цель выходного подключения. Значение по умолчанию — `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run 

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](computer-vision-how-to-install-containers.md#stop-the-container).

* **Символ продолжения строки**. В командах Docker в следующих разделах используется обратная косая черта (`\`) как символ продолжения строки. Замените или удалите ее в соответствии с требованиями вашей операционной системы. 
* **Порядок аргументов**. Не изменяйте порядок аргументов, если вы не являетесь уверенным пользователем контейнеров Docker.

Не забудьте добавить `vision/v1.0` маршрут к универсальному коду ресурса (URI) конечной точки, как показано в следующей таблице. 

Замените строку {_имя_аргумента_} собственными значениями.

| Местозаполнитель | Значение | Формат или пример |
|-------------|-------|---|
| **{API_KEY}** | Ключ `Computer Vision` конечной точки ресурса на странице ключей Azure `Computer Vision` . | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Значение конечной точки выставления счетов доступно на странице `Computer Vision` обзора Azure.| См. раздел [сбор обязательных параметров](computer-vision-how-to-install-containers.md#gathering-required-parameters) для явных примеров. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](computer-vision-how-to-install-containers.md#billing).
> Значение apiKey является **ключом** на странице ключей ресурсов Azure `Cognitive Services` . 

## <a name="recognize-text-container-docker-examples"></a>Примеры распознавания текста контейнера Docker

Следующие примеры Docker предназначены для контейнера распознавания текста. 

### <a name="basic-example"></a>Простой пример 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Пример ведения журнала 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Следующие шаги

* Изучите статью об [установке и запуске контейнеров](computer-vision-how-to-install-containers.md).
