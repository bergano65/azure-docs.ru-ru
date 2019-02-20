---
title: Архитектура Пакета средств разработки Azure Stack | Документация Майкрософт
description: Здесь описана архитектуры Пакета средств разработки Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 9157acc7517aea56f087a3dbff0fe7114f8b4c87
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55958811"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Архитектура Пакета средств разработки Azure Stack
Пакет средств разработки Azure Stack (ASDK) — это развертывание Azure Stack с использованием одного узла, выполняемое на одном главном компьютере. Компоненты пограничной маршрутизации устанавливаются на главном компьютере для обеспечения возможностей преобразования сетевых адресов (NAT) и VPN в Azure Stack. Инфраструктурные роли Azure Stack выполняются на физическом главном компьютере в слое Hyper-V.


## <a name="virtual-machine-roles"></a>Роли виртуальной машины
Пакет ASDK предлагает службы, которые используют следующие виртуальные машины на компьютере, где размещен пакет средств разработки:

| ИМЯ | ОПИСАНИЕ |
| ----- | ----- |
| **AzS-ACS01** | Службы хранилища Azure Stack.|
| **AzS-ADFS01** | Службы федерации Active Directory (AD FS).  |
| **AzS-CA01** | Службы центра сертификации для служб ролей Azure Stack.|
| **AzS-DC01** | Службы Active Directory, DNS и DHCP для Microsoft Azure Stack.|
| **AzS-ERCS01** | Виртуальная машина консоли аварийного восстановления. |
| **AzS-GWY01** | Службы пограничных шлюзов, включая VPN-подключения типа "сеть — сеть" для сетей клиентов.|
| **AzS-NC01** | Сетевой контроллер, который управляет сетевыми службами Azure Stack.  |
| **AzS-SLB01** | Службы мультиплексора балансировки нагрузки в Azure Stack для клиентов и служб инфраструктуры Azure Stack.  |
| **AzS-SQL01** | Внутреннее хранилище данных для ролей инфраструктуры Azure Stack.  |
| **AzS-WAS01** | Службы Azure Resource Manager и портал администрирования Azure Stack.|
| **AzS-WASP01**| Портал пользователя (клиента) Azure Stack и службы Azure Resource Manager.|
| **AzS-XRP01** | Контроллер управления инфраструктурой для Microsoft Azure Stack, включая поставщиков вычислительных и сетевых ресурсов, а также ресурсов хранения.|


## <a name="next-steps"></a>Дополнительная информация
[Дополнительные сведения об основных задачах администрирования ASDK](asdk-admin-basics.md)
