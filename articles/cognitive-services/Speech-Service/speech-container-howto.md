---
title: Установка речевых контейнеров - Речевой сервис
titleSuffix: Azure Cognitive Services
description: Установка и запуск речевых контейнеров. Преобразование речи в текст позволяет расшифровывать аудиопотоки в режиме реального времени и сохранять их в текстовом формате, который ваши приложения, инструменты или устройства могут использовать или отображать. Преобразование текста в речь преобразует вводимый текст в синтезированную речь, похожую на человеческую.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: 2beee81bc365d00e59a62cacabacc5f5d6b62a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474787"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Установка и запуск контейнеров службы речевой речи (Предварительный просмотр)

Контейнеры позволяют запускать некоторые AA-иносов службы речевой связи в вашей собственной среде. Контейнеры отлично подходят для конкретных требований к безопасности и управлению данными. В этой статье вы узнаете, как загружать, устанавливать и запускать контейнер Speech.

Речевые контейнеры позволяют клиентам создавать архитектуру речевого приложения, оптимизированную как для надежных облачных возможностей, так и для локальности края. Есть четыре различных контейнеров доступны. Два стандартных контейнера — **«Речь к тексту»** и **«Текст к речи».** Два пользовательских контейнера **пользовательские речи к тексту** и **пользовательских текст к речи**.

> [!IMPORTANT]
> Все речевые контейнеры в настоящее время предлагаются в рамках [публичного "Gated" Предварительный](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio)просмотр . Объявление будет сделано, когда речевые контейнеры прогресса в общей доступности (GA).

| Компонент | Компоненты | Последняя версия |
|--|--|--|
| Преобразование речи в текст | Транскрибирует непрерывную речь в реальном времени или пакетные аудиозаписи в текст с промежуточными результатами. | 2.1.1 |
| Пользовательские речи к тексту | Используя пользовательскую модель с [портала Custom Speech,](https://speech.microsoft.com/customspeech)транскрибирует непрерывную речь в реальном времени или пакетные аудиозаписи в текст с промежуточными результатами. | 2.1.1 |
| Преобразование текста в речь | Преобразует текст в естественно звучающую речь с простым ввозам текста или языком речевого синтеза разметки (SSML). | 1.3.0 |
| Пользовательский текст к речи | Используя пользовательскую модель с [портала Custom Voice,](https://aka.ms/custom-voice-portal)преобразует текст в естественно звучающую речь с простым ввозам текста или языком разметки синтеза речи (SSML). | 1.3.0 |

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Следующие предпосылки перед использованием речевых контейнеров:

| Обязательно | Назначение |
|--|--|
| Модуль Docker | На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду с Docker для [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). См. [общие сведения о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br> |
| Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`. |
| Речевой ресурс | Для использования контейнеров необходимо следующее:<br><br>Ресурс Azure _Speech_ для получения связанного ключа API и URI с конечными точками. Оба значения доступны на страницах **портала** Azure и страниц ы ключей. Они оба необходимы для запуска контейнера.<br><br>**«API_KEY:** Один из двух доступных ключей ресурса на странице **Ключей**<br><br>**(ENDPOINT_URI:** конечная точка, представленная на странице **Обзор** |

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Заполните и отправьте [форму Запроса речевых контейнеров Cognitive Services,](https://aka.ms/speechcontainerspreview/) чтобы запросить доступ к контейнеру. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Расширенная поддержка расширения векторного расширения

**Главным** является компьютер, на котором выполняется контейнер Docker. Хозяин *должен поддерживать* [Расширенные расширения вектор](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Вы можете проверить поддержку AVX2 на хостах Linux со следующей командой:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Компьютер-хозяин *необходим* для поддержки AVX2. Контейнер *не будет* функционировать правильно без поддержки AVX2.

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описывается минимальное и рекомендуемое распределение ресурсов для каждого контейнера речи.

# <a name="speech-to-text"></a>[Речево-текст](#tab/stt)

| Контейнер | Минимальные | Рекомендуемая |
|-----------|---------|-------------|
| Преобразование речи в текст | 2 ядра, 2-ГБ памяти | 4 ядра, 4-ГБ памяти |

# <a name="custom-speech-to-text"></a>[Пользовательские речи к тексту](#tab/cstt)

| Контейнер | Минимальные | Рекомендуемая |
|-----------|---------|-------------|
| Пользовательские речи к тексту | 2 ядра, 2-ГБ памяти | 4 ядра, 4-ГБ памяти |

# <a name="text-to-speech"></a>[Текст к речи](#tab/tts)

| Контейнер | Минимальные | Рекомендуемая |
|-----------|---------|-------------|
| Преобразование текста в речь | 1 ядро, 2-ГБ памяти | 2 ядра, 3-ГБ памяти |

# <a name="custom-text-to-speech"></a>[Пользовательский текст к речи](#tab/ctts)

| Контейнер | Минимальные | Рекомендуемая |
|-----------|---------|-------------|
| Пользовательский текст к речи | 1 ядро, 2-ГБ памяти | 2 ядра, 3-ГБ памяти |

***

* Частота каждого ядра должна быть минимум 2,6 ГГц.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

> [!NOTE]
> Минимальные и рекомендуемые основаны на границах Docker, *а не* на ресурсах хоста. Например, контейнеры от речи к тексту карта памяти части большой модели языка, и *рекомендуется,* чтобы весь файл вписывается в память, которая является дополнительным 4-6 ГБ. Кроме того, первый запуск любого контейнера может занять больше времени, так как модели в настоящее время на странице в памяти.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Изображения контейнеров для речи доступны в следующем реестре контейнеров.

# <a name="speech-to-text"></a>[Речево-текст](#tab/stt)

| Контейнер | Хранилище |
|-----------|------------|
| Преобразование речи в текст | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Пользовательские речи к тексту](#tab/cstt)

| Контейнер | Хранилище |
|-----------|------------|
| Пользовательские речи к тексту | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Текст к речи](#tab/tts)

| Контейнер | Хранилище |
|-----------|------------|
| Преобразование текста в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Пользовательский текст к речи](#tab/ctts)

| Контейнер | Хранилище |
|-----------|------------|
| Пользовательский текст к речи | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Докер тянуть для речи контейнеров

# <a name="speech-to-text"></a>[Речево-текст](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Докер тянуть для речи к тексту контейнера

Используйте команду [докера тянуть,](https://docs.docker.com/engine/reference/commandline/pull/) чтобы загрузить изображение контейнера из реестра контейнеров Preview.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> Тег `latest` тянет локализовать. `en-US` Для дополнительных локализов [см.](#speech-to-text-locales)

#### <a name="speech-to-text-locales"></a>Локализовать речь к тексту

Все теги, `latest` за исключением, находятся в следующем формате и чувствительны к случаям:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Следующий тег является примером формата:

```
2.1.1-amd64-en-us-preview
```

Для всех поддерживаемых лоценок контейнера **от речи к тексту,** пожалуйста, смотрите [теги изображений Speech-to-text.](../containers/container-image-tags.md#speech-to-text)

# <a name="custom-speech-to-text"></a>[Пользовательские речи к тексту](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Докер тянуть для пользовательской речи к тексту контейнера

Используйте команду [докера тянуть,](https://docs.docker.com/engine/reference/commandline/pull/) чтобы загрузить изображение контейнера из реестра контейнеров Preview.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> И `locale` `voice` для пользовательских контейнеров речи определяется пользовательской модели попадает в контейнер.

# <a name="text-to-speech"></a>[Текст к речи](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Докер тянуть для текст-к-речи контейнера

Используйте команду [докера тянуть,](https://docs.docker.com/engine/reference/commandline/pull/) чтобы загрузить изображение контейнера из реестра контейнеров Preview.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> Тег `latest` тянет локал `en-US` из `jessarus` нас и голос. Для дополнительных локализов [см.](#text-to-speech-locales)

#### <a name="text-to-speech-locales"></a>Текстовые локалы

Все теги, `latest` за исключением, находятся в следующем формате и чувствительны к случаям:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Следующий тег является примером формата:

```
1.3.0-amd64-en-us-jessarus-preview
```

Для всех поддерживаемых логоцен и соответствующих голосов контейнера **текст-речь,** пожалуйста, смотрите [текст к речи изображения теги](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> При построении *стандартного текстового* к речи HTTP POST, [сообщение language Markup Language синтеза речи (SSML)](speech-synthesis-markup.md) требует `voice` элемента с атрибутом. `name` Значение соответствующего локаль контейнера и голос, также известный как ["короткое имя"](language-support.md#standard-voices). Например, `latest` тег будет иметь голосовое имя `en-US-JessaRUS`.

# <a name="custom-text-to-speech"></a>[Пользовательский текст к речи](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Докер тянуть для пользовательского текст-к-речи контейнера

Используйте команду [докера тянуть,](https://docs.docker.com/engine/reference/commandline/pull/) чтобы загрузить изображение контейнера из реестра контейнеров Preview.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> И `locale` `voice` для пользовательских контейнеров речи определяется пользовательской модели попадает в контейнер.

***

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](speech-container-configuration.md#example-docker-run-commands) команды `docker run`.
1. [Запрос конечная точка прогнозирования контейнера.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Обратитесь к [сбору необходимых параметров](#gathering-required-parameters) `{Endpoint_URI}` для `{API_Key}` получения подробной информации о том, как получить и значения. Также доступны `docker run` [дополнительные примеры](speech-container-configuration.md#example-docker-run-commands) команды.

# <a name="speech-to-text"></a>[Речево-текст](#tab/stt)

Для запуска контейнера *«Речи к тексту»* выполните следующую `docker run` команду.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер *«Речи к тексту»* с изображения контейнера.
* Выделяет 4 ядра процессора и 4 гигабайта (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

# <a name="custom-speech-to-text"></a>[Пользовательские речи к тексту](#tab/cstt)

*Пользовательские речи к тексту* контейнер атлет опирается на пользовательскую модель речи. Пользовательская модель должна быть [обучена](how-to-custom-speech-train-model.md) с помощью [пользовательского портала речи.](https://speech.microsoft.com/customspeech)

> [!IMPORTANT]
> Модель пользовательской речи должна быть обучена из одной из следующих версий модели:
> * **20181201 (v3.3 Единый)**
> * **20190520 (v4.14 Единый)**
> * **20190701 (v4.17 Единый)**<br>
> ![Пользовательская модель контейнера поезда речи](media/custom-speech/custom-speech-train-model-container-scoped.png)

Для запуска контейнера требуется пользовательский **идентификатор модели** речи. Его можно найти на странице **обучения** пользовательского речевого портала. С пользовательского речевого портала перейдите на страницу **Training** и выберите модель.
<br>

![Пользовательская страница обучения речи](media/custom-speech/custom-speech-model-training.png)

Получите **идентификатор модели** `ModelId` для использования `docker run` в качестве аргумента в параметр команды.
<br>

![Пользовательские детали модели речи](media/custom-speech/custom-speech-model-details.png)

Следующая таблица `docker run` представляет различные параметры и соответствующие описания:

| Параметр | Описание |
|---------|---------|
| `{VOLUME_MOUNT}` | Устанавливается [объем](https://docs.docker.com/storage/volumes/)компьютера-хозяина, который докер использует для сохранения пользовательской модели. Например, *C:'CustomSpeech,* где *диск C* расположен на хост-машине. |
| `{MODEL_ID}` | **Идентификатор модели** пользовательской речи со страницы **обучения** пользовательского речевого портала. |
| `{ENDPOINT_URI}` | Конечная точка необходима для измерения и выставления счетов. Для получения дополнительной [информации](#gathering-required-parameters)см. |
| `{API_KEY}` | Требуется ключ API. Для получения дополнительной [информации](#gathering-required-parameters)см. |

Чтобы запустить контейнер *Custom Speech-to-text,* выполните следующую `docker run` команду:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает *пользовательский* контейнер «Речи к тексту» с изображения контейнера.
* Выделяет 4 ядра процессора и 4 гигабайта (ГБ) памяти.
* Загружает пользовательскую модель *речи к тексту* с крепления ввода громкости, например *C:'CustomSpeech*.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* Загружает модель `ModelId` с учетом (если не найдено на томе крепления).
* Если пользовательская модель была ранее `ModelId` загружена, она игнорируется.
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

# <a name="text-to-speech"></a>[Текст к речи](#tab/tts)

Для запуска контейнера *«Текст-к-речи»* выполните следующую `docker run` команду.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер *«Текст к речи»* с изображения контейнера.
* Выделяет 2 ядра процессора и один гигабайт (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

# <a name="custom-text-to-speech"></a>[Пользовательский текст к речи](#tab/ctts)

*Контейнер Custom Text-to-speech* опирается на пользовательскую голосовую модель. Пользовательская модель должна быть [обучена](how-to-custom-voice-create-voice.md) с помощью [пользовательского голосового портала.](https://aka.ms/custom-voice-portal) Для запуска контейнера требуется пользовательский **идентификатор модели** голоса. Его можно найти на странице **обучения** пользовательского голосового портала. С пользовательского голосового портала перейдите на страницу **Training** и выберите модель.
<br>

![Пользовательская страница обучения голоса](media/custom-voice/custom-voice-model-training.png)

Получите **идентификатор модели** `ModelId` для использования в качестве аргумента в параметр команды докера.
<br>

![Пользовательские детали голосовой модели](media/custom-voice/custom-voice-model-details.png)

Следующая таблица `docker run` представляет различные параметры и соответствующие описания:

| Параметр | Описание |
|---------|---------|
| `{VOLUME_MOUNT}` | Устанавливается [объем](https://docs.docker.com/storage/volumes/)компьютера-хозяина, который докер использует для сохранения пользовательской модели. Например, *C:'CustomSpeech,* где *диск C* расположен на хост-машине. |
| `{MODEL_ID}` | Пользовательские речи **модель id** с **учебной** странице пользовательского портала голос. |
| `{ENDPOINT_URI}` | Конечная точка необходима для измерения и выставления счетов. Для получения дополнительной [информации](#gathering-required-parameters)см. |
| `{API_KEY}` | Требуется ключ API. Для получения дополнительной [информации](#gathering-required-parameters)см. |

Чтобы запустить контейнер *Custom Text-to-speech,* выполните следующую `docker run` команду:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает *пользовательский* контейнер текст к речи с изображения контейнера.
* Выделяет 2 ядра процессора и один гигабайт (ГБ) памяти.
* Загружает пользовательскую модель *текстки в речь* от крепления ввода громкости, например *C:'CustomVoice*.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* Загружает модель `ModelId` с учетом (если не найдено на томе крепления).
* Если пользовательская модель была ранее `ModelId` загружена, она игнорируется.
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

***

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

| Контейнеры | URL-адрес SDK Host | Протокол |
|--|--|--|
| Речь к тексту и пользовательские речи к тексту | `ws://localhost:5000` | WS |
| Текст к речи и пользовательский текст к речи | `http://localhost:5000` | HTTP |

Для получения дополнительной информации об использовании [container security](../cognitive-services-container-support.md#azure-cognitive-services-container-security)протоколов WSS и HTTPS см.

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Текст к речи или пользовательский текст к речи

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Запуск нескольких контейнеров на одном узле

Если вы планируете запускать несколько контейнеров при открытых портах, обязательно назначьте каждому контейнеру отдельный открытый порт. Например, запускайте первый контейнер на порте 5000, а второй — на порте 5001.

Этот контейнер и другой контейнер Azure Cognitive Services могут одновременно работать на одном и том же узле. Также одному запущенному контейнеру Cognitive Services могут соответствовать сразу несколько контейнеров.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

При запуске или запуске контейнера могут возникнуть проблемы. Используйте выходное [крепление](speech-container-configuration.md#mount-settings) и включите журнал. Это позволит контейнеру генерировать файлы журнала, которые полезны при устранении неполадок.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

Контейнеры речевой речи отправляют информацию о выставлении счетов в Azure с помощью ресурса *Speech* в вашей учетной записи Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье вы изучили концепции и рабочий процесс для загрузки, установки и запуска контейнеров речевой речи. В разделе "Сводка" сделайте следующее.

* Речь предоставляет четыре Linux контейнеров для Docker, инкапсулируя различные возможности:
  * *Речево-текст*
  * *Пользовательские речи к тексту*
  * *Текст к речи*
  * *Пользовательский текст к речи*
* Изображения контейнеров загружаются из реестра контейнеров в Azure.
* Образы контейнеров выполняются в Docker.
* Используете ли aPI REST (только текст к речи) или SDK ("Речи к тексту" или "Текст к речи") вы указываете хост URI контейнера. 
* Вы обязаны предоставлять платежную информацию при мгновенном вхотворе контейнера.

> [!IMPORTANT]
>  Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например, анализируемые изображения или тексты).

## <a name="next-steps"></a>Дальнейшие действия

* Просмотр [контейнеров для](speech-container-configuration.md) настройки конфигурации
* Узнайте, как [использовать контейнеры для обслуживания речевых услуг с Kubernetes и Helm](speech-container-howto-on-premises.md)
* Используйте больше [контейнеров Cognitive Services](../cognitive-services-container-support.md)
