---
title: Добавление кэширования для повышения производительности в службе управления API Azure | Документация Майкрософт
description: Сведения об уменьшении задержки, использования пропускной способности и загрузки веб-службы для вызовов службы управления API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 76a87d539e19acc30944a6a896cb0e01f431fa37
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073563"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Добавление кэширования для повышения производительности в службе управления API Azure

Операции в управлении API можно настроить для кэширования ответов. Кэширование ответов может значительно уменьшить время задержки API, потребляемую пропускную способность, и нагрузку на веб-службу применительно к данным, которые изменяются редко.

Дополнительные сведения о кэшировании см. в статьях [Политики кэширования в службе управления API](api-management-caching-policies.md) и [Пользовательское кэширование в службе управления API Azure](api-management-sample-cache-by-key.md).

![Политики кэширования](media/api-management-howto-cache/cache-policies.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * добавление кэширования ответов для API;
> * проверка кэширования в действии.

## <a name="availability"></a>Доступность

> [!NOTE]
> Внутренний кэш не предусмотрен на уровне **Потребление** службы управления API Azure. Вместо этого вы можете [использовать внешний кэш Redis для Azure](api-management-howto-cache-external.md).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

+ [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md)
+ [импортируйте и опубликуйте API.](import-and-publish.md)

## <a name="caching-policies"> </a>Добавление политик кэширования

При использовании политик кэширования в этом примере на первый запрос операции **GetSpeakers** возвращается ответ из серверной службы. Этот ответ кэшируется, а также отмечается ключом указанных заголовков и параметров строки запроса. Для последующих вызовов операции с совпадающими параметрами будет возвращаться кэшированный ответ до тех пор, пока не истечет срок действия кэша.

1. Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).
2. Перейдите к экземпляру службы управления API Azure.
3. Выберите вкладку **API**.
4. В списке API выберите **Demo Conference API**.
5. Выберите **GetSpeakers**.
6. В верхней части экрана выберите вкладку **Конструктор**.
7. В разделе **Обработка входящих запросов** щелкните значок **</>** .

    ![Редактор кода](media/api-management-howto-cache/code-editor.png)

8. В элемент **inbound** добавьте следующую политику:

        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
            <vary-by-header>Authorization</vary-by-header>
        </cache-lookup>

9. В элемент **outbound** добавьте такую политику:

        <cache-store caching-mode="cache-on" duration="20" />

    **Длительность** указывает интервал срока действия кэшированных ответов. В этом примере интервал составляет **20** секунд.

> [!TIP]
> Если вы используете внешний кэш, как описано в статье [Use an external Azure Cache for Redis in Azure API Management](api-management-howto-cache-external.md) (Использование внешнего кэша Azure для Redis в службе Azure "Управление API"), возможно, потребуется указать атрибут `caching-type` политик кэширования. Дополнительные сведения см. в статье [Политики кэширования в службе управления API](api-management-caching-policies.md).

## <a name="test-operation"> </a>Вызов операции и проверка кэширования
Чтобы увидеть кэширование в действии, вызовите операцию на портале разработчика.

1. На портале Azure перейдите к экземпляру службы управления API Azure.
2. Выберите вкладку **Интерфейсы API**.
3. Выберите API, в который вы добавили политики кэширования.
4. Щелкните операцию **GetSpeakers**.
5. Перейдите на вкладку **Тестирование** в правом верхнем меню.
6. Нажмите кнопку **Отправить**.

## <a name="next-steps"> </a>Дальнейшие действия
* Дополнительные сведения о политиках кэширования см. в разделе [Политики кэширования][Caching policies] [справочника по политикам управления API][API Management policy reference].
* Сведения о кэшировании элементов по ключу с помощью выражений политики см. в статье [Пользовательское кэширование в службе управления API Azure](api-management-sample-cache-by-key.md).
* Дополнительные сведения см. в статье об [использовании внешнего кэша Azure для Redis в службе Azure "Управление API"](api-management-howto-cache-external.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
