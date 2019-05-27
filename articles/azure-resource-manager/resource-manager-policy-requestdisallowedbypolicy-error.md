---
title: Ошибка RequestDisallowedByPolicy с политикой ресурсов Azure | Документация Майкрософт
description: Описывает причину ошибки RequestDisallowedByPolicy.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c160fe39b02d8adf6c12e3736307cf7f9688b0c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66128454"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Ошибка RequestDisallowedByPolicy с политикой ресурсов Azure

В этой статье описывается причина ошибки RequestDisallowedByPolicy, а также предлагается решение для ее устранения.

## <a name="symptom"></a>Симптом

Во время развертывания может появиться ошибка **RequestDisallowedByPolicy**, которая блокирует создание ресурсов. В следующем примере показана эта ошибка:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Устранение неполадок

Чтобы получить информацию о том, какая политика блокирует развертывание, используйте один из следующих методов.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

В PowerShell укажите идентификатор политики как параметр `Id`, чтобы получить сведения о политике, заблокировавшей развертывание.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Инфраструктура CLI Azure

В Azure CLI укажите имя определения политики.

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Решение

Для обеспечения безопасности или соответствия администраторы подписки могут назначить политики, ограничивающие развертывание ресурсов. Например, для подписки может быть установлена политика, которая запрещает создание общедоступных IP-адресов, групп безопасности сети, определяемых пользователем маршрутов или таблиц маршрутов. Сообщение об ошибке в разделе **Симптомы** содержит имя политики.
Чтобы устранить эту проблему, просмотрите политики ресурсов и определите способ развертывания ресурсов, который соответствует этим политикам.

Дополнительные сведения см. в следующих статьях:

- [Что такое служба "Политика Azure"?](../governance/policy/overview.md)
- [Создание политик и управление ими для обеспечения соответствия](../governance/policy/tutorials/create-and-manage.md)
