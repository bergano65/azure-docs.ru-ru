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
ms.openlocfilehash: dabadf3a4175947f09ba20b0e644f1d2b485ee38
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73591323"
---
### <a name="sql-servers"></a>Серверы SQL Server

|  |  |
|---------|---------|
| [Проверка назначения роли администратора Azure Active Directory](../articles/governance/policy/samples/audit-no-aad-admin.md) | Выполняется проверка, назначена ли роль администратора Azure Active Directory в настройках сервера SQL. |
| [Аудит параметра обнаружения угроз на уровне сервера](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Проверяет, имеют ли политики оповещения системы безопасности для базы данных SQL указанное состояние. Задайте значение, указывающее состояние обнаружения угроз (включено или отключено).  |
| [Проверка параметров аудита SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Проверяет SQL Server в зависимости от включения параметров аудита. |
| [Аудит параметра аудита уровня SQL Server](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Проверяет, соответствуют ли параметры аудита SQL Server указанному значению. Задайте значение, указывающее, должны ли быть включены параметры аудита. |