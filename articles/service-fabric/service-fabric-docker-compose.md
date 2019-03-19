---
title: Развертывание Docker Compose для Azure Service Fabric (предварительная версия)
description: Azure Service Fabric принимает формат Docker Compose для упрощения управления существующими контейнерами с помощью Service Fabric. Сейчас эта возможность доступна в предварительной версии.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: aljo, subramar
ms.openlocfilehash: 6e03ada8c1a3aaa86ffcb11799dbe2b61852c987
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806680"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Поддержка развертывания Docker Compose в Azure Service Fabric (предварительная версия)

Средство Docker использует файл [docker-compose.yml](https://docs.docker.com/compose) для определения приложений с несколькими контейнерами. Чтобы пользователи могли ознакомиться с инструментом Docker, который позволяет организовать существующие приложения-контейнеры в Azure Service Fabric, мы изначально добавили в платформу предварительную версию поддержки развертывания Docker Compose. Платформа Service Fabric может принять файлы `docker-compose.yml` версии 3 и более поздней. 

Так как эта поддержка доступна в режиме предварительной версии, поддерживается только подмножество директив Compose. Например, обновления приложений не поддерживаются. Но вы всегда можете вместо обновления приложений удалить и развернуть их.

Для использования этой предварительной версии создайте кластер со средой выполнения Service Fabric версии 5.7 (или более новой) с помощью портала Azure и соответствующего пакета SDK. 

> [!NOTE]
> Эта функция доступна в режиме предварительной версии и не поддерживается в рабочей среде.
> Приведенные ниже примеры основаны на среде выполнения версии 6.0 и пакете SDK версии 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Развертывание файла Docker Compose в Service Fabric

Приведенные ниже команды создают приложение Service Fabric (`fabric:/TestContainerApp`), которое можно отслеживать и которым можно управлять, как и любым приложением Service Fabric. Указанное имя приложения можно использовать для запросов работоспособности.
Service Fabric распознает DeploymentName как идентификатор развертывания Compose.

### <a name="use-powershell"></a>Использование PowerShell

Чтобы создать развертывание Compose для Service Fabric из файла docker-compose.yml, выполните следующую команду в PowerShell.

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` и `RegistryPassword` задают имя пользователя и пароль реестра контейнеров. После завершения развертывания можно проверить его состояние с помощью следующей команды.

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Чтобы удалить развертывание Compose с помощью PowerShell, используйте следующую команду.

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Чтобы запустить обновление развертывания Compose с помощью PowerShell, используйте следующую команду.

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Чтобы выполнить откат обновления развертывания Compose с помощью PowerShell, используйте такую команду.

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

После согласия на обновление ход его выполнения можно отслеживать с помощью следующей команды.

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Использование интерфейса командной строки Service Fabric (sfctl)

Можно также выполнить следующую команду интерфейса командной строки Service Fabric.

```azurecli
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

После создания развертывания можно проверить его состояние с помощью следующей команды.

```azurecli
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Чтобы удалить развертывание Compose, выполните следующую команду.

```azurecli
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Чтобы начать обновление развертывания Compose, выполните следующую команду.

```azurecli
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Чтобы выполнить откат обновления развертывания Compose, используйте такую команду.

```azurecli
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

После согласия на обновление ход его выполнения можно отслеживать с помощью следующей команды.

```azurecli
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Поддерживаемые директивы Compose

В этой предварительной версии поддерживается подмножество параметров конфигурации из формата Compose версии 3. Поддерживаются следующие примитивы:

* Services > Deploy > Replicas
* Services > Deploy > Placement > Constraints
* Services > Deploy > Resources > Limits
    * -cpu-shares
    * -memory
    * -memory-swap
* Services > Commands
* Services > Environment
* Services > Ports
* Services > Image
* Services > Isolation (только для Windows)
* Services > Logging > Driver
* Services > Logging > Driver > Options
* Volume & Deploy > Volume

Настройте кластер для принудительного применения ограничения ресурсов, как описано в статье об [управлении ресурсами Service Fabric](service-fabric-resource-governance.md). Другие директивы Docker Compose не поддерживаются в этой предварительной версии.

### <a name="ports-section"></a>Раздел портов

Укажите протокол http или https в разделе "Порты", который будет использовать прослушиватель службы Service Fabric. Это обеспечит правильную публикацию протокола конечной точки с помощью службы именования, позволяя обратному прокси-серверу пересылать запросы:
* Для маршрутизации в незащищенные службы Compose в Service Fabric укажите **/http**. Например, **80:80/http**.
* Для маршрутизации в защищенные службы Compose в Service Fabric укажите **/https**. Например, **443:443/https**.

> [!NOTE]
> Синтаксис раздела портов /http и /https относится к Service Fabric. Он необходим для регистрации правильного URL-адреса прослушивателя Service Fabric.  Если синтаксис файла Docker Compose проверен программно, это может привести к ошибке проверки.

## <a name="servicednsname-computation"></a>Вычисление ServiceDnsName

Если в файле Compose указано полное доменное имя службы (то есть оно содержит точку [.]), платформа Service Fabric зарегистрирует DNS-имя `<ServiceName>` с точкой. В противном случае каждый сегмент пути в имени приложения становится меткой домена в DNS-имени службы. При этом первый сегмент пути становится меткой домена верхнего уровня.

Например, если в качестве имени приложения указано `fabric:/SampleApp/MyComposeApp`, то `<ServiceName>.MyComposeApp.SampleApp` будет зарегистрированным DNS-именем.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Сравнение развертывания Compose (определение экземпляра) и модели приложения Service Fabric (определение типа)

Файл docker-compose.yml описывает набор контейнеров, доступных для развертывания, в том числе их свойства и конфигурации.
Например, файл может содержать переменные среды и порты. В файле docker-compose.yml также указываются параметры развертывания, такие как ограничения размещения, ограничения ресурсов и DNS-имена.

[Модель приложения Service Fabric](service-fabric-application-model.md) использует типы служб и типы приложений, в которых можно создать несколько экземпляров приложений того же типа. Например, можно создать по одному экземпляру приложения на клиента. Эта модель на основе типа поддерживает несколько версий одного типа приложений, зарегистрированных в среде выполнения.

Например, для клиента A может быть создан экземпляр приложения версии 1.0 типа AppTypeA, а для клиента Б может быть создан другой экземпляр приложения того же типа и версии. Типы приложений определяются в манифестах приложений, а параметры развертывания и имя приложения указываются во время создания приложения.

Хотя эта модель обеспечивает гибкость, мы также планируем поддерживать более простую модель развертывания на основе экземпляра, в которой типы будут извлекаться из файла манифеста. В этой модели каждое приложение получает собственный независимый манифест. Мы предварительно рассматриваем это действие, добавляя поддержку docker-compose.yml, в котором используется формат развертывания на основе экземпляра.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей [Моделирование приложения в структуре службы](service-fabric-application-model.md).
* [Azure Service Fabric command line](service-fabric-cli.md) (Интерфейс командной строки Azure Service Fabric)
