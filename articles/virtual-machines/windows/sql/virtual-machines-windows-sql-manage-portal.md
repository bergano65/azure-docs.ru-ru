---
title: Управляйте вмМИ в Azure с помощью портала Azure (ru) Документы Майкрософт
description: Узнайте, как получить доступ к ресурсу виртуальной машины S'L на портале Azure для VM-сервера, размещенного на Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 244ad7d079fd7baf25f8079557576c42d25ca785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243215"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Управление ВМ сервера S'L в Azure с помощью портала Azure

На [портале Azure](https://portal.azure.com)ресурс **виртуальных машин S'L** является независимой службой управления. Вы можете использовать его для просмотра всех ваших VMs-серверов s'L одновременно и изменения настроек, посвященных серверу S'L: 

![Ресурс виртуальных машин СЗЛ](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Remarks

- Мы рекомендуем использовать ресурс **виртуальных машин S'L** для просмотра и управления ввоёнными вибристыми серверами s'L в Azure. Но в настоящее время ресурс **виртуальных машин S'L** не поддерживает управление vMs-серверами с [конечным обеспечением.](virtual-machines-windows-sql-server-2008-eos-extend-support.md) Для управления настройками для вашего vMs-сервера с конечным обеспечением s'L Server вместо этого используйте амортизированную [вкладку конфигурации сервера S'L.](#access-the-sql-server-configuration-tab) 
- Ресурс **виртуальных машин S'L** доступен только для виртуальных вмс,S L Server, [зарегистрированных в поставщике ресурсов S'L VM.](virtual-machines-windows-sql-register-with-resource-provider.md) 


## <a name="access-the-sql-virtual-machines-resource"></a>Доступ к ресурсу виртуальных машин S'L
Чтобы получить доступ к ресурсу **виртуальных машин S'L,** сделайте следующее:

1. Откройте [портал Azure](https://portal.azure.com). 
1. Выберите **все службы**. 
1. Введите **виртуальные машины s'L** в поле поиска.
1. (Необязательно): Выберите звезду рядом с **виртуальными машинами S'L,** чтобы добавить эту опцию в меню **Избранное.** 
1. Выберите **виртуальные машины s'L.** 

   ![Найдите виртуальные машины s'L Server во всех службах](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. На портале перечислены все вмМИ-сервера S'L, доступные в рамках подписки. Выберите тот, который вы хотите управлять, чтобы открыть ресурс **виртуальных машин S'L.** Используйте поле поиска, если ваш VM сервера S'L не отображается. 

   ![Все доступные VMs-серверы S'L](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

   При выборе сервера VM s'L открывается ресурс **виртуальных машин S'L:** 


   ![Ресурс виртуальных машин СЗЛ](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

> [!TIP]
> Ресурс **виртуальных машин S'L** предназначен для выделенных настроек сервера S'L. Выберите имя VM в **виртуальном автоматном поле,** чтобы открыть настройки, которые специфичны для VM, но не только для сервера S'L. 

## <a name="access-the-sql-server-configuration-tab"></a>Доступ к вкладке конфигурации сервера S'L
Вкладка **конфигурации сервера S'L** была универслена. В настоящее время это единственный способ управления vMs-сервером с [конечным уровнем поддержки,](virtual-machines-windows-sql-server-2008-eos-extend-support.md) а также vMs-сервером S'L Server, которые не были зарегистрированы в [поставщике ресурсов S'L VM.](virtual-machines-windows-sql-register-with-resource-provider.md)

Чтобы получить доступ к заблокированной **вкладке конфигурации сервера S'L,** перейдите на ресурс **Виртуальных машин.** Выполните указанные ниже действия.

1. Откройте [портал Azure](https://portal.azure.com). 
1. Выберите **все службы**. 
1. Введите **виртуальные машины** в поле поиска.
1. (Необязательно): Выберите звезду рядом с **виртуальными машинами,** чтобы добавить эту опцию в меню **Избранное.** 
1. Выберите **виртуальные машины.** 

   ![Поиск виртуальных машин](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Портал перечисляет все виртуальные машины в подписке. Выберите тот, который вы хотите управлять, чтобы открыть ресурс **виртуальных машин.** Используйте поле поиска, если ваш VM сервера S'L не отображается. 
1. Выберите **конфигурацию сервера S'L** в панели **настроек** для управления VM сервера S'L. 

   ![Конфигурация SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в следующих статьях: 

* [Обзор сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Часто задаваемые вопросы для сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Руководство по ценообразованию для сервера S'L на Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Выпуск примечаний для сервера S'L на Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


