---
title: контейнеры Docker;
titleSuffix: Language Understanding - Azure Cognitive Services
description: Контейнер LUIS загружает обученное или опубликованное приложение в контейнер Docker и предоставляет доступ к прогнозам запросов из конечных точек API контейнера.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/22/2019
ms.author: diberry
ms.openlocfilehash: ca9b08cdccd43a093ca8b5001d3e30be0e5258b5
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894684"
---
# <a name="install-and-run-luis-docker-containers"></a>Установка и запуск контейнеров Docker в LUIS
 
Контейнер Интеллектуальной службы распознавания речи (LUIS) загружает обученную или опубликованную модель Интеллектуальной службы распознавания речи ([приложение LUIS](https://www.luis.ai)) в контейнер Docker и предоставляет доступ к прогнозам запросов из конечных точек API контейнера. Можно собирать журналы запросов из контейнера и отправлять их обратно в модель Интеллектуальной службы распознавания речи Azure для повышения точности прогнозов приложения.

В следующем видео показаны способы использования этого контейнера.

[![CДемонстрация стили для Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

Для запуска контейнера LUIS требуется следующее: 

|Обязательно для заполнения|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс Интеллектуальной службы распознавания речи (LUIS) и связанное приложение |Для использования контейнера необходимо следующее:<br><br>* [Ресурс _Интеллектуальной службы распознавания речи_ Azure](luis-how-to-azure-subscription.md) и связанный ключ конечной точки и URI конечной точки (используется в качестве конечной точки выставления счетов).<br>* Обученное или опубликованное приложение, упакованное в виде подключенных входных данных к контейнеру со связанным идентификатором приложения.<br>* Ключ разработки для скачивания пакета установки, если эти действия выполняются из API.<br><br>Эти требования используются для передачи аргументов командной строки в следующие переменные:<br><br>**{AUTHORING_KEY}**. Этот ключ используется для получения упакованного приложения из службы LUIS в облаке и отправки журналов запросов обратно в облако. Формат — `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}**. Этот идентификатор используется для выбора приложения. Формат — `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{ENDPOINT_KEY}**. Этот ключ используется для запуска контейнера. Ключ конечной точки можно найти в двух местах: на портале Azure в списке ключей ресурса _Language Understanding_, а также на портале LUIS на странице параметров ключей и конечных точек. Не используйте ключ starter.<br><br>**{BILLING_ENDPOINT}**. Значение конечной точки выставления счетов доступно на странице обзора Интеллектуальной службы распознавания речи на портале Azure. Пример: `https://westus.api.cognitive.microsoft.com/luis/v2.0`.<br><br>[Ключ разработки и ключ конечной точки](luis-boundaries.md#key-limits) служат для разных целей. Не используйте их как взаимозаменяемые. |

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

Этот контейнер поддерживает минимальные и рекомендуемые значения для параметров:

|Контейнер| Минимальная | Рекомендуется | ТРАНЗАКЦИЙ В СЕКУНДУ<br>(Минимум, максимум)|
|-----------|---------|-------------|--|
|LUIS|1 ядро, 2 ГБ памяти|1 ядро, 4 ГБ памяти|20,40|

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* Транзакций в Секунду - транзакций в секунду

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
1. Переместите файл пакета в необходимый каталог **input** на [главном компьютере](#the-host-computer). Не переименовывайте, не изменяйте и не распаковывайте файл пакета LUIS.
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
|Промежуточная|Get, Post|Azure и контейнер|`{APPLICATION_ID}_STAGING.gz`|
|Производство|Get, Post|Azure и контейнер|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Не переименовывайте, не изменяйте и не распаковывайте файлы пакетов LUIS.

### <a name="packaging-prerequisites"></a>Необходимые условия для упаковки

Перед упаковкой приложения LUIS потребуется следующее:

|Требования к упаковке|Сведения|
|--|--|
|Экземпляр ресурса _Интеллектуальной службы распознавания речи_ Azure|Поддерживаемые регионы:<br><br>Западная часть США (```westus```)<br>Западная Европа (```westeurope```)<br>Восточная Австралия (```australiaeast```)|
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

| Placeholder | Значение |
|-------------|-------|
|{APPLICATION_ID} | Идентификатор опубликованного приложения LUIS. |
|{APPLICATION_ENVIRONMENT} | Среда опубликованного приложения LUIS. Используйте одно из следующих значений:<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | Ключ разработки учетной записи LUIS для опубликованного приложения LUIS.<br/>Ключ разработки можно получить на странице **User Settings** (Параметры пользователя) на портале LUIS. |
|{AZURE_REGION} | Соответствующий регион Azure:<br/><br/>```westus``` -Западная часть США<br/>```westeurope``` — Западная Европа<br/>```australiaeast``` -Юго Восток |

С помощью следующей команды CURL скачайте опубликованный пакет, подставив собственные значения:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_{APPLICATION_ENVIRONMENT}.gz
```

В случае успешного выполнения ответом будет файл пакета LUIS. Сохраните файл в месте хранения, указанном для входного подключения контейнера. 

### <a name="export-trained-apps-package-from-api"></a>Экспорт пакета обученного приложения из API

Воспользуйтесь следующим методом REST API, чтобы упаковать приложение LUIS, которое уже [обучено](luis-how-to-train.md). Замените заполнители в вызове API собственными соответствующими значениями, используя таблицу, приведенную под спецификацией HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Placeholder | Значение |
|-------------|-------|
|{APPLICATION_ID} | Идентификатор обученного приложения LUIS. |
|{APPLICATION_VERSION} | Версия обученного приложения LUIS. |
|{AUTHORING_KEY} | Ключ разработки учетной записи LUIS для опубликованного приложения LUIS.<br/>Ключ разработки можно получить на странице **User Settings** (Параметры пользователя) на портале LUIS.  |
|{AZURE_REGION} | Соответствующий регион Azure:<br/><br/>```westus``` -Западная часть США<br/>```westeurope``` — Западная Европа<br/>```australiaeast``` -Юго Восток |

С помощью следующей команды CURL скачайте обученный пакет:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_v{APPLICATION_VERSION}.gz
```

В случае успешного выполнения ответом будет файл пакета LUIS. Сохраните файл в месте хранения, указанном для входного подключения контейнера. 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. В команде используются следующие параметры:

| Placeholder | Значение |
|-------------|-------|
|{ENDPOINT_KEY} | Этот ключ используется для запуска контейнера. Не используйте ключ starter. |
|{BILLING_ENDPOINT} | Значение конечной точки выставления счетов доступно на странице обзора Интеллектуальной службы распознавания речи на портале Azure.|

В следующем примере команды `docker run` замените имена параметров собственными значениями.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> Предыдущая команда использует каталог с диска `c:`, чтобы избежать конфликтов разрешений в Windows. Если вам нужен конкретный каталог для входных данных, может потребоваться предоставить соответствующие разрешения службе Docker. В предыдущей команде Docker используется обратная косая черта (`\`) как символ продолжения строки. Замените или удалите ее в соответствии с требованиями операционной системы [главного компьютера](#the-host-computer). Не изменяйте порядок аргументов, если вы не являетесь уверенным пользователем контейнеров Docker.


Эта команда:

* запускает контейнер из образа контейнера LUIS;
* загружает приложение LUIS из входного подключения в папке c:\input, расположенной на узле контейнера;
* выделяет 2 ядра ЦП и 4 ГБ памяти;
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* сохраняет контейнер и журналы LUIS в выходном подключении в папке c:\output, расположенной на узле контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

Доступны дополнительные [примеры](luis-container-configuration.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> Для выполнения контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).
> Значение ApiKey содержит **ключ**, который можно получить на странице параметров ключей и конечной точки на портале LUIS или на странице ключей ресурса Интеллектуальной службы распознавания речи Azure.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. Конечные точки для опубликованных (промежуточных или рабочих) приложений имеют _другой_ маршрут, чем конечные точки для обученных приложений. 

Используйте узел `https://localhost:5000` для API контейнера. 

|Тип пакета|Метод|Маршрутизация|Параметры запроса|
|--|--|--|--|
|Опубликовано|[Get](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>&staging<br>[&timezoneOffset]<br>[&verbose]<br>[&log]<br>|
|Обучение пройдено|Get, Post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[&timezoneOffset]<br>[&verbose]<br>[&log]|

Параметры запроса определяют содержимое ответа на запрос и способ его получения:

|Параметр запроса|type|Назначение|
|--|--|--|
|`q`|строка|Фраза пользователя.|
|`timezoneOffset`|number|Параметр timezoneOffset позволяет [изменить часовой пояс](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity), используемый предварительно созданной сущностью datetimeV2.|
|`verbose`|Логическое|Возвращает все намерения и их оценки, если задано значение true. По умолчанию задано значение false, при котором возвращается только верхнее намерение.|
|`staging`|Логическое|Возвращает запрос из результатов промежуточной среды, если задано значение true. |
|`log`|Логическое|Записывает запросы в журнал, который затем можно использовать для [активного обучения](luis-how-to-review-endpoint-utterances.md). Значение по умолчанию — true.|

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

Контейнер LUIS отправляет в Azure данные для выставления счетов, используя соответствующий ресурс _Интеллектуальной службы распознавания речи_ в учетной записи Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](luis-container-configuration.md).

## <a name="unsupported-dependencies"></a>Неподдерживаемые зависимости

Приложение LUIS можно использовать, если оно **не содержит** следующие зависимости:

Неподдерживаемые конфигурации приложения|Сведения|
|--|--|
|Неподдерживаемые языки и региональные параметры контейнеров| Немецкий (de-DE)<br>Нидерландский (nl-NL)<br>Японский (ja-JP)<br>|
|Неподдерживаемые области|Предварительно созданные области, включая намерения и сущности в них|
|Неподдерживаемые сущности для всех языков и региональных параметров|Предварительно созданная сущность [KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) для всех языков и региональных параметров|
|Неподдерживаемые сущности для языка и региональных параметров "Английский (en-US)"|Предварительно созданные сущности [GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2)|
|Подготовка речи|Внешние зависимости не поддерживаются в контейнере.|
|Анализ мнений|Внешние зависимости не поддерживаются в контейнере.|
|Проверка орфографии Bing|Внешние зависимости не поддерживаются в контейнере.|

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

* Ознакомьтесь со статьей о [конфигурации контейнеров](luis-container-configuration.md).
* Ознакомьтесь со статьей об [устранение неполадок](troubleshooting.md), чтобы устранить проблемы, связанные с функциональностью LUIS.
* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../cognitive-services-container-support.md)
