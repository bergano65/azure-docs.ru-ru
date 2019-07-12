---
title: Управление виртуальных машин SQL Server в Azure с помощью портала Azure | Документация Майкрософт
description: Узнайте, как доступ к ресурсу виртуальной машины SQL на портале Azure для виртуальной Машины SQL Server, размещенных в Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 59a85e855c9fab9f2a3437c83c867b8076f55049
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607213"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>Управление виртуальных машин SQL Server в Azure с помощью портала Azure

Нет новой точкой доступа для управления виртуальной Машине SQL Server в Azure с помощью [портала Azure](https://portal.azure.com). 

**Виртуальные машины SQL** ресурсов теперь — это служба независимых управления, которая позволяет одновременно просмотреть все виртуальные машины SQL Server и изменить параметры, предназначенной для SQL Server: 

![Ресурс виртуальной машины SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Примечания

- **Виртуальные машины SQL** ресурсов рекомендуется использовать для просмотра и управления виртуальными машинами SQL Server. Тем не менее, в настоящее время **виртуальные машины SQL** ресурс не поддерживает управление [прекращение поддержки (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) виртуальных машин SQL Server. Чтобы управлять параметрами для виртуальных машин EOS SQL Server, использовать устаревший [вкладка "Конфигурация SQL Server"](#access-sql-server-configuration-tab) вместо этого. 
- **Виртуальные машины SQL** ресурс доступен только для виртуальных машин SQL Server, имеют [зарегистрирована в поставщике ресурсов виртуальной Машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-sql-virtual-machine-resource"></a>Ресурс виртуальной машины для доступа к SQL
Чтобы получить доступ к ресурсу виртуальных машин SQL, сделайте следующее:

1. Откройте [портал Azure](https://portal.azure.com). 
1. Выбор пункта **Все службы**. 
1. Тип `SQL virtual machines` в поле поиска.
1. (Необязательно): Выберите значок звездочки рядом с полем **виртуальные машины SQL** Чтобы добавить этот пункт меню "Избранное". 
1. Выберите **виртуальные машины SQL**. 

   ![Поиск виртуальных Машин SQL виртуальных машин во всех службах](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Появится список всех виртуальных машин SQL Server доступны в рамках подписки. Выберите тот, который вы хотите управлять запустить **виртуальные машины SQL** ресурсов. Используйте поле поиска, если виртуальной Машине SQL Server не очевидны. 

![Все доступные виртуальные машины SQL](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

При выборе виртуальной Машины SQL Server откроется **виртуальные машины SQL** ресурсов: 


![Ресурс виртуальной машины SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > **Виртуальные машины SQL** ресурс используется для специальных параметров SQL Server. Выберите имя виртуальной машины в **виртуальной машины** поля для перехода к параметрам, которые относятся к виртуальной Машине, но не является монопольной особенностью SQL Server. 

## <a name="access-sql-server-configuration-tab"></a>Вкладка конфигурации доступа к SQL Server
На вкладке конфигурации SQL Server является устаревшим. В настоящее время он является единственным способом для управления [прекращение поддержки (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) виртуальных машин SQL Server и виртуальных машин SQL Server, которые не были [зарегистрирована в поставщике ресурсов виртуальной Машины SQL](virtual-machines-windows-sql-register-with-resource-provider.md).

Для доступа к вкладке устаревшие настройки SQL server, необходимо перейти к **виртуальных машин** ресурсов. Для этого выполните следующее.

1. Откройте [портал Azure](https://portal.azure.com). 
1. Выбор пункта **Все службы**. 
1. Тип `virtual machines` в поле поиска.
1. (Необязательно): Выберите значок звездочки рядом с полем **виртуальных машин** Чтобы добавить этот пункт меню "Избранное". 
1. Выберите **Виртуальные машины**. 

   ![Поиск виртуальных машин](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Появится список всех виртуальных машин в подписке. Выберите тот, который вы хотите управлять запустить **виртуальной машины** ресурсов. Используйте поле поиска, если виртуальной Машине SQL Server не очевидны. 
1. Выберите **конфигурации SQL Server** в **параметры** области для управления сервером SQL. 

![Конфигурация SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения см. в следующих статьях: 

* [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Вопросы и ответы по SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-faq.md).
* [Руководство по выбору ценовой категории для виртуальных машин SQL Server в Azure](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Заметки о выпуске SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-release-notes.md).


