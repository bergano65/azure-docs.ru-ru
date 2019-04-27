---
title: Параметры контейнера Docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: Среда выполнения контейнера LUIS настраивается с помощью аргументов команды `docker run`. LUIS поддерживает несколько обязательных и несколько необязательных параметров.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: e05998f74223ead6bb4e94b86469e51791e0263f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599389"
---
# <a name="configure-language-understanding-docker-containers"></a>Настройка контейнеров Docker Интеллектуальной службы распознавания речи 

Среда выполнения контейнера **Интеллектуальной службы распознавания речи (LUIS)** настраивается с помощью аргументов команды `docker run`. LUIS поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные [параметры подключения](#mount-settings) и параметры выставления счетов. 

## <a name="configuration-settings"></a>Параметры конфигурации

К контейнеру применяются следующие параметры конфигурации.

|Обязательно для заполнения|Параметр|Назначение|
|--|--|--|
|Да|[ApiKey](#apikey-setting)|Используется для отслеживания данных для выставлении счетов.|
|Нет |[ApplicationInsights](#applicationinsights-setting)|Позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Да|[Выставление счетов](#billing-setting)|Задает URI конечной точки для ресурса службы в Azure.|
|Да|[Лицензионное соглашение](#eula-setting)| Указывает, что вы приняли условия лицензии для контейнера.|
|Нет |[Fluentd](#fluentd-settings)|Записывает данные в журнал и (необязательно) передает метрики на сервер Fluentd.|
|Нет |[прокси-серверу HTTP](#http-proxy-credentials-settings)|Настраивает прокси-сервер HTTP для исходящих запросов.|
|Нет |[Logging](#logging-settings)|Обеспечивает поддержку ведения журнала ASP.NET Core для вашего контейнера. |
|Да|[Подключения](#mount-settings)|Читает и записывает данные с главного компьютера в контейнер и обратно.|

> [!IMPORTANT]
> Параметры [`ApiKey`](#apikey-setting), [`Billing`](#billing-setting) и [`Eula`](#eula-setting) используются совместно, и для всех трех параметров необходимо указать допустимые значения. В противном случае контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](luis-container-howto.md#billing).

## <a name="apikey-setting"></a>Параметр ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов для контейнера. Необходимо указать значение для ApiKey и оно должно быть допустимый ключ для _Cognitive Services_ ресурс, заданный для [ `Billing` ](#billing-setting) параметр конфигурации.

Этот параметр можно найти в следующих местах.

* Портал Azure: **Cognitive Services** управления ресурсами в разделе **ключи**
* На портале LUIS. На странице **параметров ключей и конечной точки**. 

Не используйте начальный ключ или ключ разработки. 

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-setting"></a>Настройка выставления счетов

`Billing` Параметр указывает URI конечной точки из _Cognitive Services_ ресурсов в Azure позволяет контролировать использование выставления счетов для контейнера. Необходимо указать значение для этого параметра конфигурации, а значение должно быть допустимым URI конечной точки для _Cognitive Services_ ресурсов в Azure. Отчеты об использовании контейнера примерно каждые 10—15 минут.

Этот параметр можно найти в следующих местах.

* Портал Azure: **Cognitive Services** Обзор, с меткой `Endpoint`
* На портале LUIS. На странице **параметров ключей и конечной точки**, в составе URI конечной точки.

Не забудьте включить `luis/v2.0` маршрутизации в URL-адрес, как показано в следующей таблице:


|Обязательно для заполнения| ИМЯ | Тип данных | ОПИСАНИЕ |
|--|------|-----------|-------------|
|Да| `Billing` | String | URI конечной точки выставления счетов<br><br>Пример:<br>`Billing=https://westus.api.cognitive.microsoft.com/luis/v2.0` |

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

|Обязательно для заполнения| ИМЯ | Тип данных | ОПИСАНИЕ |
|-------|------|-----------|-------------|
|Да| `Input` | String | Цель входного подключения. По умолчанию используется значение `/input`. Это расположение файлов из пакета LUIS. <br><br>Пример:<br>`--mount type=bind,src=c:\input,target=/input`|
|Нет | `Output` | String | Цель выходного подключения. По умолчанию используется значение `/output`. Это расположение файлов журналов. Сюда относятся журналы запросов LUIS и журналы контейнера. <br><br>Пример:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](luis-container-howto.md#stop-the-container).


* **Символ продолжения строки**. В командах Docker в следующих разделах используется обратная косая черта (`\`) как символ продолжения строки. Замените или удалите ее в соответствии с требованиями вашей операционной системы. 
* **Порядок аргументов**. Не изменяйте порядок аргументов, если вы не являетесь уверенным пользователем контейнеров Docker.

Не забудьте включить `luis/v2.0` маршрутизации в URL-адрес, как показано в следующей таблице.

Замените строку {_имя_аргумента_} собственными значениями.

| Placeholder | Value | Формат или пример |
|-------------|-------|---|
|{ENDPOINT_KEY} | Ключ конечной точки обученного приложения LUIS. |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT} | Значение конечной точки выставления счетов можно найти в Azure `Cognitive Services` странице "Обзор". |https://westus.api.cognitive.microsoft.com/luis/v2.0|

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](luis-container-howto.md#billing).
> Значение ApiKey **ключ** доступна также на Azure и из ключей и конечные точки страницы на портале LUIS `Cognitive Services` страницу ключей ресурсов. 

### <a name="basic-example"></a>Простой пример

В следующем примере указано минимальное число аргументов, с которым можно запустить контейнер.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> Предыдущая команда использует каталог с диска `c:`, чтобы избежать конфликтов разрешений в Windows. Если вам нужен конкретный каталог для входных данных, может потребоваться предоставить соответствующие разрешения службе Docker. В предыдущей команде Docker используется обратная косая черта (`\`) как символ продолжения строки. Замените или удалите ее в соответствии с требованиями операционной системы [главного компьютера](luis-container-howto.md#the-host-computer). Не изменяйте порядок аргументов, если вы еще не очень хорошо знакомы с контейнерами Docker.


### <a name="applicationinsights-example"></a>Пример ApplicationInsights

В следующем примере задается аргумент ApplicationInsights для отправки данных телеметрии в Application Insights во время выполнения контейнера:

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
InstrumentationKey={INSTRUMENTATION_KEY}
```

### <a name="logging-example"></a>Пример журнала 

Следующая команда задает уровень ведения журнала `Logging:Console:LogLevel`, чтобы настроить уровень ведения журнала [`Information`](https://msdn.microsoft.com). 

```bash
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis:latest \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Дальнейшие действия

* Изучите статью об [установке и запуске контейнеров](luis-container-howto.md).
* Ознакомьтесь со статьей об [устранение неполадок](troubleshooting.md), чтобы устранить проблемы, связанные с функциональностью LUIS.
* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../cognitive-services-container-support.md)
