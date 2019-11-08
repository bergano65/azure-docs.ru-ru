---
title: Обнаружение встроенного брандмауэра управляемого экземпляра
description: Узнайте, как проверить защиту встроенного брандмауэра в Управляемом экземпляре Базы данных SQL.
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
ms.openlocfilehash: 555ef56aafa37a1e1d384f945b04f9237adc5f7d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821800"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Проверка встроенного брандмауэра Управляемого экземпляра

[Обязательные правила безопасности входящего трафика](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) Управляемого экземпляра требуют, чтобы порты управления 9000, 9003, 1438, 1440, 1452 были открыты из **любого источника** в группе безопасности сети (NSG), защищающей Управляемый экземпляр. Несмотря на то, что эти порты открыты на уровне группы безопасности сети, они защищены на уровне сети встроенным брандмауэром.

## <a name="verify-firewall"></a>Проверка брандмауэра

Чтобы проверить эти порты, используйте любое средство сканирования системы безопасности для проверки этих портов. На следующем снимке экрана показано, как использовать одно из этих средств.

![Проверка встроенного брандмауэра](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Управляемых экземплярах и подключениях см. в статье [Архитектура подключения к Управляемому экземпляру Базы данных SQL Azure](sql-database-managed-instance-connectivity-architecture.md).