---
title: Преимущества использования файлов NetApp Azure с базой данных Oracle Документы Майкрософт
description: Описывает технологию и обеспечивает сравнение производительности между Oracle Direct NFS (dNFS) и традиционным клиентом NFS. Показывает преимущества использования dNFS с Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: b-juche
ms.openlocfilehash: a73da39dafcc8be78fbe1c023693ffa4a19aa1d3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085014"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Преимущества использования Azure NetApp Files с Oracle Database

Oracle Direct NFS (dNFS) позволяет повысить производительность, чем собственный драйвер NFS операционной системы. В этой статье объясняется технология и обеспечивается сравнение производительности между dNFS и традиционным клиентом NFS (Kernel NFS). Он также показывает преимущества и простоту использования dNFS с Azure NetApp файлов.  

## <a name="how-oracle-direct-nfs-works"></a>Как работает Oracle Direct NFS

Oracle Direct NFS (dNFS) обходит буферный кэш операционной системы. Данные кэшируются только один раз в пользовательском пространстве, исключая накладные расходы на копии памяти.  

Традиционный клиент NFS использует единый сетевой поток, как показано в следующем примере: 

![Традиционный клиент NFS, использующий единый сетевой поток](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

В отличие от этого, Oracle dNFS повышает производительность за счет балансировки нагрузки сетевого трафика через несколько сетевых потоков. Эта возможность позволяет базе данных Oracle динамически установить значительное число 650 различных сетевых соединений, как показано в приведенном ниже примере:  

![Oracle Direct NFS повышает производительность](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[Часто задаваемые вопросы Oracle для Direct NFS](http://www.orafaq.com/wiki/Direct_NFS) показывают, что Oracle dNFS является оптимизированным клиентом NFS. Он обеспечивает быстрый и масштабируемый доступ к хранилищу NFS, которое находится на устройствах хранения NAS (доступно по Сравнению с TCP/IP). dNFS встроен в ядро базы данных так же, как ASM, который используется в основном с DAS или SAN хранения. Таким *образом, руководство заключается в использовании dNFS при реализации NAS хранения и использования ASM при реализации хранения SAN.*

dNFS является опцией по умолчанию в Oracle 18c.

dNFS доступен, начиная с Oracle Database 11g. Приведенная ниже диаграмма сравнивает dNFS с родным NFS. При использовании dNFS база данных Oracle, работая на виртуальном компьютере Azure, может управлять больше ввоза/от родного клиента NFS.

![Oracle и Azure NetApp Файлы сравнение dNFS с родной NFS](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

Вы можете включить или отключить dNFS, запустив две команды и перезапустив базу данных.

Для включения:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Чтобы отключить:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Файлы Azure NetApp в сочетании с Oracle Direct NFS

Вы можете повысить производительность Oracle dNFS с помощью сервиса Azure NetApp Files. Служба дает вам полный контроль над производительностью приложения. Он может удовлетворить чрезвычайно требовательных приложений. Сочетание Oracle dNFS с Azure NetApp Files обеспечивает большое преимущество для ваших рабочих нагрузок.

## <a name="next-steps"></a>Дальнейшие действия

- [Архитектура решений, использующих Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Обзор приложений и решений Oracle в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)
