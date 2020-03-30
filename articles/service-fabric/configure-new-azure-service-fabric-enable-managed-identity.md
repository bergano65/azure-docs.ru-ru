---
title: Настройка управляемой идентификационной поддержки для нового кластера Service Fabric
description: Вот как включить управляемую поддержку идентификаторов в новом кластере Azure Service Fabric
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 0e35d2192fdcdb294b349105f3f0158564cec86b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76930466"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster-preview"></a>Настройка управляемой идентификационной поддержки для нового кластера Service Fabric (предварительный просмотр)

Чтобы использовать [управляемые идентификаторы для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) в приложениях Service Fabric, сначала включите *службу управляемых токенов* в кластере. Эта служба отвечает за аутентификацию приложений Service Fabric с использованием управляемых идентификаторов, а также за получение токенов доступа от их имени. После включения службы вы можете увидеть ее в Service Fabric Explorer под разделом **System** в левом стеле, работая под **названием ткани:/System/ManagedIdentityTokenService** рядом с другими системными службами.

> [!NOTE]
> Сервис Ная Фадрина версия 6.5.658.9590 или выше требуется для включения **службы управляемого токенов**идентификации.  

## <a name="enable-the-managed-identity-token-service"></a>Включить службу управляемого токенов идентификации

Чтобы включить службу управляемых токенов в момент создания кластера, добавьте следующий фрагмент в шаблон управления ресурсами кластера Azure:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>ошибки

Если развертывание не справляется с этим сообщением, это означает, что кластер не находится в требуемой версии Service Fabric (минимальное время выполнения поддерживается 6.5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Связанные статьи

* Обзор [управляемой поддержки идентификации](./concepts-managed-identity.md) в Azure Service Fabric

* [Включить управляемую идентификационную поддержку в существующем кластере Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание приложения Azure Service Fabric с системой, назначенной управляемой личностью](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Развертывание приложения Azure Service Fabric с помощью управляемого удостоверения пользователя](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Использование управляемой идентификации приложения Service Fabric из сервисного кода](./how-to-managed-identity-service-fabric-app-code.md)
* [Предоставить доступ к другим ресурсам Azure](./how-to-grant-access-other-resources.md)