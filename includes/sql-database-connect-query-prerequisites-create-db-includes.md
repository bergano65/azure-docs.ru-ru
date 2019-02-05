---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198067"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- [База данных SQL Azure, размещенная на логическом сервере](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) или [Управляемый экземпляр](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index). Создать базу данных можно одним из следующих способов:

| Логический сервер | Управляемый экземпляр |
| --- | --- |
| [Портал](../articles/sql-database/sql-database-get-started-portal.md) | [Портал](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](../articles/sql-database/sql-database-get-started-cli.md) | |
| [PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **Только логический сервер**. Это правило настройки брандмауэра на уровне сервера, которое позволяет подключаться к логическому серверу. Подробные сведения см. в статье [Краткое руководство. Создание правила брандмауэра на уровне сервера для базы данных SQL с помощью портала Azure](../articles/sql-database/sql-database-get-started-portal-firewall.md).
- **Только Управляемый экземпляр**. Это настроенное на компьютере исходящее подключение для доступа к Управляемому экземпляру. Можно использовать приведенные ниже параметры.
  - [Виртуальная машина Azure](../articles/sql-database/sql-database-managed-instance-configure-vm.md) в той же виртуальной сети Azure, что и Управляемый экземпляр, с доступом к экземпляру.
  - [Подключение "точка — сеть"](../articles/sql-database/sql-database-managed-instance-configure-p2s.md) на вашем компьютере. Это позволит подключить компьютер к виртуальной сети, в которой размещен Управляемый экземпляр, а также использовать такой экземпляр как любой другой  экземпляр SQL Server в сети.
