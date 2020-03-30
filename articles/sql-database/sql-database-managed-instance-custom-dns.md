---
title: Пользовательский DNS экземпляра
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
ms.openlocfilehash: 9c98176413f858d236902c332553d47f749aa3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247084"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Настройка пользовательской службы DNS для Управляемого экземпляра Базы данных SQL Azure

Управляемый экземпляр Базы данных SQL Azure должен быть развернут в [виртуальной сети (VNet)](../virtual-network/virtual-networks-overview.md). Существует несколько сценариев (например, Database Mail, связанные серверы с другими экземплярами SQL в облачной или гибридной среде), для которых требуется, чтобы имена частных узлов разрешались из Управляемого экземпляра. В таком случае в Azure необходимо настроить пользовательскую службу DNS. 

Поскольку Managed Instance использует те же DNS для своей внутренней работы, настроить пользовательский DNS-сервер так, чтобы он мог решать имена общественного достояния.

> [!IMPORTANT]
> Всегда используйте полностью квалифицированное доменное имя (ФЗДН) для почтового сервера, экземпляра сервера S'L, а также для других служб, даже если они находятся в вашей частной зоне DNS. Например, `smtp.contoso.com` используйте для `smtp` почтового сервера, потому что не будет решать правильно. Для создания связанного сервера или репликации, которая ссылается на виртуальные ввоза s-L внутри той же виртуальной сети, также требуется F-DN и суффикс DNS по умолчанию. Например, `SQLVM.internal.cloudapp.net`. Для получения дополнительной информации см. [разрешение имен, которое использует ваш собственный DNS-сервер.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)

> [!IMPORTANT]
> Обновление виртуальных сетевых DNS-серверов не повлияет сразу на управляемое экземпляр. Конфигурация Управляемой DNS экземпляра обновляется после истечения срока аренды DHCP или после обновления платформы, в зависимости от того, что произойдет в первую очередь. **Пользователям рекомендуется настроить конфигурацию DNS виртуальной сети перед созданием своего первого Управляемого instanceа.**

## <a name="next-steps"></a>Дальнейшие действия

- Для обзора [см.](sql-database-managed-instance.md)
- См. дополнительные сведения о [создании Управляемого экземпляра](sql-database-managed-instance-get-started.md).
- См. дополнительные сведения о [настройке виртуальной сети для Управляемого экземпляра](sql-database-managed-instance-connectivity-architecture.md).
