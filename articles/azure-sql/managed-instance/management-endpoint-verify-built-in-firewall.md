---
title: Проверка безопасности портов во встроенном брандмауэре
description: Узнайте, как проверить встроенную защиту брандмауэра в Azure SQL Управляемый экземпляр.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
ms.date: 12/04/2018
ms.openlocfilehash: 1d2fffabba3615394bdf96ed487177bf21f3ecec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708711"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Проверка встроенного брандмауэра Azure SQL Управляемый экземпляр
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Для использования [обязательных правил безопасности для входящего трафика](connectivity-architecture-overview.md#mandatory-inbound-security-rules) Azure SQL управляемый экземпляр требуется, чтобы порты управления 9000, 9003, 1438, 1440 и 1452 были открыты из **любого источника** в группе безопасности сети (NSG), защищающей SQL управляемый экземпляр. Несмотря на то, что эти порты открыты на уровне группы безопасности сети, они защищены на уровне сети встроенным брандмауэром.

## <a name="verify-firewall"></a>Проверка брандмауэра

Чтобы проверить эти порты, используйте любое средство сканирования системы безопасности для проверки этих портов. На следующем снимке экрана показано, как использовать одно из этих средств.

![Проверка встроенного брандмауэра](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о Управляемый экземпляр SQL и подключении см. в статье [Архитектура подключения управляемый экземпляр SQL Azure](connectivity-architecture-overview.md).
