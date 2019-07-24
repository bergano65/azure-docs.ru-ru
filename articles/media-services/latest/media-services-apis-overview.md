---
title: Разработка с помощью API V3 в Azure | Документация Майкрософт
description: В этой статье обсуждаются правила, применяемые к сущностям и API при разработке с помощью служб мультимедиа v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 26fea4322df625b2e38028a3b7121fb41f2acf81
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311864"
---
# <a name="developing-with-media-services-v3-apis"></a>Разработка с помощью API-интерфейсов служб мультимедиа v3

Как разработчик вы можете использовать [REST API](https://aka.ms/ams-v3-rest-ref) Служб мультимедиа или клиентские библиотеки, которые позволяют взаимодействовать с REST API для простого создания, контроля и обслуживания настраиваемых рабочих процессов. API [Служб мультимедиа версии 3](https://aka.ms/ams-v3-rest-sdk) основан на спецификации OpenAPI (ранее известной как Swagger).

В этой статье обсуждаются правила, применяемые к сущностям и API при разработке с помощью служб мультимедиа v3.

## <a name="accessing-the-azure-media-services-api"></a>Доступ к API служб мультимедиа Azure

Чтобы получить разрешение для доступа к ресурсам служб мультимедиа и API служб мультимедиа, вы должны пройти аутентификацию. Службы мультимедиа поддерживают проверку подлинности [на основе Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) . Ниже приведены два стандартных варианта проверки подлинности.
 
* **Проверка** подлинности субъекта-службы — используется для проверки подлинности службы (например, веб-приложений, приложений функций, приложений логики, API и микрослужб). Этот метод аутентификации обычно используют приложения, которые выполняют службы управляющей программы, службы среднего уровня или запланированные задания. Например, для веб-приложений всегда должен быть промежуточный уровень, который подключается к службам мультимедиа с помощью субъекта службы.
* **Проверка подлинности пользователя** — используется для аутентификации пользователя, который использует приложение для взаимодействия с ресурсами служб мультимедиа. Интерактивное приложение должно сначала запросить у пользователя учетные данные. Примером может послужить приложение консоли управления, которое используется авторизованными пользователями для мониторинга заданий кодирования или потоковой трансляции.

API служб мультимедиа требует, чтобы пользователь или приложение, выполняющие запросы REST API, имели доступ к ресурсу учетной записи служб мультимедиа и использовали роль **участника** или **владельца** . Доступ к API можно получить с помощью роли **читателя** , но будут доступны только операции **Get** или **List**   . Дополнительные сведения см. в статье [Управление доступом на основе ролей для учетных записей служб мультимедиа](rbac-overview.md).

Вместо создания субъекта-службы рекомендуется использовать управляемые удостоверения для ресурсов Azure, чтобы получить доступ к API служб мультимедиа с помощью Azure Resource Manager. Дополнительные сведения об управляемых удостоверениях для ресурсов Azure см. в статье [что такое управляемые удостоверения для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Субъект-служба Azure AD 

Если вы создаете приложение Azure AD и субъект-службу, приложение должно находиться в собственном клиенте. После создания приложения предоставьте участнику приложения или роли  **владельца** доступ к учетной записи служб мультимедиа. 

Если вы не уверены, есть ли у вас разрешения на создание приложения Azure AD, см. раздел [необходимые разрешения](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

На следующем рисунке числа представляют поток запросов в хронологическом порядке:

![Приложения среднего уровня](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Приложение среднего уровня запрашивает маркер доступа Azure AD со следующими параметрами:  

   * Конечная точка клиента Azure AD.
   * Универсальный код ресурса (URI) для ресурса служб мультимедиа.
   * Универсальный код ресурса (URI) для ресурса REST служб мультимедиа.
   * Значения приложения Azure AD: идентификатор клиента и секрет клиента.
   
   Чтобы получить все необходимые значения, см. статью [доступ к API служб мультимедиа Azure с помощью Azure CLI](access-api-cli-how-to.md)

2. Маркер доступа Azure AD передается на средний уровень.
4. Средний уровень отправляет запрос к REST API служб мультимедиа Azure с помощью маркера Azure AD.
5. Средний уровень получает данные из служб мультимедиа.

### <a name="samples"></a>Примеры

Ознакомьтесь со следующими примерами, в которых показано, как подключиться к субъекту-службе Azure AD.

* [Подключение с помощью других компонентов](media-rest-apis-with-postman.md)  
* [Подключение с помощью Java](configure-connect-java-howto.md)
* [Подключение с помощью .NET](configure-connect-dotnet-howto.md)
* [Подключение с помощью Node.js](configure-connect-nodejs-howto.md)
* [Подключение с помощью Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Соглашения об именовании.

К именам ресурсов Служб мультимедиа версии 3 (например, "Ресурсы", "Задания", "Преобразования") применяются ограничения именования Azure Resource Manager. В соответствии с Azure Resource Manager имена ресурсов всегда уникальны. Таким образом, вы можете использовать любые уникальные строки идентификаторов (например, GUID) для имен ваших ресурсов. 

Имена ресурсов Служб мультимедиа не должны содержать следующие символы: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", символ единичной кавычки или любые управляющие символы. Все остальные символы разрешены. Максимальная длина имени ресурса составляет 260 символов. 

Дополнительные сведения об именовании в Azure Resource Manager см. в статье [о требованиях к именованию](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) и статье [Соглашения об именовании для ресурсов Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Длительные операции

Операции, помеченные `x-ms-long-running-operation` в [файлах Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) служб мультимедиа Azure, являются длительными операциями. 

Дополнительные сведения об отслеживании асинхронных операций Azure см. в разделе [асинхронные операции](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

В службах мультимедиа выполняются следующие длительные операции:

* [Создание событий Live](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Обновление динамических событий](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Удалить событие прямой трансляции](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Начать интерактивное событие](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Завершение Лививент](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Используйте параметр `removeOutputsOnStop` , чтобы удалить все связанные динамические выходные данные при остановке события.  
* [Сбросить Лививент](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Создание Ливеаутпут](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Удалить Ливеаутпут](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Создание StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Обновление StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Удалить StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Запустить StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Завершение StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Масштабирование StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

При успешной отправке длительной операции вы получите "202 Accepted" и должны опросить завершение операции, используя возвращенный идентификатор операции.

Для данного интерактивного события или любого связанного с ним динамического выхода поддерживается только одна долго выполняющаяся операция. После запуска длительная операция должна завершиться до запуска следующей длительной операции с тем же Лививент или с любыми связанными динамическими выходами. Для событий в реальном времени с несколькими динамическими выходами необходимо ожидать завершения длительной операции над одним выходом в режиме реального времени, прежде чем запускать длительную операцию в другом выходном потоке. 

## <a name="sdks"></a>Пакеты SDK

> [!NOTE]
> Пакеты SDK для Служб мультимедиа Azure версии 3 могут не обеспечивать нужную безопасность для потоков. При разработке приложений с несколькими потоками вам следует добавить собственную логику синхронизации потоков, чтобы защитить клиент, или использовать новый объект AzureMediaServicesClient для каждого потока. Также помните, что предоставляемые кодом клиенту дополнительные объекты (например, экземпляр HttpClient в .NET) могут создавать проблемы при обработке нескольких потоков.

|SDK|Ссылка|
|---|---|
|[Пакет SDK для .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Справочник по .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[пакет SDK для Java](https://aka.ms/ams-v3-java-sdk)|[Справочник по Java](https://aka.ms/ams-v3-java-ref)|
|[Пакет SDK для Python](https://aka.ms/ams-v3-python-sdk)|[Справочник по Python](https://aka.ms/ams-v3-python-ref)|
|[Пакет SDK для Node.js](https://aka.ms/ams-v3-nodejs-sdk) |[Справочник по Node.js](https://aka.ms/ams-v3-nodejs-ref)| 
|[Пакет SDK для GO](https://aka.ms/ams-v3-go-sdk) |[Справочник по Go](https://aka.ms/ams-v3-go-ref)|
|[Пакет SDK для Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>См. также

- [Пакет SDK .NET EventGrid, который включает события Служб мультимедиа](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Определения событий Служб мультимедиа](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Обозреватель Служб мультимедиа Azure

[Обозреватель Служб мультимедиа Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) — это инструмент, доступный для клиентов Windows, которые хотят узнать о Службах мультимедиа. AMSE — это приложение Winforms или C#, которое отправляет, скачивает, кодирует, транслирует видео по запросу и в режиме реального времени с помощью Служб мультимедиа. Инструмент AMSE предназначен для клиентов, которые хотят протестировать Службы мультимедиа без написания кода. Код AMSE предоставляется как ресурс для клиентов, которым необходима разработка с помощью Служб мультимедиа.

AMSE — это проект с открытым кодом, поддержка которого предоставляется сообществом (о проблемах можно сообщать по ссылке https://github.com/Azure/Azure-Media-Services-Explorer/issues). Этот проект соответствует [правилам поведения Microsoft Open Source](https://opensource.microsoft.com/codeofconduct/). Дополнительные сведения см. в [вопросах и ответах по правилам поведения](https://opensource.microsoft.com/codeofconduct/faq/). С любыми другими вопросами или комментариями обращайтесь по адресу opencode@microsoft.com.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа

См. статью [Фильтрация, упорядочение, разбиение на страницы сущностей служб мультимедиа Azure](entities-overview.md) .

## <a name="ask-questions-give-feedback-get-updates"></a>Получение справки, отправка отзывов, получение обновлений

Прочитайте статью [сообщества Служб мультимедиа Azure](media-services-community.md), чтобы узнать, как задавать вопросы, оставлять отзывы и получать новости о Службах мультимедиа.

## <a name="see-also"></a>См. также

[Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Следующие шаги

* [Подключение к службам мультимедиа с помощью Java](configure-connect-java-howto.md)
* [Подключение к службам мультимедиа с помощью .NET](configure-connect-dotnet-howto.md)
* [Подключение к службам мультимедиа с помощью Node. js](configure-connect-nodejs-howto.md)
* [Подключение к службам мультимедиа с помощью Python](configure-connect-python-howto.md)
