---
title: Поддержка 32-разрядных операционных систем на виртуальных машинах Azure | Документация Майкрософт
description: Сведения об операционных системах, поддерживаемых на виртуальных машинах Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 81b7efdd6bca0471719c11d130be95405f4d54e1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210194"
---
# <a name="support-for-32-bit-operating-systems-in-azure-virtual-machines"></a>Поддержка 32-разрядных операционных систем на виртуальных машинах Azure

Microsoft Azure теперь позволяет пользователям переносить свои 32-разрядные операционные системы Windows в Azure. Поддерживаются только специализированные виртуальные жесткие диски, а обобщенные образы не будут работать в Azure. Поскольку некоторые из этих операционных систем уже достигли соглашения об окончании срока службы, корпорация Майкрософт может не предоставлять дополнительную поддержку для них. Поддержка также не предоставляется для операционных систем на основе Linux или BIND, которые выполняются на виртуальной машине Microsoft Azure.

> [!NOTE]
> Для платформы Azure предусмотрено ограничение адресного пространства памяти на виртуальных машинах под управлением 32-разрядных операционных систем, где может быть доступен только 1 ГБ памяти (*особенно в SKU клиента, например Win7 или Win10*), а оставшаяся часть памяти виртуальной машины будет показана как зарезервированная на гостевой виртуальной машине. Это известная проблема, и в настоящее время у нас нет проблем с исправлением. Мы рекомендуем перейти к версии 64 OS.
> 

## <a name="more-information"></a>Дополнительные сведения

Дополнительные сведения об операционных системах, поддерживаемых на виртуальных машинах Azure, см. в следующих статьях базы знаний Майкрософт:

* [Поддержка серверного ПО Майкрософт для виртуальных машин Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)
* [Поддержка Linux и технологий с открытым исходным кодом в Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

## <a name="references"></a>Ссылки

* [Дополнительные сведения о бесплатных обновлениях для системы безопасности для Windows Server 2008 и R2 в Azure](https://www.microsoft.com/cloud-platform/windows-server-2008)
* [Дополнительные сведения о поддержке для Windows Server 2008 с пакетом обновления 2 (SP2) 32-разрядных образов в Azure](/windows-server/get-started/uploading-specialized-ws08-image-to-azure)
* [Дополнительные сведения о поддержке миграции образов Windows Server 2008 в Azure с помощью Azure Site Recovery](../../site-recovery/migrate-tutorial-windows-server-2008.md)
* [Дополнительные сведения о поддерживаемых операционных системах в расширении Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)
* [Дополнительные сведения об использовании Windows Server 2003 на Microsoft Azure](https://support.microsoft.com/help/3206074/running-windows-server-2003-on-microsoft-azure)

## <a name="next-steps"></a>Дальнейшие действия

Если вам нужна дополнительная помощь в любой момент в этой статье, обратитесь к экспертам по Azure на [форумах MSDN Azure и Stack overflow](https://azure.microsoft.com/support/forums/).

Кроме того, можно зафайлировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
