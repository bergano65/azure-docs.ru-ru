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
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 14a360e7dc672266e8445a5ae5eb2168ae766af8
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741862"
---
# <a name="install-and-run-speech-service-containers"></a>Установка и запуск контейнеров речевых служб

Речевые контейнеры позволяют клиентам создавать одну архитектуру приложения для распознавания речи, оптимизированную для использования преимуществ как надежных облачных возможностей, так и пограничных локализации. 

Два речевых контейнера — преобразование **речи в текст** и преобразование **текста в речь**. 

|Функция|Компоненты|Последний|
|-|-|--|
|Преобразование речи в текст| <li>Расшифровывает непрерывных речевых или пакетных звуковых записей в режиме реального времени в текст с промежуточными результатами.|1.1.3|
|Преобразование текста в речь| <li>Преобразует текст в естественно звучащую речь. с обычным текстовым входом или с языком разметки речи (SSML). |1.1.0|

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительные требования

Перед использованием речевых контейнеров необходимо выполнить следующие предварительные требования:

|Обязательное значение|Цель|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Речевой ресурс |Для использования контейнеров необходимо следующее:<br><br>Ресурс _речи_ Azure для получения связанного ключа API и URI конечной точки. Оба значения доступны на страницах "Обзор **речи** " и "ключи" портал Azure. Они необходимы для запуска контейнера.<br><br>**{API_KEY}** : Один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}** : Конечная точка, указанная на странице **обзора**|

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Сначала необходимо завершить и отправить [форму запроса Cognitive Services речевых контейнеров](https://aka.ms/speechcontainerspreview/) , чтобы запросить доступ к контейнеру. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Поддержка расширенного векторного расширения

**Главным** является компьютер, на котором выполняется контейнер Docker. Узел должен поддерживать [Расширенные векторные расширения](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Эту поддержку можно проверить на узлах Linux с помощью следующей команды: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальные и Рекомендуемые ядра ЦП и память, выделяемые для каждого контейнера речи.

| Контейнер | Минимум | Рекомендуется |
|-----------|---------|-------------|
|Озвучивание-службы-преобразование речи в текст | 2 ядра<br>2 ГБ памяти  | 4 ядра<br>4 ГБ памяти  |
|Распознавание-услуги-преобразование текста в речь | 1 ядро, 0,5 ГБ памяти| 2 ядра, 1 ГБ памяти |

* Частота каждого ядра должна быть минимум 2,6 ГГц.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

**Примечание**. Минимальное и рекомендуемое значение основано на ограничениях DOCKER, а *не* на ресурсах компьютера размещения. Например, контейнеры преобразования речи в текст отображают части модели больших языков, и _рекомендуется_ , чтобы весь файл поместился в память, что является дополнительным 4-6 ГБ. Кроме того, первый запуск любого контейнера может занять больше времени, так как модели разбиваются на страницы в памяти.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Доступны образы контейнеров для речи.

| Контейнер | Репозиторий |
|-----------|------------|
| Озвучивание-службы-преобразование речи в текст | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| Распознавание-услуги-преобразование текста в речь | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Языковой стандарт языка находится в теге контейнера

Тег извлекает языковой стандарт и `jessarus` голосовое значение `en-us`. `latest`

#### <a name="speech-to-text-locales"></a>Преобразование речи в языки текста

Все теги, кроме `latest` , имеют следующий формат, `<culture>` где указывает на языковой стандарт.

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Следующий тег является примером формата:

```
1.1.3-amd64-en-us-preview
```

В следующей таблице перечислены поддерживаемые языковые стандарты для преобразования **речи в текст** в 1.1.3 версии контейнера.

|Языковой стандарт|Tags|
|--|--|
|Китайский|`zh-cn`|
|Английский |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Французский |`fr-ca`<br>`fr-fr`|
|Немецкий|`de-de`|
|Итальянский|`it-it`|
|Японский|`ja-jp`|
|Корейский|`ko-kr`|
|Португальский|`pt-br`|
|Испанский|`es-es`<br>`es-mx`|

#### <a name="text-to-speech-locales"></a>Языки перевода текста в речь

Все теги, кроме `latest` , имеют следующий формат, `<culture>` где `<voice>` указывает языковой стандарт, а указывает голоса контейнера:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Следующий тег является примером формата:

```
1.1.0-amd64-en-us-jessarus-preview
```

В следующей таблице перечислены поддерживаемые языковые стандарты для преобразования **текста в речь** в 1.1.0 версии контейнера.

|Языковой стандарт|Tags|Поддерживаемые голоса|
|--|--|--|
|Китайский|`zh-cn`|хуихуирус<br>кангканг — Apollo<br>яойао — Apollo|
|Английский |`en-au`|касерине<br>хайлэйрус|
|Английский |`en-gb`|Георгия-Apollo<br>хазелрус<br>Ирина-Apollo|
|Английский |`en-in`|Хира — Apollo<br>прийарус<br>Рави — Apollo<br>|
|Английский |`en-us`|жессарус<br>бенжаминрус<br>jessa24krus<br>зирарус<br>guy24krus|
|Французский|`fr-ca`|Кэролайн<br>хармониерус|
|Французский|`fr-fr`|хортенсерус<br>Юлия — Apollo<br>Пол-Apollo|
|Немецкий|`de-de`|хедда<br>хеддарус<br>Стефан — Apollo|
|Итальянский|`it-it`|Косимо — Apollo<br>луЦиарус|
|Японский|`ja-jp`|айуми — Apollo<br>харукарус<br>Ичиро — Apollo|
|Корейский|`ko-kr`|хеамирус|
|Португальский|`pt-br`|Даниэль — Apollo<br>хелоисарус|
|Испанский|`es-es`|еленарус<br>Мария-Apollo<br>Пабло — Apollo<br>|
|Испанский|`es-mx`|хилдарус<br>Павел — Apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Извлечение DOCKER для контейнеров распознавания речи

#### <a name="speech-to-text"></a>Преобразование речи в текст

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Преобразование текста в речь

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](speech-container-configuration.md#example-docker-run-commands) команды `docker run`.
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска любого из трех контейнеров. В команде используются следующие параметры:

В **течение предварительной версии**параметры выставления счетов должны быть допустимыми для запуска контейнера, но плата за использование не взимается.

| Местозаполнитель | Значение |
|-------------|-------|
|{API_KEY} | Этот ключ используется для запуска контейнера и доступен на странице портал Azure голосовых ключей.  |
|{ENDPOINT_URI} | Значение URI конечной точки выставления счетов доступно на странице обзора речи портал Azure.|

В следующем примере команды `docker run` замените имена параметров собственными значениями.

### <a name="text-to-speech"></a>Преобразование текста в речь

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="speech-to-text"></a>Преобразование речи в текст

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* Запускает контейнер речи из образа контейнера.
* Выделяет 2 ядра ЦП и 2 гигабайта (ГБ) памяти.
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

|Контейнер|Конечная точка|
|--|--|
|Преобразование речи в текст|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Преобразование текста в речь|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Преобразование речи в текст

Контейнер предоставляет интерфейсы API конечной точки запроса на основе WebSocket, доступ к которым осуществляется через [пакет SDK для распознавания речи](index.yml).

По умолчанию в пакете SDK для речевых функций используются службы речевого перевода. Чтобы использовать контейнер, вам необходимо изменить метод инициализации. Ознакомьтесь с указанными ниже примерами.

#### <a name="for-c"></a>Для C#

Смените этот вызов инициализации облака Azure:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

на этот вызов с использованием конечной точки контейнера:

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>Для Python

Смените этот вызов инициализации облака Azure:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

на этот вызов с использованием конечной точки контейнера:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Преобразование текста в речь

Контейнер предоставляет интерфейсы API конечной точки RESTFUL, которые можно найти [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) , а примеры можно найти [здесь](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

При запуске контейнер использует **stdout** и **stderr** для вывода информации, полезной при устранении неполадок, которые случаются при запуске или выполнении контейнера.

## <a name="billing"></a>Выставление счетов

Контейнеры распознавания речи отправляют сведения о выставлении счетов в Azure с помощью речевого ресурса в учетной записи Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье вы узнали основные понятия и рабочий процесс по скачиванию, установке и запуску речевых контейнеров. В разделе "Сводка" сделайте следующее.

* Речь предоставляет два контейнера Linux для DOCKER, инкапсулирующий речь в тексте и тексте в речь.
* Образы контейнеров скачиваются из частного реестра контейнеров в Azure.
* Образы контейнеров выполняются в Docker.
* Вы можете использовать REST API или пакет SDK для вызова операций в контейнерах речи, указав универсальный код ресурса (URI) узла контейнера.
* При создании экземпляра контейнера необходимо указать сведения о выставлении счетов.

> [!IMPORTANT]
>  Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например, анализируемые изображения или тексты).

## <a name="next-steps"></a>Следующие шаги

* Ознакомьтесь со статьей о [конфигурации контейнеров](speech-container-configuration.md).
* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../cognitive-services-container-support.md)
