---
title: Настройка управляемой идентификации в существующем кластере Service Fabric
description: Вот как включить управляемую поддержку идентификаторов в существующем кластере Azure Service Fabric
ms.topic: article
ms.date: 03/11/2019
ms.custom: sfrev
ms.openlocfilehash: 73c890e960f26b8e0e3fa924d9ff6b7a4cd4a4dc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415690"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Настройка управляемой идентификации в существующем кластере Service Fabric

Чтобы использовать [управляемые идентификаторы для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) в приложениях Service Fabric, сначала включите *службу управляемых токенов* в кластере. Эта служба отвечает за аутентификацию приложений Service Fabric с использованием управляемых идентификаторов, а также за получение токенов доступа от их имени. После включения сервиса вы можете увидеть ее в Service Fabric Explorer под разделом **System** в левом стеле, работая под **названием ткани:/System/ManagedIdentityTokenService.**

> [!NOTE]
> Сервис Ная Фадрина версия 6.5.658.9590 или выше требуется для включения **службы управляемого токенов**идентификации.  
>
> Версию кластера Service Fabric можно найти на портале Azure, открыв ресурс кластера и проверив свойство **версии Service Fabric** в разделе **Essentials.**
>
> Если кластер находится в режиме **обновления руководства,** необходимо сначала обновить его до 6.5.658.9590 или позже.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Включить *службу управляемых токенов идентификации* в существующем кластере

Для включения службы управляемых токенов в существующий кластер необходимо инициировать обновление кластера с указанием двух изменений: (1) Включение службы управляемого маркера и (2) запроса перезагрузки каждого узла. Во-первых, добавьте следующий фрагмент шаблона менеджера ресурсов Azure:

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

Для того, чтобы изменения вступили в силу, необходимо также изменить политику обновления, чтобы указать принудительное перезапуск времени выполнения service Fabric на каждом узлах по мере прохождения обновления через кластер. Этот перезапуск гарантирует запуск и работу недавно включенной системы на каждом узлах. В фрагменте ниже, `forceRestart` является необходимым параметром для запуска. Для остальных параметров используйте значения, описанные ниже, или используйте существующие пользовательские значения, уже указанные для ресурса кластера. Пользовательские настройки для политики обновления ткани ('upgradeDescription') можно просмотреть с портала Azure, выбрав опцию «Обновление ткани» на ресурсе Service Fabric или resources.azure.com. Параметры по умолчанию для политики обновления ('upgradeDescription') не просматриваются с powershell или resources.azure.com. Дополнительную информацию можно узнать в [группах UpgradePolicy.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet)  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> После успешного завершения обновления, не забудьте `forceRestart` откат настройки, чтобы свести к минимуму воздействие последующих обновлений. 

## <a name="errors-and-troubleshooting"></a>Ошибки и устранение неполадок

Если развертывание завершается сбоем со следующим сообщением, это означает, что кластер не работает на достаточно высокой версии Service Fabric:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Дальнейшие действия
* [Развертывание приложения Azure Service Fabric с системой, назначенной управляемой личностью](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Развертывание приложения Azure Service Fabric с помощью управляемого удостоверения пользователя](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Использование управляемой идентификации приложения Service Fabric из сервисного кода](./how-to-managed-identity-service-fabric-app-code.md)
* [Предоставить доступ к другим ресурсам Azure](./how-to-grant-access-other-resources.md)
