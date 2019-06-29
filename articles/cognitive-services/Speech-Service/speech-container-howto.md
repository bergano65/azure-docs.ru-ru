---
title: Установить контейнеры речи
titleSuffix: Azure Cognitive Services
description: Установите и запустите контейнеры речи. Преобразование речи в текст позволяет расшифровывать аудиопотоки в режиме реального времени и сохранять их в текстовом формате, который ваши приложения, инструменты или устройства могут использовать или отображать. Преобразование текста в речь преобразует вводимый текст в синтезированную речь, похожую на человеческую.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 6df40909e2072ac2126344ba5b696c9e520e9955
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67434140"
---
# <a name="install-and-run-speech-service-containers"></a>Установка и запуск контейнеров службы распознавания речи

Контейнеры речи Предоставьте клиентам возможность создавать один архитектура приложения речи, оптимизированный для преимущества надежные облачные возможности и Населенный пункт edge. 

Контейнеры два речи — **речи в текст** и **преобразования текста в речь**. 

|Функция|Функции|Последние|
|-|-|--|
|Преобразование речи в текст| <li>Расшифровывает непрерывной в режиме реального времени речи или пакета, аудиозаписи в текст с промежуточными результатами.|1.1.2|
|Преобразование текста в речь| <li>Преобразует текст в естественно звучащую речь. с помощью ввода обычного текста или языка разметки синтеза речи (SSML). |1.1.0|

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

Прежде чем использовать контейнеры речи, необходимо выполнить следующие условия:

|Обязательно для заполнения|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс речи |Для использования контейнеров необходимо следующее:<br><br>Объект _речи_ ресурсов Azure, чтобы получить связанный ключ выставления счетов и выставления счетов URI конечной точки. Оба значения доступны на портале Azure **речи** страницы Обзор и ключи на них, необходимые для запуска контейнера.<br><br>**{BILLING_KEY}** : ключ ресурса<br><br>**{BILLING_ENDPOINT_URI}** : пример URI конечной точки: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Сначала выполните и отправить [форму запроса контейнеры речи Cognitive Services](https://aka.ms/speechcontainerspreview/) чтобы запросить доступ к контейнеру. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Расширенная поддержка векторные расширения

**Главным** является компьютер, на котором выполняется контейнер Docker. Узел должен поддерживать [расширения Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Вы можете проверить эта поддержка на узлах Linux с помощью следующей команды: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

Ниже перечислены минимальные и рекомендуемые ядер ЦП и памяти, выделяемой для каждого контейнера речи.

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 ядра<br>2 ГБ памяти  | 4 ядра<br>4 ГБ памяти  |
|cognitive-services-text-to-speech | 1 ядро, 0,5 ГБ памяти| 2 ядра, 1 ГБ памяти |

* Частота каждого ядра должна быть минимум 2,6 ГГц.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

**Примечание**; Минимальные и рекомендуемые основаны на ограничения Docker *не* ресурсов компьютера, узел. Например, контейнеры речи в текст частей карты памяти больших языковая модель и он является _рекомендуется_ что весь файл помещается в памяти, который является Дополнительно 4 – 6 ГБ. Кроме того время первого выполнения, либо контейнера может занять больше времени, так как модели страниц в памяти.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Доступны образы контейнеров для распознавания речи.

| Контейнер | Репозиторий |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Языковой стандарт — в теге контейнера

`latest` Тег переносит `en-us` языкового стандарта и `jessarus` голоса.

#### <a name="speech-to-text-locales"></a>Преобразование речи в текст языковых стандартов

Все теги, за исключением `latest` находятся в следующем формате, где `<culture>` указывает контейнер языкового стандарта:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Следующий тег приведен пример формата:

```
1.1.2-amd64-en-us-preview
```

В следующей таблице перечислены поддерживаемые языковые стандарты для **речи в текст** в 1.1.2 версии контейнера:

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

#### <a name="text-to-speech-locales"></a>Языковые стандарты преобразования текста в речь

Все теги, за исключением `latest` находятся в следующем формате, где `<culture>` указывает языковой стандарт и `<voice>` обозначает голос контейнера:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Следующий тег приведен пример формата:

```
1.1.0-amd64-en-us-jessarus-preview
```

В следующей таблице перечислены поддерживаемые языковые стандарты для **преобразования текста в речь** в 1.1.0 версии контейнера:

|Языковой стандарт|Tags|Поддерживаемые голоса|
|--|--|--|
|Китайский|`zh-cn`|huihuirus<br>kangkang apollo<br>yaoyao apollo|
|Английский |`en-au`|catherine<br>hayleyrus|
|Английский |`en-gb`|Джордж apollo<br>hazelrus<br>сьюзан apollo|
|Английский |`en-in`|heera apollo<br>priyarus<br>Рави apollo<br>|
|Английский |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Французский|`fr-ca`|кэролайн<br>harmonierus|
|Французский|`fr-fr`|hortenserus<br>Джули apollo<br>Пол apollo|
|Немецкий|`de-de`|hedda<br>heddarus<br>Стефан apollo|
|Итальянский|`it-it`|cosimo apollo<br>luciarus|
|Японский|`ja-jp`|ayumi apollo<br>harukarus<br>ichiro apollo|
|Корейский|`ko-kr`|heamirus|
|Португальский|`pt-br`|Дэниэл apollo<br>heloisarus|
|Испанский|`es-es`|elenarus<br>Лора apollo<br>пабло apollo<br>|
|Испанский|`es-mx`|hildarus<br>Павел apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Получение образов docker для контейнеров речи

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

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми, но не используемыми настройками выставления счетов. Доступны дополнительные [примеры](speech-container-configuration.md#example-docker-run-commands) команды `docker run`.
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска любого из трех контейнеров. В команде используются следующие параметры:

**На этапе предварительной версии**, выставления счетов параметров должен быть допустимым для запуска контейнера, но не будет выставлен счет за использование.

| Placeholder | Значение |
|-------------|-------|
|{BILLING_KEY} | Этот ключ используется для запуска контейнера, а также доступны на странице ключей речи на портале Azure.  |
|{BILLING_ENDPOINT_URI} | Выставления счетов значение URI конечная точка доступна на странице обзора речи на портале Azure.|

В следующем примере команды `docker run` замените имена параметров собственными значениями.

### <a name="text-to-speech"></a>Преобразование текста в речь

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

### <a name="speech-to-text"></a>Преобразование речи в текст

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Эта команда:

* Запускает контейнер речи из образа контейнера
* Выделяет 2 ядер ЦП и 2 гигабайта (ГБ) памяти
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

|Контейнер|Конечная точка|
|--|--|
|Преобразование речи в текст|ws: / / localhost:5000/речи/распознавания/диктовки/cognitiveservices/v1|
|Преобразование текста в речь|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Преобразование речи в текст

Контейнер предоставляет конечную точку запросов websocket API, который можно открыть с помощью [Speech SDK](index.yml).

По умолчанию Speech SDK использует службы online речи. Чтобы использовать контейнер, вам необходимо изменить метод инициализации. Ознакомьтесь с указанными ниже примерами.

#### <a name="for-c"></a>Для C#

Смените этот вызов инициализации облака Azure:

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

на этот вызов с использованием конечной точки контейнера:

```C#
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

Контейнер предоставляет конечную точку REST API-интерфейсы, которые можно найти [здесь](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) и примеры можно найти [здесь](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

При запуске контейнер использует **stdout** и **stderr** для вывода информации, полезной при устранении неполадок, которые случаются при запуске или выполнении контейнера.

## <a name="billing"></a>Выставление счетов

Отправить контейнеры речи, выставления счетов в Azure, с помощью _речи_ ресурсов на учетную запись Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье вы узнали основные понятия и рабочий процесс для загрузки, установки и запуска контейнеров речи. В разделе "Сводка" сделайте следующее.

* Речи реализовано два вида контейнеров Linux для Docker, инкапсулируя преобразования речи в текст и текста в речь.
* Образы контейнеров скачиваются из частного реестра контейнеров в Azure.
* Образы контейнеров выполняются в Docker.
* Пакет SDK или REST API можно использовать для вызова операций в контейнерах речи, указав узла URI контейнера.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
>  Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например, анализируемые изображения или тексты).

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [конфигурации контейнеров](speech-container-configuration.md).
* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../cognitive-services-container-support.md)
