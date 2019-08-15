---
title: Примеры сценариев Azure PowerShell — Service Fabric | Документы Майкрософт
description: Примеры сценариев Azure PowerShell — Service Fabric
services: service-fabric
documentationcenter: service-fabric
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 73e88e692b68d73c90176a6f5b8fce06bdf8b8c7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035799"
---
# <a name="azure-powershell-samples"></a>Примеры для Azure PowerShell

Ниже приведена таблица с ссылками на примеры сценариев PowerShell, которые позволяют создавать кластеры, приложения и службы Service Fabric и управлять ими.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Создать кластер** ||
| [Создание кластера (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Создает кластер Azure Service Fabric. |
| **Управление кластером, узлами и инфраструктурой** ||
| [Добавление сертификата приложения](./scripts/service-fabric-powershell-add-application-certificate.md)| Добавляет сертификат X.509 приложения на все узлы в кластере. |
| [Обновление диапазона портов RDP на виртуальных машинах кластера](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Изменяет диапазон портов RDP на виртуальных машинах узла кластера в развернутом кластере.|
| [Обновление имени пользователя и пароля администратора для виртуальных машин узла кластера](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Обновляет имя пользователя и пароль администратора для виртуальных машин узла кластера. |
| [Открытие порта в подсистеме балансировки нагрузки](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Открывает порт приложения в Azure Load Balancer, чтобы разрешить входящий трафик через определенный порт. |
| [Создание правила входящего трафика для группы безопасности сети](./scripts/service-fabric-powershell-add-nsg-rule.md) | Создает правило входящего трафика для группы безопасности сети, чтобы разрешить передачу входящего трафика в кластер через определенный порт. |
| **Управление приложениями** ||
| [Развертывание приложения](./scripts/service-fabric-powershell-deploy-application.md)| Развертывание приложения в кластере.|
| [Обновление приложения](./scripts/service-fabric-powershell-upgrade-application.md)| Обновляет приложение.|
| [Удаление приложения](./scripts/service-fabric-powershell-remove-application.md)| Удаление приложения из кластера.|
