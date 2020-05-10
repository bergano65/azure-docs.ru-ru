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
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005813"
---
# <a name="common-parameters-and-headers"></a>Распространенные параметры и заголовки

Следующие сведения относятся ко всем операциям, которые можно выполнять для ресурсов Key Vault.

- Заголовок HTTP `Host` всегда должен присутствовать и должен указывать имя узла хранилища. Например, `Host: contoso.vault.azure.net`. Обратите внимание, что большинство клиентских технологий `Host` заполняют заголовок URI. Например, `GET https://contoso.vault.azure.net/secrets/mysecret{...}` задаст `Host` значение. `contoso.vault.azure.net` Это означает, что если вы обращаетесь к Key Vault с использованием `GET https://10.0.0.23/secrets/mysecret{...}`необработанного IP- `Host` адреса, например, автоматическое значение заголовка будет неправильным, и вам придется `Host` вручную убедиться, что заголовок содержит имя узла хранилища.
- Замена `{api-version}` на api-version в универсальном коде ресурса (URI).
- Замена `{subscription-id}` на идентификатор подписки в URI.
- Замена `{resource-group-name}` на группу ресурсов. Дополнительные сведения см. в статье об использовании групп ресурсов для управления ресурсами Azure.
- Замена `{vault-name}` на имя хранилища ключей в URI.
- Установка значения application/json в качестве заголовка Content-Type.
- Установка веб-маркера JSON, полученного из Azure Active Directory (AAD), в качестве заголовка авторизации. Дополнительные сведения см. в разделе [Проверка подлинности запросов Azure Resource Manager](authentication-requests-and-responses.md) .

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

|Имя элемента | Type | Описание |
|---|---|---|
| code | строка | Тип возникшей ошибки.|
| сообщение | строка | Описание причины ошибки. |



## <a name="see-also"></a>См. также
 [Azure Key Vault REST API reference](/rest/api/keyvault/) (Справочник по REST API для Azure Key Vault)
