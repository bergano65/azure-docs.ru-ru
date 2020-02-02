---
title: Настройка поддержки управляемого удостоверения в существующем кластере Service Fabric
description: Вот как можно включить поддержку управляемых удостоверений в существующем кластере Azure Service Fabric.
ms.topic: article
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: cb6e4ab00afd80cba41881e46296f7046a905919
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934951"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster-preview"></a>Настройка поддержки управляемого удостоверения в существующем кластере Service Fabric (Предварительная версия)

Чтобы использовать [управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) в Service Fabric приложениях, сначала включите *службу токенов управляемого удостоверения* в кластере. Эта служба отвечает за проверку подлинности Service Fabric приложений с помощью их управляемых удостоверений и получение маркеров доступа от их имени. После включения службы ее можно увидеть в Service Fabric Explorer в разделе **System (система** ) в левой области, работающей под именем **Fabric:/System/манажедидентититокенсервице**.

> [!NOTE]
> Для включения **службы токенов управляемого удостоверения**требуется среда выполнения Service Fabric версии 6.5.658.9590 или выше.  
>
> Service Fabric версию кластера можно найти в портал Azure, открыв ресурс кластера и установив свойство **версия Service Fabric** в разделе **основные** сведения.
>
> Если кластер находится в режиме обновления **вручную** , необходимо сначала обновить его до 6.5.658.9590 или более поздней версии.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Включение *управляемой службы токенов удостоверений* в существующем кластере

Чтобы включить службу маркеров управляемого удостоверения в существующем кластере, необходимо запустить обновление кластера с указанием двух изменений: (1) Включение службы токенов управляемого удостоверения и (2) запрос на перезапуск каждого узла. Сначала добавьте следующий фрагмент кода в шаблон Azure Resource Manager кластера:

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

Чтобы изменения вступили в силу, необходимо также изменить политику обновления, чтобы указать принудительный перезапуск среды выполнения Service Fabric на каждом узле по мере выполнения обновления в кластере. Эта перезагрузка гарантирует, что недавно включенная системная служба будет запущена и запущена на каждом узле. В следующем фрагменте кода `forceRestart` является основным параметром. Используйте существующие значения для оставшейся части параметров.  

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
> После успешного завершения обновления не забудьте выполнить откат параметра `forceRestart`, чтобы свести к сведению влияние последующих обновлений. 

## <a name="errors-and-troubleshooting"></a>Ошибки и устранение неполадок

Если развертывание завершается сбоем со следующим сообщением, это означает, что кластер не работает на достаточном уровне Service Fabric версии:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Дальнейшие действия
* [Развертывание приложения Service Fabric Azure с управляемым удостоверением, назначенным системой](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Развертывание приложения Service Fabric Azure с помощью управляемого удостоверения, назначенного пользователем](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Использование управляемого удостоверения Service Fabric приложения из кода службы](./how-to-managed-identity-service-fabric-app-code.md)
* [Предоставление приложению Azure Service Fabric доступа к другим ресурсам Azure](./how-to-grant-access-other-resources.md)