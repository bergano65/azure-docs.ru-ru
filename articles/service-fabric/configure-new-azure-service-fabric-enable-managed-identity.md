---
title: Развертывание нового кластера Service Fabric с управляемым удостоверением
description: В этой статье показано, как создать новый кластер Service Fabric с включенным управляемым удостоверением.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 4893fe47de78445a7dccb4f5800498b30cd6c1f2
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614865"
---
# <a name="create-a-new-azure-service-fabric-cluster-with-managed-identity-support-preview"></a>Создание нового кластера Azure Service Fabric с поддержкой управляемого удостоверения (Предварительная версия)

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