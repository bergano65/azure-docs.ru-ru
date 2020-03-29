---
title: Докер контейнеры - LUIS
titleSuffix: Azure Cognitive Services
description: Контейнер LUIS загружает обученное или опубликованное приложение в контейнер Docker и предоставляет доступ к прогнозам запросов из конечных точек API контейнера.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: dapine
ms.openlocfilehash: 308a474970db54022e5351fdf349d9572fbafb0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219932"
---
# <a name="install-and-run-luis-docker-containers"></a>Установка и запуск контейнеров Docker в LUIS
 
Контейнер Language Understanding (LUIS) загружает вашу обученную или опубликованную модель понимания языка. Как [приложение LUIS,](https://www.luis.ai)контейнер докера обеспечивает доступ к прогнозам запроса из конечных точек API контейнера. Вы можете собирать журналы запросов из контейнера и загружать их обратно в приложение Language Understanding, чтобы повысить точность прогнозирования приложения.

В следующем видео показаны способы использования этого контейнера.

[![Демонстрация контейнеров для когнитивных услуг](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для запуска контейнера LUIS обратите внимание на следующие предпосылки:

|Обязательно|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду с Docker для [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). См. [общие сведения о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс `Cognitive Services` Azure и [упакованный](luis-how-to-start-new-app.md) файл приложения LUIS |Для использования контейнера необходимо следующее:<br><br>Ресурс _Cognitive Services_ Azure и связанный с ним ключ выставления счетов в конечный пункт выставления. Оба значения доступны на страницах «Обзор» и «Ключи» для ресурса и необходимы для запуска контейнера. <br>* Обученное или опубликованное приложение, упакованное в виде подключенных входных данных к контейнеру со связанным идентификатором приложения. Пакет файла можно получить на портале LUIS или в авторских AIS. Если вы получаете пакетное приложение LUIS от [авторских AIS,](#authoring-apis-for-package-file)вам также понадобится ваш _ключ-автор._<br><br>Эти требования используются для передачи аргументов командной строки в следующие переменные:<br><br>**AUTHORING_KEY этот**ключ используется для получения упакованного приложения из службы LUIS в облаке и загрузки журналов запросов обратно в облако. Формат — `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**APP_ID этот**идентификатор используется для выбора приложения. Формат — `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**API_KEY этот**ключ используется для запуска контейнера. Ключ конечной точки можно найти в двух местах: Во-первых, это портал Azure в списке ключей ресурса _Cognitive Services._ а также на портале LUIS на странице параметров ключей и конечных точек. Не используйте ключ starter.<br><br>**(ENDPOINT_URI:** конечная точка, представленная на странице Обзор.<br><br>[Ключ разработки и ключ конечной точки](luis-boundaries.md#key-limits) служат для разных целей. Не используйте их как взаимозаменяемые. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Авторство AIS для файла пакетов

Авторизание AI для упакованных приложений:

* [API опубликованного пакета](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [API, не опубликованный, обученный только пакет](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

Этот контейнер поддерживает минимальные и рекомендуемые значения для параметров:

|Контейнер| Минимальные | Рекомендуемая | Tps<br>(Минимальный, максимальный)|
|-----------|---------|-------------|--|
|LUIS|1 ядро, 2-ГБ памяти|1 ядро, 4-ГБ памяти|20, 40|

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* TPS — транзакций в секунду.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Используйте [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) команду для загрузки `mcr.microsoft.com/azure-cognitive-services/luis` изображения контейнера из репозитория:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Полное описание доступных тегов, таких как `latest` из предыдущей команды, см. в разделе [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) на сайте Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

![Процесс использования контейнера Интеллектуальной службы распознавания речи (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Экспортируйте пакет](#export-packaged-app-from-luis) для контейнера с портала LUIS или из API LUIS.
1. Переместите файл пакета в необходимый каталог **input** на [главном компьютере](#the-host-computer). Не переименовывайте, не изменяйте, не переписывайте и не распаковывайте файл пакета LUIS.
1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками _input mount_ и выставления счетов. Доступны дополнительные [примеры](luis-container-configuration.md#example-docker-run-commands) команды `docker run`. 
1. Выполните [запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint). 
1. Завершив работу с контейнером, [импортируйте журналы конечной точки](#import-the-endpoint-logs-for-active-learning) из выходного подключения на портале LUIS и [остановите](#stop-the-container) контейнер.
1. Воспользуйтесь средствами [активного обучения](luis-how-to-review-endpoint-utterances.md) на портале LUIS или страницей **просмотра фраз конечной точки** для улучшения приложения.

Приложение, выполняющееся в контейнере, изменить нельзя. Чтобы изменить приложение в контейнере, необходимо изменить его в службе LUIS с помощью портала [LUIS](https://www.luis.ai) или [API разработки](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) LUIS. Затем выполните обучение и (или) публикацию, скачайте новый пакет и снова запустите контейнер.

Приложение LUIS, содержащееся в контейнере, нельзя экспортировать обратно в службу LUIS. Отправить можно только журналы запросов. 

## <a name="export-packaged-app-from-luis"></a>Экспорт упакованного приложения из LUIS

Контейнер LUIS ожидает от обученного или опубликованного приложения LUIS ответа на запросы прогноза пользовательских фраз. Чтобы получить приложение LUIS, используйте API обученного или опубликованного пакета. 

Расположение по умолчанию — вложенный каталог `input` по отношению к каталогу, для которого была выполнена команда `docker run`.  

Поместите файл пакета в каталог и укажите ссылку этот каталог как входное подключение при запуске контейнера Docker. 

### <a name="package-types"></a>Типы пакетов

Каталог входного монтажа может содержать **модели Production,** **Staging**и **Versioned** приложения одновременно. Все пакеты являются подключенными.

|Тип пакета|Запрос API конечной точки|Запрос доступности|Формат имени файла пакета|
|--|--|--|--|
|Версии|ПОЛУЧИТЬ, ПОЧТА|Только контейнер|`{APP_ID}_v{APP_VERSION}.gz`|
|Промежуточная|ПОЛУЧИТЬ, ПОЧТА|Azure и контейнер|`{APP_ID}_STAGING.gz`|
|Производство|ПОЛУЧИТЬ, ПОЧТА|Azure и контейнер|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Не переименовывайте, не изменяйте, не переписывайте и не распаковывайте файлы пакета LUIS.

### <a name="packaging-prerequisites"></a>Необходимые условия для упаковки

Перед упаковкой приложения LUIS потребуется следующее:

|Требования к упаковке|Сведения|
|--|--|
|Экземпляр ресурсов _когнитивных служб_ Azure|Поддерживаемые регионы:<br><br>Западная часть США (`westus`)<br>Западная Европа (`westeurope`)<br>Восточная Австралия (`australiaeast`)|
|Обученное или опубликованное приложение LUIS|Без [неподдерживаемых зависимостей][unsupported-dependencies]. |
|Доступ к файловой системе [главного компьютера](#the-host-computer) |Главный компьютер должен разрешать [входные подключения](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Экспорт пакета приложения с портала LUIS

На [портале](https://www.luis.ai) LUIS можно экспортировать пакет обученного или опубликованного приложения.

### <a name="export-published-apps-package-from-luis-portal"></a>Экспорт пакета опубликованного приложения с портала LUIS

Пакет опубликованного приложения доступен на странице списка **My Apps** (Мои приложения). 

1. Войдите на портал [LUIS](https://www.luis.ai).
1. Установите флажок слева от имени приложения в списке. 
1. Выберите элемент **Export** (Экспорт) на контекстной панели инструментов над списком.
1. Выберите **Export for container (GZIP)** (Экспорт для контейнера (GZIP)).
1. Выберите **Production slot** (Рабочий слот) или **Staging slot** (Промежуточный слот).
1. Пакет будет скачан из браузера.

![Экспорт опубликованного пакета для контейнера из меню экспорта на странице приложения](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Пакет приложения экспортной версии с портала LUIS

Пакет приложения с версиями доступен на странице списка **версий.**

1. Войдите на портал [LUIS](https://www.luis.ai).
1. Выберите приложение в списке. 
1. Выберите **Manage** (Управление) на панели навигации приложения.
1. Выберите **Versions** (Версии) на панели навигации слева.
1. Установите флажок слева от имени версии в списке.
1. Выберите элемент **Export** (Экспорт) на контекстной панели инструментов над списком.
1. Выберите **Export for container (GZIP)** (Экспорт для контейнера (GZIP)).
1. Пакет будет скачан из браузера.

![Экспорт обученного пакета для контейнера из меню экспорта на странице версий](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Экспорт пакета опубликованного приложения из API

Воспользуйтесь следующим методом REST API, чтобы упаковать приложение LUIS, которое уже [опубликовано](luis-how-to-publish-app.md). Замените заполнители в вызове API собственными соответствующими значениями, используя таблицу, приведенную под спецификацией HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Заполнитель | Значение |
|-------------|-------|
| **(APP_ID)** | Идентификатор опубликованного приложения LUIS. |
| **(SLOT_NAME)** | Среда опубликованного приложения LUIS. Используйте одно из следующих значений:<br/>`PRODUCTION`<br/>`STAGING` |
| **(AUTHORING_KEY)** | Ключ разработки учетной записи LUIS для опубликованного приложения LUIS.<br/>Ключ разработки можно получить на странице **User Settings** (Параметры пользователя) на портале LUIS. |
| **(AZURE_REGION)** | Соответствующий регион Azure:<br/><br/>`westus` — западная часть США<br/>`westeurope` — Западная Европа<br/>`australiaeast` — Восточная Австралия |

Чтобы загрузить опубликованный пакет, обратитесь к [документации API здесь][download-published-package]. При успешной загрузке ответ — это файл пакета LUIS. Сохраните файл в месте хранения, указанном для входного подключения контейнера. 

### <a name="export-versioned-apps-package-from-api"></a>Пакет приложения экспортной версии из API

Воспользуйтесь следующим методом REST API, чтобы упаковать приложение LUIS, которое уже [обучено](luis-how-to-train.md). Замените заполнители в вызове API собственными соответствующими значениями, используя таблицу, приведенную под спецификацией HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Заполнитель | Значение |
|-------------|-------|
| **(APP_ID)** | Идентификатор приложения обученного приложения LUIS. |
| **(APP_VERSION)** | Версия приложения обученного приложения LUIS. |
| **(AUTHORING_KEY)** | Ключ разработки учетной записи LUIS для опубликованного приложения LUIS.<br/>Ключ разработки можно получить на странице **User Settings** (Параметры пользователя) на портале LUIS. |
| **(AZURE_REGION)** | Соответствующий регион Azure:<br/><br/>`westus` — западная часть США<br/>`westeurope` — Западная Европа<br/>`australiaeast` — Восточная Австралия |

Чтобы загрузить пакет с versioned, обратитесь к [документации API здесь][download-versioned-package]. При успешной загрузке ответ — это файл пакета LUIS. Сохраните файл в месте хранения, указанном для входного подключения контейнера. 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Обратитесь к [сбору необходимых параметров](#gathering-required-parameters) `{ENDPOINT_URI}` для `{API_KEY}` получения подробной информации о том, как получить и значения.

[Приведены примеры](luis-container-configuration.md#example-docker-run-commands) `docker run` команды.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* Этот пример использует каталог `C:` с диска, чтобы избежать конфликтов разрешений в Windows. Если вам нужен конкретный каталог для входных данных, может потребоваться предоставить соответствующие разрешения службе Docker. 
* Не изменяйте порядок аргументов, если вы не знакомы с контейнерами докера.
* Если вы используете другую операционную систему, используйте правильную консоль/терминал, синтаксис папки для креплений и символ продолжения строки для вашей системы. Эти примеры предполагают консоль Windows `^`с символом продолжения строки. Поскольку контейнер представляет собой операционную систему Linux, целевое крепление использует синтаксис в стиле папки ВОльс.

Эта команда:

* запускает контейнер из образа контейнера LUIS;
* Загружает приложение LUIS от вхотливого крепления на *C: 'вход*, расположенный на контейнерном хосте
* выделяет 2 ядра ЦП и 4 ГБ памяти;
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* Сохраняет контейнер и журналы LUIS для вывода на *выходе на C: выход*, расположенный на контейнере хоста
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

Доступны дополнительные [примеры](luis-container-configuration.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).
> Значение ApiKey — это **ключ** со страницы **ресурсов Azure** на портале `Cognitive Services` LUIS, а также доступно на странице ключей ресурсов Azure.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>AIS конечных точек, поддерживаемые контейнером

Оба V2 и [V3](luis-migration-api-v3.md) версии API доступны с контейнером. 

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. Конечные точки для опубликованных (постановка или производство) приложений имеют _другой_ маршрут, чем конечные точки для версий приложений.

Используйте узел `http://localhost:5000` для API контейнера.

# <a name="v3-prediction-endpoint"></a>[Конечная точка прогнозирования V3](#tab/v3)

|Тип пакета|HTTP-команда|Маршрут|Параметры запроса|
|--|--|--|--|
|Опубликован|ПОЛУЧИТЬ, ПОЧТА|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Версии|ПОЛУЧИТЬ, ПОЧТА|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Параметры запроса определяют содержимое ответа на запрос и способ его получения:

|Параметр запроса|Тип|Назначение|
|--|--|--|
|`query`|строка|Фраза пользователя.|
|`verbose`|Логическое|Значение boolean, указывающее на возвращение всех метаданных для прогнозируемых моделей. Значение по умолчанию — false.|
|`log`|Логическое|Записывает запросы в журнал, который затем можно использовать для [активного обучения](luis-how-to-review-endpoint-utterances.md). Значение по умолчанию — false.|
|`show-all-intents`|Логическое|Boolean значение с указанием того, чтобы вернуть все намерения или верхней забил намерения только. Значение по умолчанию — false.|

# <a name="v2-prediction-endpoint"></a>[Конечная точка прогнозирования V2](#tab/v2)

|Тип пакета|HTTP-команда|Маршрут|Параметры запроса|
|--|--|--|--|
|Опубликован|[ПОЛУЧИТЬ](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [ПОЧТА](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Версии|ПОЛУЧИТЬ, ПОЧТА|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Параметры запроса определяют содержимое ответа на запрос и способ его получения:

|Параметр запроса|Тип|Назначение|
|--|--|--|
|`q`|строка|Фраза пользователя.|
|`timezoneOffset`|number|Параметр timezoneOffset позволяет [изменить часовой пояс](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity), используемый предварительно созданной сущностью datetimeV2.|
|`verbose`|Логическое|Возвращает все намерения и их оценки, если задано значение true. По умолчанию задано значение false, при котором возвращается только верхнее намерение.|
|`staging`|Логическое|Возвращает запрос из результатов промежуточной среды, если задано значение true. |
|`log`|Логическое|Записывает запросы в журнал, который затем можно использовать для [активного обучения](luis-how-to-review-endpoint-utterances.md). Значение по умолчанию — true.|

***

### <a name="query-the-luis-app"></a>Запрос приложения LUIS

Ниже приведен пример команды CURL для выполнения запросов к контейнеру для опубликованного приложения:

# <a name="v3-prediction-endpoint"></a>[Конечная точка прогнозирования V3](#tab/v3)

Чтобы задать запрос модели в слоте, используйте следующий API:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Чтобы сделать запросы в `production` среде **staging,** замените в `staging`маршруте:

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Для запроса модели версии используйте следующий API:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[Конечная точка прогнозирования V2](#tab/v2)

Чтобы задать запрос модели в слоте, используйте следующий API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Для выполнения запросов к **промежуточной** среде измените значение параметра **staging** строки запроса на true: 

`staging=true`

Для запроса модели версии используйте следующий API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Имя версии имеет максимальную длину 10 символов и содержит только символы, разрешенные в URL-адресе.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Импорт журналов конечной точки для активного обучения

Если выходное крепление указано для контейнера LUIS, файлы журнала `{INSTANCE_ID}` запросов приложения сохраняются в каталоге вывода, где находится идентификатор контейнера. Журнал запросов приложения содержит запрос, ответ и метки времени для каждого прогнозирующего запроса, отправленного в контейнер LUIS. 

В следующем расположении показана вложенная структура каталогов для файлов журнала контейнера.
```
/output/luis/{INSTANCE_ID}/
```
 
На портале LUIS выберите приложение, затем выберите **Import endpoint logs** (Импорт журналов конечной точки) для отправки этих журналов. 

![Импорт файлов журнала контейнера для активного обучения](./media/luis-container-how-to/upload-endpoint-log-files.png)

После отправки журнала [просмотрите фразы конечной точки](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) на портале LUIS.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

Чтобы завершить работу контейнера, в среде командной строки, где выполняется контейнер, нажмите клавиши **CTRL+C**.

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](luis-container-configuration.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

Контейнер LUIS отправляет платежную информацию в Azure с помощью ресурса _Cognitive Services_ в вашей учетной записи Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](luis-container-configuration.md).

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье описаны основные понятия и рабочий процесс для скачивания, установки и выполнения контейнеров Интеллектуальной службы распознавания речи (LUIS). В разделе "Сводка" сделайте следующее.

* Интеллектуальная служба распознавания речи (LUIS) предоставляет один контейнер Linux для Docker, обеспечивая прогнозы фраз по запросам конечных точек.
* Образы контейнеров скачиваются из Реестра контейнеров Майкрософт (MCR).
* Образы контейнеров выполняются в Docker.
* С помощью REST API можно выполнять запросы к конечным точкам контейнера, указывая URI узла контейнера.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например анализируемые изображения или тексты).

## <a name="next-steps"></a>Дальнейшие действия

* Просмотрите [настройку контейнеров](luis-container-configuration.md) для настроек конфигурации.
* См, как [ограничения контейнеров LUIS](luis-container-limitations.md) для известных ограничений на возможности.
* Ознакомьтесь со статьей об [устранение неполадок](troubleshooting.md), чтобы устранить проблемы, связанные с функциональностью LUIS.
* Используйте больше [контейнеров когнитивных услуг](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container
