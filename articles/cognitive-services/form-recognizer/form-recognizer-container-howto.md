---
title: How to install and run container for Form Recognizer
titleSuffix: Azure Cognitive Services
description: This article will explain how to use the Azure Cognitive Services Form Recognizer container to parse form and table data.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 21582a5a17a3c6f67182173bfe08d80c48765f7d
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325856"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Install and run Form Recognizer containers (Preview)

Распознаватель документов Azure применяет технологии машинного обучения, чтобы идентифицировать и извлечь пары "ключ — значение" и таблицы из форм. Он связывает значения и записи в таблицах с парами "ключ — значение", а затем выводит структурированные данные, которые включают в себя связи в исходном файле. 

Вы можете вызывать пользовательскую модель Распознавателя документов с помощью простого REST API, чтобы упростить и без труда интегрировать ее в свой рабочий процесс автоматизации или другое приложение. Only five form documents (or one empty form and two filled-in forms) are needed, so you can get results quickly, accurately, and tailored to your specific content. При этом не требуется выполнять трудоемкие операции вручную или хорошо разбираться в обработке и анализе данных. В процессе также не предусмотрено добавление меток данных или заметок к данным.

|Функция|Компоненты|
|-|-|
|Распознаватель документов| <li>Обрабатывает файлы PDF, PNG и JPG<li>Trains custom models with a minimum of five forms of the same layout <li>Извлекает пары "ключ — значение" и сведения о таблице <li>Использует функцию "Распознавания текста" API Компьютерного зрения Cognitive Service для обнаружения и извлечения печатного текста из изображений в формах<li>Не требует добавления заметок или меток|

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

Прежде чем использовать контейнеры Распознавателя документов, необходимо выполнить следующие условия:

|Обязательно для заполнения|Цель|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> В ОС Windows для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, таких как реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.|
|Интерфейс командной строки Azure| Установите в узле [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).|
|Ресурс API Компьютерного зрения| Для обработки отсканированных документов и изображений требуется API Компьютерного зрения. Функция "Распознавание текста" доступна либо в виде ресурса Azure (REST API или пакет SDK), либо в виде [контейнера](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull) *cognitive-services-recognize-text*. Взымается обычная плата. <br><br>Pass in both the API key and endpoints for your Computer Vision resource (Azure cloud or Cognitive Services container). Use this API key and the endpoint as **{COMPUTER_VISION_API_KEY}** and **{COMPUTER_VISION_ENDPOINT_URI}** .<br><br> Если вы используете контейнер *cognitive-services-recognize-text*, проверьте следующее:<br><br>Ваш ключ Компьютерного зрения для контейнера Распознавателя документов — это ключ, указанный в команде `docker run` Компьютерного зрения для контейнера *cognitive-services-recognize-text*.<br>Ваша конечная точка выставления счетов — это конечная точка контейнера, например `http://localhost:5000`. При совместном использовании контейнеров Компьютерного зрения и Распознавателя документов в одному узле их нельзя одновременно запустить с помощью порта *5000*, заданного по умолчанию. |
|Ресурс Распознавателя документов |Для использования контейнеров необходимо следующее:<br><br>An Azure **Form Recognizer** resource to get the associated API key and endpoint URI. Both values are available on the Azure portal **Form Recognizer** Overview and Keys pages, and both values are required to start the container.<br><br>**{FORM_RECOGNIZER_API_KEY}** : One of the two available resource keys on the Keys page<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}** : The endpoint as provided on the Overview page|

## <a name="gathering-required-parameters"></a>Gathering required parameters

There are three primary parameters for all Cognitive Services' containers that are required. The end-user license agreement (EULA) must be present with a value of `accept`. Additionally, both an Endpoint URL and API Key are needed.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Endpoint URI `{COMPUTER_VISION_ENDPOINT_URI}` and `{FORM_RECOGNIZER_ENDPOINT_URI}`

The **Endpoint** URI value is available on the Azure portal *Overview* page of the corresponding Cognitive Service resource. Navigate to the *Overview* page, hover over the Endpoint, and a `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> icon will appear. Copy and use where needed.

![Gather the endpoint uri for later use](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Keys `{COMPUTER_VISION_API_KEY}` and `{FORM_RECOGNIZER_API_KEY}`

This key is used to start the container, and is available on the Azure portal's Keys page of the corresponding Cognitive Service resource. Navigate to the *Keys* page, and click on the `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> icon.

![Get one of the two keys for later use](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> These subscription keys are used to access your Cognitive Service API. Do not share your keys. Store them securely, for example, using Azure Key Vault. We also recommend regenerating these keys regularly. Only one key is necessary to make an API call. When regenerating the first key, you can use the second key for continued access to the service.

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Прежде всего необходимо заполнить и отправить [форму запроса доступа к контейнерам Распознавателя документов Cognitive Services](https://aka.ms/FormRecognizerRequestAccess), чтобы запросить доступ к контейнеру. При этом вы зарегистрируетесь для использования API Компьютерного зрения. Вам не нужно специально выполнять вход в систему, чтобы заполнить форму запроса на API Компьютерного зрения. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В приведенной ниже таблице указаны минимальное и рекомендуемое число ядер ЦП и памяти для каждого контейнера Распознавателя документов.

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
| Распознаватель документов | 2 core, 4-GB memory | 4 core, 8-GB memory |
| Распознавание текста | 1 core, 8-GB memory | 2 cores, 8-GB memory |

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

> [!Note]
> Минимальные и рекомендуемые значения основаны на ограничениях Docker, *а не на* ресурсах узла.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Get the container images with the docker pull command

Container images for both the **Form Recognizer** and **Recognize Text** offerings are available in the following container registry:

| Контейнер | Fully qualified image name |
|-----------|------------|
| Распознаватель документов | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Распознавание текста | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

You will need both containers, please note that the **Recognizer Text** container is [detailed outside of this article.](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Выполнение docker pull для контейнера Распознавателя документов

#### <a name="form-recognizer"></a>Распознаватель документов

Получить контейнер Распознавателя документов можно с помощью следующей команды:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>docker pull для контейнера Распознавания текста

#### <a name="recognize-text"></a>Распознавание текста

To get the Recognize Text container, use the following command:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) выполните приведенные ниже инструкции для работы с ним.

1. [Запустите контейнер](#run-the-container-by-using-the-docker-run-command) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](form-recognizer-container-configuration.md#example-docker-run-commands) команды `docker run`.
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Команда docker run, выполняющая запуск контейнера

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Refer to [gathering required parameters](#gathering-required-parameters) for details on how to get the `{COMPUTER_VISION_ENDPOINT_URI}`, `{COMPUTER_VISION_API_KEY}`, `{FORM_RECOGNIZER_ENDPOINT_URI}` and `{FORM_RECOGNIZER_API_KEY}` values.

[Examples](form-recognizer-container-configuration.md#example-docker-run-commands) of the `docker run` command are available.

### <a name="form-recognizer"></a>Распознаватель документов

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
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

Для совместного локального размещения Распознавателя документов и Распознавателя текста в одном узле используйте два следующих примера команд CLI Docker:

Запустите первый контейнер на порте 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
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

Для совместного локального размещения Распознавателя документов и Распознавателя текста в одном узле используйте следующий пример YAML-файла Docker Compose: Распознаватель текста `{COMPUTER_VISION_API_KEY}` должен быть одинаковым для обоих контейнеров: `formrecognizer` и `ocr`. `{COMPUTER_VISION_ENDPOINT_URI}` используется только в контейнере `ocr`, так как контейнер `formrecognizer` использует имя и порт `ocr`. 

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
      apikey: "{COMPUTER_VISION_API_KEY}"

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
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
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
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`, а также параметры `FormRecognizer:ComputerVisionApiKey` и `FormRecognizer:ComputerVisionEndpointUri`. Иначе контейнер не запустится. Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

|Контейнер|Конечная точка|
|--|--|
|form-recognizer|http://localhost:5000

### <a name="form-recognizer"></a>Распознаватель документов

Контейнер предоставляет API конечных точек запроса на основе веб-сокетов, доступ к которым осуществляется с помощью [документации по пакету SDK для служб Распознавателя документов](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

По умолчанию пакет SDK Распознавателя документов использует веб-службы. Чтобы использовать контейнер, вам необходимо изменить метод инициализации. Ознакомьтесь с указанными ниже примерами.

#### <a name="for-c"></a>Для C#

Смените этот вызов инициализации облака Azure:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
на этот вызов, в котором используется конечная точка контейнера:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Для Python

Смените этот вызов инициализации облака Azure:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

на этот вызов, в котором используется конечная точка контейнера:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Распознаватель документов

Контейнер предоставляет REST API конечной точки, которые можно найти на странице [API Распознавателя документов](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неисправностей

Если контейнер запускается с выходным [подключением](form-recognizer-container-configuration.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

Контейнеры Распознавателя документов отправляют данные для выставления счетов в Azure с помощью ресурса _Распознавателя документов_ в учетной записи Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в разделе [Настройка контейнеров](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Резюме

В этой статье вы узнали основные понятия и рабочий процесс для скачивания, установки и выполнения контейнеров Распознавателя документов. В разделе "Сводка" сделайте следующее.

* Распознаватель документов предоставляет один контейнер Linux для Docker.
* Образы контейнеров скачиваются из частного реестра контейнеров в Azure.
* Образы контейнеров выполняются в Docker.
* Указав узел URI контейнера, вы можете вызвать операции в контейнерах Распознавателя документов с помощью пакета SDK REST или REST API.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
>  Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например анализируемые изображения или тексты).

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [конфигурации контейнеров](form-recognizer-container-configuration.md).
* Воспользуйтесь другими [контейнерами Cognitive Services](../cognitive-services-container-support.md).
