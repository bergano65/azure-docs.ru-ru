---
title: Разработка с помощью AIS v3
titleSuffix: Azure Media Services
description: Узнайте о правилах, которые применяются к юридическим лицам и AA при разработке с помощью Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472090"
---
# <a name="develop-with-media-services-v3-apis"></a>Разработка с помощью медиа-услуг v3 AIS

Как разработчик вы можете использовать [REST API](https://docs.microsoft.com/rest/api/media/) Служб мультимедиа или клиентские библиотеки, которые позволяют взаимодействовать с REST API для простого создания, контроля и обслуживания настраиваемых рабочих процессов. [Медиа-сервис ы v3](https://aka.ms/ams-v3-rest-sdk) API основан на спецификации OpenAPI (ранее известной как Swagger).

В этой статье рассматриваются правила, которые применяются к юридическим лицам и AA при разработке с Помощью Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Доступ к API медиаслужбы Azure

Чтобы получить разрешение для доступа к ресурсам служб мультимедиа и API служб мультимедиа, вы должны пройти аутентификацию. Медиа-службы поддерживают проверку подлинности [Active Directory (Azure AD) на основе Azure.](../../active-directory/fundamentals/active-directory-whatis.md) Два общих варианта аутентификации:
 
* **Проверка подлинности службы:** Используется для проверки подлинности службы (например, веб-приложений, функциональных приложений, логических приложений, API и микрослужб). Этот метод аутентификации обычно используют приложения, которые выполняют службы управляющей программы, службы среднего уровня или запланированные задания. Например, для веб-приложений всегда должен быть средний уровень, который подключается к службам мультимедиа с принципом службы.
* **Аутентификация пользователя:** Используется для проверки подлинности пользователя, использующего приложение для взаимодействия с ресурсами Медиа-сервисов. Интерактивное приложение должно сначала подсказать пользователю учетные данные пользователя. Примером может послужить приложение консоли управления, которое используется авторизованными пользователями для мониторинга заданий кодирования или потоковой трансляции.

API Media Services требует, чтобы пользователь или приложение, предоставляющее запросы REST API, имели доступ к ресурсу учетной записи Media Services и использовали роль **участника** или **владельца.** Доступ к API можно получить с ролью **Reader,** но будут доступны только операции **Get** или **List.**Для получения дополнительной [Role-based access control for Media Services accounts](rbac-overview.md)информации см.

Вместо создания основного обслуживания следует использовать управляемые идентификаторы для ресурсов Azure для доступа к API медиа-служб через Менеджер ресурсов Azure. Подробнее об управляемых идентификаторах ресурсов Azure читайте в отделе поиска ресурсов [Azure.](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="azure-ad-service-principal"></a>Директор службы Azure AD

Если вы создаете приложение Azure AD и принцип обслуживания, приложение должно быть в своем собственном арендаторе. После создания приложения предоставьте доступ к учетной записи Media Services **для роли автора** или **владельца.**

Если вы не уверены, есть ли у вас разрешения на создание приложения Azure AD, [см.](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)

В следующем рисунке цифры отражают поток запросов в хронологическом порядке:

![Аутентификация приложений среднего уровня с Помощью AAD с помощью веб-API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Приложение среднего уровня запрашивает токен доступа Azure AD с следующими параметрами:  

   * Конечная точка клиента Azure AD.
   * Универсальный код ресурса (URI) для ресурса служб мультимедиа.
   * Универсальный код ресурса (URI) для ресурса REST служб мультимедиа.
   * Значения приложения Azure AD: идентификатор клиента и секрет клиента.

   Чтобы получить все необходимые значения, смотрите [API Службы мультимедиа Access Azure с помощью Azure CLI.](access-api-cli-how-to.md)

2. Маркер доступа Azure AD передается на средний уровень.
4. Средний уровень отправляет запрос к REST API служб мультимедиа Azure с помощью маркера Azure AD.
5. Средний уровень получает данные из служб мультимедиа.

### <a name="samples"></a>Примеры

Ознакомьтесь со следующими примерами, которые показывают, как подключиться к принципу службы Azure AD:

* [Связь с REST](media-rest-apis-with-postman.md)  
* [Подключение с помощью Java](configure-connect-java-howto.md)
* [Подключение с помощью .NET](configure-connect-dotnet-howto.md)
* [Подключение с помощью Node.js](configure-connect-nodejs-howto.md)
* [Подключение с помощью Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Соглашения об именовании.

К именам ресурсов Служб мультимедиа версии 3 (например, "Ресурсы", "Задания", "Преобразования") применяются ограничения именования Azure Resource Manager. В соответствии с Azure Resource Manager имена ресурсов всегда уникальны. Таким образом, вы можете использовать любые уникальные строки идентификаторов (например, GUID) для имен ваших ресурсов.

Названия ресурсов медиа-служб не могут включать: "<", ">", "%", "&", ""&#92;", "?", "/",",",",",","","","","","","",","","",",",",",",",",",",",",",",",",",",",",",",", "",", "", "", "",", ",", ",", ",", ",", ",", ",", ",", ",", ",", ",", ",", ",", ",", "",", "", " Все остальные символы разрешены. Максимальная длина имени ресурса составляет 260 символов.

Для получения дополнительной информации о именованиях менеджера ресурсов Azure [см.](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) [Naming conventions](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

### <a name="names-of-filesblobs-within-an-asset"></a>Названия файлов/blobs внутри актива

Названия файлов/капли в активе должны следовать как [требованиям к названию капли,](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) так и [требованиям к названию NTFS.](https://docs.microsoft.com/windows/win32/fileio/naming-a-file) Причина этих требований заключается в том, что файлы могут быть скопированы из хранилища капли на локальный диск NTFS для обработки.

## <a name="long-running-operations"></a>Долгосрочные операции

Операции, отмеченные `x-ms-long-running-operation` в [файлах swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) Медиа Службы Azure, являются длительными операциями. 

Подробную информацию о том, как отслеживать асинхронные операции Azure, можно узнать об [операциях Async.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)

Медиа-сервисы ведут следующие длительные операции:

* [Создание живых событий](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Обновление событий в прямом эфире](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Удаление события в прямом эфире](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Начало события в прямом эфире](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Остановить LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Используйте `removeOutputsOnStop` параметр, чтобы удалить все связанные живые выходы при остановке события.  
* [Сбросить LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Создание LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Удаление LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Создание конечной точки потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Обновление StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Удаление StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Запуск конечной точки потоковой передачи](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Остановить потоковуюточку](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Масштаб потоковой передачиEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

При успешном представлении длительной операции вы получаете "202 Принято" и должны опросить для завершения операции с помощью идентификатора возвращенной операции.

В статье [Track asynchronous Azure operations](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) (Отслеживание асинхронных операций Azure) приведены подробные сведения о том, как отслеживать состояние асинхронных операций Azure с помощью значений, возвращаемых в ответе.

Поддерживается только одна длительная операция для данного Live Event или любого из связанных с ним выходов Live. После запуска длительная операция должна быть завершена перед началом последующей длительной операции на том же LiveEvent или любых связанных с ними живых выходов. Для живых событий с несколькими live Outputs, вы должны ждать завершения длительной операции на одном Выходе Live, прежде чем запустить длительную операцию на другом выходе Live. 

## <a name="sdks"></a>Пакеты SDK

> [!NOTE]
> V3 SDK Для мультимедиа Azure не гарантируется безопасным для потоков. При разработке многопоточного приложения следует добавить собственную логику синхронизации потоков для защиты клиента или использовать новый объект AzureMediaServicesClient на поток. Также помните, что предоставляемые кодом клиенту дополнительные объекты (например, экземпляр HttpClient в .NET) могут создавать проблемы при обработке нескольких потоков.

|SDK|Справочник|
|---|---|
|[Пакет SDK для .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Справочник по .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[пакет SDK для Java](https://aka.ms/ams-v3-java-sdk)|[Справочник по Java](https://aka.ms/ams-v3-java-ref)|
|[Питон SDK](https://aka.ms/ams-v3-python-sdk)|[Справочник по Python](https://aka.ms/ams-v3-python-ref)|
|[Пакет SDK для Node.js](https://aka.ms/ams-v3-nodejs-sdk) |[Справочник по Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[Пакет SDK для GO](https://aka.ms/ams-v3-go-sdk) |[Справочник по Go](https://aka.ms/ams-v3-go-ref)|
|[Пакет SDK для Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>См. также

- [Пакет SDK .NET EventGrid, который включает события Служб мультимедиа](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Определения событий Служб мультимедиа](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Обозреватель Служб мультимедиа Azure

[Обозреватель Служб мультимедиа Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) — это инструмент, доступный для клиентов Windows, которые хотят узнать о Службах мультимедиа. AMSE — это приложение Winforms или C#, которое отправляет, скачивает, кодирует, транслирует видео по запросу и в режиме реального времени с помощью Служб мультимедиа. Инструмент AMSE предназначен для клиентов, которые хотят протестировать Службы мультимедиа без написания кода. Код AMSE предоставляется как ресурс для клиентов, которым необходима разработка с помощью Служб мультимедиа.

AMSE — это проект с открытым кодом, поддержка которого предоставляется сообществом (о проблемах можно сообщать по ссылке https://github.com/Azure/Azure-Media-Services-Explorer/issues). В рамках этого проекта действуют [правила поведения в отношении продуктов с открытым исходным кодом Майкрософт](https://opensource.microsoft.com/codeofconduct/). Для получения дополнительной информации ознакомьтесь с [часто задаваемыми вопросами Кодекса поведения](https://opensource.microsoft.com/codeofconduct/faq/) или свяжитесь opencode@microsoft.com с любыми другими вопросами или комментариями.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа

Смотрите [фильтрацию, заказ, paging объектов медиа-служб Azure.](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Получение справки, отправка отзывов, получение обновлений

Прочитайте статью [сообщества Служб мультимедиа Azure](media-services-community.md), чтобы узнать, как задавать вопросы, оставлять отзывы и получать новости о Службах мультимедиа.

## <a name="see-also"></a>См. также

[Лазурный CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Дальнейшие действия

* [Подключение к медиа-службам с помощью Java](configure-connect-java-howto.md)
* [Подключение к медиа-сервисам с помощью .NET](configure-connect-dotnet-howto.md)
* [Подключение к медиа-службам с помощью Node.js](configure-connect-nodejs-howto.md)
* [Подключение к медиа-службам с помощью Python](configure-connect-python-howto.md)
