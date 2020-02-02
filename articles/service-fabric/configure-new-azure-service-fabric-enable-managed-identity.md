---
title: Настройка поддержки управляемого удостоверения для нового кластера Service Fabric
description: Вот как можно включить поддержку управляемых удостоверений в новом кластере Azure Service Fabric
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 0e35d2192fdcdb294b349105f3f0158564cec86b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930466"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster-preview"></a>Настройка поддержки управляемого удостоверения для нового кластера Service Fabric (Предварительная версия)

Чтобы использовать [управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) в Service Fabric приложениях, сначала включите *службу токенов управляемого удостоверения* в кластере. Эта служба отвечает за проверку подлинности Service Fabric приложений с помощью их управляемых удостоверений и получение маркеров доступа от их имени. Когда служба будет включена, ее можно увидеть в Service Fabric Explorer в разделе " **система** " в левой области, работающей под именем **Fabric:/System/манажедидентититокенсервице** рядом с другими системными службами.

> [!NOTE]
> Для включения **службы токенов управляемого удостоверения**требуется среда выполнения Service Fabric версии 6.5.658.9590 или выше.  

## <a name="enable-the-managed-identity-token-service"></a>Включение службы токенов управляемых удостоверений

Чтобы включить службу токенов управляемого удостоверения во время создания кластера, добавьте следующий фрагмент кода в шаблон Azure Resource Manager кластера:

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

Если развертывание завершается сбоем с этим сообщением, это означает, что кластер не находится в требуемой версии Service Fabric (минимальная поддерживаемая среда выполнения — 6,5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Связанные статьи

* Проверка [поддержки управляемого удостоверения](./concepts-managed-identity.md) в Azure Service Fabric

* [Включение поддержки управляемого удостоверения в существующем кластере Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание приложения Service Fabric Azure с управляемым удостоверением, назначенным системой](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Развертывание приложения Service Fabric Azure с помощью управляемого удостоверения, назначенного пользователем](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Использование управляемого удостоверения Service Fabric приложения из кода службы](./how-to-managed-identity-service-fabric-app-code.md)
* [Предоставление приложению Azure Service Fabric доступа к другим ресурсам Azure](./how-to-grant-access-other-resources.md)