---
title: Пользовательский DNS
titleSuffix: Azure SQL Managed Instance
description: В этом разделе описываются параметры конфигурации для пользовательского DNS с Управляемый экземпляр Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: 017698975706aa8501cd059351cf9a9d88594f77
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779906"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Настройка пользовательского DNS для Управляемый экземпляр Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Управляемый экземпляр Azure SQL необходимо развернуть в [виртуальной сети](../../virtual-network/virtual-networks-overview.md)Azure. Существует несколько сценариев (например, Database Mail, связанные серверы с другими экземплярами SQL Server в облачной или гибридной среде), для которых требуется, чтобы имена частных узлов разрешались из Управляемого экземпляра SQL. В таком случае в Azure необходимо настроить пользовательскую службу DNS. 

Так как SQL Управляемый экземпляр использует одну и ту же службу DNS для своей внутренней работы, настройте пользовательский DNS-сервер, чтобы он мог разрешать общедоступные доменные имена.

> [!IMPORTANT]
> Всегда используйте полное доменное имя (FQDN) для почтового сервера, для экземпляра SQL Server и для других служб, даже если они находятся в частной зоне DNS. Например, используйте `smtp.contoso.com` для почтового сервера, так как `smtp` не будет правильно разрешаться. Для создания связанного сервера или репликации, которая ссылается на SQL Server виртуальных машин в одной виртуальной сети, также требуется полное доменное имя и DNS-суффикс по умолчанию. Например, `SQLVM.internal.cloudapp.net`. Дополнительные сведения см. в разделе [разрешение имен, использующее собственный DNS-сервер](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Обновление DNS-серверов виртуальной сети не повлияет на Управляемый экземпляр SQL немедленно. Конфигурация DNS Управляемый экземпляр SQL обновляется по истечении срока действия аренды DHCP или после обновления платформы (в зависимости от того, что происходит раньше). **Пользователям рекомендуется задать конфигурацию DNS виртуальной сети перед созданием своего первого управляемого экземпляра.**

## <a name="next-steps"></a>Дальнейшие действия

- Общие сведения см. в статье [что такое Azure SQL управляемый экземпляр?](sql-managed-instance-paas-overview.md).
- Руководство, в котором показано, как создать управляемый экземпляр, см. в разделе [Создание управляемого экземпляра](instance-create-quickstart.md).
- Сведения о настройке виртуальной сети для управляемого экземпляра см. в разделе [Конфигурация виртуальной сети для управляемых экземпляров](connectivity-architecture-overview.md).