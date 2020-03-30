---
title: Настройка gMSA для контейнерных услуг Azure Service Fabric
description: Узнайте сейчас о настройке группы управляемых учетных записей служб (gMSA) для контейнера, работаемого в Azure Service Fabric.
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9873e2d7672412b0e1e22c6c2a774cf629fd728a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639213"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Настройка учетных записей gMSA для контейнеров Windows, запущенных в Service Fabric

Чтобы настроить gMSA, необходимо разместить файл спецификаций учетных данных (`credspec`) на всех узлах в кластере. Вы можете скопировать файл на все узлы, используя расширение виртуальной машины.  Файл `credspec` должен содержать информацию об учетной записи gMSA. Для получения дополнительной `credspec` информации о файле см. [Create a Credential Spec](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec) Спецификация учетных `Hostname` данных и тег указаны в манифесте приложения. Тег `Hostname` должен соответствовать имени учетной записи gMSA, которая используется для запуска контейнера.  Тег `Hostname` позволяет контейнеру автоматически проходить проверку подлинности в других службах в домене с помощью проверки подлинности Kerberos.  В следующем фрагменте кода показан пример с указанием тегов `Hostname` и `credspec` в манифесте приложения.

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
Теперь ознакомьтесь со следующими статьями:

* [Развертывание контейнера Windows в Service Fabric на платформе Windows Server 2016](service-fabric-get-started-containers.md)
* [Развертывание контейнера Docker в Service Fabric на платформе Linux](service-fabric-get-started-containers-linux.md)
