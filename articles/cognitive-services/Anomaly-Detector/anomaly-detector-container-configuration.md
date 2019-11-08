---
title: Настройка контейнера для API детектора аномалий
titleSuffix: Azure Cognitive Services
description: Среда выполнения контейнера API детектора аномалий настраивается с помощью аргументов командной строки `docker run`. Контейнер поддерживает несколько обязательных и несколько необязательных параметров.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: f7e04a16fa35d492b8e5e6c53a05220e8b96a38a
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795851"
---
# <a name="configure-anomaly-detector-containers"></a>Настройка контейнеров детекторов аномалий

Среда выполнения контейнера **детектора аномалий** настраивается с помощью аргументов команды `docker run`. Контейнер поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные параметры выставления счетов. 

## <a name="configuration-settings"></a>Параметры конфигурации

К контейнеру применяются следующие параметры конфигурации.

|Обязательно|Настройка|Назначение|
|--|--|--|
|Да|[apiKey](#apikey-configuration-setting)|Используется для отслеживания данных для выставлении счетов.|
|Нет|[ApplicationInsights](#applicationinsights-setting)|Позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Да|[Выставление счетов](#billing-configuration-setting)|Задает URI конечной точки для ресурса службы в Azure.|
|Да|[Лицензионное соглашение](#eula-setting)| Указывает, что вы приняли условия лицензии для контейнера.|
|Нет|[Fluentd](#fluentd-settings)|Записывает данные в журнал и (необязательно) передает метрики на сервер Fluentd.|
|Нет|[прокси-серверу HTTP](#http-proxy-credentials-settings)|Настраивает прокси-сервер HTTP для исходящих запросов.|
|Нет|[Logging](#logging-settings)|Обеспечивает поддержку ведения журнала ASP.NET Core для вашего контейнера. |
|Нет|[Mounts](#mount-settings)|Читает и записывает данные с главного компьютера в контейнер и обратно.|

> [!IMPORTANT]
> Параметры [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) и [`Eula`](#eula-setting) используются совместно, и для всех трех параметров необходимо указать допустимые значения. В противном случае контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Настройка конфигурации ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов для контейнера. Необходимо указать значение для параметра ApiKey, а значение должно быть допустимым ключом для ресурса _детектора аномалий_ , указанного для параметра конфигурации [`Billing`](#billing-configuration-setting) .

Этот параметр можно найти в следующем месте.

* Портал Azure: управление ресурсами **детектора аномалий** в разделе **ключи**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

Параметр `Billing` указывает URI конечной точки ресурса _детектора аномалий_ в Azure, который используется для отслеживания сведений о выставлении счетов для контейнера. Необходимо указать значение для этого параметра конфигурации, а значение должно быть допустимым URI конечной точки для ресурса _детектора аномалий_ в Azure.

Этот параметр можно найти в следующем месте.

* Портал Azure: Общие сведения **детектора аномалий** , с метками `Endpoint`

|Обязательно| Имя | Тип данных | Description (Описание) |
|--|------|-----------|-------------|
|Да| `Billing` | string | URI конечной точки выставления счетов. Дополнительные сведения о получении URI выставления счетов см. в разделе [сбор обязательных параметров](anomaly-detector-container-howto.md#gathering-required-parameters). Дополнительные сведения и полный список региональных конечных точек см. в статье [Custom subdomain names for Cognitive Services](../cognitive-services-custom-subdomains.md) (Пользовательские имена поддоменов для Cognitive Services). |

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

|Необязательно| Имя | Тип данных | Description (Описание) |
|-------|------|-----------|-------------|
|Не разрешено| `Input` | string | В контейнерах детекторов аномалий это не используется.|
|Необязательно| `Output` | string | Цель выходного подключения. По умолчанию используется значение `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run 

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](anomaly-detector-container-howto.md#stop-the-container).

* **Символ продолжения строки**: команды DOCKER в следующих разделах используют обратную косую черту, `\`в качестве символа продолжения строки для оболочки bash. Замените или удалите ее в соответствии с требованиями вашей операционной системы. Например, символ продолжения строки для Windows — это крышка (`^`). Замените обратную косую черту крышкой. 
* **Порядок аргументов**. не изменяйте порядок аргументов, если вы не знакомы с контейнерами DOCKER.

Замените значение в квадратных скобках `{}`собственными значениями:

| Placeholder | Значение | Формат или пример |
|-------------|-------|---|
| **{API_KEY}** | Ключ конечной точки `Anomaly Detector` ресурса на странице ключей `Anomaly Detector` Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Значение конечной точки выставления счетов доступно на странице обзора `Anomaly Detector` Azure.| См. раздел [сбор обязательных параметров](anomaly-detector-container-howto.md#gathering-required-parameters) для явных примеров. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Для выполнения контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](anomaly-detector-container-howto.md#billing).
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

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание контейнера детекторов аномалий в службе "экземпляры контейнеров Azure"](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Дополнительные сведения о службе API детектора аномалий](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
