---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: c8453a2ec00a2fca107f85a23a8af1e6313a70b6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318249"
---
### <a name="sql-servers"></a>Серверы SQL Server

|  |  |
|---------|---------|
| [Проверка назначения роли администратора Azure Active Directory](../articles/governance/policy/samples/audit-no-aad-admin.md) | Выполняется проверка, назначена ли роль администратора Azure Active Directory в настройках сервера SQL. |
| [Аудит параметра обнаружения угроз на уровне сервера](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Проверяет, имеют ли политики оповещения системы безопасности для базы данных SQL указанное состояние. Задайте значение, указывающее состояние обнаружения угроз (включено или отключено).  |
| [Проверка параметров аудита SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Проверяет SQL Server в зависимости от включения параметров аудита. |
| [Аудит параметра аудита уровня SQL Server](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Проверяет, соответствуют ли параметры аудита SQL Server указанному значению. Задайте значение, указывающее, должны ли быть включены параметры аудита. |
| [Требование наличия SQL Server версии 12.0](../articles/governance/policy/samples/require-sql-12.md) | Требует серверы SQL Server версии 12.0.  |