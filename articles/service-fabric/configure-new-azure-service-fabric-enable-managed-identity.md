---
title: Service Fabric Azure. Развертывание нового кластера Service Fabric Azure с поддержкой управляемого удостоверения | Документация Майкрософт
description: В этой статье показано, как создать новый кластер Service Fabric с включенным управляемым удостоверением.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 24fcdaf612a26109194524733e1fb9069dc664e0
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965576"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support"></a>Создание нового кластера Azure Service Fabric с поддержкой управляемого удостоверения

Чтобы получить доступ к функции управляемого удостоверения для приложений Azure Service Fabric, необходимо сначала включить службу токенов управляемого удостоверения в кластере. Эта служба отвечает за проверку подлинности Service Fabric приложений с помощью их управляемых удостоверений и получение маркеров доступа от их имени. Когда служба будет включена, ее можно увидеть в Service Fabric Explorer в разделе " **система** " в левой области, работающей под именем **Fabric:/System/манажедидентититокенсервице** рядом с другими системными службами.

> [!NOTE]
> Для включения **службы токенов управляемого удостоверения**требуется среда выполнения Service Fabric версии 6.5.658.9590 или выше.  

## <a name="enable-the-managed-identity-token-service"></a>Включение службы токенов управляемых удостоверений 
Чтобы включить службу токенов управляемого удостоверения во время создания кластера, можно использовать следующий фрагмент кода в шаблоне Azure Resource Manager:

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

## <a name="errors"></a>Ошибки

Если развертывание завершается сбоем с этим сообщением, это означает, что кластер не находится в требуемой версии Service Fabric (минимальная поддерживаемая среда выполнения — 6,5 CU2):



```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Следующие шаги
* [Развертывание приложения Service Fabric Azure с управляемым удостоверением, назначенным системой](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Развертывание приложения Service Fabric Azure с помощью управляемого удостоверения, назначенного пользователем](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Использование управляемого удостоверения Service Fabric приложения из кода службы](./how-to-managed-identity-service-fabric-app-code.md)
* [Предоставление приложению Azure Service Fabric доступа к другим ресурсам Azure](./how-to-grant-access-other-resources.md)

## <a name="related-articles"></a>Связанные статьи
* Проверка [поддержки управляемого удостоверения](./concepts-managed-identity.md) в Azure Service Fabric

* [Включение поддержки управляемого удостоверения в существующем кластере Azure Service Fabric](./configure-existing-cluster-enable-managed-identity-token-service.md)
