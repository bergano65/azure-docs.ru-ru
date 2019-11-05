---
title: Настройка контейнеров — Перевод текстов
titleSuffix: Azure Cognitive Services
description: Перевод текстов предоставляет контейнеры с общей структурой конфигурации, что позволяет легко настраивать хранилище, вести журнал и данные телеметрии, а также параметры безопасности для контейнеров.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 3dbedcb5e5212091dc8906defa2b1cf5c7868d60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507674"
---
# <a name="configure-translator-text-docker-containers"></a>Настройка Перевод текстов контейнеров DOCKER

Перевод текстов предоставляет каждый контейнер с общей структурой конфигурации, что позволяет легко настраивать хранилище, вести журнал и данные телеметрии, а также параметры безопасности для контейнеров.

## <a name="configuration-settings"></a>Параметры конфигурации

К контейнеру применяются следующие параметры конфигурации:

|Обязательно|Настройка|Назначение|
|--|--|--|
|Нет|[ApplicationInsights](#applicationinsights-setting)|Позволяет добавить в контейнер поддержку телеметрии [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Да|[Лицензионное соглашение](#eula-setting)| Указывает, что вы приняли условия лицензии для контейнера.|
|Нет|[Fluentd](#fluentd-settings)|Записывает данные в журнал и при необходимости передает метрики на сервер Fluentd.|
|Нет|Прокси-сервер HTTP|Настраивает прокси-сервер HTTP для исходящих запросов.|
|Нет|[Logging](#logging-settings)|Обеспечивает поддержку ведения журнала ASP.NET Core для вашего контейнера. |
|Нет|[Mounts](#mount-settings)|Считывает и записывает данные с главного компьютера в контейнер и обратно.|

> [!IMPORTANT]
> Необходимо указать параметр [`Eula`](#eula-setting) со значением `accept`; в противном случае контейнер не будет запущен.

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

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

Контейнеры Перевод текстов не используют подключения ввода или вывода для хранения данных обучения или службы. 

Точный синтаксис расположения для подключения к узлу зависит от операционной системы узла. Кроме того,расположение подключения на [главном компьютере](how-to-install-containers.md#the-host-computer) может оказаться недоступным из-за конфликта между разрешениями для учетной записи службы Docker и расположения подключения к узлу. 

|Необязательно| Имя | Тип данных | Description (Описание) |
|-------|------|-----------|-------------|
|Не разрешено| `Input` | string | Контейнеры Перевод текстов не используют это.|
|Необязательно| `Output` | string | Цель выходного подключения. По умолчанию используется значение `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run 

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`.  После запуска контейнер продолжает работу, пока вы его не [остановите](how-to-install-containers.md#stop-the-container).

* **Символ продолжения строки**. в командах DOCKER в следующих разделах используется обратная косая черта, `\`, как символ продолжения строки. Замените или удалите ее в соответствии с требованиями вашей операционной системы. 
* **Порядок аргументов**. не изменяйте порядок аргументов, если вы не знакомы с контейнерами DOCKER.

## <a name="translator-text-container-docker-examples"></a>Примеры Перевод текстов контейнера DOCKER

Следующие примеры DOCKER предназначены для контейнера Перевод текстов.

### <a name="basic-example"></a>Простой пример

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept
```

### <a name="logging-example"></a>Пример ведения журнала

```
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text \
Eula=accept \
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Дальнейшие действия

* Изучите статью об [установке и запуске контейнеров](how-to-install-containers.md).
* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../cognitive-services-container-support.md)
