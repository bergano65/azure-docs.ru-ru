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
ms.openlocfilehash: b7eb9ecd6b94aad263346ad6b5c45b694e0bd46f
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359770"
---
# <a name="determine-the-management-endpoint-ip-address"></a>Определение IP-адреса конечной точки управления

Виртуальный кластер Управляемого экземпляра Базы данных SQL Azure содержит конечную точку управления, которую корпорация Майкрософт использует для операций управления. Конечная точка управления защищена с помощью встроенного брандмауэра на уровне сети и взаимной проверкой сертификатов на уровне приложения. Вы можете определить IP-адрес конечной точки управления, но вы не можете получить доступ к этой конечной точке.

## <a name="determine-ip-address"></a>Определение IP-адреса

Предположим, что узел Управляемого экземпляра — `mi-demo.xxxxxx.database.windows.net`. Запустите `nslookup` с использованием имени узла.

![Разрешение имени внутреннего узла](./media/sql-database-managed-instance-management-endpoint/01_find_internal_host.png)

Теперь снова выполните команду `nslookup` для выделенного имени, удалив сегмент `.vnet.`. При выполнении этой команды вы получите общедоступный IP-адрес.

![Разрешение общедоступного IP-адреса](./media/sql-database-managed-instance-management-endpoint/02_find_public_ip.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Управляемых экземплярах и подключениях см. в статье [Архитектура подключения к Управляемому экземпляру Базы данных SQL Azure](sql-database-managed-instance-connectivity-architecture.md).
