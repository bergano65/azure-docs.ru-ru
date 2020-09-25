---
title: Обнаружение IP-адреса конечной точки управления
titleSuffix: Azure SQL Managed Instance
description: Узнайте, как получить общедоступный IP-адрес конечной точки управления Управляемый экземпляр Azure SQL и проверить его встроенную защиту брандмауэра.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: e80c1ce7357feac19104c3f88764edffb0bdbfbc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91263221"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Определение IP-адреса конечной точки управления в Azure SQL Управляемый экземпляр 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Виртуальный кластер Управляемый экземпляр Azure SQL содержит конечную точку управления, которую Azure использует для операций управления. Конечная точка управления защищена с помощью встроенного брандмауэра на уровне сети и взаимной проверкой сертификатов на уровне приложения. Вы можете определить IP-адрес конечной точки управления, но вы не можете получить доступ к этой конечной точке.

Чтобы определить IP-адрес управления, выполните [Поиск DNS](/windows-server/administration/windows-commands/nslookup) по полному доменному имени SQL управляемый экземпляр: `mi-name.zone_id.database.windows.net` . Будет возвращена запись DNS, аналогичная `trx.region-a.worker.vnet.database.windows.net` . После этого можно выполнить поиск DNS для этого полного доменного имени с удаленным именем ". vnet". Будет возвращен IP-адрес управления. 

Этот код PowerShell сделает все, если заменить на \<MI FQDN\> DNS-запись SQL управляемый экземпляр: `mi-name.zone_id.database.windows.net` :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Дополнительные сведения о Управляемый экземпляр SQL и подключении см. в статье [Архитектура подключения управляемый экземпляр SQL Azure](connectivity-architecture-overview.md).
