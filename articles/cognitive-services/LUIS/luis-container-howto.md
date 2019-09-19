---
title: Контейнеры DOCKER — LUIS
titleSuffix: Azure Cognitive Services
description: Контейнер LUIS загружает обученное или опубликованное приложение в контейнер Docker и предоставляет доступ к прогнозам запросов из конечных точек API контейнера.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 9d1a6ab698ceb6ac1c0a4fc635b5a8fe1e68b0c6
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102043"
---
# <a name="install-and-run-luis-docker-containers"></a>Установка и запуск контейнеров Docker в LUIS
 
Контейнер Интеллектуальной службы распознавания речи (LUIS) загружает обученную или опубликованную модель Интеллектуальной службы распознавания речи ([приложение LUIS](https://www.luis.ai)) в контейнер Docker и предоставляет доступ к прогнозам запросов из конечных точек API контейнера. Вы можете получить журналы запросов из контейнера и отправить их обратно в приложение Language Understanding, чтобы повысить точность прогноза приложения.

В следующем видео показаны способы использования этого контейнера.

[![Демонстрация контейнера для Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для запуска контейнера LUIS требуется следующее: 

|Обязательное значение|Цель|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс `Cognitive Services` Azure и файл [упакованного приложения](luis-how-to-start-new-app.md#export-app-for-containers) Luis |Для использования контейнера необходимо следующее:<br><br>* Ресурс Azure _Cognitive Services_ и соответствующий ключ выставления счетов URI конечной точки выставления счетов. Оба значения доступны на страницах обзора и ключей для ресурса и необходимы для запуска контейнера. Необходимо добавить `luis/v2.0` маршрутизацию к универсальному коду ресурса (URI) конечной точки, как показано в следующем примере BILLING_ENDPOINT_URI. <br>* Обученное или опубликованное приложение, упакованное в виде подключенных входных данных к контейнеру со связанным идентификатором приложения. Пакетный файл можно получить на портале LUIS или в API-интерфейсах разработки. Если вы получаете упакованное приложение LUIS из API- [интерфейсов разработки](#authoring-apis-for-package-file), вам также потребуется ваш _ключ разработки_.<br><br>Эти требования используются для передачи аргументов командной строки в следующие переменные:<br><br>**{AUTHORING_KEY}** . Этот ключ используется для получения упакованного приложения из службы LUIS в облаке и отправки журналов запросов обратно в облако. Формат — `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** . Этот идентификатор используется для выбора приложения. Формат — `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}** : Этот ключ используется для запуска контейнера. Ключ конечной точки можно найти в двух местах: Первый — это портал Azure в списке ключей ресурса _Cognitive Services_ . а также на портале LUIS на странице параметров ключей и конечных точек. Не используйте ключ starter.<br><br>**{ENDPOINT_URI}** : Конечная точка, указанная на странице обзора.<br><br>[Ключ разработки и ключ конечной точки](luis-boundaries.md#key-limits) служат для разных целей. Не используйте их как взаимозаменяемые. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Создание API-интерфейсов для файла пакета

Создание API для упакованных приложений:

* [API опубликованного пакета](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Неопубликованный, только обученный пакет API пакета](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

Этот контейнер поддерживает минимальные и рекомендуемые значения для параметров:

|Контейнер| Минимум | Рекомендуется | ПЛАТЫ<br>(Минимум, максимум)|
|-----------|---------|-------------|--|
|LUIS|1 ядро, 2 ГБ памяти|1 ядро, 4 ГБ памяти|20, 40|

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* TPS — транзакций в секунду.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Воспользуйтесь командой [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера из репозитория `mcr.microsoft.com/azure-cognitive-services/luis`.

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Воспользуйтесь командой [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера.

Полное описание доступных тегов, таких как `latest` из предыдущей команды, см. в разделе [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) на сайте Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

![Процесс использования контейнера Интеллектуальной службы распознавания речи (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Экспортируйте пакет](#export-packaged-app-from-luis) для контейнера с портала LUIS или из API LUIS.
1. Переместите файл пакета в необходимый каталог **input** на [главном компьютере](#the-host-computer). Не переименовывайте, не изменяйте, перезапишите или распакуйте файл пакета LUIS.
1. [Запустите контейнер](##run-the-container-with-docker-run) с необходимыми настройками _input mount_ и выставления счетов. Доступны дополнительные [примеры](luis-container-configuration.md#example-docker-run-commands) команды `docker run`. 
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

Каталог входного подключения может одновременно содержать **рабочую**, **промежуточную** и **обученную** версии приложения. Все пакеты являются подключенными. 

|Тип пакета|Запрос API конечной точки|Запрос доступности|Формат имени файла пакета|
|--|--|--|--|
|Обучение пройдено|Get, Post|Только контейнер|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Промежуточное хранение|Get, Post|Azure и контейнер|`{APPLICATION_ID}_STAGING.gz`|
|Рабочая среда|Get, Post|Azure и контейнер|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Не переименовывайте, не изменяйте, перезаписывают или распаковывает файлы пакетов LUIS.

### <a name="packaging-prerequisites"></a>Необходимые условия для упаковки

Перед упаковкой приложения LUIS потребуется следующее:

|Требования к упаковке|Подробнее|
|--|--|
|Экземпляр ресурса _Cognitive Services_ Azure|Поддерживаемые регионы:<br><br>Западная часть США (`westus`)<br>Западная Европа (`westeurope`)<br>Восточная Австралия (`australiaeast`)|
|Обученное или опубликованное приложение LUIS|Без [неподдерживаемых зависимостей](#unsupported-dependencies). |
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

### <a name="export-trained-apps-package-from-luis-portal"></a>Экспорт пакета обученного приложения с портала LUIS

Пакет обученного приложения доступен на странице списка **Versions** (Версии). 

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
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Местозаполнитель | Значение |
|-------------|-------|
|{APPLICATION_ID} | Идентификатор опубликованного приложения LUIS. |
|{APPLICATION_ENVIRONMENT} | Среда опубликованного приложения LUIS. Используйте одно из следующих значений:<br/>`PRODUCTION`<br/>`STAGING` |
|{AUTHORING_KEY} | Ключ разработки учетной записи LUIS для опубликованного приложения LUIS.<br/>Ключ разработки можно получить на странице **User Settings** (Параметры пользователя) на портале LUIS. |
|{AZURE_REGION} | Соответствующий регион Azure:<br/><br/>`westus` — западная часть США<br/>`westeurope` — Западная Европа<br/>`australiaeast` — Восточная Австралия |

Чтобы скачать опубликованный пакет, обратитесь к [документации по API здесь][download-published-package]. Если загрузка успешно завершена, ответ представляет собой файл пакета LUIS. Сохраните файл в месте хранения, указанном для входного подключения контейнера. 

### <a name="export-trained-apps-package-from-api"></a>Экспорт пакета обученного приложения из API

Воспользуйтесь следующим методом REST API, чтобы упаковать приложение LUIS, которое уже [обучено](luis-how-to-train.md). Замените заполнители в вызове API собственными соответствующими значениями, используя таблицу, приведенную под спецификацией HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Местозаполнитель | Значение |
|-------------|-------|
|{APPLICATION_ID} | Идентификатор обученного приложения LUIS. |
|{APPLICATION_VERSION} | Версия обученного приложения LUIS. |
|{AUTHORING_KEY} | Ключ разработки учетной записи LUIS для опубликованного приложения LUIS.<br/>Ключ разработки можно получить на странице **User Settings** (Параметры пользователя) на портале LUIS.  |
|{AZURE_REGION} | Соответствующий регион Azure:<br/><br/>`westus` — западная часть США<br/>`westeurope` — Западная Европа<br/>`australiaeast` — Восточная Австралия |

Чтобы скачать обученный пакет, обратитесь к [документации по API здесь][download-trained-package]. Если загрузка успешно завершена, ответ представляет собой файл пакета LUIS. Сохраните файл в месте хранения, указанном для входного подключения контейнера. 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. Дополнительные сведения о том, как получить значения и `{API_KEY}` , `{ENDPOINT_URI}` см. в разделе [сбор обязательных параметров](#gathering-required-parameters) .

[Доступны примеры](luis-container-configuration.md#example-docker-run-commands) `docker run` команд.

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

* В этом примере каталог находится `C:` за пределами диска во избежание конфликтов разрешений в Windows. Если вам нужен конкретный каталог для входных данных, может потребоваться предоставить соответствующие разрешения службе Docker. 
* Не изменяйте порядок аргументов, если вы не знакомы с контейнерами DOCKER.
* Если используется другая операционная система, используйте правильную консоль или терминал, синтаксис папок для подключений и символ продолжения строки для вашей системы. В этих примерах предполагается наличие в консоли Windows символа `^`продолжения строки. Так как контейнер является операционной системой Linux, целевое подключение использует синтаксис папки в формате Linux.

Эта команда:

* запускает контейнер из образа контейнера LUIS;
* Загружает приложение LUIS из входных данных Mount в *к:\инпут*, расположенном на узле контейнера.
* выделяет 2 ядра ЦП и 4 ГБ памяти;
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* Сохраняет журналы контейнеров и LUIS для подключения к выходу в *C:\output*, расположенном на узле контейнера.
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

Доступны дополнительные [примеры](luis-container-configuration.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> Для выполнения контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).
> Значение apiKey — это **ключ** на странице **ресурсов Azure** на портале Luis. Он также доступен на странице ключей ресурсов Azure `Cognitive Services` .  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Интерфейсы API конечной точки, поддерживаемые контейнером

В контейнере доступны версии v2 и [v3 (Предварительная версия)](luis-migration-api-v3.md) API. 

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. Конечные точки для опубликованных (промежуточных или рабочих) приложений имеют _другой_ маршрут, чем конечные точки для обученных приложений. 

Используйте узел `http://localhost:5000` для API контейнера. 

|Тип пакета|Метод|Маршрут|Параметры запроса|
|--|--|--|--|
|Опубликовано|[Get](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>&staging<br>[&timezoneOffset]<br>[&verbose]<br>[&log]<br>|
|Обучение пройдено|Get, Post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[&timezoneOffset]<br>[&verbose]<br>[&log]|

Параметры запроса определяют содержимое ответа на запрос и способ его получения:

|Параметр запроса|Тип|Цель|
|--|--|--|
|`q`|строка|Фраза пользователя.|
|`timezoneOffset`|number|Параметр timezoneOffset позволяет [изменить часовой пояс](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity), используемый предварительно созданной сущностью datetimeV2.|
|`verbose`|boolean|Возвращает все намерения и их оценки, если задано значение true. По умолчанию задано значение false, при котором возвращается только верхнее намерение.|
|`staging`|boolean|Возвращает запрос из результатов промежуточной среды, если задано значение true. |
|`log`|boolean|Записывает запросы в журнал, который затем можно использовать для [активного обучения](luis-how-to-review-endpoint-utterances.md). Значение по умолчанию — true.|

### <a name="query-published-app"></a>Запрос опубликованного приложения

Ниже приведен пример команды CURL для выполнения запросов к контейнеру для опубликованного приложения:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Для выполнения запросов к **промежуточной** среде измените значение параметра **staging** строки запроса на true: 

`staging=true`

### <a name="query-trained-app"></a>Запрос обученного приложения

Ниже приведен пример команды CURL для выполнения запросов к контейнеру для обученного приложения: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Имя версии имеет максимальную длину 10 символов и содержит только символы, разрешенные в URL-адресе. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Импорт журналов конечной точки для активного обучения

Если для контейнера LUIS указано выходное подключение, файлы журнала запросов приложения сохраняются в выходном каталоге, где {INSTANCE_ID} — идентификатор контейнера. Журнал запросов приложения содержит запрос, ответ и метки времени для каждого прогнозирующего запроса, отправленного в контейнер LUIS. 

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

## <a name="billing"></a>Выставление счетов

Контейнер LUIS отправляет сведения о выставлении счетов в Azure с помощью _Cognitive Services_ ресурса в учетной записи Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](luis-container-configuration.md).

## <a name="supported-dependencies-for-latest-container"></a>Поддерживаемые зависимости для `latest` контейнера

Последний контейнер, выпущенный в 2019//Build, будет поддерживать следующее:

* Проверка орфографии Bing: запросы к конечной точке прогнозирования запросов `&spellCheck=true&bing-spell-check-subscription-key={bingKey}` с параметрами строки запроса. Дополнительные сведения см. в [руководстве по проверка орфографии Bing версии 7](luis-tutorial-bing-spellcheck.md) . Если эта функция используется, контейнер отправляет utterance в ресурс Проверка орфографии Bing версии 7.
* [Новые](luis-reference-prebuilt-domains.md)предварительно созданные домены. Эти корпоративные домены включают сущности, примеры фразы продолжительностью и закономерности. Расширьте эти домены для собственного использования. 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>Неподдерживаемые зависимости для `latest` контейнера

Если приложение LUIS имеет неподдерживаемые зависимости, вы не сможете [экспортировать для контейнера](#export-packaged-app-from-luis) , пока не будут удалены неподдерживаемые компоненты. При попытке экспорта для контейнера портал LUIS сообщает о неподдерживаемых функциях, которые необходимо удалить.

Приложение LUIS можно использовать, если оно **не содержит** следующие зависимости:

Неподдерживаемые конфигурации приложения|Подробнее|
|--|--|
|Неподдерживаемые языки и региональные параметры контейнеров| Нидерландский (nl-NL)<br>Японский (ja-JP)<br>Немецкий поддерживается только с помощью подсчета [1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Неподдерживаемые сущности для всех языков и региональных параметров|Предварительно созданная сущность [KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) для всех языков и региональных параметров|
|Неподдерживаемые сущности для языка и региональных параметров "Английский (en-US)"|Предварительно созданные сущности [GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2)|
|Подготовка речи|Внешние зависимости не поддерживаются в контейнере.|
|Анализ тональности|Внешние зависимости не поддерживаются в контейнере.|

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

## <a name="next-steps"></a>Следующие шаги

* Ознакомьтесь со статьей о [конфигурации контейнеров](luis-container-configuration.md).
* Ознакомьтесь со статьей об [устранение неполадок](troubleshooting.md), чтобы устранить проблемы, связанные с функциональностью LUIS.
* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-trained-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip