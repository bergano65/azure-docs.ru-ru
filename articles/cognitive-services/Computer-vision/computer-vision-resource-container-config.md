---
title: Настройка контейнеров — компьютерное зрение
titleSuffix: Azure Cognitive Services
description: В этой статье показано, как настроить обязательные и необязательные параметры для Распознавание текста контейнеров в Компьютерное зрение.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.custom: seodec18
ms.openlocfilehash: 765001ae7380ff2e99e6b390930b94302ce506bf
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89433693"
---
# <a name="configure-computer-vision-docker-containers"></a>Настройка Компьютерное зрение контейнеров DOCKER

Вы настраиваете среду выполнения контейнера Компьютерное зрение с помощью `docker run` аргументов команды. Контейнер поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные параметры выставления счетов. 

## <a name="configuration-settings"></a>Параметры конфигурации

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Параметры, [`Billing`](#billing-configuration-setting) и [`Eula`](#eula-setting) используются совместно, и необходимо указать допустимые значения для всех трех из них; в противном случае контейнер не будет запущен. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](computer-vision-how-to-install-containers.md).

Контейнер также имеет следующие параметры конфигурации, относящиеся к контейнеру:

|Обязательно|Параметр|Назначение|
|--|--|--|
|Нет|Реаденгинеконфиг: Ресултекспиратионпериод|Истечение срока действия результата в часах. Значение по умолчанию — 48 часов. Этот параметр указывает, когда система должна очищать результаты распознавания. Например, если `resultExpirationPeriod=1` , система очищает результат распознавания 1 час после процесса. Если значение `resultExpirationPeriod=0` равно, система очищает результат распознавания после получения результата.|
|Нет|Кэш: Redis|Включает хранилище Redis для хранения результатов. Кэш *необходим* , если за подсистемой балансировки нагрузки помещается несколько контейнеров чтения.|
|Нет|Очередь: RabbitMQ|Включает RabbitMQ для задач диспетчеризации. Этот параметр полезен при помещении нескольких контейнеров чтения за пределы подсистемы балансировки нагрузки.|
|Нет|Хранилище::D Окументсторе:: MongoDB|Включает MongoDB для постоянного хранения результатов.|

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

`ApiKey`Параметр указывает `Cognitive Services` ключ ресурса Azure, используемый для трассировки сведений о выставлении счетов для контейнера. Необходимо указать значение для параметра ApiKey, а значение должно быть допустимым ключом для ресурса _Cognitive Services_ , указанного для [`Billing`](#billing-configuration-setting) параметра конфигурации.

Этот параметр можно найти в следующем месте.

* Портал Azure: управление ресурсами **Cognitive Services** в разделе **ключи**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

`Billing`Параметр указывает URI конечной точки ресурса _Cognitive Services_ в Azure, который используется для отслеживания сведений о выставлении счетов для контейнера. Необходимо указать значение для этого параметра конфигурации, а значение должно быть допустимым URI конечной точки для ресурса _Cognitive Services_ в Azure. Отчеты об использовании контейнера примерно каждые 10—15 минут.

Этот параметр можно найти в следующем месте.

* Портал Azure: **Cognitive Services** обзор с меткой `Endpoint`

Не забудьте добавить `vision/v1.0` маршрут к универсальному коду ресурса (URI) конечной точки, как показано в следующей таблице. 

|Обязательно| name | Тип данных | Описание |
|--|------|-----------|-------------|
|Да| `Billing` | Строка | URI конечной точки выставления счетов<br><br>Пример<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/vision/v1.0` |

## <a name="eula-setting"></a>Параметр Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Параметры Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Параметры учетных данных прокси-сервера HTTP

[!INCLUDE [Container shared configuration HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Параметры ведения журнала
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Параметры подключения

Используйте подключения привязок для чтения данных из контейнера и записи в него. Вы можете указать входное или выходное подключение, указав параметр `--mount` в команде [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Контейнеры API компьютерного зрения не используют входные или выходные подключения для хранения учебных данных или данных службы. 

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того, расположение подключения [главного компьютера](computer-vision-how-to-install-containers.md#the-host-computer)может быть недоступно из-за конфликта между разрешениями, используемыми учетной записью службы DOCKER, и разрешениями на расположение для подключения к узлу. 

|Необязательно| name | Тип данных | Описание |
|-------|------|-----------|-------------|
|Нельзя использовать| `Input` | Строка | Контейнеры API компьютерного зрения не используют этот элемент.|
|Необязательный| `Output` | Строка | Цель выходного подключения. Значение по умолчанию — `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](computer-vision-how-to-install-containers.md#stop-the-container).

* **Символ продолжения строки**: команды DOCKER, приведенные в следующих разделах, используют обратную косую черту в `\` качестве символа продолжения строки. Замените или удалите ее в соответствии с требованиями вашей операционной системы. 
* **Порядок аргументов**. не изменяйте порядок аргументов, если вы не знакомы с контейнерами DOCKER.

Замените строку {_имя_аргумента_} собственными значениями.

| Заполнитель | Значение | Формат или пример |
|-------------|-------|---|
| **{API_KEY}** | Ключ конечной точки `Computer Vision` ресурса на `Computer Vision` странице ключей Azure. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **{ENDPOINT_URI}** | Значение конечной точки выставления счетов доступно на `Computer Vision` странице обзора Azure.| См. раздел [сбор обязательных параметров](computer-vision-how-to-install-containers.md#gathering-required-parameters) для явных примеров. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](computer-vision-how-to-install-containers.md#billing).
> Значение ApiKey является **ключом** на `Cognitive Services` странице ключей ресурсов Azure.

## <a name="container-docker-examples"></a>Примеры DOCKER Container

Следующие примеры DOCKER предназначены для контейнера Read.

### <a name="basic-example"></a>Простой пример

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} 
  ```

### <a name="logging-example"></a>Пример ведения журнала 

  ```docker
  docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
  containerpreview.azurecr.io/microsoft/cognitive-services-read \
  Eula=accept \
  Billing={ENDPOINT_URI} \
  ApiKey={API_KEY} \
  Logging:Console:LogLevel:Default=Information
  ```

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте [, как устанавливать и запускать контейнеры](computer-vision-how-to-install-containers.md).
