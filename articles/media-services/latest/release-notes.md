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
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 09f9731f78093354f324f263899519bc5d31fa5d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682148"
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

## <a name="april-2019"></a>Апреля 2019 г.

[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) был добавлен в анализатор встроенные стили.

## <a name="march-2019"></a>Марта 2019 г.

Динамическая упаковка теперь поддерживает Dolby Atmos. Дополнительные сведения см. в разделе [аудиокодеки поддерживаются динамической упаковкой](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

Теперь можно указать список фильтров активов или учетной записи, которые относятся к вашей указатель потоковой передачи. Дополнительные сведения см. в разделе [связать фильтры с указатель потоковой передачи](filters-concept.md#associate-filters-with-streaming-locator).

## <a name="february-2019"></a>Февраль 2019 г.

Версии 3 службы мультимедиа теперь поддерживается в Национальное облако Azure. Некоторые возможности пока недоступны во всех облаках. Дополнительные сведения см. в разделе [Облака и регионы, в которых существуют Службы мультимедиа Azure версии 3](azure-clouds-regions.md).

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

### <a name="net-sdk"></a>ПАКЕТ SDK .NET

Следующие компоненты входят в пакет SDK для .NET:

* **Преобразования** и **задания**, позволяющие кодировать или анализировать мультимедийное содержимое. Примеры см. в руководстве по [потоковой передаче](stream-files-tutorial-with-api.md) и [анализе видео](analyze-videos-tutorial-with-api.md).
* **Указатели потоковой передачи**, отвечающие за публикацию и потоковую передачу содержимого на устройства пользователей.
* **Политики потоковой передачи** и **ключа содержимого**, отвечающие за настройку доставки ключей и защиту содержимого (DRM) при его доставке.
* **Динамические события** и **динамические выходные данные**, отвечающие за прием и потоковую трансляцию содержимого.
* **Ресурсы**, отвечающие за хранение и публикацию содержимого в службу хранилища Azure. 
* **Конечные точки потоковой передачи**, позволяющие настраивать и масштабировать динамическую упаковку, шифрование и потоковую передачу мультимедийного содержимого в реальном времени и по запросу.

### <a name="known-issues"></a>Известные проблемы

* При отправке задания можно указать исходное видео для приема, используя URL-адреса HTTPS, URL-адреса SAS или пути к файлам, находящимся в хранилище BLOB-объектов Azure. В настоящее время AMS версии 3 не поддерживает кодирование блочной передачи по URL-адресам HTTPS.

## <a name="provide-feedback"></a>Отзывы

Прочитайте статью [сообщества Служб мультимедиа Azure](media-services-community.md), чтобы узнать, как задавать вопросы, оставлять отзывы и получать новости о Службах мультимедиа.

## <a name="next-steps"></a>Дальнейшие действия

[Обзор](media-services-overview.md)
