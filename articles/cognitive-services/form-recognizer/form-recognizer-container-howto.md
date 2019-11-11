---
title: Установка и запуск контейнера для распознавателя форм
titleSuffix: Azure Cognitive Services
description: В этой статье объясняется, как использовать контейнер распознавателя форм Azure Cognitive Services для анализа данных формы и таблицы.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: dapine
ms.openlocfilehash: f26fe9768930c9d8b99a06e3ea8b51ed1657bcb2
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904509"
---
# <a name="install-and-run-form-recognizer-containers"></a>Установка и запуск контейнеров Распознавателя документов

Распознаватель документов Azure применяет технологии машинного обучения, чтобы идентифицировать и извлечь пары "ключ — значение" и таблицы из форм. Он связывает значения и записи в таблицах с парами "ключ — значение", а затем выводит структурированные данные, которые включают в себя связи в исходном файле. 

Вы можете вызывать пользовательскую модель Распознавателя документов с помощью простого REST API, чтобы упростить и без труда интегрировать ее в свой рабочий процесс автоматизации или другое приложение. Требуется только пять документов форм (или одна пустая форма и две заполненные формы), что позволяет быстро и точно получать результаты и адаптироваться к конкретному содержимому. При этом не требуется выполнять трудоемкие операции вручную или хорошо разбираться в обработке и анализе данных. В процессе также не предусмотрено добавление меток данных или заметок к данным.

|Функция|Функции|
|-|-|
|Распознаватель документов| <li>Обрабатывает файлы PDF, PNG и JPG<li>Обучение пользовательских моделей как минимум из пяти форм одинакового макета <li>Извлекает пары "ключ — значение" и сведения о таблице <li>Использует функцию "Распознавания текста" API Компьютерного зрения Cognitive Service для обнаружения и извлечения печатного текста из изображений в формах<li>Не требует добавления заметок или меток|

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем использовать контейнеры Распознавателя документов, необходимо выполнить следующие условия:

|обязательные|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). См. [общие сведения о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> В ОС Windows для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, таких как реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.|
|Интерфейс командной строки Azure| Установите в узле [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).|
|Ресурс API Компьютерного зрения| Для обработки отсканированных документов и изображений требуется API Компьютерного зрения. Функция "Распознавание текста" доступна либо в виде ресурса Azure (REST API или пакет SDK), либо в виде *контейнера* [cognitive-services-recognize-text](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull). Взымается обычная плата. <br><br>Передайте ключ API и конечные точки для ресурса Компьютерное зрение (облако Azure или контейнер Cognitive Services). Используйте этот ключ API и конечную точку как **{COMPUTER_VISION_API_KEY}** и **{COMPUTER_VISION_ENDPOINT_URI}** .<br><br> Если вы используете контейнер *cognitive-services-recognize-text*, проверьте следующее:<br><br>Ваш ключ Компьютерного зрения для контейнера Распознавателя документов — это ключ, указанный в команде `docker run` Компьютерного зрения для контейнера *cognitive-services-recognize-text*.<br>Ваша конечная точка выставления счетов — это конечная точка контейнера, например `http://localhost:5000`. При совместном использовании контейнеров Компьютерного зрения и Распознавателя документов в одному узле их нельзя одновременно запустить с помощью порта *5000*, заданного по умолчанию. |
|Ресурс Распознавателя документов |Для использования контейнеров необходимо следующее:<br><br>Ресурс **распознавателя форм** Azure для получения соответствующего ключа API и URI конечной точки. Оба значения доступны на странице Обзор **распознавателя форм** портал Azure и на страницах ключей, и оба значения необходимы для запуска контейнера.<br><br>**{FORM_RECOGNIZER_API_KEY}** : один из двух доступных ключей ресурсов на странице "ключи"<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}** : конечная точка, указанная на странице обзора|

## <a name="gathering-required-parameters"></a>Идет сбор обязательных параметров

Есть три основных параметра для всех обязательных контейнеров Cognitive Services ". Лицензионное соглашение (EULA) должно присутствовать в значении `accept`. Кроме того, требуются URL-адрес конечной точки и ключ API.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>URI конечной точки `{COMPUTER_VISION_ENDPOINT_URI}` и `{FORM_RECOGNIZER_ENDPOINT_URI}`

Значение URI **конечной точки** доступно на странице *обзора* портал Azure соответствующего ресурса службы. Перейдите на страницу *обзора* , наведите указатель мыши на конечную точку, <span class="docon docon-edit-copy x-hidden-focus"></span> и появится значок `Copy to clipboard`. Копирование и использование при необходимости.

![Сбор URI конечной точки для последующего использования](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Ключи `{COMPUTER_VISION_API_KEY}` и `{FORM_RECOGNIZER_API_KEY}`

Этот ключ используется для запуска контейнера и доступен на странице ключей портал Azure соответствующего ресурса службы. Перейдите на страницу *ключи* и щелкните значок `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> .

![Получите один из двух ключей для последующего использования](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Эти ключи подписки используются для доступа к API-интерфейсу для работы со службой. Не предоставляйте общий доступ к ключам. Храните их безопасно, например, с помощью Azure Key Vault. Мы также советуем регулярно создавать эти ключи. Для вызова API требуется только один ключ. При повторном создании первого ключа можно использовать второй ключ для постоянного доступа к службе.

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
| Распознаватель документов | 2 ядра, 4 ГБ памяти | 4 ядра, 8 ГБ памяти |
| Распознавание текста | 1 ядро, 8 ГБ памяти | 2 ядра, 8 ГБ памяти |

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

> [!Note]
> Минимальные и рекомендуемые значения основаны на ограничениях Docker, *а не на* ресурсах узла.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Получение образов контейнеров с помощью команды docker pull

Образы контейнеров как для **распознавателя форм** , так и для предложений **Распознавание текста** доступны в следующем реестре контейнеров:

| Контейнер | Полное имя образа |
|-----------|------------|
| Распознаватель документов | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Распознавание текста | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Вам понадобятся оба контейнера. Обратите внимание, что контейнер **текста распознавателя** [подробно указан за пределами этой статьи.](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Выполнение docker pull для контейнера Распознавателя документов

#### <a name="form-recognizer"></a>Распознаватель документов

Получить контейнер Распознавателя документов можно с помощью следующей команды:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>docker pull для контейнера Распознавания текста

#### <a name="recognize-text"></a>Распознавание текста

Чтобы получить контейнер Распознавание текста, используйте следующую команду:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) выполните приведенные ниже инструкции для работы с ним.

1. [Запустите контейнер](#run-the-container-by-using-the-docker-run-command) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](form-recognizer-container-configuration.md#example-docker-run-commands) команды `docker run`.
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Команда docker run, выполняющая запуск контейнера

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Дополнительные сведения о том, как получить значения `{COMPUTER_VISION_ENDPOINT_URI}`, `{COMPUTER_VISION_API_KEY}`, `{FORM_RECOGNIZER_ENDPOINT_URI}` и `{FORM_RECOGNIZER_API_KEY}`, см. в разделе [сбор обязательных параметров](#gathering-required-parameters) .

Доступны [примеры](form-recognizer-container-configuration.md#example-docker-run-commands) команды `docker run`.

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

Выполните первый контейнер на порте 5000. 

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

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](form-recognizer-container-configuration.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

Контейнеры Распознавателя документов отправляют данные для выставления счетов в Azure с помощью ресурса _Распознавателя документов_ в учетной записи Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>summary

В этой статье вы узнали основные понятия и рабочий процесс для скачивания, установки и выполнения контейнеров Распознавателя документов. Краткая сводка.

* Распознаватель документов предоставляет один контейнер Linux для Docker.
* Образы контейнеров скачиваются из частного реестра контейнеров в Azure.
* Образы контейнеров, которые выполняются в Docker.
* Указав узел URI контейнера, вы можете вызвать операции в контейнерах Распознавателя документов с помощью пакета SDK REST или REST API.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
>  Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например анализируемые изображения или тексты).

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со статьей о [конфигурации контейнеров](form-recognizer-container-configuration.md).
* Воспользуйтесь другими [контейнерами Cognitive Services](../cognitive-services-container-support.md).
