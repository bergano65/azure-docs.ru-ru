---
title: Install Speech containers - Speech Service
titleSuffix: Azure Cognitive Services
description: Install and run speech containers. Преобразование речи в текст позволяет расшифровывать аудиопотоки в режиме реального времени и сохранять их в текстовом формате, который ваши приложения, инструменты или устройства могут использовать или отображать. Преобразование текста в речь преобразует вводимый текст в синтезированную речь, похожую на человеческую.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 3d9373067c78f1fe0fa0b414886c30f2ed3c1c9f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325865"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Install and run Speech Service containers (Preview)

Containers enable you to run some of the Speech Service APIs in your own environment. Containers are great for specific security and data governance requirements. In this article you'll learn how to download, install, and run a Speech container.

Speech containers enable customers to build a speech application architecture that is optimized for both robust cloud capabilities and edge locality. There are four different containers available. The two standard containers are **Speech-to-text** and **Text-to-speech**. The two custom containers are **Custom Speech-to-text** and **Custom Text-to-speech**.

> [!IMPORTANT]
> All speech containers are currently offered as part of a [Public "Gated" Preview](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). An announcement will be made when speech containers progress to General Availability (GA).

| Функция | Компоненты | Latest |
|--|--|--|
| Преобразование речи в текст | Transcribes continuous real-time speech or batch audio recordings into text with intermediate results. | 2.0.0 |
| Custom Speech-to-text | Using a custom model from the [Custom Speech portal](https://speech.microsoft.com/customspeech), transcribes continuous real-time speech or batch audio recordings into text with intermediate results. | 2.0.0 |
| Преобразование текста в речь | Converts text to natural-sounding speech with plain text input or Speech Synthesis Markup Language (SSML). | 1.3.0 |
| Custom Text-to-speech | Using a custom model from the [Custom Voice portal](https://aka.ms/custom-voice-portal), converts text to natural-sounding speech with plain text input or Speech Synthesis Markup Language (SSML). | 1.3.0 |

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

The following prerequisites before using Speech containers:

| Обязательно для заполнения | Цель |
|--|--|
| Модуль Docker | На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br> |
| Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`. |
| Speech resource | Для использования контейнеров необходимо следующее:<br><br>An Azure _Speech_ resource to get the associated API key and endpoint URI. Both values are available on the Azure portal's **Speech** Overview and Keys pages. They are both required to start the container.<br><br>**{API_KEY}** : One of the two available resource keys on the **Keys** page<br><br>**{ENDPOINT_URI}** : The endpoint as provided on the **Overview** page |

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Fill out and submit the [Cognitive Services Speech Containers Request form](https://aka.ms/speechcontainerspreview/) to request access to the container. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Advanced Vector Extension support

**Главным** является компьютер, на котором выполняется контейнер Docker. The host *must support* [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). You can check for AVX2 support on Linux hosts with the following command:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> The host computer is *required* to support AVX2. The container *will not* function correctly without AVX2 support.

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

The following table describes the minimum and recommended allocation of resources for each Speech container.

# <a name="speech-to-texttabstt"></a>[Преобразование речи в текст](#tab/stt)

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
| Преобразование речи в текст | 2 core, 2-GB memory | 4 core, 4-GB memory |

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech-to-text](#tab/cstt)

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
| Custom Speech-to-text | 2 core, 2-GB memory | 4 core, 4-GB memory |

# <a name="text-to-speechtabtts"></a>[Преобразование текста в речь](#tab/tts)

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
| Преобразование текста в речь | 1 core, 2-GB memory | 2 core, 3-GB memory |

# <a name="custom-text-to-speechtabctts"></a>[Custom Text-to-speech](#tab/ctts)

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
| Custom Text-to-speech | 1 core, 2-GB memory | 2 core, 3-GB memory |

***

* Частота каждого ядра должна быть минимум 2,6 ГГц.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

> [!NOTE]
> The minimum and recommended are based off of Docker limits, *not* the host machine resources. For example, speech-to-text containers memory map portions of a large language model, and it is *recommended* that the entire file fits in memory, which is an additional 4-6 GB. Also, the first run of either container may take longer, since models are being paged into memory.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Container images for Speech are available in the following Container Registry.

# <a name="speech-to-texttabstt"></a>[Преобразование речи в текст](#tab/stt)

| Контейнер | Репозиторий |
|-----------|------------|
| Преобразование речи в текст | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech-to-text](#tab/cstt)

| Контейнер | Репозиторий |
|-----------|------------|
| Custom Speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[Преобразование текста в речь](#tab/tts)

| Контейнер | Репозиторий |
|-----------|------------|
| Преобразование текста в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[Custom Text-to-speech](#tab/ctts)

| Контейнер | Репозиторий |
|-----------|------------|
| Custom Text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker pull for the Speech containers

# <a name="speech-to-texttabstt"></a>[Преобразование речи в текст](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker pull for the Speech-to-text container

Use the [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) command to download a container image from Container Preview registry.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> The `latest` tag pulls the `en-US` locale. For additional locales see [Speech-to-text locales](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Speech-to-text locales

All tags, except for `latest` are in the following format and are case-sensitive:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

The following tag is an example of the format:

```
2.0.0-amd64-en-us-preview
```

For all of the supported locales of the **speech-to-text** container, please see [Speech-to-text image tags](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech-to-text](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker pull for the Custom Speech-to-text container

Use the [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) command to download a container image from Container Preview registry.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> The `locale` and `voice` for custom Speech containers is determined by the custom model ingested by the container.

# <a name="text-to-speechtabtts"></a>[Преобразование текста в речь](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker pull for the Text-to-speech container

Use the [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) command to download a container image from Container Preview registry.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> The `latest` tag pulls the `en-US` locale and `jessarus` voice. For additional locales see [Text-to-speech locales](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Text-to-speech locales

All tags, except for `latest` are in the following format and are case-sensitive:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

The following tag is an example of the format:

```
1.3.0-amd64-en-us-jessarus-preview
```

For all of the supported locales and corresponding voices of the **text-to-speech** container, please see [Text-to-speech image tags](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> When constructing a *Standard Text-to-speech* HTTP POST, the [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md) message requires a `voice` element with a `name` attribute. The value is the corresponding container locale and voice, also known as the ["short name"](language-support.md#standard-voices). For example, the `latest` tag would have a voice name of `en-US-JessaRUS`.

# <a name="custom-text-to-speechtabctts"></a>[Custom Text-to-speech](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker pull for the Custom Text-to-speech container

Use the [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) command to download a container image from Container Preview registry.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> The `locale` and `voice` for custom Speech containers is determined by the custom model ingested by the container.

***

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](speech-container-configuration.md#example-docker-run-commands) команды `docker run`.
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Refer to [gathering required parameters](#gathering-required-parameters) for details on how to get the `{Endpoint_URI}` and `{API_Key}` values. Additional [examples](speech-container-configuration.md#example-docker-run-commands) of the `docker run` command are also available.

# <a name="speech-to-texttabstt"></a>[Преобразование речи в текст](#tab/stt)

To run the *Speech-to-text* container, execute the following `docker run` command.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Runs a *Speech-to-text* container from the container image.
* Allocates 4 CPU cores and 4 gigabytes (GB) of memory.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech-to-text](#tab/cstt)

The *Custom Speech-to-text* container relies on a custom speech model. The custom model has to have been [trained](how-to-custom-speech-train-model.md) using the [custom speech portal](https://speech.microsoft.com/customspeech). The custom speech **Model ID** is required to run the container. It can be found on the **Training** page of the custom speech portal. From the custom speech portal, navigate to the **Training** page and select the model.
<br>

![Custom speech training page](media/custom-speech/custom-speech-model-training.png)

Obtain the **Model ID** to use as the argument to the `ModelId` parameter of the `docker run` command.
<br>

![Custom speech model details](media/custom-speech/custom-speech-model-details.png)

The following table represents the various `docker run` parameters and their corresponding descriptions:

| Параметр | Описание |
|---------|---------|
| `{VOLUME_MOUNT}` | The host computer [volume mount](https://docs.docker.com/storage/volumes/), which docker uses to persist the custom model. For example, *C:\CustomSpeech* where the *C drive* is located on the host machine. |
| `{MODEL_ID}` | The Custom Speech **Model ID** from the **Training** page of the custom speech portal. |
| `{ENDPOINT_URI}` | The endpoint is required for metering and billing. For more information, see [gathering required parameters](#gathering-required-parameters). |
| `{API_KEY}` | The API key is required. For more information, see [gathering required parameters](#gathering-required-parameters). |

To run the *Custom Speech-to-text* container, execute the following `docker run` command:

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

* Runs a *Custom Speech-to-text* container from the container image.
* Allocates 4 CPU cores and 4 gigabytes (GB) of memory.
* Loads the *Custom Speech-to-Text* model from the volume input mount, for example *C:\CustomSpeech*.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* Downloads the model given the `ModelId` (if not found on the volume mount).
* If the custom model was previously downloaded, the `ModelId` is ignored.
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

# <a name="text-to-speechtabtts"></a>[Преобразование текста в речь](#tab/tts)

To run the *Text-to-speech* container, execute the following `docker run` command.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Runs a *Text-to-speech* container from the container image.
* Allocates 2 CPU cores and one gigabyte (GB) of memory.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

# <a name="custom-text-to-speechtabctts"></a>[Custom Text-to-speech](#tab/ctts)

The *Custom Text-to-speech* container relies on a custom voice model. The custom model has to have been [trained](how-to-custom-voice-create-voice.md) using the [custom voice portal](https://aka.ms/custom-voice-portal). The custom voice **Model ID** is required to run the container. It can be found on the **Training** page of the custom voice portal. From the custom voice portal, navigate to the **Training** page and select the model.
<br>

![Custom voice training page](media/custom-voice/custom-voice-model-training.png)

Obtain the **Model ID** to use as the argument to the `ModelId` parameter of the docker run command.
<br>

![Custom voice model details](media/custom-voice/custom-voice-model-details.png)

The following table represents the various `docker run` parameters and their corresponding descriptions:

| Параметр | Описание |
|---------|---------|
| `{VOLUME_MOUNT}` | The host computer [volume mount](https://docs.docker.com/storage/volumes/), which docker uses to persist the custom model. For example, *C:\CustomSpeech* where the *C drive* is located on the host machine. |
| `{MODEL_ID}` | The Custom Speech **Model ID** from the **Training** page of the custom voice portal. |
| `{ENDPOINT_URI}` | The endpoint is required for metering and billing. For more information, see [gathering required parameters](#gathering-required-parameters). |
| `{API_KEY}` | The API key is required. For more information, see [gathering required parameters](#gathering-required-parameters). |

To run the *Custom Text-to-speech* container, execute the following `docker run` command:

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

* Runs a *Custom Text-to-speech* container from the container image.
* Allocates 2 CPU cores and one gigabyte (GB) of memory.
* Loads the *Custom Text-to-speech* model from the volume input mount, for example *C:\CustomVoice*.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* Downloads the model given the `ModelId` (if not found on the volume mount).
* If the custom model was previously downloaded, the `ModelId` is ignored.
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

***

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

| Контейнер | Конечная точка | Протокол |
|--|--|--|
| Преобразование речи в текст | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | WS |
| Custom Speech-to-text | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | WS |
| Преобразование текста в речь | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |
| Custom Text-to-speech | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |

For more information on using WSS and HTTPS protocols, see [container security](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Text-to-speech or Custom Text-to-speech

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Запуск нескольких контейнеров на одном узле

Если вы планируете запускать несколько контейнеров при открытых портах, обязательно назначьте каждому контейнеру отдельный открытый порт. Например, запускайте первый контейнер на порте 5000, а второй — на порте 5001.

Этот контейнер и другой контейнер Azure Cognitive Services могут одновременно работать на одном и том же узле. Также одному запущенному контейнеру Cognitive Services могут соответствовать сразу несколько контейнеров.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неисправностей

When starting or running the container, you may experience issues. Use an output [mount](speech-container-configuration.md#mount-settings) and enable logging. Doing so will allow the container to generate log files that are helpful when troubleshooting issues.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

The Speech containers send billing information to Azure, using a *Speech* resource on your Azure account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в разделе [Настройка контейнеров](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Резюме

In this article, you learned concepts and workflow for downloading, installing, and running Speech containers. В разделе "Сводка" сделайте следующее.

* Speech provides four Linux containers for Docker, encapsulating various capabilities:
  * *Преобразование речи в текст*
  * *Custom Speech-to-text*
  * *Преобразование текста в речь*
  * *Custom Text-to-speech*
* Container images are downloaded from the container registry in Azure.
* Образы контейнеров выполняются в Docker.
* You can use either the REST API or SDK to call operations in Speech containers by specifying the host URI of the container.
* You're required to provide billing information when instantiating a container.

> [!IMPORTANT]
>  Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например, анализируемые изображения или тексты).

## <a name="next-steps"></a>Дальнейшие действия

* Review [configure containers](speech-container-configuration.md) for configuration settings
* Learn how to [use Speech Service containers with Kubernetes and Helm](speech-container-howto-on-premises.md)
* Use more [Cognitive Services containers](../cognitive-services-container-support.md)
