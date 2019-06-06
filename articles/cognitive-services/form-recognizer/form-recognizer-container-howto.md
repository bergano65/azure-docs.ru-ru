---
title: Установка и запуск контейнера. Распознаватель документов
titleSuffix: Azure Cognitive Services
description: Сведения об интерпретации данных формы и таблицы с помощью контейнера Распознавателя документов.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/31/2019
ms.author: pafarley
ms.openlocfilehash: 1c9e68f643f27f70190b5847225692d554cc5480
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475444"
---
# <a name="install-and-run-form-recognizer-containers"></a>Установка и запуск контейнеров Распознавателя документов
Распознаватель документов применяет технологии машинного обучения, чтобы идентифицировать и извлечь пары "ключ — значение" и таблицы из форм. Он связывает с ними значения и записи в таблицах, а затем выводит структурированные данные, которые включают в себя связи в исходном файле. Вы можете вызывать пользовательскую модель Распознавателя документов с помощью простого REST API, чтобы упростить и легко интегрировать ее в свой рабочий процесс автоматизации или другое приложение. Необходимо всего пять документов (или пустых форм), поэтому вы можете получать результаты быстро, точно и с учетом специфики вашего содержимого, не прибегая к сложному ручному вмешательству или продолжительной обработке и анализу данных. Это не требует добавления меток данных или заметок к данным.

|Функция|Функции|
|-|-|
|Распознаватель документов| <li>Обрабатывает файлы типа PDF, PNG и JPG.<li>Обучает пользовательские модели с минимум 5 формами одного макета. <li>Извлекает пары "ключ — значение" и сведения о таблице. <li>Использует API распознавания текста Компьютерного зрения Cognitive Service для обнаружения и извлечения печатного текста из изображений в формах.<li>Не требует добавления заметок или меток.|

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем использовать контейнеры Распознавателя документов, необходимо выполнить следующие условия:

|Обязательно|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.|
|Инфраструктура CLI Azure| Вам нужно установить [интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) на узле.|
|Ресурс API Компьютерного зрения| Для обработки отсканированных документов и изображений требуется **Компьютерное зрение**. Вы можете получить доступ к **Распознаванию текста** как к ресурсу Azure (REST API или пакету SDK) или [контейнеру](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) `cognitive-services-recognize-text`. Взымается обычная плата. <br><br>Вы должны передать ключ и конечную точку выставления счетов для определенного ресурса Компьютерного зрения (облако Azure или контейнер Cognitive Services). Используйте этот ключ и конечную точку выставления счетов в качестве {COMPUTER_VISION_API_KEY} и {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Если вы используете **контейнер `cognitive-services-recognize-text`** , убедитесь, что:<br><br>* Ваш ключ Компьютерного зрения для контейнера Распознавателя документов — это ключ, указанный в команде `docker run` Компьютерного зрения для контейнера `cognitive-services-recognize-text`.<br>* Ваша конечная точка выставления счетов — это конечная точка контейнера, например `https://localhost:5000`. При совместном использовании контейнеров Компьютерного зрения и Распознавателя документов на одному узле их невозможно одновременно запустить с помощью порта по умолчанию `5000`.  |  
|Ресурс Распознавателя документов |Для использования контейнеров необходимо следующее:<br><br>Ресурс Azure _Распознаватель документов_, чтобы получить связанный ключ выставления счетов и URI конечной точки выставления счетов. Оба значения доступны на страницах "Обзор" и "Ключи" **Распознавателя документов** на портале Azure и необходимы для запуска контейнера.<br><br>**{BILLING_KEY}** : ключ ресурса<br><br>**{BILLING_ENDPOINT_URI}** : пример URI конечной точки: `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Прежде всего необходимо заполнить и отправить [форму запроса к контейнерам Распознавателя документов Cognitive Services](https://aka.ms/FormRecognizerRequestAccess), чтобы запросить доступ к контейнеру. При этом вы зарегистрируетесь в Компьютерном зрении. Вам не нужно регистрироваться в форме запроса Компьютерного зрения отдельно. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальное и рекомендуемое число ядер ЦП и памяти для каждого контейнера Распознавателя документов.

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 ядра, 4 ГБ памяти | 4 ядра, 8 ГБ памяти |

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* TPS — транзакций в секунду.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

> [!Note]
> Минимальные и рекомендуемые значения основаны на ограничениях Docker,а *не* на ресурсах узла.

## <a name="get-the-container-image-with-docker-pull-command"></a>Получение образа контейнера с помощью команды docker pull

Доступны образы контейнеров для Распознавателя документов.

| Контейнер | Репозиторий |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Если вы планируете использовать [контейнер](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) `cognitive-services-recognize-text` вместо Распознавателя документов, убедитесь, что используется команда `docker pull` с правильным именем контейнера: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-form-recognizer-container"></a>Выполнение docker pull для контейнера Распознавателя документов

#### <a name="form-recognizer"></a>Распознаватель документов

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми, но не используемыми настройками выставления счетов. Доступны дополнительные [примеры](form-recognizer-container-configuration.md#example-docker-run-commands) команды `docker run`.
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска любого из трех контейнеров. В команде используются следующие параметры:

| Placeholder | Значение |
|-------------|-------|
|{BILLING_KEY} | Этот ключ используется для запуска контейнера и доступен на странице ключей Распознавателя документов на портале Azure.  |
|{BILLING_ENDPOINT_URI} | Значение URI конечной точки выставления счетов доступно на странице обзора Распознавателя документов на портале Azure.|
|{COMPUTER_VISION_API_KEY}| Ключ доступен на странице ключей API Компьютерного зрения на портале Azure.|
|{COMPUTER_VISION_ENDPOINT_URI}|Конечная точка выставления счетов. При использовании облачного ресурса Компьютерного зрения значение URI доступно на странице обзора API Компьютерного зрения на портале Azure. Если вы используете контейнер `cognitive-services-recognize-text`, используйте URL-адрес конечной точки выставления счетов, переданный контейнеру в команде `docker run`.|

В следующем примере команды `docker run` замените имена параметров собственными значениями.

### <a name="form-recognizer"></a>Распознаватель документов

```bash
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

Эта команда:

* запускает контейнер Распознавателя документов из образа контейнера;
* выделяет 2 ядра ЦП и 8 гигабайт (ГБ) памяти;
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере.
* подключает тома /input и /output к контейнеру.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Запуск отдельных контейнеров в качестве отдельных команд docker run

Для комбинации Распознавателя документов и Распознавателя текста, размещенной локально на одном узле, приведено два примера команд CLI Docker.

Выполните первый контейнер на порте 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Запустите второй контейнер на порте 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Каждому последующему контейнеру должен быть назначен отдельный порт. 

### <a name="run-separate-containers-with-docker-compose"></a>Запуск отдельных контейнеров с помощью Docker Compose

Для комбинации Распознавателя документов и Распознавателя текста, размещенной локально на одном узле, приведен пример файла YAML Docker Compose. Распознаватель текста `{COMPUTER_VISION_API_KEY}` должен быть одинаковым для обоих контейнеров: `formrecognizer` и `ocr`. `{COMPUTER_VISION_ENDPOINT_URI}` используется только в контейнере `ocr`, так как контейнер `formrecognizer` использует имя и порт `ocr`. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: {COMPUTER_VISION_API_KEY}  

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"  
```


> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`, а также параметры `FormRecognizer:ComputerVisionApiKey` и `FormRecognizer:ComputerVisionEndpointUri`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

|Контейнер|Конечная точка|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Распознаватель документов

Контейнер предоставляет API конечных точек запроса на основе веб-сокетов, доступ к которым осуществляется с помощью [документации по пакету SDK для служб Распознавателя документов](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

По умолчанию пакет SDK Распознавателя документов использует веб-службы. Чтобы использовать контейнер, вам необходимо изменить метод инициализации. Ознакомьтесь с указанными ниже примерами.

#### <a name="for-c"></a>Для C#

Смените этот вызов инициализации облака Azure:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

на этот вызов с использованием конечной точки контейнера:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>Для Python

Смените этот вызов инициализации облака Azure:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

на этот вызов с использованием конечной точки контейнера:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Распознаватель документов

Контейнер предоставляет API-интерфейсы конечной точки REST, которые можно найти [здесь](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

При запуске контейнер использует **stdout** и **stderr** для вывода информации, полезной при устранении неполадок, которые случаются при запуске или выполнении контейнера.

## <a name="billing"></a>Выставление счетов

Контейнеры Распознавания текста отправляют данные для выставления счетов в Azure с помощью ресурса _Распознаватель документов_ в учетной записи Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](form-recognizer-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье вы узнали основные понятия и рабочий процесс для скачивания, установки и выполнения контейнеров Распознавателя документов. В разделе "Сводка" сделайте следующее.

* Распознаватель документов предоставляет один контейнер Linux для Docker.
* Образы контейнеров скачиваются из частного реестра контейнеров в Azure.
* Образы контейнеров выполняются в Docker.
* Указав узел URI контейнера, вы можете вызвать операции в контейнерах Распознавателя документов с помощью пакета SDK или REST API.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
>  Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например, анализируемые изображения или тексты).

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со статьей о [конфигурации контейнеров](form-recognizer-container-configuration.md).
* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../cognitive-services-container-support.md)
