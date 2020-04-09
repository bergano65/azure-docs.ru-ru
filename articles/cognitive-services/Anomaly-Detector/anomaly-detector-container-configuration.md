---
title: Как настроить контейнер для API детектора аномалий
titleSuffix: Azure Cognitive Services
description: Среда выполнения контейнера Anomaly Detector API `docker run` настраивается с помощью аргументов команды. Контейнер поддерживает несколько обязательных и несколько необязательных параметров.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 569499002c5e047d7030575342790e9a074b9404
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875186"
---
# <a name="configure-anomaly-detector-containers"></a>Настройка контейнеров Детектора аномалий

Среда выполнения контейнера **Anomaly Detector** настроена с помощью `docker run` аргументов команды. Контейнер поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные параметры выставления счетов. 

## <a name="configuration-settings"></a>Параметры конфигурации

К контейнеру применяются следующие параметры конфигурации.

|Обязательно|Параметр|Назначение|
|--|--|--|
|Да|[ApiKey](#apikey-configuration-setting)|Используется для отслеживания данных для выставлении счетов.|
|нет|[ApplicationInsights](#applicationinsights-setting)|Позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Да|[Выставление счетов](#billing-configuration-setting)|Задает URI конечной точки для ресурса службы в Azure.|
|Да|[Лицензионное соглашение](#eula-setting)| Указывает, что вы приняли условия лицензии для контейнера.|
|нет|[Fluentd](#fluentd-settings)|Записывает данные в журнал и (необязательно) передает метрики на сервер Fluentd.|
|нет|[Прокси-сервер HTTP](#http-proxy-credentials-settings)|Настраивает прокси-сервер HTTP для исходящих запросов.|
|нет|[Logging](#logging-settings)|Обеспечивает поддержку ведения журнала ASP.NET Core для вашего контейнера. |
|нет|[Подключения](#mount-settings)|Читает и записывает данные с главного компьютера в контейнер и обратно.|

> [!IMPORTANT]
> , [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)и [`Eula`](#eula-setting) настройки используются вместе, и вы должны предоставить действительные значения для всех трех из них; в противном случае ваш контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](anomaly-detector-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов для контейнера. Необходимо указать значение для ApiKey, а значение должно быть действительным ключом [`Billing`](#billing-configuration-setting) для ресурса _Anomaly Detector,_ указанного для настройки конфигурации.

Этот параметр можно найти в следующем месте.

* Портал Azure: Управление **ресурсами детектора аномалий,** под **ключами**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

В `Billing` параметре указывается конечная точка URI _ресурса детектора аномалий_ в Azure, используемая для измерения платежной информации для контейнера. Необходимо указать значение для этого параметра конфигурации, а значение должно быть действительным URI-конечным пунктом для ресурса _детектора аномалий_ в Azure.

Этот параметр можно найти в следующем месте.

* Портал Azure: Обзор **детектора аномалий,** помеченный`Endpoint`

|Обязательно| Имя | Тип данных | Описание |
|--|------|-----------|-------------|
|Да| `Billing` | Строка | Биллинг endpoint URI. Для получения дополнительной информации о получении биллинга URI [см.](anomaly-detector-container-howto.md#gathering-required-parameters) Дополнительные сведения и полный список региональных конечных точек см. в статье [Custom subdomain names for Cognitive Services](../cognitive-services-custom-subdomains.md) (Пользовательские имена поддоменов для Cognitive Services). |

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

Контейнеры Anomaly Detector не используют входные или выходные крепления для хранения обучаемых или сервисных данных. 

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того, место расположения [крепления компьютера-хозяина](anomaly-detector-container-howto.md#the-host-computer)может быть недоступно из-за конфликта между разрешениями, используемыми учетной записью службы Docker, и разрешениями на размещение хоста. 

|Необязательный| Имя | Тип данных | Описание |
|-------|------|-----------|-------------|
|Не разрешено| `Input` | Строка | Контейнеры детектора аномалий не используют это.|
|Необязательный| `Output` | Строка | Цель выходного подключения. Значение по умолчанию — `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run 

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](anomaly-detector-container-howto.md#stop-the-container).

* **Характер продолжения линии**: Команды Докера в следующих `\`разделах используют заднюю слэш, в качестве персонажа продолжения линии для оболочки Bash. Замените или удалите ее в соответствии с требованиями вашей операционной системы. Например, символ продолжения строки для Windows — это крышка (`^`). Замените обратную косую черту крышкой. 
* **Порядок аргумента**: Не изменяйте порядок аргументов, если вы не очень хорошо знакомы с контейнерами Docker.

Замените значение в `{}`скобках, с вашими собственными значениями:

| Заполнитель | Значение | Формат или пример |
|-------------|-------|---|
| **(API_KEY)** | Ключ конечной точки `Anomaly Detector` ресурса на `Anomaly Detector` странице Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **(ENDPOINT_URI)** | Значение конечных точек выставления `Anomaly Detector` счетов доступно на странице Azure Overview.| [Ознакомьтесь со сбором необходимых параметров](anomaly-detector-container-howto.md#gathering-required-parameters) для явных примеров. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](anomaly-detector-container-howto.md#billing).
> Значение ApiKey — это **ключ** со страницы ключей ресурсов ресурсов детектора аномалий Azure. 

## <a name="anomaly-detector-container-docker-examples"></a>Примеры контейнера для детектора аномалий Docker

Следующие примеры Docker относятся к контейнеру Anomaly Detector. 

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

* [Развертывание контейнера детектора аномалий в экземплярах контейнеров Azure](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Подробнее об услуге API Anomaly Detector API](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
