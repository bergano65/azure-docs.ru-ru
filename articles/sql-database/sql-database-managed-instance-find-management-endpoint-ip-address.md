---
title: Обнаружение конечной точки управления Управляемого экземпляра Базы данных SQL Azure | Документация Майкрософт
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
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: c5304c62b29d842f9beeadb34eba1cb53048d179
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302276"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Определение IP-адреса конечной точки управления

Виртуальный кластер Управляемого экземпляра Базы данных SQL Azure содержит конечную точку управления, которую корпорация Майкрософт использует для операций управления. Конечная точка управления защищена с помощью встроенного брандмауэра на уровне сети и взаимной проверкой сертификатов на уровне приложения. Вы можете определить IP-адрес конечной точки управления, но вы не можете получить доступ к этой конечной точке.

Чтобы определить IP-адрес управления, выполните поиск DNS в полном доменном имени управляемого `mi-name.zone_id.database.windows.net`экземпляра:. Будет возвращена запись DNS, аналогичная `trx.region-a.worker.vnet.database.windows.net`. После этого можно выполнить поиск DNS для этого полного доменного имени с удаленным именем ". vnet". Будет возвращен IP-адрес управления. 

Если вы заменили \<полное доменное имя\> MI на запись DNS управляемого экземпляра `mi-name.zone_id.database.windows.net`, это сделает это.
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Дополнительные сведения об Управляемых экземплярах и подключениях см. в статье [Архитектура подключения к Управляемому экземпляру Базы данных SQL Azure](sql-database-managed-instance-connectivity-architecture.md).
