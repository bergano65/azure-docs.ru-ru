---
title: Установка речевых контейнеров — служба речи
titleSuffix: Azure Cognitive Services
description: Установите и запустите речевые контейнеры. Преобразование речи в текст позволяет расшифровывать аудиопотоки в режиме реального времени и сохранять их в текстовом формате, который ваши приложения, инструменты или устройства могут использовать или отображать. Преобразование текста в речь преобразует вводимый текст в синтезированную речь, похожую на человеческую.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: b83ece803a72fe6132c1ba98414e95897a0fcc90
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304880"
---
# <a name="install-and-run-speech-service-containers"></a>Установка и запуск контейнеров речевых служб

Контейнеры позволяют запускать некоторые API речевых служб в собственной среде. Контейнеры отлично подходят для конкретных требований к безопасности и управлению данными. В этой статье вы узнаете, как скачать, установить и запустить речевой контейнер.

Речевые контейнеры позволяют клиентам создавать архитектуру приложений для распознавания речи, оптимизированных как для надежных облачных возможностей, так и для пограничных локализации. Доступно четыре разных контейнера. Два стандартных контейнера — преобразование **речи в текст** и преобразование **текста в речь**. Два пользовательских контейнера — это **пользовательское распознавание речи** и **Пользовательский текст в речь**.

> [!IMPORTANT]
> Все контейнеры речи в настоящее время предоставляются как часть [общедоступного "условного" предварительного просмотра](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). Объявление будет создано, когда речевые контейнеры переводятся в общедоступную версию.

| Функция | Функции | Последняя |
|--|--|--|
| Преобразование речи в текст. | Расшифровывает непрерывных речевых или пакетных звуковых записей в режиме реального времени в текст с промежуточными результатами. | 2.0.0 |
| Пользовательское распознавание речи к тексту | Используя настраиваемую модель на [портале пользовательское распознавание речи](https://speech.microsoft.com/customspeech), расшифровывает непрерывную голосовую или пакетную звукозапись в режиме реального времени в текст с промежуточными результатами. | 2.0.0 |
| Преобразование текста в речь | Преобразует текст в голосовую речь с помощью обычного текстового ввода или языка разметки речи (SSML). | 1.3.0 |
| Пользовательский текст в речь | С помощью настраиваемой модели [пользовательского голосового портала](https://aka.ms/custom-voice-portal)преобразует текст в голосовую речь с помощью обычного текстового ввода или языка разметки речи (SSML). | 1.3.0 |

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

Перед использованием речевых контейнеров выполните следующие предварительные требования.

| обязательные | Назначение |
|--|--|
| Модуль Docker | На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). См. [общие сведения о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br> |
| Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`. |
| Речевой ресурс | Для использования контейнеров необходимо следующее:<br><br>Ресурс _речи_ Azure для получения связанного ключа API и URI конечной точки. Оба значения доступны на страницах "Обзор **речи** " и "ключи" портал Azure. Они необходимы для запуска контейнера.<br><br>**{API_KEY}** : один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}** : конечная точка, указанная на странице **обзора** |

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Заполните и отправьте [форму запроса Cognitive Services речевых контейнеров](https://aka.ms/speechcontainerspreview/) , чтобы запросить доступ к контейнеру. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Поддержка расширенного векторного расширения

**Главным** является компьютер, на котором выполняется контейнер Docker. Узел *должен поддерживать* [Расширенные векторные расширения](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Вы можете проверить поддержку AVX2 на узлах Linux с помощью следующей команды:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Главный компьютер *необходим* для поддержки AVX2. Контейнер *не будет* работать правильно без поддержки AVX2.

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальное и рекомендуемое выделение ресурсов для каждого контейнера речи.

# <a name="speech-to-texttabstt"></a>[Преобразование речи в текст](#tab/stt)

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
| Преобразование речи в текст. | 2 ядра, 2 ГБ памяти | 4 ядра, 4 ГБ памяти |

# <a name="custom-speech-to-texttabcstt"></a>[Пользовательское распознавание речи к тексту](#tab/cstt)

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
| Пользовательское распознавание речи к тексту | 2 ядра, 2 ГБ памяти | 4 ядра, 4 ГБ памяти |

# <a name="text-to-speechtabtts"></a>[Преобразование текста в речь](#tab/tts)

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
| Преобразование текста в речь | 1 ядро, 2 ГБ памяти | 2 ядра, 3 ГБ памяти |

# <a name="custom-text-to-speechtabctts"></a>[Пользовательский текст в речь](#tab/ctts)

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
| Пользовательский текст в речь | 1 ядро, 2 ГБ памяти | 2 ядра, 3 ГБ памяти |

***

* Частота каждого ядра должна быть минимум 2,6 ГГц.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

> [!NOTE]
> Минимальное и рекомендуемое значение основано на ограничениях DOCKER, а *не* на ресурсах компьютера размещения. Например, контейнеры преобразования речи в текст отображают части модели больших языков, и *рекомендуется* , чтобы весь файл поместился в память, что является дополнительным 4-6 ГБ. Кроме того, первый запуск любого контейнера может занять больше времени, так как модели разбиваются на страницы в памяти.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Образы контейнеров для речи доступны в следующем реестре контейнеров.

# <a name="speech-to-texttabstt"></a>[Преобразование речи в текст](#tab/stt)

| Контейнер | Репозиторий |
|-----------|------------|
| Преобразование речи в текст. | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[Пользовательское распознавание речи к тексту](#tab/cstt)

| Контейнер | Репозиторий |
|-----------|------------|
| Пользовательское распознавание речи к тексту | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[Преобразование текста в речь](#tab/tts)

| Контейнер | Репозиторий |
|-----------|------------|
| Преобразование текста в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[Пользовательский текст в речь](#tab/ctts)

| Контейнер | Репозиторий |
|-----------|------------|
| Пользовательский текст в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Извлечение DOCKER для контейнеров распознавания речи

# <a name="speech-to-texttabstt"></a>[Преобразование речи в текст](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Опрашивающий запрос DOCKER для контейнера преобразования речи в текст

Используйте команду [DOCKER Pull](https://docs.docker.com/engine/reference/commandline/pull/) , чтобы скачать образ контейнера из реестра предварительного просмотра контейнера.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> Тег `latest` извлекает языковой стандарт `en-US`. Дополнительные языковые стандарты см. [в разделе языки и речь](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Языки перевода речи в текст

Все теги, кроме `latest`, имеют следующий формат и чувствительны к регистру:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Следующий тег является примером формата:

```
2.0.0-amd64-en-us-preview
```

Сведения о всех поддерживаемых языковых стандартах контейнера **для преобразования речи** в текст см. в статье [теги изображений](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-texttabcstt"></a>[Пользовательское распознавание речи к тексту](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Опрашивающий запрос DOCKER для контейнера Пользовательское распознавание речи в текст

Используйте команду [DOCKER Pull](https://docs.docker.com/engine/reference/commandline/pull/) , чтобы скачать образ контейнера из реестра предварительного просмотра контейнера.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> `locale` и `voice` для пользовательских речевых контейнеров определяются настраиваемой моделью, принимаемой контейнером.

# <a name="text-to-speechtabtts"></a>[Преобразование текста в речь](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Извлечение DOCKER для контейнера текста в речь

Используйте команду [DOCKER Pull](https://docs.docker.com/engine/reference/commandline/pull/) , чтобы скачать образ контейнера из реестра предварительного просмотра контейнера.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> Тег `latest` извлекает языковой стандарт `en-US` и `jessarus` голоса. Дополнительные языковые стандарты см. [в разделе Языки перевода текста в речь](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Языки перевода текста в речь

Все теги, кроме `latest`, имеют следующий формат и чувствительны к регистру:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Следующий тег является примером формата:

```
1.3.0-amd64-en-us-jessarus-preview
```

Все поддерживаемые языковые стандарты и соответствующие **голоса контейнера преобразования текста в речь см** . в разделе [теги изображений текста в речь](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> При создании стандартного HTTP-запроса POST *для преобразования текста в речь* для сообщения [SSML (язык разметки речи)](speech-synthesis-markup.md) требуется элемент `voice` с атрибутом `name`. Значение представляет собой соответствующий языковой стандарт контейнера и голоса, также называемый ["коротким именем"](language-support.md#standard-voices). Например, тег `latest` будет иметь имя голоса `en-US-JessaRUS`.

# <a name="custom-text-to-speechtabctts"></a>[Пользовательский текст в речь](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Опрашивающий запрос DOCKER для пользовательского контейнера преобразования текста в речь

Используйте команду [DOCKER Pull](https://docs.docker.com/engine/reference/commandline/pull/) , чтобы скачать образ контейнера из реестра предварительного просмотра контейнера.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> `locale` и `voice` для пользовательских речевых контейнеров определяются настраиваемой моделью, принимаемой контейнером.

***

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](speech-container-configuration.md#example-docker-run-commands) команды `docker run`.
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Сведения о том, как получить значения `{Endpoint_URI}` и `{API_Key}`, см. в разделе [сбор обязательных параметров](#gathering-required-parameters) . Также доступны дополнительные [примеры](speech-container-configuration.md#example-docker-run-commands) команды `docker run`.

# <a name="speech-to-texttabstt"></a>[Преобразование речи в текст](#tab/stt)

Чтобы запустить контейнер преобразования *речи в текст* , выполните следующую команду `docker run`.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер преобразования *речи в текст* из образа контейнера.
* Выделяет 4 ядра ЦП и 4 гигабайта (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

# <a name="custom-speech-to-texttabcstt"></a>[Пользовательское распознавание речи к тексту](#tab/cstt)

Пользовательское распознавание речи контейнер полагается на пользовательскую модель речевого *ввода* . Пользовательская модель должна быть [обучена](how-to-custom-speech-train-model.md) с помощью [пользовательского портала речевого](https://speech.microsoft.com/customspeech)ввода. Для запуска контейнера требуется **идентификатор пользовательской модели** распознавания речи. Его можно найти на странице " **обучение** " пользовательского речевого портала. На настраиваемом портале речевого ввода перейдите на страницу **обучения** и выберите модель.
<br>

![Страница обучения пользовательской речи](media/custom-speech/custom-speech-model-training.png)

Получите **идентификатор модели** для использования в качестве аргумента параметра `ModelId` команды `docker run`.
<br>

![Сведения о пользовательской модели речи](media/custom-speech/custom-speech-model-details.png)

В следующей таблице представлены различные параметры `docker run` и их соответствующие описания.

| Параметр | ОПИСАНИЕ |
|---------|---------|
| `{VOLUME_MOUNT}` | Узел [тома](https://docs.docker.com/storage/volumes/)главного компьютера, который DOCKER использует для сохранения настраиваемой модели. Например, *к:\кустомспич* , где *диск C* находится на хост-компьютере. |
| `{MODEL_ID}` | **Идентификатор модели** пользовательское распознавание речи на странице **обучения** пользовательского речевого портала. |
| `{ENDPOINT_URI}` | Для оценки и выставления счетов требуется конечная точка. Дополнительные сведения см. в разделе [сбор обязательных параметров](#gathering-required-parameters). |
| `{API_KEY}` | Требуется ключ API. Дополнительные сведения см. в разделе [сбор обязательных параметров](#gathering-required-parameters). |

Чтобы запустить контейнер *пользовательское распознавание речи в текст* , выполните следующую команду `docker run`:

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

* Выполняет *пользовательское распознавание речиный* контейнер из образа контейнера.
* Выделяет 4 ядра ЦП и 4 гигабайта (ГБ) памяти.
* Загружает *пользовательское распознавание речиную* модель из подключения ввода тома, например *к:\кустомспич*.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* Скачивает модель с учетом `ModelId` (если она не найдена на подключении тома).
* Если ранее была загружена пользовательская модель, `ModelId` игнорируется.
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

# <a name="text-to-speechtabtts"></a>[Преобразование текста в речь](#tab/tts)

Чтобы запустить контейнер преобразования *текста в речь* , выполните следующую команду `docker run`.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Выполняет контейнер преобразования *текста в речь* из образа контейнера.
* Выделяет 2 ядра ЦП и один гигабайт (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

# <a name="custom-text-to-speechtabctts"></a>[Пользовательский текст в речь](#tab/ctts)

Пользовательский контейнер преобразования *текста в речь* использует пользовательскую голосовую модель. Пользовательская модель должна быть [обучена](how-to-custom-voice-create-voice.md) с помощью [пользовательского Voice](https://aka.ms/custom-voice-portal). Для запуска контейнера требуется идентификатор настраиваемой **модели** голоса. Его можно найти на странице **обучение** на настраиваемом голосовом портале. На портале настраиваемого голоса перейдите на страницу **обучения** и выберите модель.
<br>

![Страница настраиваемого речевого обучения](media/custom-voice/custom-voice-model-training.png)

Получите **идентификатор модели** для использования в качестве аргумента параметра `ModelId` команды DOCKER Run.
<br>

![Сведения о настраиваемой модели голоса](media/custom-voice/custom-voice-model-details.png)

В следующей таблице представлены различные параметры `docker run` и их соответствующие описания.

| Параметр | ОПИСАНИЕ |
|---------|---------|
| `{VOLUME_MOUNT}` | Узел [тома](https://docs.docker.com/storage/volumes/)главного компьютера, который DOCKER использует для сохранения настраиваемой модели. Например, *к:\кустомспич* , где *диск C* находится на хост-компьютере. |
| `{MODEL_ID}` | **Идентификатор модели** пользовательское распознавание речи на странице " **обучение** " настраиваемого голоса Portal. |
| `{ENDPOINT_URI}` | Для оценки и выставления счетов требуется конечная точка. Дополнительные сведения см. в разделе [сбор обязательных параметров](#gathering-required-parameters). |
| `{API_KEY}` | Требуется ключ API. Дополнительные сведения см. в разделе [сбор обязательных параметров](#gathering-required-parameters). |

Чтобы запустить пользовательский контейнер преобразования *текста в речь* , выполните следующую команду `docker run`:

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

* Выполняет пользовательский контейнер преобразования *текста в речь* из образа контейнера.
* Выделяет 2 ядра ЦП и один гигабайт (ГБ) памяти.
* Загружает *настраиваемую модель преобразования текста в речь* из подключения ввода тома, например *к:\кустомвоице*.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* Скачивает модель с учетом `ModelId` (если она не найдена на подключении тома).
* Если ранее была загружена пользовательская модель, `ModelId` игнорируется.
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

***

> [!IMPORTANT]
> Для выполнения контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

| Контейнер | Конечная точка | Протокол |
|--|--|--|
| Преобразование речи в текст. | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | WS |
| Пользовательское распознавание речи к тексту | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | WS |
| Преобразование текста в речь | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |
| Пользовательский текст в речь | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |

Дополнительные сведения об использовании протоколов WSS и HTTPS см. в разделе [Безопасность контейнера](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Преобразование текста в речь или пользовательский текст в речь

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Запуск нескольких контейнеров на одном узле

Если вы планируете запускать несколько контейнеров при открытых портах, обязательно назначьте каждому контейнеру отдельный открытый порт. Например, запускайте первый контейнер на порте 5000, а второй — на порте 5001.

Этот контейнер и другой контейнер Azure Cognitive Services могут одновременно работать на одном и том же узле. Также одному запущенному контейнеру Cognitive Services могут соответствовать сразу несколько контейнеров.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

При запуске или запуске контейнера могут возникнуть проблемы. Используйте [Подключение](speech-container-configuration.md#mount-settings) к выходным данным и включите ведение журнала. Это позволит контейнеру создавать файлы журналов, которые полезны при устранении неполадок.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

Контейнеры распознавания речи отправляют сведения о выставлении счетов в Azure с помощью *речевого* ресурса в учетной записи Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>summary

В этой статье вы узнали основные понятия и рабочий процесс по скачиванию, установке и запуску речевых контейнеров. Краткая сводка.

* Речь предоставляет четыре контейнера Linux для DOCKER, включая различные возможности:
  * *Преобразование речи в текст*
  * *Пользовательское распознавание речи к тексту*
  * *Преобразование текста в речь*
  * *Пользовательский текст в речь*
* Образы контейнеров загружаются из реестра контейнеров в Azure.
* Образы контейнеров, которые выполняются в Docker.
* Вы можете использовать REST API или пакет SDK для вызова операций в контейнерах речи, указав универсальный код ресурса (URI) узла контейнера.
* При создании экземпляра контейнера необходимо указать сведения о выставлении счетов.

> [!IMPORTANT]
>  Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например, анализируемые изображения или тексты).

## <a name="next-steps"></a>Дополнительная информация

* Проверьте настройки [контейнеров](speech-container-configuration.md) на наличие параметров конфигурации.
* Узнайте, как [использовать контейнеры службы речи с Kubernetes и Helm](speech-container-howto-on-premises.md)
* Использование большего числа [контейнеров Cognitive Services](../cognitive-services-container-support.md)
