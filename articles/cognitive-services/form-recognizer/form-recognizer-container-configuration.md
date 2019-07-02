---
title: Настройка контейнера Распознавателя документов
titleSuffix: Azure Cognitive Services
description: Узнайте, как настраивать контейнер Распознавателя документов для синтаксического анализа данных форм и таблиц.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 7e8e7a13cd02a6f3b109a84829dba2a81fd36aaa
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296235"
---
# <a name="configure-form-recognizer-containers"></a>Настройка контейнера Распознавателя документов

Контейнеры Распознавателя документов позволяют создавать такую архитектуру приложения, которая использует преимущества как облачных возможностей, так и пограничного размещения.

Среда выполнения контейнера Распознавателя документов настраивается с помощью аргументов команды `docker run`. У контейнера есть несколько обязательных и необязательных параметров. Примеры команд см. в [соответствующем разделе](#example-docker-run-commands) этой статьи. Для конкретного контейнера настраиваются входные параметры выставления счетов.

## <a name="configuration-settings"></a>Параметры конфигурации

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Параметры [`ApiKey`](#apikey-configuration-setting), [`Billing`](#billing-configuration-setting) и [`Eula`](#eula-setting) используются совместно. Для всех трех параметров необходимо указать допустимые значения. В противном случае контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов за контейнер. Значение ApiKey должно содержать допустимый ключ ресурса _Распознаватель документов_, который указан для параметра `Billing` в разделе настроек параметров выставления счетов.

Этот параметр указывается в разделе **ключей** на странице **управления ресурсом "Распознаватель документов"** портала Azure.

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

Параметр `Billing` определяет URI конечной точки ресурса _Распознаватель документов_ в Azure и используется для расчета сумм выставляемых счетов за контейнер. Значением этого параметра должен быть допустимый URI конечной точки для ресурса _Распознаватель документов_ в Azure. Отчеты об использовании контейнера примерно каждые 10—15 минут.

Этот параметр указывается в разделе **конечной точки** на странице **общих сведений о Распознавателе документов** портала Azure.

|Обязательно| ИМЯ | Тип данных | ОПИСАНИЕ |
|--|------|-----------|-------------|
|Да| `Billing` | Строка, | URI конечной точки выставления счетов<br><br>Пример:<br>`Billing=https://westus2.api.cognitive.microsoft.com/` |

## <a name="eula-setting"></a>Параметр Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Параметры Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Параметры учетных данных прокси-сервера HTTP

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Параметры ведения журнала

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Параметры подключения

Используйте подключения привязок для чтения данных из контейнера и записи в него. Вы можете задать входное или выходное подключение, указав параметр `--mount` в [команде `docker run`](https://docs.docker.com/engine/reference/commandline/run/).

Для работы контейнера Распознавателя документов требуется входное и выходное подключение. Входное подключение может допускать только чтение и необходимо для доступа к данным, которые используются для обучения и оценки. Выходное подключение должно обеспечивать возможность записи. Оно предназначено для хранения моделей и временных данных.

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того, расположение подключения на [главном компьютере](form-recognizer-container-howto.md#the-host-computer) может оказаться недоступным из-за конфликта между разрешениями для учетной записи службы Docker и разрешениями для расположения подключения к узлу.

|Необязательно| ИМЯ | Тип данных | ОПИСАНИЕ |
|-------|------|-----------|-------------|
|Обязательно| `Input` | Строка, | Цель входного подключения. По умолчанию используется значение `/input`.    <br><br>Пример:<br>`--mount type=bind,src=c:\input,target=/input`|
|Обязательно| `Output` | Строка, | Цель выходного подключения. По умолчанию используется значение `/output`.  <br><br>Пример:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Примеры команд docker run

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`. После запуска контейнер работает, пока не будет [остановлен](form-recognizer-container-howto.md#stop-the-container).

* **Символ продолжения строки**. В следующих разделах в командах Docker используется обратная косая черта (\\) как символ продолжения строки. Замените или удалите ее в соответствии с требованиями вашей операционной системы.
* **Порядок аргументов**. Не изменяйте порядок аргументов, если вы плохо знакомы с контейнерами Docker.

Замените {_имя_аргумента_} в следующей таблице собственными значениями.

| Placeholder | Значение |
|-------------|-------|
|{BILLING_KEY} | Этот ключ используется для запуска контейнера. Ключ можно найти на странице ключей Распознавателя документов на портале Azure.  |
|{BILLING_ENDPOINT_URI} | Значение URI конечной точки выставления счетов доступно на странице общих сведений о Распознавателе документов на портале Azure.|
|{COMPUTER_VISION_API_KEY}| Ключ можно найти на странице ключей API Компьютерного зрения на портале Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|Конечная точка выставления счетов. При использовании облачного ресурса Компьютерного зрения значение URI доступно на странице общих сведений об API Компьютерного зрения на портале Azure. При использовании контейнера *cognitive-services-recognize-text* URL-адрес конечной точки выставления счетов можно получить в контейнере с помощью команды `docker run`.|

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится. Дополнительные сведения см. в [разделе о выставлении счетов](#billing-configuration-setting).

> [!NOTE] 
> Значение ApiKey — это **ключ** со страницы ключей ресурса Распознавателя документов Azure.

## <a name="form-recognizer-container-docker-examples"></a>Примеры контейнера Docker Распознавателя документов

Следующие примеры Docker предназначены для контейнера Распознавателя документов.

### <a name="basic-example-for-form-recognizer"></a>Простой пример для Распознавателя документов

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Пример записи в журнал для Распознавателя документов

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```


## <a name="next-steps"></a>Дополнительная информация

* [Установка и запуск контейнеров Распознавателя документов](form-recognizer-container-howto.md)
