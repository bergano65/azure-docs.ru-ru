---
title: Проверка безопасности портов во встроенном брандмауэре
description: Узнайте, как проверить встроенную защиту брандмауэра в Azure SQL Управляемый экземпляр.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 35d0053d8c45547eaa5e89e6da15fcaf0343d6c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617304"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Проверка встроенного брандмауэра Управляемого экземпляра SQL Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Для использования [обязательных правил безопасности для входящего трафика](connectivity-architecture-overview.md#mandatory-inbound-security-rules) Azure SQL управляемый экземпляр требуется, чтобы порты управления 9000, 9003, 1438, 1440 и 1452 были открыты из **любого источника** в группе безопасности сети (NSG), защищающей SQL управляемый экземпляр. Несмотря на то, что эти порты открыты на уровне группы безопасности сети, они защищены на уровне сети встроенным брандмауэром.

## <a name="verify-firewall"></a>Проверка брандмауэра

Чтобы проверить эти порты, используйте любое средство сканирования системы безопасности для проверки этих портов. На следующем снимке экрана показано, как использовать одно из этих средств.

![Проверка встроенного брандмауэра](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о Управляемый экземпляр SQL и подключении см. в статье [Архитектура подключения управляемый экземпляр SQL Azure](connectivity-architecture-overview.md).
