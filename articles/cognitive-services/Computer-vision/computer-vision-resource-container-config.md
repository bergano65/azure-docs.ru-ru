---
title: Настройка контейнеров — компьютерное зрение
titleSuffix: Azure Cognitive Services
description: В этой статье показано, как настроить как необходимые, так и дополнительные настройки для контейнеров Распознавания Текста в Computer Vision.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: ddbee3695c2a7ef7cb63c48cccacbd2d53a8c1a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73718991"
---
# <a name="configure-computer-vision-docker-containers"></a>Настройка контейнеров Computer Vision Docker

Вы настраиваете среду выполнения контейнера Computer `docker run` Vision с помощью аргументов команды. Контейнер поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные параметры выставления счетов. 

## <a name="configuration-settings"></a>Параметры конфигурации

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> , [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)и [`Eula`](#eula-setting) настройки используются вместе, и вы должны предоставить действительные значения для всех трех из них; в противном случае ваш контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](computer-vision-how-to-install-containers.md).

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

В `ApiKey` настройках указан ключ `Cognitive Services` ресурса Azure, используемый для отслеживания платежной информации для контейнера. Необходимо указать значение для ApiKey, а значение должно быть действительным ключом для ресурса _Cognitive Services,_ указанного для настройки [`Billing`](#billing-configuration-setting) конфигурации.

Этот параметр можно найти в следующем месте.

* Портал Azure: **Управление ресурсами когнитивных услуг,** под **ключами**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

В `Billing` настройке указывается конечная точка URI ресурса _Cognitive Services_ в Azure, используемая для измерения платежной информации для контейнера. Необходимо указать значение для этого параметра конфигурации, а значение должно быть действительным URI для ресурса _Cognitive Services_ в Azure. Отчеты об использовании контейнера примерно каждые 10—15 минут.

Этот параметр можно найти в следующем месте.

* Портал Azure: Обзор **когнитивных услуг,** помеченный`Endpoint`

Не забудьте `vision/v1.0` добавить разгром в конечную точку URI, как показано в следующей таблице. 

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

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того, место расположения [крепления компьютера-хозяина](computer-vision-how-to-install-containers.md#the-host-computer)может быть недоступно из-за конфликта между разрешениями, используемыми учетной записью службы Docker, и разрешениями на размещение хоста. 

|Необязательный| name | Тип данных | Описание |
|-------|------|-----------|-------------|
|Не разрешено| `Input` | Строка | Контейнеры API компьютерного зрения не используют этот элемент.|
|Необязательный| `Output` | Строка | Цель выходного подключения. Значение по умолчанию — `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](computer-vision-how-to-install-containers.md#stop-the-container).

* **Персонаж продолжения строки**: Команды Докера в следующих `\`разделах используют заднюю слэш, в качестве символа продолжения линии. Замените или удалите ее в соответствии с требованиями вашей операционной системы. 
* **Порядок аргумента**: Не изменяйте порядок аргументов, если вы не очень хорошо знакомы с контейнерами Docker.

Замените строку {_имя_аргумента_} собственными значениями.

| Заполнитель | Значение | Формат или пример |
|-------------|-------|---|
| **(API_KEY)** | Ключ конечной точки `Computer Vision` ресурса на `Computer Vision` странице Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **(ENDPOINT_URI)** | Значение конечных точек выставления `Computer Vision` счетов доступно на странице Azure Overview.| [Ознакомьтесь со сбором необходимых параметров](computer-vision-how-to-install-containers.md#gathering-required-parameters) для явных примеров. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](computer-vision-how-to-install-containers.md#billing).
> Значение ApiKey — это **ключ** `Cognitive Services` со страницы ключей ресурса Azure.

## <a name="container-docker-examples"></a>Примеры контейнерных докеров

Следующие примеры Docker приведены для контейнера Read.

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

* [Просмотрите, как установить и запустить контейнеры.](computer-vision-how-to-install-containers.md)
