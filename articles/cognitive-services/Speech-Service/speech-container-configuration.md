---
title: Настройка контейнеров для речевой речи
titleSuffix: Azure Cognitive Services
description: Служба речевого сообщения предоставляет каждому контейнеру общую структуру конфигурации, так что вы можете легко настроить и управлять хранением, журналом и телеметрией, а также настройками безопасности для контейнеров.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c74aa48b18661236eb55278d1e5a05215b2432c
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877581"
---
# <a name="configure-speech-service-containers"></a>Настройка контейнеров для обслуживания речи

Речевые контейнеры позволяют клиентам создавать одну архитектуру речевого приложения, которая оптимизирована, чтобы использовать как надежные возможности облака, так и локализацию края. Четыре речевых контейнеров мы поддерживаем в настоящее время, **речь к тексту**, **пользовательские речи к тексту**, **текст к речи**, и **пользовательские текст к речи**.

Среда выполнения контейнера **речи** настроена `docker run` с помощью аргументов команды. Контейнер поддерживает несколько обязательных и несколько необязательных параметров. Доступны несколько [примеров](#example-docker-run-commands) этой команды. Для конкретного контейнера настраиваются входные параметры выставления счетов.

## <a name="configuration-settings"></a>Параметры конфигурации

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> , [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)и [`Eula`](#eula-setting) настройки используются вместе, и вы должны предоставить действительные значения для всех трех из них; в противном случае ваш контейнер не запустится. Дополнительные сведения об использовании этих параметров конфигурации для создания экземпляра контейнера см. в разделе [Выставление счетов](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Параметр конфигурации ApiKey

Параметр `ApiKey` определяет ключ ресурса Azure, который используется для отслеживания данных для выставления счетов для контейнера. Необходимо указать значение для ApiKey, а значение должно быть действительным [`Billing`](#billing-configuration-setting) ключом для ресурса _Speech,_ указанного для настройки конфигурации.

Этот параметр можно найти в следующем месте.

- Портал Azure: Управление ресурсами **речи,** под **ключами**

## <a name="applicationinsights-setting"></a>Параметр ApplicationInsights.

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Параметр конфигурации выставления счетов

В `Billing` параметре указывается конечная точка URI ресурса _Speech_ на Azure, используемая для измерения платежной информации для контейнера. Необходимо указать значение для этого параметра конфигурации, а значение должно быть действительным URI-конечным пунктом для ресурса _Speech_ в Azure. Отчеты об использовании контейнера примерно каждые 10—15 минут.

Этот параметр можно найти в следующем месте.

- Портал Azure: Обзор **речи,** помеченный`Endpoint`

| Обязательно | Имя | Тип данных | Описание |
| -------- | ---- | --------- | ----------- |
| Да | `Billing` | Строка | Биллинг endpoint URI. Для получения дополнительной информации о получении биллинга URI [см.](speech-container-howto.md#gathering-required-parameters) Дополнительные сведения и полный список региональных конечных точек см. в статье [Custom subdomain names for Cognitive Services](../cognitive-services-custom-subdomains.md) (Пользовательские имена поддоменов для Cognitive Services). |

## <a name="eula-setting"></a>Параметр Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Параметры Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Параметры учетных данных прокси-сервера HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Параметры ведения журнала

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Параметры подключения

Используйте подключения привязок для чтения данных из контейнера и записи в него. Вы можете указать входное или выходное подключение, указав параметр `--mount` в команде [docker run](https://docs.docker.com/engine/reference/commandline/run/).

Контейнеры Standard Speech не используют входные или выходные крепления для хранения обучаемых или сервисных данных. Тем не менее, пользовательские речевые контейнеры полагаются на объемные крепления.

Точный синтаксис расположения подключения к узлу зависит от операционной системы узла. Кроме того,расположение подключения на [главном компьютере](speech-container-howto.md#the-host-computer) может оказаться недоступным из-за конфликта между разрешениями для учетной записи службы Docker и расположения подключения к узлу.

| Необязательный | Имя | Тип данных | Описание |
| -------- | ---- | --------- | ----------- |
| Не разрешено | `Input` | Строка | Стандартные контейнеры речи не используют это. Пользовательские речевые контейнеры используют [реанительные крепления.](#volume-mount-settings)                                                                                    |
| Необязательный | `Output` | Строка | Цель выходного подключения. Значение по умолчанию — `/output`. Это расположение файлов журналов. Сюда входят журналы контейнера. <br><br>Пример<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Настройки крепления объема

Пользовательские речевые контейнеры используют [реанительные крепления](https://docs.docker.com/storage/volumes/) для сохранения пользовательских моделей. Можно указать монтаж громкости, добавив `-v` (или) `--volume`опцию в команду [докера.](https://docs.docker.com/engine/reference/commandline/run/)

Пользовательские модели загружаются в первый раз, когда новая модель попадает в качестве части пользовательского докера контейнера речи запустить команду. Последовательные запуски того же `ModelId` для пользовательского речевого контейнера будут использовать ранее загруженную модель. Если монтировка громкости не предусмотрена, пользовательские модели не могут быть упорными.

Установка крепления объема состоит `:` из трех цветовых разделенных полей:

1. Первое поле — это название громкости на хост-машине, например _C:'ввод_.
2. Второе поле — каталог в контейнере, например _/usr/local/models_.
3. Третье поле (необязательно) представляет собой разделенный на запятую список опций, для получения дополнительной [информации см.](https://docs.docker.com/storage/volumes/)

### <a name="volume-mount-example"></a>Пример крепления объема

```bash
-v C:\input:/usr/local/models
```

Эта команда устанавливает каталог _хоста C: ввод_ в контейнеры _/usr/local/models._

> [!IMPORTANT]
> Настройки крепления громкости применимы только к **контейнерам Custom Speech-to-text** и **Custom Text-to-speech.** Стандартные контейнеры **«Речи к тексту»** и **«Текст-к-речи»** не используют объемные крепления.

## <a name="example-docker-run-commands"></a>Примеры команд docker run

В следующих примерах параметры конфигурации иллюстрируют процесс написания и использования команд `docker run`. После запуска контейнер продолжает работу, пока вы его не [остановите](speech-container-howto.md#stop-the-container).

- **Персонаж продолжения строки**: Команды Докера в следующих `\`разделах используют заднюю слэш, в качестве символа продолжения линии. Замените или удалите ее в соответствии с требованиями вашей операционной системы.
- **Порядок аргумента**: Не изменяйте порядок аргументов, если вы не знакомы с контейнерами Docker.

Замените строку {_имя_аргумента_} собственными значениями.

| Заполнитель | Значение | Формат или пример |
| ----------- | ----- | ----------------- |
| **(API_KEY)** | Ключ конечной точки `Speech` ресурса на `Speech` странице Azure Keys.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **(ENDPOINT_URI)** | Значение конечных точек выставления `Speech` счетов доступно на странице Azure Overview. | [Ознакомьтесь со сбором необходимых параметров](speech-container-howto.md#gathering-required-parameters) для явных примеров. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится. Дополнительные сведения см. в [разделе о выставлении счетов](#billing-configuration-setting).
> Значение ApiKey — это **ключ** со страницы клавиш ресурсов речи Azure.

## <a name="speech-container-docker-examples"></a>Примеры речевого контейнера Docker

Следующие примеры Docker для контейнера речи.

## <a name="speech-to-text"></a>[Преобразование речи в текст](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Базовый пример для речи к тексту

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Пример регистрации для речевого текста

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Пользовательские речи к тексту](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Базовый пример для пользовательской речи к тексту

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Пример регистрации для пользовательской речи к тексту

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[Преобразование текста в речь](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Основной пример для текста к речи

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Пример регистрации для текста к речи

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Пользовательский текст к речи](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Базовый пример для пользовательского текста к речи

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Пример регистрации для пользовательского текста к речи

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Дальнейшие действия

- Изучите статью об [установке и запуске контейнеров](speech-container-howto.md).
