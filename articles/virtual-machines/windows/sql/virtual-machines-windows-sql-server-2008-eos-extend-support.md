---
title: Расширения поддержки SQL Server 2008 и SQL Server 2008 R2 с помощью Azure
description: Узнайте, как расширять поддержку для SQL Server 2008 и SQL Server 2008 R2, переносе экземпляра SQL Server в Azure или приобретения расширенной поддержки, чтобы хранить экземпляры локально.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ecb7030fa3652525a36ce15d66ea6e5daf9c3296
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304224"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Расширения поддержки SQL Server 2008 и SQL Server 2008 R2 с помощью Azure

SQL Server 2008 и SQL Server 2008 R2 и всех последующих версий [конца своего жизненного цикла поддержки (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Так как многие из наших клиентов по-прежнему используются обе версии, мы предоставляем несколько вариантов, чтобы продолжить получение технической поддержки. Можно перенести ваши локальные экземпляры SQL Server для виртуальных машин (ВМ) Azure, перенести базу данных SQL Azure, или в локальном и приобрести расширенной системы безопасности и обновлений.

В отличие от с управляемым экземпляром, переход к виртуальной Машине Azure не требует recertifying приложений. И в отличие от с остаются в локальной среде вы получите бесплатную расширенную исправлений путем переноса на виртуальных машинах Azure.

В оставшейся части этой статьи содержит вопросы переноса экземпляра SQL Server на виртуальных машинах Azure.

## <a name="provisioning"></a>Подготовка

Имеется оплаты по мере использования `SQL Server 2008 R2 on Windows Server 2008 R2` образа в Azure marketplace.

Клиенты, которые находятся в SQL Server 2008 должны будут либо самостоятельную установку или обновление до SQL Server 2008 R2. Аналогичным образом клиенты, использующие Windows Server 2008 нужно развернуть их виртуальную Машину из пользовательского VHD-файла или обновления до Windows Server 2008 R2.

Образов, развернутых в Marketplace поставляются с предустановленным расширения SQL IaaS. Расширение SQL IaaS обязательна для гибкой лицензирования и автоматической установки исправлений. Клиентам, развертывание самостоятельно установленных виртуальных машин, будет необходимо вручную установить расширение SQL IaaS. Расширение SQL IaaS не поддерживается в Windows 2008.

  > [!NOTE]
  > Хотя SQL Server `Create` и `Manage` колонки будет работать с помощью образа SQL Server 2008 R2 на портале Azure, ниже приведены функции _не поддерживается_: Автоматическое создание резервных копий, интеграцию хранилища ключей Azure, службы R и конфигурацию хранилища.

## <a name="licensing"></a>Лицензирование
Оплата по мере использования SQL Server 2008 R2 развертываний можно преобразовать в [преимущество (гибридного использования AZURE — Преимущество гибридного на Azure)](https://azure.microsoft.com/pricing/hybrid-benefit/).

Чтобы преобразовать лицензию Software Assurance (SA) на основе оплаты по мере использования, должны регистрировать клиентов с виртуальной Машиной SQL [поставщика ресурсов](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). После регистрации в поставщике ресурсов виртуальной Машины SQL, тип лицензии SQL будет взаимозаменяемыми между Преимущество гибридного использования AZURE и оплаты по мере использования.

Самостоятельно установленных экземпляров SQL Server 2008 или SQL Server 2008 R2 на виртуальной Машине Azure можно зарегистрировать с помощью поставщика ресурсов SQL и преобразовать тип лицензии для оплаты по мере использования.

## <a name="migration"></a>Миграция
Можно перенести экземпляры EOS SQL Server на виртуальных машинах Azure с помощью методов ручное резервное копирование и восстановление; Это наиболее распространенный способ переноса из локальной на виртуальную Машину Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Для массового переноса, мы рекомендуем [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) службы. С помощью Azure Site Recovery клиенты можно реплицировать всю виртуальную Машину, включая SQL Server из локальной на виртуальную Машину Azure.

SQL Server требуется согласованные моментальные снимки Azure Site Recovery для обеспечения восстановления; и Azure Site Recovery поддерживает согласованные моментальные снимки с минимальный интервал 1 час. Минимальное значение RPO, возможных для SQL Server с помощью Azure Site Recovery migrations составляет 1 час, а RTO — 2 часа, а также время восстановления SQL Server.

### <a name="database-migration-service"></a>Database Migration Service

[Database Migration Service](/azure/dms/dms-overview) является параметром для клиентов, если миграция из локальной виртуальной машине Azure, выполнив обновление SQL Server до SQL Server 2012 и более.

## <a name="disaster-recovery"></a>Аварийное восстановление

Ниже приведены решения для аварийного восстановления для EOS SQL Server на виртуальной Машине Azure.

- **Резервные копии SQL Server**: Используйте службу архивации Azure для защиты сервера SQL EOS от программ-шантажистов, случайного удаления и повреждения. Решение сейчас доступна Предварительная версия для EOS SQL Server и поддерживает SQL Server 2008 и 2008 R2 под управлением Windows 2008 R2 SP1. Дополнительные сведения см. в разделе это [статьи](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#support-for-sql-server-2008-and-sql-server-2008-r2)
- **Доставка журналов**: Можно создать реплику доставки журналов в другую зону или регион Azure с непрерывной восстановления для уменьшения RTO. Клиентам будет необходимо вручную настроить доставку журналов.
- **Azure Site Recovery.** Между зонами и регионы посредством репликации Azure Site Recovery можно реплицировать виртуальную Машину. Согласованные моментальные снимки приложений для обеспечения восстановления в аварийных ситуациях требуется SQL Server. Azure Site Recovery обеспечивает минимальное значение RPO в 1 час и 2-часовое + время восстановления SQL Server RTO для EOS аварийного восстановления SQL Server.

## <a name="security-patching"></a>Безопасность/исправление
Обновления безопасности для виртуальных машин SQL Server будут предоставляться по каналам центра обновления Майкрософт, после регистрации виртуальной Машины SQL Server с помощью SQL [поставщика ресурсов](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Либо можно загрузить исправления, вручную или автоматически.

**Automated patching** включен по умолчанию. Автоматическая установка исправлений позволяет Azure автоматически исправлять SQL Server и операционную систему. Можно указать день недели, время и длительность периода обслуживания, если установлено расширение SQL IaaS. Azure устанавливает исправления в период обслуживания. Расписание периода обслуживания использует для определения времени региональные параметры VM.  Дополнительные сведения см. в статье [Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Дальнейшие действия

Перенос виртуальной Машины SQL Server в Azure

* [Миграция базы данных SQL Server в экземпляр SQL Server на виртуальной машине Azure](virtual-machines-windows-migrate-sql.md)

Начало работы с SQL Server на виртуальных машинах Azure:

* [Создание виртуальной машины SQL Server на портале Azure](quickstart-sql-vm-create-portal.md).

Ответы на часто задаваемые вопросы о виртуальных машинах SQL:

* [Часто задаваемые вопросы об SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-faq.md)
