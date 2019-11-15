---
title: Управляемые приложения Azure с уведомлениями
description: Настройка управляемого приложения с конечными точками веб-перехватчика для получения уведомлений о создании, обновлениях, удалениях и ошибках в экземплярах управляемых приложений.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: a00e5be4493b8c8116e2925e88a3ce4bf8cfb722
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085321"
---
# <a name="azure-managed-applications-with-notifications"></a>Управляемые приложения Azure с уведомлениями

Уведомления управляемых приложений Azure позволяют издателям автоматизировать действия на основе событий жизненного цикла экземпляров управляемых приложений. Издатели могут указывать пользовательские конечные точки веб-перехватчика уведомлений для получения уведомлений о событиях новых и существующих экземпляров управляемых приложений. Он позволяет издателю настраивать пользовательские рабочие процессы во время подготовки, обновления и удаления приложений.

## <a name="getting-started"></a>Приступая к работе
Чтобы начать получать управляемые приложения, запустите общедоступную конечную точку HTTPS и укажите ее при публикации определения приложения каталога услуг или предложения Marketplace.

Ниже приведена рекомендуемая последовательность действий, которые помогут быстро приступить к работе.
1. Запустите общедоступную конечную точку HTTPS, которая регистрирует входящие запросы POST и возвращает `200 OK`.
2. Добавьте конечную точку в определение приложения каталога услуг или предложение Marketplace, как описано ниже.
3. Создайте экземпляр управляемого приложения, который ссылается на определение приложения или предложение Marketplace.
4. Проверьте, что уведомления получены успешно.
5. Включите авторизацию, как описано в разделе **Проверка подлинности конечной точки** ниже.
6. Следуйте приведенной ниже документации по **схеме уведомлений** , чтобы проанализировать запросы уведомлений и реализовать бизнес-логику на основе уведомления.

## <a name="adding-service-catalog-application-definition-notifications"></a>Добавление уведомлений об определении приложения из каталога услуг
#### <a name="azure-portal"></a>портале Azure
Чтобы приступить к работе, прочитайте статью [Публикация приложения каталога услуг с помощью портал Azure](./publish-portal.md) .

![Уведомления об определении приложения каталога услуг на портале](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>Интерфейс REST API

> [!NOTE]
> В настоящее время только одна конечная точка поддерживается как часть **нотификатионендпоинтс** в свойствах определения приложения.

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
## <a name="adding-marketplace-managed-application-notifications"></a>Добавление уведомлений управляемого приложения Marketplace
Дополнительные сведения см. [в разделе Создание предложения приложения Azure](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md).

![Уведомления об определении приложения каталога услуг на портале](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Триггеры событий
В следующей таблице описаны все возможные сочетания EventType + ProvisioningState и их триггеров.

EventType | ProvisioningState | Триггер для уведомления
---|---|---
ОТПРАВКА | Принято | Управляемая группа ресурсов создана и спроектирована успешно после размещения приложения. (Перед началом развертывания в управляемом RG.)
ОТПРАВКА | Успешно | Полная подготовка управляемого приложения прошла удачно после размещения.
ОТПРАВКА | Ошибка | Сбой помещения подготовки экземпляра приложения в любой момент.
PATCH | Успешно | После успешного исправления в экземпляре управляемого приложения для обновления тегов, политики JIT-доступа или управляемого удостоверения.
УДАЛИТЬ | Удаление | Как только пользователь инициирует удаление экземпляра управляемого приложения.
УДАЛИТЬ | Deleted | После полного и успешного удаления управляемого приложения.
УДАЛИТЬ | Ошибка | После любой ошибки в процессе отмены инициализации, блокирующей удаление.
## <a name="notification-schema"></a>Схема уведомления
При перезапуске конечной точки веб-перехватчика для обработки уведомлений необходимо проанализировать полезные данные, чтобы получить важные свойства, которые затем будут действовать при уведомлении. Как для каталога услуг, так и для уведомлений управляемого приложения Marketplace предусмотрено множество тех же свойств, что и небольшое отличие, описанное ниже.

#### <a name="service-catalog-application-notification-schema"></a>Схема уведомления приложения каталога услуг
Ниже приведен пример уведомления каталога услуг после успешной подготовки экземпляра управляемого приложения.
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

Если подготовка завершается неудачно, в указанную конечную точку будет отправлено уведомление с подробными сведениями об ошибке.

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

#### <a name="marketplace-application-notification-schema"></a>Схема уведомления приложения Marketplace

Ниже приведен пример уведомления каталога услуг после успешной подготовки экземпляра управляемого приложения.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Если подготовка завершается неудачно, в указанную конечную точку будет отправлено уведомление с подробными сведениями об ошибке.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
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

Параметр | ОПИСАНИЕ
---|---
eventType | Тип события, вызвавшего уведомление. (например, "размещение", "исправление", "Удалить")
applicationId | Полный идентификатор ресурса управляемого приложения, для которого было активировано уведомление. 
eventTime | Метка времени события, вызвавшего уведомление. (Дата и время в формате UTC 8601.)
ProvisioningState | Состояние подготовки экземпляра управляемого приложения. (например, "успешно", "сбой", "удаление", "удалено")
error | *Указывается только при сбое provisioningState*. Содержит код ошибки, сообщение и сведения о неполадке, которая привела к сбою.
аппликатиондефинитионид | *Указывается только для управляемых приложений каталога услуг*. Представляет полный идентификатор ресурса определения приложения, для которого была подготовлена инициализация экземпляра управляемого приложения.
План | *Указывается только для управляемых приложений Marketplace*. Представляет издателя, предложение, номер SKU и версию управляемого экземпляра приложения.

## <a name="endpoint-authentication"></a>Проверка подлинности конечной точки
Чтобы защитить конечную точку веб-перехватчика и убедиться в подлинности уведомления, сделайте следующее:
- Укажите параметр запроса поверх URI веб-перехватчика, например https://your-endpoint.com?sig=Guid. При каждом уведомлении выполните быструю проверку того, что параметр запроса `sig` имеет ожидаемое значение `Guid`.
- Выполните GET в экземпляре управляемого приложения с помощью applicationId. Проверьте, что provisioningState соответствует provisioningState уведомления, чтобы обеспечить согласованность.

## <a name="notification-retries"></a>Повторы уведомлений

Служба уведомлений управляемого приложения принимает на уведомление `200 OK` ответ от конечной точки веб-перехватчика к уведомлению. Служба уведомлений повторит попытку, если конечная точка веб-перехватчика возвращает код ошибки HTTP > = 500, 429 или если конечная точка временно недоступна. Если конечная точка веб-перехватчика не станет доступной в течение 10 часов, сообщение уведомления будет удалено, а повторные попытки будут прекращены.

