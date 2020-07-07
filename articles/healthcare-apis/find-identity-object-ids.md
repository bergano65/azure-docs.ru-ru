---
title: Поиск идентификаторов объектов удостоверений для проверки подлинности с помощью API Azure для FHIR
description: В этой статье объясняется, как определить идентификаторы объектов удостоверений, необходимые для настройки проверки подлинности для Azure API для FHIR.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 88c0349ce220229920a39ba4cb6640f3d4dde93b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871204"
---
# <a name="find-identity-object-ids-for-authentication-configuration"></a>Поиск идентификаторов объектов удостоверений для конфигурации проверки подлинности

В этой статье вы узнаете, как найти идентификаторы объектов удостоверений, необходимые при настройке API Azure для FHIR, чтобы [использовать внешний или вторичный клиент Active Directory](configure-local-rbac.md) для плоскости данных.

## <a name="find-user-object-id"></a>Найти идентификатор объекта пользователя

Если у вас есть пользователь с именем пользователя `myuser@consoso.com` , вы можете выбрать его `ObjectId` с помощью следующей команды PowerShell:

```azurepowershell-interactive
$(Get-AzureADUser -Filter "UserPrincipalName eq 'myuser@consoso.com'").ObjectId
```

также можно использовать Azure CLI:

```azurecli-interactive
az ad user show --upn-or-object-id myuser@consoso.com | jq -r .objectId
```

## <a name="find-service-principal-object-id"></a>Поиск идентификатора объекта субъекта-службы

Предположим, вы зарегистрировали [клиентское приложение службы](register-service-azure-ad-client-app.md) и хотите разрешить этому клиенту службы доступ к API Azure для FHIR. идентификатор объекта для субъекта-службы клиента можно найти с помощью следующей команды PowerShell:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "AppId eq 'XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'").ObjectId
```

где `XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX` — идентификатор клиентского приложения службы. Кроме того, можно использовать `DisplayName` клиент службы:

```azurepowershell-interactive
$(Get-AzureADServicePrincipal -Filter "DisplayName eq 'testapp'").ObjectId
```

При использовании Azure CLI можно использовать:

```azurecli-interactive
az ad sp show --id XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | jq -r .objectId
```

## <a name="find-a-security-group-object-id"></a>Поиск идентификатора объекта группы безопасности

Если вы хотите указать идентификатор объекта группы безопасности, можно использовать следующую команду PowerShell:

```azurepowershell-interactive
$(Get-AzureADGroup -Filter "DisplayName eq 'mygroup'").ObjectId
```
Где `mygroup` — имя интересующей вас группы.

При использовании Azure CLI можно использовать:

```azurecli-interactive
az ad group show --group "mygroup" | jq -r .objectId
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как найти идентификаторы объектов удостоверений, необходимые для настройки API Azure для FHIR на использование внешнего или дополнительного клиента Azure Active Directory. Далее прочитайте об использовании идентификаторов объектов для настройки локальных параметров RBAC.
 
>[!div class="nextstepaction"]
>[Настройка локальных параметров RBAC](configure-local-rbac.md)