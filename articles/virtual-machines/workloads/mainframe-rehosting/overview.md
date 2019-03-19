---
title: Повторного размещения мэйнфреймов, на виртуальных машинах Azure | Документация Майкрософт
description: Повторное размещение рабочих нагрузок мэйнфреймов, например IBM Z-системы с помощью виртуальных машин (ВМ) в Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 8be0ebc486739f8826e8a1d5a5307a219ba71b6f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192723"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Повторного размещения мэйнфреймов, на виртуальных машинах Azure

Перенос рабочих нагрузок с больших ЭВМ среды в облако позволяет модернизация инфраструктуры и часто сэкономить на стоимости. Многие рабочие нагрузки можно перенести в Azure с минимальными изменениями кода — например, исправив только имена баз данных.

Термин *мэйнфреймов* обычно относится к большой компьютерной системе, но большинство развернутые серверы IBM Z системы или IBM plug-compatible систем MVS DOS, VSE, OS/390 и z/OS.

Виртуальной машине Azure (ВМ) используется для изоляции и управления ресурсами для определенного приложения на одном экземпляре. Мэйнфреймов, например IBM z/OS для этой цели использовать логические разделы (LPARS). Мэйнфрейме использовать один LPAR CICS региона с помощью связанных программ COBOL и отдельные LPAR для базы данных IBM Db2. Типичный [n уровневого приложения в Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) развертывает виртуальные машины Azure в виртуальной сети, которую можно разделить на подсети для каждого уровня.

Виртуальные машины Azure можно запустить мэйнфреймов эмуляции сред и компиляторы, которые поддерживают сценарии lift-and-shift. Разработка и тестирование часто встречаются среди первых рабочих нагрузок для переноса с мэйнфрейма, в среде разработки и тестирования Azure. Общие серверные компоненты, которые можно эмулировать включают процесс онлайн-транзакций (OLTP), пакетной службы и систем приема данных, как показано на следующем рисунке.

![Эмуляция среды в Azure позволяют выполнять z/OS-системы.](media/01-overview.png)

Относительно легко можно перенести в Azure для некоторых рабочих нагрузок мэйнфреймов, хотя другие может быть повторно размещен в Azure с помощью одного из партнерских решений к сети. Подробные инструкции о выборе одного из партнерских решений [Центр миграции Azure мэйнфреймов](https://azure.microsoft.com/migration/mainframe/) может помочь.

## <a name="set-up-devtest-environment-using-a-micro-focus-rehosting-platform"></a>Настройка среды разработки и тестирования с помощью платформы Micro Focus повторного размещения

Micro Focus Enterprise Server является одним из крупнейших мэйнфреймов, повторное размещение доступных платформ. Можно использовать то же самое рабочих нагрузок z/OS на дешевле x86 платформы в Azure.

Приступить к работе, см. в следующих статьях:

- [Установка Micro Focus Enterprise Server 4.0 и Enterprise Developer 4.0 в Azure](./microfocus/set-up-micro-focus-on-azure.md)
- [Настройка BankDemo CICS Micro фокус для 4.0 Micro фокус Enterprise Developer в Azure](./microfocus/demo.md)

## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame в Azure

TmaxSoft OpenFrame является популярным мэйнфреймов, повторное размещение решение, которое позволяет легко перенести существующие ресурсы мэйнфреймов и перемещать их в Azure. OpenFrame среды в Azure подходит для разработки, демонстрации, тестирования или производственных рабочих нагрузок.

Чтобы приступить к работе, скачайте [установить TmaxSoft OpenFrame в Azure](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/) электронная книга.

## <a name="set-up-a-devtest-environment-using-ibm-z-devtest-120"></a>Настройка среды разработки и тестирования с помощью IBM Z разработки и тестирования 12.0

IBM Z разработки и тестовой среды (IBM zD & T) настраивает непроизводственной среде в Azure, который можно использовать для разработки, тестирования и демонстрации приложений на базе z/OS.

Настройки эмуляции среды в Azure может поддерживать множество экземпляров Z с помощью приложения разработчикам управлять дистрибутивов (ADCDs). ZD & T Personal Edition, zD & параллельных Sysplex T и zD & T Enterprise Edition можно запустить в Azure и Azure Stack.

Приступить к работе, см. в следующих статьях:

-   [Настройка IBM zD & 12.0 T в Azure](./ibm/install-ibm-z-environment.md)
-   [Настройка ADCD на zD & T](./ibm/demo.md)

## <a name="migrate-ibm-db2-purescale-to-azure"></a>Перенос pureScale IBM DB2 в Azure

Среда IBM DB2 pureScale предоставляет кластер баз данных для Azure с высоким уровнем доступности и масштабируемости в операционных системах Linux. Хотя эта среда не идентична исходной, IBM DB2 pureScale в Linux предоставляет примерно те же функции обеспечения высокого уровня доступности и масштабируемости, что и IBM DB2 для z/OS в конфигурации Parallel Sysplex на мейнфрейме.

Чтобы приступить к работе, см. в разделе [pureScale IBM DB2 на платформе Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Рекомендации

При переносе рабочих нагрузок мэйнфреймов в инфраструктуру Azure как услуга (IaaS), можно выбрать из нескольких типов запрашиваемых масштабируемых вычислительных ресурсов, включая виртуальные машины Azure. Azure предлагает широкий набор [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/overview) и [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/overview) виртуальных машин.

### <a name="high-availability-and-failover"></a>Высокий уровень доступности и отработка отказа

Azure предлагает основе обязательства соглашение об уровне обслуживания (SLA), где несколько девяток доступности используется по умолчанию, оптимизированные для работы с локальной или на основе географической репликации служб. В полном [соглашении об уровне обслуживания Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) поясняется гарантированная доступность Azure в целом.

Для Azure IaaS, например виртуальных машин, отработки отказа зависит от конкретной системы функции, такие как экземпляры отказоустойчивой кластеризации и [группы доступности](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets). При использовании платформы Azure как услуги (PaaS) ресурсы, такие как [базы данных SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) и [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction), платформа автоматически выполняет отработку отказа.

### <a name="scalability"></a>Масштабируемость

Мэйнфреймы обычно увеличивать масштаб, при горизонтального масштабирования облачной среды. Azure предлагает широкий набор [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) и [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) размеры в соответствии с требованиями. Облако также масштабируется вверх или вниз для соответствия спецификации точно. Вычислительные power, хранилища и службы [масштабирования](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) по запросу в разделе модель выставления счетов на основе использования.

### <a name="storage"></a>Хранилище

В облаке у вас есть несколько вариантов хранения гибкую, масштабируемую и вы платите только за то, что вам нужно. В [службе хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) предоставляется хранилище с высокой масштабируемостью для объектов данных, служба файловой системы для облака, надежное хранилище сообщений, а также хранилище NoSQL. Для виртуальных машин управляемые и неуправляемые диски предоставляют постоянное и безопасное дисковое хранилище.

### <a name="backup-and-recovery"></a>Архивация и восстановление

Поддержанием собственного аварийного восстановления может оказаться дорогим проектом. Azure доступны в развертывании и экономичного варианта [резервного копирования](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup), [восстановления](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), и [избыточности](https://docs.microsoft.com/azure/storage/common/storage-redundancy) на локальном или региональные или с помощью географической избыточности.

## <a name="azure-government-for-mainframe-migrations"></a>Azure для государственных организаций для миграция решений для мэйнфреймов

Многие государственного сектора хотели переместить их больших ЭВМ к более современные гибкую платформу. Microsoft Azure для государственных организаций — это физически отделенные экземпляр облачных служб на основе глобальной платформы Microsoft Azure, но в пакет для систем правительства федерального, штатов и местным. Он предоставляет уровень безопасности мирового класса, защиты и службы обеспечения соответствия требованиям специально для правительственных учреждений США и их партнеров.

Azure для государственных организаций, полученные центром временное, чтобы выполнить (P-ATO) за высокая степень влияния FedRAMP для систем, которым требуется такой тип среды. 

Чтобы приступить к работе, скачайте [облако Microsoft Azure для государственных организаций для приложений для мейнфреймов](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="learn-more"></a>Подробнее

Если вы рассматриваете переход с мэйнфреймов, наши [партнера](partner-workloads.md) экосистемы могут помочь вам. См. дополнительные сведение о критериях выбора решений от партнеров в статье [Platform Modernization Alliance](https://www.platformmodernization.org/pages/mainframe.aspx) (Альянс модернизации платформы).

См. также:

-   [Переход с мэйнфреймов](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
-   [Устранение неполадок](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
-   [Мифы о больших ЭВМ к миграции в Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
