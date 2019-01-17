---
title: Обзор SQL Server на виртуальных машинах Linux в Azure | Документация Майкрософт
description: Из этой статьи вы узнаете, как запускать полные выпуски SQL Server на виртуальных машинах Linux в Azure. Прямые ссылки на все образы виртуальных машин SQL Server на базе Linux и соответствующие статьи.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 18f9dd5318c6aecff8819178168469d9334e5e29
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332410"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Обзор SQL Server на виртуальных машинах Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

SQL Server на виртуальных машинах Azure позволяет использовать полные версии SQL Server в облаке без необходимости управлять каким-либо локальным оборудованием. Кроме того, оплата по мере использования упрощает лицензирование для виртуальных машин SQL Server.

Виртуальные машины Azure работают во многих [географических регионах](https://azure.microsoft.com/regions/) по всему миру. На выбор предоставляется множество [размеров машин](../sizes.md). Коллекция образов позволяет создать виртуальную машину SQL Server нужной версии и выпуска, а также с требуемой операционной системой. Благодаря этому виртуальные машины — отличный вариант для различных рабочих нагрузок SQL Server.

## <a id="create"></a> Начало работы с виртуальными машинами SQL

Чтобы приступить к работе, выберите образ виртуальной машины SQL Server требуемой версии, выпуска и с нужной операционной системой. В следующих разделах приведены прямые ссылки на портал Azure для получения образов виртуальной машины SQL Server из коллекции.

> [!TIP]
> Дополнительные сведения о ценах на образы SQL см. на [странице с ценами на виртуальные машины Azure SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Version (версия) | Операционная система | Выпуск |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) версии 12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Сведения о доступных образах виртуальных машин SQL Server на базе Windows см. в статье [Приступая к работе с SQL Server в виртуальных машинах Azure](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Установленные пакеты

При настройке SQL Server в Linux установите пакет ядра СУБД, а затем некоторые дополнительные пакеты, в зависимости от ваших целей. Образы виртуальных машин Linux для SQL Server автоматически устанавливают большинство пакетов. В следующей таблице показаны установленные пакеты для каждого дистрибутива.

| Дистрибутив | [Ядро СУБД](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Инструменты](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Агент SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Полнотекстовый поиск](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Надстройка для обеспечения высокого уровня доступности](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Нет](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Нет](./media/sql-server-linux-virtual-machines-overview/no.png) | ![Нет](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Да](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="related-products-and-services"></a>Связанные продукты и услуги

### <a name="linux-virtual-machines"></a>Виртуальные машины Linux

* [Обзор виртуальных машин](../overview.md).

### <a name="storage"></a>Хранилище

* [Введение в службу хранилища Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Сеть

* [Обзор виртуальной сети](../../../virtual-network/virtual-networks-overview.md)
* [IP-адреса в Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Создание полного доменного имени на портале Azure](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Документация по SQL Server в Linux](https://docs.microsoft.com/sql/linux).
* [Сравнение со службой "База данных SQL"](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

## <a name="next-steps"></a>Дополнительная информация

Начало работы с SQL Server на виртуальных машинах Azure с Linux:

* [Создание виртуальной машины SQL Server на портале Azure](provision-sql-server-linux-virtual-machine.md).

Ответы на часто задаваемые вопросы о виртуальных машинах SQL на платформе Linux:

* [Часто задаваемые вопросы об SQL Server на виртуальных машинах Azure с Linux](sql-server-linux-faq.md)
