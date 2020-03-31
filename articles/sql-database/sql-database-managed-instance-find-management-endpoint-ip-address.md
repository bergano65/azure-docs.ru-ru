---
title: Откройте для себя конечную точку управления управляемыми экземплярами
description: Сведения о том, как получить общедоступный IP-адрес конечной точки управления Управляемого экземпляра Базы данных SQL Azure и проверить ее встроенный брандмауэр для защиты
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 03cd89084c2bae3339311f2f684a0d5e7bac1f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825718"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Определение IP-адреса конечной точки управления

Виртуальный кластер Управляемого экземпляра Базы данных SQL Azure содержит конечную точку управления, которую корпорация Майкрософт использует для операций управления. Конечная точка управления защищена с помощью встроенного брандмауэра на уровне сети и взаимной проверкой сертификатов на уровне приложения. Вы можете определить IP-адрес конечной точки управления, но вы не можете получить доступ к этой конечной точке.

Чтобы определить IP-адрес управления, сделайте поиск DNS на управляемом экземпляре F'DN: `mi-name.zone_id.database.windows.net`. Это вернет запись DNS, `trx.region-a.worker.vnet.database.windows.net`которая похожа на. Затем вы можете сделать поиск DNS на этом F-DN с ".vnet" удалены. Это вернет IP-адрес управления. 

Эта PowerShell сделает все это за \<вас, если\> вы замените MI F-DN с DNS запись вашего управляемого экземпляра: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Дополнительные сведения об Управляемых экземплярах и подключениях см. в статье [Архитектура подключения к Управляемому экземпляру Базы данных SQL Azure](sql-database-managed-instance-connectivity-architecture.md).
