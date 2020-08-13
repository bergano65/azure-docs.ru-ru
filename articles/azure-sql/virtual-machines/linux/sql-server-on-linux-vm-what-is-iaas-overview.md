---
title: Обзор SQL Server на виртуальных машинах Azure для Linux | Документация Майкрософт
description: Узнайте, как запускать полные выпуски SQL Server на виртуальных машинах Azure для Linux. Прямые ссылки на все образы виртуальных машин SQL Server на базе Linux и соответствующие статьи.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41b74ed713485679576fdf7f4f0df54803b56caa
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192106"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Обзор SQL Server на виртуальных машинах Azure (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

SQL Server на виртуальных машинах Azure позволяет использовать полные версии SQL Server в облаке без необходимости управлять локальным оборудованием. Кроме того, оплата по мере использования упрощает лицензирование для виртуальных машин SQL Server.

Виртуальные машины Azure работают во многих [географических регионах](https://azure.microsoft.com/regions/) по всему миру. На выбор предоставляется множество [размеров машин](../../../virtual-machines/windows/sizes.md). Коллекция образов позволяет создать виртуальную машину SQL Server нужной версии и выпуска, а также с требуемой операционной системой. Благодаря этому виртуальные машины — отличный вариант для различных рабочих нагрузок SQL Server. 

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> Начало работы с виртуальными машинами SQL Server

Чтобы приступить к работе, выберите образ виртуальной машины SQL Server требуемой версии, выпуска и с нужной операционной системой. В следующих разделах приведены прямые ссылки на портал Azure для получения образов виртуальной машины SQL Server из коллекции.

> [!TIP]
> Дополнительные сведения о ценах на SQL Server образы см. [на странице цен на виртуальные машины Linux под управлением SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Версия | Операционная система | Выпуск |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) V12 SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) версии 12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Сведения о доступных образах виртуальных машин SQL Server для Windows см. в статье [обзор SQL Server на виртуальных машинах Azure (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Установленные пакеты

При настройке SQL Server на Linux устанавливается пакет ядро СУБД, а затем несколько дополнительных пакетов в зависимости от требований. Образы виртуальных машин Linux для SQL Server автоматически устанавливают большинство пакетов. В следующей таблице показаны установленные пакеты для каждого дистрибутива.

| Distribution | [Ядро СУБД](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Инструменты](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Агент SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Полнотекстовый поиск](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [MSSQL Integration Services](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Надстройка для обеспечения высокого уровня доступности](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![нет](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![да](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Связанные продукты и услуги

### <a name="linux-virtual-machines"></a>Виртуальные машины Linux

* [Обзор виртуальных машин Azure](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Память

* [Введение в службу хранилища Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Сеть

* [Обзор виртуальной сети](../../../virtual-network/virtual-networks-overview.md)
* [IP-адреса в Azure](../../../virtual-network/public-ip-addresses.md)
* [Создание полного доменного имени на портале Azure](../../../virtual-machines/windows/portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Документация по SQL Server в Linux](https://docs.microsoft.com/sql/linux).
* [Сравнение со службой "База данных SQL"](../../azure-sql-iaas-vs-paas-what-is-overview.md).

## <a name="next-steps"></a>Дальнейшие действия

Приступая к работе с SQL Server на Linux виртуальными машинами:

* [Создание виртуальной машины SQL Server на портале Azure](sql-vm-create-portal-quickstart.md).

Получите ответы на часто задаваемые вопросы о SQL Server виртуальных машинах в Linux:

* [Часто задаваемые вопросы об SQL Server на виртуальных машинах Azure](frequently-asked-questions-faq.md)
