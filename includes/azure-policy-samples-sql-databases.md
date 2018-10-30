---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 314f29a1135e355597e890b72ef3c0b7372194e6
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226842"
---
### <a name="sql-databases"></a>Базы данных SQL

|  |  |
|---------|---------|
| [Допустимые номера SKU базы данных SQL](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Требует, чтобы базы данных SQL использовали утвержденные номера SKU. Вы можете указать массив допустимых идентификаторов или названий SKU. |
| [Аудит параметра обнаружения угроз на уровне базы данных](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | Проверяет, имеют ли политики оповещения системы безопасности для базы данных SQL указанное состояние. Задайте значение, указывающее состояние обнаружения угроз (включено или отключено).  |
| [Проверка шифрования базы данных SQL](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Проверяет, включено ли прозрачное шифрование данных для базы данных SQL. |
| [Аудит параметров аудита уровня базы данных SQL](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Проверяет, соответствуют ли заданной настройке параметры аудита базы данных SQL. Задайте значение, указывающее, должны ли быть включены параметры аудита.  |