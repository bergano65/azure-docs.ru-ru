---
title: Настройка учетных записей gMSA для служб контейнеров Azure Service Fabric | Документация Майкрософт
description: Сведения о настройке учетных записей gMSA для контейнера, запущенного в Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2019
ms.author: subramar
ms.openlocfilehash: b4aa08c10cef91b3bf2eef4c87967a74524f6500
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621790"
---
# <a name="set-up-gmsa-for-windows-containers-running-on-service-fabric"></a>Настройка учетных записей gMSA для контейнеров Windows, запущенных в Service Fabric

Чтобы настроить gMSA, необходимо разместить файл спецификаций учетных данных (`credspec`) на всех узлах в кластере. Вы можете скопировать файл на все узлы, используя расширение виртуальной машины.  Файл `credspec` должен содержать информацию об учетной записи gMSA. Дополнительные сведения о `credspec` файл, см. в разделе [Создание спецификация учетных данных](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts#create-a-credential-spec). Спецификация учетных данных и тег `Hostname` указаны в манифесте приложения. Тег `Hostname` должен соответствовать имени учетной записи gMSA, которая используется для запуска контейнера.  Тег `Hostname` позволяет контейнеру автоматически проходить проверку подлинности в других службах в домене с помощью проверки подлинности Kerberos.  В следующем фрагменте кода показан пример с указанием тегов `Hostname` и `credspec` в манифесте приложения.

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
