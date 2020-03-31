---
title: Управляемые приложения с уведомлениями
description: Настройка управляемых приложений с конечными точками webhook для получения уведомлений о создает, обновляет, удаляет и ошибки в управляемых экземплярах приложения.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715673"
---
# <a name="azure-managed-applications-with-notifications"></a>Управляемые приложенияAzра с уведомлениями

Уведомления управляемых приложений Azure позволяют издателям автоматизировать действия на основе событий жизненного цикла управляемых экземпляров приложений. Издатели могут указывать конечные точки веб-крючка для получения уведомлений о новых и существующих управляемых экземплярах приложения. Издатели могут настроить пользовательские рабочие процессы во время подготовки приложений, обновлений и удалений.

## <a name="getting-started"></a>Начало работы
Чтобы начать получать управляемые приложения, подкрутите общедоступную конечную точку HTTPS и укажите ее при публикации определения приложения каталога услуг или предложения Azure Marketplace.

Вот рекомендуемые шаги, чтобы начать работу быстро:
1. Закрутите общедоступную конечную точку HTTPS, `200 OK`которая регистрирует входящие запросы и возвраты POST.
2. Добавьте конечную точку в определение приложения каталога услуг или предложение Azure Marketplace, как было улично в этой статье.
3. Создайте экземпляр управляемого приложения, который ссылается на определение приложения или предложение Azure Marketplace.
4. Проверка получения уведомлений.
5. Включить авторизацию, как это объясняется в разделе **проверки подлинности endpoint** этой статьи.
6. Следуйте инструкциям в разделе **схемы уведомлений** этой статьи, чтобы разобрать запросы уведомлений и реализовать бизнес-логику на основе уведомления.

## <a name="add-service-catalog-application-definition-notifications"></a>Добавление уведомлений об определении приложения каталога услуг
#### <a name="azure-portal"></a>Портал Azure
Для начала смотрите [публикацию приложения каталога услуг через портал Azure](./publish-portal.md).

![Уведомления об определении приложения каталога услуг на портале Azure](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> В настоящее время можно предоставить `notificationEndpoints` только одну конечную точку в свойствах определения приложения.

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>Добавление уведомлений управляемых приложений Azure Marketplace
Для получения дополнительной информации [см.](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)

![Azure Marketplace управлял уведомлениями приложений на портале Azure](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Триггеры событий
В следующей таблице описаны все возможные комбинации EventType и ProvisioningState и их триггеры:

EventType | ProvisioningState | Триггер для уведомления
---|---|---
ОТПРАВКА | Принято | Управляемая группа ресурсов была создана и успешно проецируется после приложения PUT (до развертывания внутри управляемой группы ресурсов стартует).
ОТПРАВКА | Выполнено | Полное подготовка управляемого приложения удалось после PUT.
ОТПРАВКА | Ошибка | Сбой PUT подготовки экземпляра приложения в любой момент.
PATCH | Выполнено | После успешного ПАТЧа на экземпляре управляемого приложения для обновления тегов, политики доступа JIT или управляемого удостоверения личности.
DELETE | Удаление | Как только пользователь инициирует DELETE управляемого экземпляра приложения.
DELETE | Deleted | После полного и успешного удаления управляемого приложения.
DELETE | Ошибка | После любой ошибки в процессе деобеспечения, который блокирует удаление.
## <a name="notification-schema"></a>Схема уведомлений
Когда вы закручиваете конечную точку webhook для обработки уведомлений, необходимо разобрать полезную нагрузку, чтобы получить важные свойства, чтобы затем действовать после уведомления. Каталог служб и управляемые уведомления приложений Azure Marketplace предоставляют многие из тех же свойств. Два небольших отличия изложены в таблице, которая следует за образцами.

#### <a name="service-catalog-application-notification-schema"></a>Схема уведомления каталога услуг
Вот пример уведомления каталога услуг после успешного подготовки экземпляра управляемого приложения:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

В случае сбоя в подготовке уведомление с сведениями об ошибке будет отправлено в указанную конечную точку.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Схема уведомления приложения Azure Marketplace

Вот пример уведомления каталога услуг после успешного подготовки экземпляра управляемого приложения:
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

В случае сбоя в подготовке уведомление с сведениями об ошибке будет отправлено в указанную конечную точку.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Параметр | Описание
---|---
eventType | Тип события, срабатывающего уведомление. (Например, PUT, PATCH, DELETE.)
applicationId | Полностью квалифицированный идентификатор ресурса управляемого приложения, для которого было срабатывано уведомление.
eventTime | Отметка времени события, срабатывающего уведомления. (Дата и время в формате UTC ISO 8601.)
provisioningState | Состояние подготовки экземпляра управляемого приложения. (Например, преуспевать, не удалось, Удаление, Удаление.)
error | *Указано только в случае отказа положения подготовки.* Содержит код ошибки, сообщение и сведения о проблеме, которая вызвала сбой.
applicationDefinitionId | *Указано только для сервисного каталога управляемых приложений*. Представляет собой полностью квалифицированный идентификатор ресурса определения приложения, для которого был подготовлен управляемый экземпляр приложения.
План | *Указано только для управляемых приложений Azure Marketplace.* Представляет издателя, предложение, SKU и версию управляемого экземпляра приложения.
БиллингПодробности | *Указано только для управляемых приложений Azure Marketplace.* Сведения о выставлении счетов в экземпляре управляемого приложения. Содержит ресурсUsageId, который можно использовать для запроса Azure Marketplace для сведения об использовании.

## <a name="endpoint-authentication"></a>Проверка подлинности конечных точек
Чтобы обеспечить конечную точку webhook и обеспечить подлинность уведомления:
1. Предоставьте параметр запроса поверх webhook URI,\:как это: https //your-endpoint.com?sig-Guid. С каждым уведомлением проверяйте, `sig` имеет ли `Guid`параметр запроса ожидаемое значение.
2. Выпуск GET на экземпляре управляемого приложения с помощью applicationId. Проверка того, что положение о подготовке соответствует состоянию подготовки уведомления для обеспечения согласованности.

## <a name="notification-retries"></a>Уведомления retries

Служба уведомления управляемых `200 OK` приложений ожидает ответа от конечной точки webhook на уведомление. Служба уведомлений будет повторно пытаться, если конечная точка webhook возвращает код ошибки HTTP больше или равен 500, если он возвращает код ошибки 429, или если конечная точка временно недостижима. Если конечная точка webhook не станет доступна в течение 10 часов, сообщение об уведомлении будет удалено, и повторы прекратятся.
