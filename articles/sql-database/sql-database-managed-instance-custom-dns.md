---
title: Настраиваемый DNS-сервер управляемого экземпляра
description: В этой статье описаны параметры конфигурации пользовательской службы DNS для Управляемого экземпляра Базы данных SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 07/17/2019
ms.openlocfilehash: 3161d931c6e912c05c595db7f3c790da454dd5ed
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823357"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Настройка пользовательской службы DNS для Управляемого экземпляра Базы данных SQL Azure

Управляемый экземпляр Базы данных SQL Azure должен быть развернут в [виртуальной сети (VNet)](../virtual-network/virtual-networks-overview.md). Существует несколько сценариев (например, Database Mail, связанные серверы с другими экземплярами SQL в облачной или гибридной среде), для которых требуется, чтобы имена частных узлов разрешались из Управляемого экземпляра. В таком случае в Azure необходимо настроить пользовательскую службу DNS. 

Поскольку Управляемый экземпляр использует одну и ту же службу DNS для внутренних операций, настройте пользовательский DNS-сервер, чтобы он мог разрешать общедоступные доменные имена.

> [!IMPORTANT]
> Всегда используйте полное доменное имя (FQDN) для почтового сервера, экземпляра SQL Server и других служб, даже если они находятся в частной зоне DNS. Например, используйте `smtp.contoso.com` для почтового сервера, так как `smtp` не будет правильно разрешаться. Для создания связанного сервера или репликации, ссылающегося на виртуальные машины SQL в одной виртуальной сети, также требуется полное доменное имя и суффикс DNS по умолчанию. Пример: `SQLVM.internal.cloudapp.net`. Дополнительные сведения см. в разделе [разрешение имен, использующее собственный DNS-сервер](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

> [!IMPORTANT]
> Обновление DNS-серверов виртуальной сети не повлияет на Управляемый экземпляр немедленно. Управляемый экземпляр конфигурация DNS обновляется после истечения срока действия аренды DHCP или после упгараде платформы, в зависимости от того, что происходит раньше. **Прежде чем создавать первые Управляемый экземпляр, пользователям рекомендуется задать конфигурацию DNS виртуальной сети.**

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. в статье [Общие сведения об Управляемом экземпляре Базы данных SQL Azure (предварительная версия)](sql-database-managed-instance.md).
- См. дополнительные сведения о [создании Управляемого экземпляра](sql-database-managed-instance-get-started.md).
- См. дополнительные сведения о [настройке виртуальной сети для Управляемого экземпляра](sql-database-managed-instance-connectivity-architecture.md).
