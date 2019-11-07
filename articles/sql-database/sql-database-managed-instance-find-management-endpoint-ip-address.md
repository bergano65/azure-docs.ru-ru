---
title: Обнаружение конечной точки управления Управляемый экземпляр Базы данных SQL Azure
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
ms.openlocfilehash: 0cc3ca3a2b8130e52d8c7fc670c0ee7997e4aa47
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73688027"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Определение IP-адреса конечной точки управления

Виртуальный кластер Управляемого экземпляра Базы данных SQL Azure содержит конечную точку управления, которую корпорация Майкрософт использует для операций управления. Конечная точка управления защищена с помощью встроенного брандмауэра на уровне сети и взаимной проверкой сертификатов на уровне приложения. Вы можете определить IP-адрес конечной точки управления, но вы не можете получить доступ к этой конечной точке.

Чтобы определить IP-адрес управления, выполните поиск DNS в полном доменном имени управляемого экземпляра: `mi-name.zone_id.database.windows.net`. Будет возвращена запись DNS, такая как `trx.region-a.worker.vnet.database.windows.net`. После этого можно выполнить поиск DNS для этого полного доменного имени с удаленным именем ". vnet". Будет возвращен IP-адрес управления. 

При замене полного доменного имени \<MI\> с записью DNS управляемого экземпляра PowerShell сделает это все самостоятельно: `mi-name.zone_id.database.windows.net`:
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Дополнительные сведения об Управляемых экземплярах и подключениях см. в статье [Архитектура подключения к Управляемому экземпляру Базы данных SQL Azure](sql-database-managed-instance-connectivity-architecture.md).
