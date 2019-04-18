---
title: Разработка с использованием интерфейсов API v3 — Azure | Документация Майкрософт
description: В этой статье рассматриваются правила, применяемые к сущностям и API при разработке с помощью Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 5ae7563892cb4792f5c329b2850d7b88d37c0e7d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698278"
---
# <a name="developing-with-media-services-v3-apis"></a>Разработка с использованием служб мультимедиа версии 3 API-интерфейсов

В этой статье рассматриваются правила, применяемые к сущностям и API при разработке с помощью Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Доступ к API служб мультимедиа Azure

Для доступа к ресурсам служб мультимедиа Azure, можно использовать проверку подлинности субъекта-службы Azure Active Directory (AD).
API служб мультимедиа требует, что пользователь или приложение, инициирующее REST API запрашивает доступ к ресурсу учетной записи служб мультимедиа и использовать **участник** или **владельца** роли. Доступ к API с **чтения** только роли **получить** или **списка**   операции будут доступны. Дополнительные сведения см. в разделе [управление доступом на основе ролей для учетных записей служб мультимедиа](rbac-overview.md).

Вместо создания субъекта-службы, рассмотрите возможность использования управляемых удостоверений для ресурсов Azure для доступа к API служб мультимедиа Azure Resource Manager. Дополнительные сведения об управляемых удостоверениях для ресурсов Azure, см. в разделе [что такое управляемые удостоверения для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Субъект-служба Azure AD 

Если вы создаете приложение Azure AD и службу субъекта, приложение должно быть в собственном клиенте. После создания приложения назначьте приложению **участник** или **владельца** роли доступ к учетной записи служб мультимедиа. 

Если вы не уверены, что у вас есть разрешения на создание приложения Azure AD, см. в разделе [необходимые разрешения](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

На следующем рисунке цифры обозначают последовательность запросов в хронологическом порядке:

![Приложения среднего уровня](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Приложение среднего уровня запрашивает маркер доступа Azure AD, который имеет следующие параметры:  

   * Конечная точка клиента Azure AD.
   * Универсальный код ресурса (URI) для ресурса служб мультимедиа.
   * Универсальный код ресурса (URI) для ресурса REST служб мультимедиа.
   * Значения приложения Azure AD: идентификатор клиента и секрет клиента.
   
   Чтобы получить все необходимые значения, см. в разделе [доступа к API служб мультимедиа Azure с помощью Azure CLI](access-api-cli-how-to.md)

2. Маркер доступа Azure AD передается на средний уровень.
4. Средний уровень отправляет запрос к REST API служб мультимедиа Azure с помощью маркера Azure AD.
5. Средний уровень получает данные из служб мультимедиа.

## <a name="naming-conventions"></a>Соглашения об именовании.

К именам ресурсов Служб мультимедиа версии 3 (например, "Ресурсы", "Задания", "Преобразования") применяются ограничения именования Azure Resource Manager. В соответствии с Azure Resource Manager имена ресурсов всегда уникальны. Таким образом, вы можете использовать любые уникальные строки идентификаторов (например, GUID) для имен ваших ресурсов. 

Имена ресурсов Служб мультимедиа не должны содержать следующие символы: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", символ единичной кавычки или любые управляющие символы. Все остальные символы разрешены. Максимальная длина имени ресурса составляет 260 символов. 

Дополнительные сведения об именовании в Azure Resource Manager см. в статье [о требованиях к именованию](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) и статье [Соглашения об именовании для ресурсов Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Длительные операции

Операции с пометкой `x-ms-long-running-operation` в службах мультимедиа Azure [swagger файлы](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) длинные выполняющиеся операции. 

Дополнительные сведения о том, как отслеживание асинхронных операций Azure, см. в разделе [асинхронных операций](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Службы мультимедиа доступны следующие долго выполняющейся операции:

* Создание LiveEvent
* Обновить LiveEvent
* Удалить LiveEvent
* Запустить LiveEvent
* Остановить LiveEvent
* Сброс LiveEvent
* Создание LiveOutput
* Удалить LiveOutput
* Создание конечной точки потоковой передачи
* Обновление конечной точки потоковой передачи
* Удаление конечной точки потоковой передачи
* Запуск конечной точки потоковой передачи
* Остановить конечную точку потоковой передачи
* Масштабирование конечной точки потоковой передачи

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Фильтрация, упорядочивание и разбиение по страницам сущностей Служб мультимедиа

См. в разделе [фильтрация, упорядочение, разбиение на страницы сущностей служб мультимедиа Azure](entities-overview.md)

## <a name="next-steps"></a>Дальнейшие действия

[Начало разработки с использованием API служб мультимедиа версии 3, с помощью пакетов SDK/tools](developers-guide.md)
