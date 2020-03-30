---
title: Распространенные параметры и заголовки
description: В этой статье представлены параметры и заголовки, общие для всех операций, которые можно выполнять для ресурсов Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d0ada9c1e6b45b1be17b15b67f67fc64fc266203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197595"
---
# <a name="common-parameters-and-headers"></a>Распространенные параметры и заголовки

Следующие сведения относятся ко всем операциям, которые можно выполнять для ресурсов Key Vault.

- Замена `{api-version}` на api-version в универсальном коде ресурса (URI).
- Замена `{subscription-id}` на идентификатор подписки в URI.
- Замена `{resource-group-name}` на группу ресурсов. Дополнительные сведения см. в статье об использовании групп ресурсов для управления ресурсами Azure.
- Замена `{vault-name}` на имя хранилища ключей в URI.
- Установка значения application/json в качестве заголовка Content-Type.
- Установка веб-маркера JSON, полученного из Azure Active Directory (AAD), в качестве заголовка авторизации. Для получения дополнительной информации смотрите запросы [менеджера ресурсов Authenticating Azure.](authentication-requests-and-responses.md)

## <a name="common-error-response"></a>Распространенное сообщение об ошибке
Служба будет использовать коды состояния HTTP для указания успеха или сбоя. Кроме того, ошибки содержат ответ в следующем формате:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Имя элемента | Тип | Описание |
|---|---|---|
| код | строка | Тип возникшей ошибки.|
| message | строка | Описание причины ошибки. |



## <a name="see-also"></a>См. также
 [Azure Key Vault REST API reference](/rest/api/keyvault/) (Справочник по REST API для Azure Key Vault)
