---
title: Настройка контейнера для API детектора аномалий
titleSuffix: Azure Cognitive Services
description: Среда выполнения контейнера API детектора аномалий настраивается с `docker run` помощью аргументов команды. Контейнер поддерживает несколько обязательных и несколько необязательных параметров.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 4a961080bc124e53a8c5fe4dcc5f3cd6f21e9e5c
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102574"
---
# <a name="configure-anomaly-detector-containers"></a>Настройка контейнеров Детектора аномалий

Среда выполнения контейнера детектора аномалий настраивается с `docker run` помощью аргументов команды. Контейнер поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные параметры выставления счетов. 

## <a name="configuration-settings"></a>Параметры конфигурации

К контейнеру применяются следующие параметры конфигурации.

|Обязательное значение|Параметр|Цель|
|--|--|--|
|Да|[ApiKey](#apikey-configuration-setting)|Используется для отслеживания данных для выставлении счетов.|
|Нет|[ApplicationInsights](#applicationinsights-setting)|Позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Да|[Выставление счетов](#billing-configuration-setting)|Задает URI конечной точки для ресурса службы в Azure.|
|Да|[Лицензионное соглашение](#eula-setting)| Указывает, что вы приняли условия лицензии для контейнера.|
|Нет|[Fluentd](#fluentd-settings)|Записывает данные в журнал и (необязательно) передает метрики на сервер Fluentd.|
|Нет|[прокси-серверу HTTP](#http-proxy-credentials-settings)|Настраивает прокси-сервер HTTP для исходящих запросов.|
|Нет|[Logging](#logging-settings)|Обеспечивает поддержку ведения журнала ASP.NET Core для вашего контейнера. |
|Нет|[Подключения](#mount-settings)|Читает и записывает данные с главного компьютера в контейнер и обратно.|

> [!IMPORTANT]
> Параметры [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) и [`Eula`](#eula-setting) используются совместно, и для всех трех параметров необходимо указать допустимые значения. В противном случае контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов для контейнера. Необходимо указать значение для параметра apiKey, а значение должно быть допустимым ключом для ресурса детектора _аномалий_ , [`Billing`](#billing-configuration-setting) указанного для параметра конфигурации.

Этот параметр можно найти в следующем месте.

* Портал Azure: **Детектор аномалий** Управление ресурсами в разделе **ключи**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

Параметр указывает URI конечной точки ресурса детектора аномалий в Azure, который используется для отслеживания сведений о выставлении счетов для контейнера. `Billing` Необходимо указать значение для этого параметра конфигурации, а значение должно быть допустимым URI конечной точки для ресурса детектора _аномалий_ в Azure.

Этот параметр можно найти в следующем месте.

* Портал Azure: **Детектор аномалий** Общие сведения, помеченные`Endpoint`

|Обязательное значение| Название | Тип данных | Описание |
|--|------|-----------|-------------|
|Да| `Billing` | Строковое | URI конечной точки выставления счетов<br><br>Пример:<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

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

Контейнеры детекторов аномалий не используют входные или выходные подключения для хранения данных для обучения или службы. 

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того, расположение подключения на [главном компьютере](anomaly-detector-container-howto.md#the-host-computer) может оказаться недоступным из-за конфликта между разрешениями для учетной записи службы Docker и расположением подключения к узлу. 

|Необязательный| Название | Тип данных | Описание |
|-------|------|-----------|-------------|
|Не разрешено| `Input` | Строковое | В контейнерах детекторов аномалий это не используется.|
|Необязательный| `Output` | Строковое | Цель выходного подключения. Значение по умолчанию — `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run 

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](anomaly-detector-container-howto.md#stop-the-container).

* **Символ продолжения строки**. В командах Docker в следующих разделах используется обратная косая черта (`\`) как символ продолжения строки для оболочки bash. Замените или удалите ее в соответствии с требованиями вашей операционной системы. Например, символ продолжения строки для Windows — это крышка (`^`). Замените обратную косую черту крышкой. 
* **Порядок аргументов**. Не изменяйте порядок аргументов, если вы не являетесь уверенным пользователем контейнеров Docker.

Замените значение в квадратных скобках `{}`собственными значениями:

| Местозаполнитель | Значение | Формат или пример |
|-------------|-------|---|
| **{API_KEY}** | Ключ `Anomaly Detector` конечной точки ресурса на странице ключей Azure `Anomaly Detector` . | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Значение конечной точки выставления счетов доступно на странице `Anomaly Detector` обзора Azure.| См. раздел [сбор обязательных параметров](anomaly-detector-container-howto.md#gathering-required-parameters) для явных примеров. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](anomaly-detector-container-howto.md#billing).
> Значение ApiKey является **ключом** на странице "ключи ресурсов детектора аномалий Azure". 

## <a name="anomaly-detector-container-docker-examples"></a>Примеры DOCKER для контейнера детекторов

Следующие примеры DOCKER предназначены для контейнера детекторов аномалий. 

### <a name="basic-example"></a>Простой пример 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Пример настройки ведения журнала через аргументы командной строки

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={ENDPOINT_URI} ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Следующие шаги

* [Развертывание контейнера детекторов аномалий в службе "экземпляры контейнеров Azure"](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Дополнительные сведения о службе API детектора аномалий](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
