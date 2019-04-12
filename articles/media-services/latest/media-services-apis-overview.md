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
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 18b72ceaee0ca0747a0bf2144d5f9ffddbee8b8c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59492147"
---
# <a name="developing-with-media-services-v3-apis"></a>Разработка с использованием служб мультимедиа версии 3 API-интерфейсов

В этой статье рассматриваются правила, применяемые к сущностям и API при разработке с помощью Media Services v3.

## <a name="naming-conventions"></a>Соглашения об именовании.

К именам ресурсов Служб мультимедиа версии 3 (например, "Ресурсы", "Задания", "Преобразования") применяются ограничения именования Azure Resource Manager. В соответствии с Azure Resource Manager имена ресурсов всегда уникальны. Таким образом, вы можете использовать любые уникальные строки идентификаторов (например, GUID) для имен ваших ресурсов. 

Имена ресурсов Служб мультимедиа не должны содержать следующие символы: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", символ единичной кавычки или любые управляющие символы. Все остальные символы разрешены. Максимальная длина имени ресурса составляет 260 символов. 

Дополнительные сведения об именовании в Azure Resource Manager см. в статье [о требованиях к именованию](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) и статье [Соглашения об именовании для ресурсов Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles-and-rbac"></a>принципы проектирования API V3 и RBAC

Один из ключевых принципов проектирования API версии 3 — сделать API более безопасным. API-интерфейсы v3 не возвращают секретные данные или учетные данные на **получить** или **списка** операций. Ключи всегда являются NULL, пустыми или исключенными из ответа. Пользователь должен вызвать метод отдельное действие для получения секретов или учетные данные. **Чтения** роли не может вызывать операции, поэтому не может вызвать операции, такие как ContentKeyPolicies.GetPolicyPropertiesWithSecrets Asset.ListContainerSas StreamingLocator.ListContentKeys,. Наличие отдельных действий позволяет при необходимости задайте более детализированных разрешений системы безопасности RBAC в пользовательскую роль.

Дополнительные сведения можно найти в разделе 

- [Определения встроенной роли](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)
- [Использование RBAC для управления доступом](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Управление доступом на основе ролей для учетных записей служб мультимедиа](rbac-overview.md)
- [Получение содержимого ключа политики - .NET](get-content-key-policy-dotnet-howto.md).

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

[Начало разработки с использованием API Служб мультимедиа версии 3 с помощью пакетов SDK и служб](developers-guide.md)
