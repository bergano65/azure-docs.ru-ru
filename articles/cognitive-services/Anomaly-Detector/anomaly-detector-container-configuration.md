---
title: Настройка контейнеров — средства обнаружения аномалий
titleSuffix: Azure Cognitive Services
description: Среда выполнения контейнера обнаружения аномалий настраивается с помощью `docker run` аргументы команды. Контейнер поддерживает несколько обязательных и несколько необязательных параметров.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/07/2019
ms.author: aahi
ms.openlocfilehash: 0d09ce29aa5431de3eb82e5d9fe7440d4e3352e1
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026394"
---
# <a name="configure-anomaly-detector-containers"></a>Настройка обнаружения аномалий контейнеров

**Обнаружения аномалий** среду выполнения контейнера настраивается с помощью `docker run` аргументы команды. Контейнер поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные параметры выставления счетов. 

# <a name="configuration-settings"></a>Параметры конфигурации

К контейнеру применяются следующие параметры конфигурации.

|Обязательно для заполнения|Параметр|Назначение|
|--|--|--|
|Да|[ApiKey](#apikey-configuration-setting)|Используется для отслеживания данных для выставлении счетов.|
|Нет |[ApplicationInsights](#applicationinsights-setting)|Позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Да|[Выставление счетов](#billing-configuration-setting)|Задает URI конечной точки для ресурса службы в Azure.|
|Да|[Лицензионное соглашение](#eula-setting)| Указывает, что вы приняли условия лицензии для контейнера.|
|Нет |[Fluentd](#fluentd-settings)|Записывает данные в журнал и (необязательно) передает метрики на сервер Fluentd.|
|Нет |[прокси-серверу HTTP](#http-proxy-credentials-settings)|Настраивает прокси-сервер HTTP для исходящих запросов.|
|Нет |[Logging](#logging-settings)|Обеспечивает поддержку ведения журнала ASP.NET Core для вашего контейнера. |
|Нет |[Подключения](#mount-settings)|Читает и записывает данные с главного компьютера в контейнер и обратно.|

> [!IMPORTANT]
> Параметры [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) и [`Eula`](#eula-setting) используются совместно, и для всех трех параметров необходимо указать допустимые значения. В противном случае контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов для контейнера. Необходимо указать значение для ApiKey и оно должно быть допустимый ключ для _обнаружения аномалий_ ресурс, заданный для [ `Billing` ](#billing-configuration-setting) параметр конфигурации.

Этот параметр можно найти в следующем месте.

* Портал Azure: **Средства обнаружения аномалий** управления ресурсами в разделе **ключи**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

`Billing` Параметр указывает URI конечной точки из _обнаружения аномалий_ ресурсов в Azure позволяет контролировать использование выставления счетов для контейнера. Необходимо указать значение для этого параметра конфигурации, а значение должно быть допустимым URI конечной точки для _обнаружения аномалий_ ресурсов в Azure.

Этот параметр можно найти в следующем месте.

* Портал Azure: **Средства обнаружения аномалий** Обзор, с меткой `Endpoint`

|Обязательно для заполнения| ИМЯ | Тип данных | ОПИСАНИЕ |
|--|------|-----------|-------------|
|Да| `Billing` | Строка | URI конечной точки выставления счетов<br><br>Пример:<br>`Billing=https://westus2.api.cognitive.microsoft.com` |

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

Средства обнаружения аномалий контейнеры не используются входные данные или подключает выходных данных для хранения обучения или службы данных. 

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того, расположение подключения на [главном компьютере](anomaly-detector-container-howto.md#the-host-computer) может оказаться недоступным из-за конфликта между разрешениями для учетной записи службы Docker и расположением подключения к узлу. 

|Необязательно| ИМЯ | Тип данных | ОПИСАНИЕ |
|-------|------|-----------|-------------|
|Не разрешено| `Input` | Строка | Контейнеры обнаружения аномалий не следует использовать.|
|Необязательно| `Output` | Строка | Цель выходного подключения. По умолчанию используется значение `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run 

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](anomaly-detector-container-howto.md#stop-the-container).

* **Символ продолжения строки**. Команды Docker в следующих разделах используйте обратную косую черту, `\`, как символ продолжения строки для оболочки bash. Замените или удалите ее в соответствии с требованиями вашей операционной системы. Например, символ продолжения строки для windows — Символ каретки `^`. Замените обратную косую черту курсор. 
* **Порядок аргументов**. Не изменяйте порядок аргументов, если вы не являетесь уверенным пользователем контейнеров Docker.

Замените значения в квадратных скобках, `{}`, собственными значениями:

| Placeholder | Value | Формат или пример |
|-------------|-------|---|
|{BILLING_KEY} | Ключ конечной точки ресурса обнаружения аномалий. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | Количество выставленных счетов за конечную точку, включая регион.|`https://westus2.api.cognitive.microsoft.com`|

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](anomaly-detector-container-howto.md#billing).
> Значение ApiKey **ключ** на странице ключей ресурсов обнаружения аномалий Azure. 

## <a name="anomaly-detector-container-docker-examples"></a>Примеры Docker для контейнера обнаружения аномалий

В следующих примерах Docker предназначены для обнаружения аномалий контейнера. 

### <a name="basic-example"></a>Простой пример 

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>Пример настройки ведения журнала через аргументы командной строки

  ```Docker
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```
