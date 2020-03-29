---
title: Настройки контейнеров Docker - LUIS
titleSuffix: Azure Cognitive Services
description: Среда выполнения контейнера LUIS настраивается с помощью аргументов команды `docker run`. LUIS поддерживает несколько обязательных и несколько необязательных параметров.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: a30fcd0ec7e53c78876596baf787639e81c638db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73795008"
---
# <a name="configure-language-understanding-docker-containers"></a>Настройка контейнеров Docker Интеллектуальной службы распознавания речи 

Среда выполнения контейнера **Language Understanding** (LUIS) `docker run` настроена с помощью аргументов команды. LUIS поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные [параметры подключения](#mount-settings) и параметры выставления счетов. 

## <a name="configuration-settings"></a>Параметры конфигурации

К контейнеру применяются следующие параметры конфигурации.

|Обязательно|Параметр|Назначение|
|--|--|--|
|Да|[ApiKey](#apikey-setting)|Используется для отслеживания данных для выставлении счетов.|
|нет|[ApplicationInsights](#applicationinsights-setting)|Позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Да|[Выставление счетов](#billing-setting)|Задает URI конечной точки для ресурса службы в Azure.|
|Да|[Лицензионное соглашение](#eula-setting)| Указывает, что вы приняли условия лицензии для контейнера.|
|нет|[Fluentd](#fluentd-settings)|Записывает данные в журнал и (необязательно) передает метрики на сервер Fluentd.|
|нет|[Прокси-сервер HTTP](#http-proxy-credentials-settings)|Настраивает прокси-сервер HTTP для исходящих запросов.|
|нет|[Ведение журнала](#logging-settings)|Обеспечивает поддержку ведения журнала ASP.NET Core для вашего контейнера. |
|Да|[Подключения](#mount-settings)|Читает и записывает данные с главного компьютера в контейнер и обратно.|

> [!IMPORTANT]
> , [`ApiKey`](#apikey-setting) [`Billing`](#billing-setting)и [`Eula`](#eula-setting) настройки используются вместе, и вы должны предоставить действительные значения для всех трех из них; в противном случае ваш контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Параметр ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов для контейнера. Необходимо указать значение для ApiKey, а значение должно быть действительным ключом для ресурса _Cognitive Services,_ указанного для настройки [`Billing`](#billing-setting) конфигурации.

Этот параметр можно найти в следующих местах.

* Портал Azure: **Управление ресурсами когнитивных услуг,** под **ключами**
* Портал LUIS: Страница **настроек ключей и конечных точек.** 

Не используйте начальный ключ или ключ разработки. 

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Настройка выставления счетов

В `Billing` настройке указывается конечная точка URI ресурса _Cognitive Services_ в Azure, используемая для измерения платежной информации для контейнера. Необходимо указать значение для этого параметра конфигурации, а значение должно быть действительным URI для ресурса _Cognitive Services_ в Azure. Отчеты об использовании контейнера примерно каждые 10—15 минут.

Этот параметр можно найти в следующих местах.

* Портал Azure: Обзор **когнитивных услуг,** помеченный`Endpoint`
* Портал LUIS: Страница **настроек ключей и конечных точек,** как часть конечной точки URI.

| Обязательно | name | Тип данных | Описание |
|----------|------|-----------|-------------|
| Да      | `Billing` | строка | Биллинг endpoint URI. Для получения дополнительной информации о получении биллинга URI [см.](luis-container-howto.md#gathering-required-parameters) Дополнительные сведения и полный список региональных конечных точек см. в статье [Custom subdomain names for Cognitive Services](../cognitive-services-custom-subdomains.md) (Пользовательские имена поддоменов для Cognitive Services). |

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

Контейнер LUIS не использует входные или выходные подключения для хранения учебных данных или данных службы. 

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того,расположение подключения на [главном компьютере](luis-container-howto.md#the-host-computer) может оказаться недоступным из-за конфликта между разрешениями для учетной записи службы Docker и расположения подключения к узлу. 

В следующей таблице описаны поддерживаемые параметры.

|Обязательно| name | Тип данных | Описание |
|-------|------|-----------|-------------|
|Да| `Input` | Строка | Цель входного подключения. Значение по умолчанию — `/input`. Это расположение файлов из пакета LUIS. <br><br>Пример<br>`--mount type=bind,src=c:\input,target=/input`|
|нет| `Output` | Строка | Цель выходного подключения. Значение по умолчанию — `/output`. Это расположение файлов журналов. Сюда относятся журналы запросов LUIS и журналы контейнера. <br><br>Пример<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](luis-container-howto.md#stop-the-container).

* Эти примеры используют каталог `C:` с диска, чтобы избежать конфликтов с разрешения в Windows. Если вам нужен конкретный каталог для входных данных, может потребоваться предоставить соответствующие разрешения службе Docker. 
* Не изменяйте порядок аргументов, если вы не являетесь уверенным пользователем контейнеров Docker.
* Если вы используете другую операционную систему, используйте правильную консоль/терминал, синтаксис папки для креплений и символ продолжения строки для вашей системы. Эти примеры предполагают консоль Windows `^`с символом продолжения строки. Поскольку контейнер представляет собой операционную систему Linux, целевое крепление использует синтаксис в стиле папки ВОльс.

Замените строку {_имя_аргумента_} собственными значениями.

| Заполнитель | Значение | Формат или пример |
|-------------|-------|---|
| **(API_KEY)** | Ключ конечной точки `LUIS` ресурса на `LUIS` странице Azure Keys. | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| **(ENDPOINT_URI)** | Значение конечных точек выставления `LUIS` счетов доступно на странице Azure Overview.| [Ознакомьтесь со сбором необходимых параметров](luis-container-howto.md#gathering-required-parameters) для явных примеров. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится. Дополнительные сведения см. в [разделе о выставлении счетов](luis-container-howto.md#billing).
> Значение ApiKey — это страница **ключ** ей ключей и конечных точек `Cognitive Services` на портале LUIS, а также доступно на странице ключей ресурсов Azure. 

### <a name="basic-example"></a>Простой пример

В следующем примере указано минимальное число аргументов, с которым можно запустить контейнер.

```console
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY}
```

### <a name="applicationinsights-example"></a>Пример ApplicationInsights

В следующем примере задается аргумент ApplicationInsights для отправки данных телеметрии в Application Insights во время выполнения контейнера:

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Пример ведения журнала 

Следующая команда устанавливает уровень регистрации, `Logging:Console:LogLevel`чтобы [`Information`](https://msdn.microsoft.com)настроить уровень регистрации до . 

```console
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis:latest ^
Eula=accept ^
Billing={ENDPOINT_URL} ^
ApiKey={API_KEY} ^
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Дальнейшие действия

* Изучите статью об [установке и запуске контейнеров](luis-container-howto.md).
* Ознакомьтесь со статьей об [устранение неполадок](troubleshooting.md), чтобы устранить проблемы, связанные с функциональностью LUIS.
* Используйте больше [контейнеров когнитивных услуг](../cognitive-services-container-support.md)
