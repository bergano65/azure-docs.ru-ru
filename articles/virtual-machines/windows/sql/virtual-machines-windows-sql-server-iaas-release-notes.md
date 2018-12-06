---
title: Заметки о выпуске SQL Server на виртуальных машинах в Azure | Документация Майкрософт
description: Узнайте о новых функциях и улучшения SQL Server на виртуальных машинах Azure
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: 44aee447c7f935cd67ca55902c53c5f6f9eb6fda
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52577084"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Заметки о выпуске SQL Server на виртуальных машинах Azure

Azure позволяет развертывать виртуальные машины со встроенным образом SQL Server. В этой статье перечислены новые функции и усовершенствования, доступные в последней версии SQL Server при развертывании на виртуальной машине Azure. 


## <a name="november-2018"></a>Ноябрь 2018 г.
- **Новый поставщик ресурсов SQL**: новый поставщик ресурсов для виртуальных машин SQL, обеспечивающий более удобное управление вашей виртуальной машиной. См. дополнительные сведения о [регистрации предыдущих версий SQL виртуальных машин с использованием нового поставщика ресурсов](virtual-machines-windows-sql-ahb.md#register-legacy-sql-vm-with-new-resource-provider).
- **Переключение модели лицензирования**: теперь вы можете переключаться между моделью оплаты за использование и моделью BYOL для виртуальной машины SQL с помощью Azure CLI или PowerShell. См. дополнительные сведения об [изменении модели лицензирования для виртуальной машины SQL в Azure](virtual-machines-windows-sql-ahb.md).



## <a name="additional-resources"></a>Дополнительные ресурсы

**Для виртуальных машин Windows**:

* [Приступая к работе с SQL Server на виртуальных машинах Azure (Windows)](virtual-machines-windows-sql-server-iaas-overview.md)
* [Подготовка виртуальной машины SQL Server на базе Windows](virtual-machines-windows-portal-sql-server-provision.md).
* [Миграция базы данных в SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md)
* [Высокий уровень доступности и аварийное восстановление для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Рекомендации по оптимизации производительности SQL Server в виртуальных машинах Azure](virtual-machines-windows-sql-performance.md)
* [Шаблоны приложений и стратегии разработки для SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Для виртуальных машин Linux**:

* [Обзор SQL Server на виртуальных машинах Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md).
* [Подготовка виртуальной машины SQL Server на базе Linux на портале Azure](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Frequently asked questions for SQL Server on Linux Azure Virtual Machines](../../linux/sql/sql-server-linux-faq.md) (Часто задаваемые вопросы об SQL Server на виртуальных машинах Azure (Linux))
* [Документация по SQL Server в Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
