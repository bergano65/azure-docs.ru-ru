---
title: Распространенные параметры и заголовки
description: В этой статье представлены параметры и заголовки, общие для всех операций, которые можно выполнять для ресурсов Key Vault.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 01ac02ca0e449dcac8fcd05450566fd8138acba3
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "64696691"
---
# <a name="common-parameters-and-headers"></a>Распространенные параметры и заголовки

Следующие сведения относятся ко всем операциям, которые можно выполнять для ресурсов Key Vault.

- Замена `{api-version}` на api-version в универсальном коде ресурса (URI).
- Замена `{subscription-id}` на идентификатор подписки в URI.
- Замена `{resource-group-name}` на группу ресурсов. Дополнительные сведения см. в статье об использовании групп ресурсов для управления ресурсами Azure.
- Замена `{vault-name}` на имя хранилища ключей в URI.
- Установка значения application/json в качестве заголовка Content-Type.
- Установка веб-маркера JSON, полученного из Azure Active Directory (AAD), в качестве заголовка авторизации. Дополнительные сведения см. в статье [Authentication, requests and responses](authentication-requests-and-responses.md) (Аутентификация, запросы и ответы).

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

|Имя элемента | type | Описание |
|---|---|---|
| code | string | Тип возникшей ошибки.|
| message | string | Описание причины ошибки. |



## <a name="see-also"></a>См. также
 [Azure Key Vault REST API reference](/rest/api/keyvault/) (Справочник по REST API для Azure Key Vault)
