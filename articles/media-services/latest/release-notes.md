---
title: Заметки о выпуске Служб мультимедиа Azure версии 3 | Документация Майкрософт
description: Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены последние обновления Служб мультимедиа Azure версии 3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 10/07/2019
ms.author: juliako
ms.openlocfilehash: 34d68974366eb17704b4ea1291bd1d29a0eea95a
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168632"
---
# <a name="azure-media-services-v3-release-notes"></a>Заметки о выпуске Служб мультимедиа Azure версии 3

Чтобы вы оставались в курсе последних разработок, в этой статье предоставлены такие сведения:

* Последние выпуски.
* Известные проблемы
* Исправления ошибок
* Нерекомендуемые функции.

## <a name="known-issues"></a>Известные проблемы

> [!NOTE]
> В настоящее время вы не можете использовать портал Azure для управления ресурсами версии 3. Используйте [REST API](https://aka.ms/ams-v3-rest-sdk), CLI или один из поддерживаемых пакетов SDK.

Дополнительные сведения см. в статье [Руководство по миграции из версии 2 в версию 3 Служб мультимедиа](migrate-from-v2-to-v3.md#known-issues).

## <a name="september-2019"></a>Сентябрь 2019 г.

###  <a name="media-services-v3"></a>Службы мультимедиа версии 3  

#### <a name="live-linear-encoding-of-live-events"></a>Динамическое линейное кодирование событий в реальном времени

Службы мультимедиа v3 сообщают о предварительной версии в 24 часа x 365 дней в реальном времени в реальном времени.

###  <a name="media-services-v2"></a>Службы мультимедиа версии 2  

#### <a name="deprecation-of-media-processors"></a>Устаревшие обработчики мультимедиа

Мы объявляем объявление о нерекомендуемых *Azure Media indexer* и *Azure Media indexer 2 Preview*. [Azure Media Indexerный](../previous/media-services-index-content.md) обработчик мультимедиа будет снят с 1 октября по 2020. Процессоры мультимедиа [Azure Media indexer 2 Preview](../previous/media-services-process-content-with-indexer2.md) будут сняты с 1 января 2020. [Индексатор видео служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/video-indexer/) заменяет эти устаревшие обработчики мультимедиа.

Дополнительные сведения см. в статье [Миграция с Azure Media indexer и Azure Media indexer 2 в индексатор видео служб мультимедиа Azure](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Август 2019 г.

###  <a name="media-services-v3"></a>Службы мультимедиа версии 3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Для служб мультимедиа открыта пара региональных стандартов Южно-Африканского языка 

Теперь службы мультимедиа доступны в регионах Южной и Южной Африки.

Дополнительные сведения см. [в разделе облака и регионы, в которых существует версия 3 служб мультимедиа](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Службы мультимедиа версии 2  

#### <a name="deprecation-of-media-processors"></a>Устаревшие обработчики мультимедиа

Мы сообщаем о прекращении использования *Windows Azure Media Encoder* (Ваме) и обработчиков мультимедиа *кодировщика мультимедиа Azure* (\ Media Encoder), которые выводятся на выходе 30 ноября 2019.

Дополнительные сведения см. в статье [Миграция Ваме в Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101334) и [перенос в Media Encoder Standard](https://go.microsoft.com/fwlink/?LinkId=2101335).
 
## <a name="july-2019"></a>2019 июля

### <a name="content-protection"></a>Защита содержимого

Если потоковая передача содержимого, защищенного с помощью ограничения маркера, конечным пользователям необходимо получить маркер, который отправляется как часть запроса на доставку ключа. Функция *предотвращения воспроизведения маркеров* позволяет клиентам служб мультимедиа устанавливать ограничения на то, сколько раз один и тот же маркер можно использовать для запроса ключа или лицензии. Дополнительные сведения см. в разделе [Предотвращение воспроизведения маркеров](content-protection-overview.md#token-replay-prevention).

В настоящее время эта функция доступна в центральной части США и западной центральной части США.

## <a name="june-2019"></a>Июнь 2019 г.

### <a name="video-subclipping"></a>Подрезка видео

Теперь можно обрезать или подрезать видео при кодировании с помощью [задания](https://docs.microsoft.com/rest/api/media/jobs). 

Эта функция работает с любым [преобразованием](https://docs.microsoft.com/rest/api/media/transforms), построенным с помощью предустановок [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) или [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). 

См. примеры:

* [Подклип видео с помощью .NET](subclip-video-dotnet-howto.md)
* [Подрезать видео с помощью RESTFUL](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Май 2019 г.

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Поддержка Azure Monitor для журналов диагностики и метрик в службах мультимедиа

Теперь вы можете использовать Azure Monitor для просмотра данных телеметрии, еммитед с помощью служб мультимедиа.

* Используйте журналы диагностики Azure Monitor, чтобы отслеживать запросы, отправленные конечной точкой доставки ключей служб мультимедиа. 
* Отслеживать метрики, созданные [конечными точками потоковой передачи](streaming-endpoint-concept.md)служб мультимедиа.   

Дополнительные сведения см. в статье [мониторинг метрик и журналов диагностики служб мультимедиа](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Поддержка нескольких звуковых дорожек в динамической упаковке 

При потоковой передаче ресурсов, имеющих несколько звуковых дорожек с несколькими кодеками и языков, [Динамическая упаковка](dynamic-packaging-overview.md) теперь поддерживает несколько звуковых дорожек для выходных данных HLS (версии 4 или более поздней).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Для служб мультимедиа открыта пара региональных стандартов Кореи 

Службы мультимедиа теперь доступны в центральных и Южной регионах Кореи. 

Дополнительные сведения см. [в разделе облака и регионы, в которых существует версия 3 служб мультимедиа](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Повышение производительности.

Добавлены обновления, включающие улучшения производительности служб мультимедиа.

* Обновлен максимальный размер файла, поддерживаемый для обработки. См. раздел, [квоты и ограничения](limits-quotas-constraints.md).
* [Улучшение скорости кодирования](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Апрель 2019 г.

### <a name="new-presets"></a>Новые предустановки

* [Фацедетекторпресет](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) был добавлен во встроенные предустановки анализатора.
* [Контентаваринкодинжекспериментал](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) был добавлен во встроенные предустановки кодировщика. Дополнительные сведения см. в разделе [кодировка с учетом содержимого](cae-experimental.md). 

## <a name="march-2019"></a>Март 2019 г.

Динамическая упаковка теперь поддерживает Dolby атмос. Дополнительные сведения см. в разделе [аудиокодеки, поддерживаемые динамической упаковкой](dynamic-packaging-overview.md#audio-codecs).

Теперь можно указать список фильтров активов или учетных записей, которые будут применяться к указателю потоковой передачи. Дополнительные сведения см. в разделе [связывание фильтров с указателем потоковой передачи](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Февраль 2019 г.

Службы мультимедиа версии 3 теперь поддерживаются в национальных облаках Azure. Некоторые возможности пока недоступны во всех облаках. Дополнительные сведения см. в разделе [Облака и регионы, в которых существуют Службы мультимедиа Azure версии 3](azure-clouds-regions.md).

Событие [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) для Служб мультимедиа добавлено в Сетку событий Azure.

## <a name="january-2019"></a>Январь 2019 г.

### <a name="media-encoder-standard-and-mpi-files"></a>Файлы MPI и Media Encoder Standard 

При кодировании с помощью Media Encoder Standard для создания файлов MP4 создается и добавляется в выходной ресурс новый MPI-файл. Этот MPI-файл предназначен для повышения производительности [динамической упаковки](dynamic-packaging-overview.md) и потоковой передачи.

Не следует изменять или удалять MPI-файл, а также ваша служба не должна зависеть от существования (или отсутствия) такого файла.

## <a name="december-2018"></a>Декабрь 2018 г.

Обновления от выпуска общедоступной версии API V3 включают:
       
* Свойства **PresentationTimeRange** больше не имеют значение "required" для **AssetFilters** и **AccountFilters**. 
* Параметры запроса $top и $skip для **заданий** и **преобразований** были удалены, а $orderby добавлен. В рамках добавления новой функциональности упорядочивания было обнаружено, что параметры $top и $skip были случайно выставлены ранее, даже если они не реализованы.
* Расширение перечисления было повторно включено. Эта функция была включена в предыдущих версиях пакета SDK и случайно отключена в общедоступной версии.
* Две стандартные политики потоковой передачи были переименованы. **SecureStreaming** теперь — **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** — **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Ноябрь 2018 г.

Модуль CLI 2.0 теперь доступен для [Службы мультимедиа Azure, общедоступная версия 3](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) — версия 2.0.50.

### <a name="new-commands"></a>Новые команды

- [az ams account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) позволяет управлять зарезервированными единицами мультимедиа. Дополнительные сведения см. в статье [Масштабирование при обработке мультимедиа](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Новые функции и критические изменения

#### <a name="asset-commands"></a>Команды для ресурсов

- Добавлены аргументы ```--storage-account``` и ```--container```.
- Добавлены значения по умолчанию для времени истечения срока действия (23 часа от текущего момента) и разрешений (чтение) в команду ```az ams asset get-sas-url```.

#### <a name="job-commands"></a>Команды для заданий

- Добавлены аргументы ```--correlation-data``` и ```--label```.
- Аргумент ```--output-asset-names``` переименован в ```--output-assets```. Теперь он принимает список ресурсов, разделенных пробелами, в формате assetName=label. Ресурс без метки можно передать следующим образом: assetName=.

#### <a name="streaming-locator-commands"></a>Команды для потокового указателя

- Базовая команда ```az ams streaming locator``` заменена на ```az ams streaming-locator```.
- Добавлены аргументы ```--streaming-locator-id``` и ```--alternative-media-id support```.
- Обновлен аргумент ```--content-keys argument```.
- Аргумент ```--content-policy-name``` переименован в ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Команды для политики потоковой передачи

- Базовая команда ```az ams streaming policy``` заменена на ```az ams streaming-policy```.
- Добавлена поддержка параметров шифрования в ```az ams streaming-policy create```.

#### <a name="transform-commands"></a>Команды преобразования

- Аргумент ```--preset-names``` заменен на ```--preset```. Теперь можно одновременно задавать только один вывод/набор параметров (для добавления дополнительных нужно запустить команду ```az ams transform output add```). Также можно задать пользовательский параметр StandardEncoderPreset, указав путь к пользовательскому файлу JSON.
- ```az ams transform output remove``` теперь можно выполнять путем передачи выходного индекса для удаления.
- Аргументы ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` добавлены в команды ```az ams transform create``` и ```az ams transform output add```.

## <a name="october-2018---ga"></a>Октябрь 2018 г. Общедоступная версия

В этом разделе описываются октябрьские обновления Служб мультимедиа Azure (AMS).

### <a name="rest-v3-ga-release"></a>Общедоступный выпуск REST версии 3

[Общедоступный выпуск REST версии 3](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) включает дополнительные API для динамических событий, фильтров манифестов на уровне ресурса и учетной записи и поддержку DRM.

#### <a name="azure-resource-management"></a>API управления ресурсами Azure 

Благодаря поддержке API управления ресурсами Azure доступен унифицированный API управления и операций (теперь все в одном месте).

Начиная с этого выпуска, для создания динамических событий можно использовать шаблоны Resource Manager.

#### <a name="improvement-of-asset-operations"></a>Улучшение операций с ресурсами 

Появились следующие улучшения:

- прием из URL-адреса HTTP или URL-адреса SAS хранилища BLOB-объектов Azure;
- указание собственных имен контейнеров для ресурсов; 
- простая поддержка выходных данных для создания пользовательских рабочих процессов с помощью службы "Функции Azure".

#### <a name="new-transform-object"></a>Новый объект преобразования

Новый объект **Преобразование** упрощает модель кодирования. Новый объект позволяет легко создавать и совместно использовать кодировку шаблонов Resource Manager и предустановок. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Аутентификация Azure Active Directory и RBAC

Аутентификация Azure AD и управление доступом на основе ролей (RBAC) обеспечивают безопасные преобразования, динамические события, политики ключей содержимого и предоставление ресурсов для ролей или пользователей в Azure AD.

#### <a name="client-sdks"></a>Клиентские пакеты SDK  

Языки, поддерживаемые в Службах мультимедиа версии 3: .NET Core, Java, Node.js, Ruby, Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Обновления кодирования в реальном времени

Представлены следующие обновления кодирования в реальном времени:

- Новый режим с низкой задержкой для трансляции (10 секунд для полного процесса).
- Улучшенная поддержка RTMP (повышенная стабильность и поддержка исходного кодировщика).
- Безопасный прием RTMPS.

    При создании динамических событий вы получите 4 URL-адреса для приема. 4 URL-адреса для приема практически идентичны, они имеют тот же токен потоковой передачи (AppId) с отличной частью номера порта. Два из этих URL-адресов являются первичным и резервным для RTMPS. 
- 24-часовая система поддержки перекодирования. 
- Улучшена поддержка рекламных сигналов в RTMP с помощью SCTE35.

#### <a name="improved-event-grid-support"></a>Улучшенная поддержка Сетки событий

Вы увидите следующие улучшения поддержки Сетки событий:

- Интеграция Сетки событий Azure для упрощения разработки с использованием Logic Apps и Функций Azure. 
- Подписка на события Службы кодирования, динамических каналов и др.

### <a name="cmaf-support"></a>Поддержка CMAF

Поддержка шифрования CMAF и cbcs для Apple HLS (iOS 11 и выше) и проигрывателей MPEG-DASH, поддерживающих CMAF.

### <a name="video-indexer"></a>Индексатор видео

Общедоступный выпуск службы "Индексатор видео" вышел в августе. Новые сведения о поддерживаемых функциях см. в статье [Что такое Индексатор видео?](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json) 

### <a name="plans-for-changes"></a>Планы по изменениям.

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Модуль Azure CLI 2.0, который включает в себя операции со всеми функциями (включая динамические события, политики ключа содержимого, фильтры учетной записи и ресурсов, политики потоковой передачи), ожидается в ближайшее время. 

### <a name="known-issues"></a>Известные проблемы

Эта проблема повлияет только на пользователей, которые используют API предварительной версии для фильтров ресурса или учетной записи.

Если вы создали фильтры учетных записей или ресурсов в период с 28.09 по 12.10 с помощью CLI или API для Служб мультимедиа версии 3, необходимо удалить все эти фильтры и повторно создать их из-за конфликта версий. 

## <a name="may-2018---preview"></a>Май 2018 г. Предварительная версия

### <a name="net-sdk"></a>Пакет SDK для .NET

В пакете SDK для .NET имеются следующие возможности.

* **Преобразования** и **задания**, позволяющие кодировать или анализировать мультимедийное содержимое. Примеры см. в руководстве по [потоковой передаче](stream-files-tutorial-with-api.md) и [анализе видео](analyze-videos-tutorial-with-api.md).
* **Указатели потоковой передачи**, отвечающие за публикацию и потоковую передачу содержимого на устройства пользователей.
* **Политики потоковой передачи** и **ключа содержимого**, отвечающие за настройку доставки ключей и защиту содержимого (DRM) при его доставке.
* **Динамические события** и **динамические выходные данные**, отвечающие за прием и потоковую трансляцию содержимого.
* **Ресурсы**, отвечающие за хранение и публикацию содержимого в службу хранилища Azure. 
* **Конечные точки потоковой передачи**, позволяющие настраивать и масштабировать динамическую упаковку, шифрование и потоковую передачу мультимедийного содержимого в реальном времени и по запросу.

### <a name="known-issues"></a>Известные проблемы

* При отправке задания можно указать исходное видео для приема, используя URL-адреса HTTPS, URL-адреса SAS или пути к файлам, находящимся в хранилище BLOB-объектов Azure. В настоящее время AMS версии 3 не поддерживает кодирование блочной передачи по URL-адресам HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Получение справки, отправка отзывов, получение обновлений

Прочитайте статью [сообщества Служб мультимедиа Azure](media-services-community.md), чтобы узнать, как задавать вопросы, оставлять отзывы и получать новости о Службах мультимедиа.

## <a name="next-steps"></a>Следующие шаги

- [Обзор](media-services-overview.md)
- [Заметки о выпуске служб мультимедиа версии 2](../previous/media-services-release-notes.md)
