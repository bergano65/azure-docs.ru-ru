---
title: Service Fabric Azure. Настройте существующий кластер Azure Service Fabric, чтобы включить поддержку управляемого удостоверения | Документация Майкрософт
description: В этой статье показано, как настроить существующий кластер Azure Service Fabric, чтобы включить поддержку управляемых удостоверений.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: adc21358011454c8687998dc5d257052959b933b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640733"
---
# <a name="configure-an-existing-azure-service-fabric-cluster-to-enable-managed-identity-support-preview"></a>Настройка существующего кластера Azure Service Fabric для включения поддержки управляемого удостоверения (Предварительная версия)
Чтобы получить доступ к функции управляемого удостоверения для приложений Azure Service Fabric, необходимо сначала включить **службу токенов управляемого удостоверения** в кластере. Эта служба отвечает за проверку подлинности Service Fabric приложений с помощью их управляемых удостоверений и получение маркеров доступа от их имени. После включения службы ее можно увидеть в Service Fabric Explorer в разделе **System (система** ) в левой области, работающей под именем **Fabric:/System/манажедидентититокенсервице**.

> [!NOTE]
> Для включения **службы токенов управляемого удостоверения**требуется среда выполнения Service Fabric версии 6.5.658.9590 или выше.  
> 
> Service Fabric версию кластера можно найти в портал Azure, открыв ресурс кластера и установив свойство **версия Service Fabric** в разделе **основные** сведения.
> 
> Если кластер находится в режиме обновления **вручную** , необходимо сначала обновить его до 6.5.658.9590 или более поздней версии.


## <a name="enable-the-managed-identity-token-service-in-an-existing-cluster"></a>Включение службы токенов управляемого удостоверения в существующем кластере
Чтобы включить службу маркеров управляемого удостоверения в существующем кластере, необходимо запустить обновление кластера с указанием двух изменений: Включение службы токенов управляемого удостоверения и запрос перезапуска каждого узла. Для этого добавьте следующие два фрагмента кода в шаблон Azure Resource Manager:

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

Чтобы изменения вступили в силу, необходимо также изменить политику обновления, чтобы указать принудительный перезапуск среды выполнения Service Fabric на каждом узле по мере выполнения обновления в кластере. Эта перезагрузка гарантирует, что недавно включенная системная служба будет запущена и запущена на каждом узле. В приведенном ниже `forceRestart` фрагменте является основным параметром; Используйте существующие значения для оставшейся части параметров.  

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
> После успешного завершения обновления не забудьте выполнить откат `forceRestart` параметра, чтобы свести к сведению влияние последующих обновлений. 

## <a name="errors-and-troubleshooting"></a>Ошибки и устранение неполадок

Если развертывание завершается сбоем со следующим сообщением, это означает, что кластер не работает на достаточном уровне Service Fabric версии:

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