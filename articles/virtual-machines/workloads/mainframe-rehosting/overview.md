---
title: Повторного размещения мэйнфреймов, на виртуальных машинах Azure
description: Повторное размещение рабочих нагрузок мэйнфреймов, например IBM Z-системы с помощью виртуальных машин (ВМ) в Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: c1d7b52bdce77ca108781a999a8a85b3e3fca0b8
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004420"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Повторного размещения мэйнфреймов, на виртуальных машинах Azure

Перенос рабочих нагрузок с больших ЭВМ среды в облако позволяет модернизация инфраструктуры и часто сэкономить на стоимости. Многие рабочие нагрузки можно перенести в Azure с минимальными изменениями кода — например, исправив только имена баз данных.

В целом, термин *мэйнфреймов* означает больших компьютерной системы. В частности большинство в настоящий момент находятся серверы IBM Z системы или IBM plug-compatible систем, использующих MVS DOS, VSE, OS/390 и z/OS.

Виртуальной машине Azure (ВМ) используется для изоляции и управления ресурсами для определенного приложения на одном экземпляре. Мэйнфреймов, например IBM z/OS для этой цели использовать логические разделы (LPARS). Мэйнфрейме использовать один LPAR CICS региона с помощью связанных программ COBOL и отдельные LPAR для базы данных IBM Db2. Типичный [n уровневого приложения в Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) развертывает виртуальные машины Azure в виртуальной сети, которую можно разделить на подсети для каждого уровня.

Виртуальные машины Azure можно запустить мэйнфреймов эмуляции сред и компиляторы, которые поддерживают сценарии lift-and-shift. Разработка и тестирование часто встречаются среди первых рабочих нагрузок для переноса с мэйнфрейма, в среде разработки и тестирования Azure. Общие серверные компоненты, которые можно эмулировать включают процесс онлайн-транзакций (OLTP), пакетной службы и систем приема данных, как показано на следующем рисунке.

![Эмуляция среды в Azure позволяют выполнять z/OS-системы.](media/01-overview.png)

Относительно легко можно перенести в Azure для некоторых рабочих нагрузок мэйнфреймов, хотя другие может быть повторно размещен в Azure с помощью одного из партнерских решений к сети. Подробные инструкции о выборе одного из партнерских решений [Центр миграции Azure мэйнфреймов](https://azure.microsoft.com/migration/mainframe/) может помочь.

## <a name="mainframe-migration"></a>Миграция мейнфреймов

Повторное размещение, rebuild, замените или снять с учета? IaaS или PaaS? Для определения стратегии правой миграции приложения мэйнфрейма, см. в разделе [переход с мэйнфреймов](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) руководство в центре архитектуры Azure.

## <a name="micro-focus-rehosting-platform"></a>Micro фокус повторного размещения платформы

Micro Focus Enterprise Server является одним из крупнейших мэйнфреймов, повторное размещение доступных платформ. Можно использовать то же самое рабочих нагрузок z/OS на дешевле x86 платформы в Azure.

Чтобы начать работу:

- [Установка Enterprise Server и Enterprise Developer в Azure](./microfocus/set-up-micro-focus-azure.md)
- [Настройка CICS BankDemo Enterprise Developer на платформе Azure](./microfocus/demo.md)
- [Запуск Enterprise Server в контейнере Docker в Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame в Azure

TmaxSoft OpenFrame — это решение повторного размещения мэйнфреймов популярных, используется в сценариях lift-and-shift. OpenFrame среды в Azure подходит для разработки, демонстрации, тестирования или производственных рабочих нагрузок.

Чтобы начать работу:

- [Начало работы с TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Скачайте бесплатную электронную книгу](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD & T 12.0

IBM Z разработки и тестовой среды (IBM zD & T) настраивает непроизводственной среде в Azure, который можно использовать для разработки, тестирования и демонстрации приложений на базе z/OS.

Настройки эмуляции среды в Azure можно размещать различные виды Z экземпляров с помощью приложения разработчикам управлять дистрибутивов (ADCDs). ZD & T Personal Edition, zD & параллельных Sysplex T и zD & T Enterprise Edition можно запустить в Azure и Azure Stack.

Чтобы начать работу:

- [Настройка IBM zD & 12.0 T в Azure](./ibm/install-ibm-z-environment.md)
- [Настройка ADCD на zD & T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale в Azure

Среда pureScale IBM DB2 предлагает кластер базы данных Azure. Она не совпадает с исходной среде, но она обеспечивает аналогичные доступности и масштабирования как IBM DB2 для z/OS в файл Sysplex параллельной установки.

Чтобы приступить к работе, см. в разделе [pureScale IBM DB2 на платформе Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Рекомендации

При переносе рабочих нагрузок мэйнфреймов в инфраструктуру Azure как услуга (IaaS), можно выбрать из нескольких типов запрашиваемых масштабируемых вычислительных ресурсов, включая виртуальные машины Azure. Azure предлагает широкий набор [Linux](/azure/virtual-machines/linux/overview) и [Windows](/azure/virtual-machines/windows/overview) виртуальных машин.

### <a name="compute"></a>Службы вычислений

Azure вычислительной мощности выигрывает по емкости мэйнфреймов. Если вы рассматриваете переход рабочую нагрузку мэйнфреймов в Azure, сравните метрики мэйнфреймов один миллион команд в секунду (MIP-ОБЪЕКТЫ), чтобы виртуальные ЦП. 

Узнайте, как [перемещение больших ЭВМ вычислений в Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Высокий уровень доступности и отработка отказа

Azure предлагает основе обязательства соглашения об уровне обслуживания (SLA). Несколько девятки доступности используется по умолчанию и соглашения об уровне обслуживания можно оптимизировать с помощью локального или на основе географической репликации служб. В полном [соглашении об уровне обслуживания Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) поясняется гарантированная доступность Azure в целом.

С помощью Azure IaaS, например, виртуальной Машины, определенные системные функции обеспечения отказоустойчивости — например, экземпляры отказоустойчивой кластеризации и [группы доступности](/azure/virtual-machines/windows/regions-and-availability#availability-sets). При использовании платформы Azure как услуги (PaaS) ресурсы, платформа автоматически выполняет отработку отказа. К ним относятся [базы данных SQL Azure](/azure/sql-database/sql-database-technical-overview) и [Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Масштабируемость

Мейнфреймы обычно масштабируются вертикально, а облачные среды — горизонтально. Azure предлагает широкий набор [Linux](/azure/virtual-machines/linux/sizes) и [Windows](/azure/virtual-machines/windows/sizes) размеры в соответствии с требованиями. Облако также масштабируется вверх или вниз для соответствия спецификации точно. Вычислительные power, хранилища и службы [масштабирования](/azure/architecture/best-practices/auto-scaling) по запросу в разделе модель выставления счетов на основе использования.

### <a name="storage"></a>Хранилище

В облаке у вас есть несколько вариантов хранения гибкую, масштабируемую и вы платите только за то, что вам нужно. В [службе хранилища Azure](/azure/storage/common/storage-introduction) предоставляется хранилище с высокой масштабируемостью для объектов данных, служба файловой системы для облака, надежное хранилище сообщений, а также хранилище NoSQL. Для виртуальных машин управляемые и неуправляемые диски предоставляют постоянное и безопасное дисковое хранилище.

Узнайте, как [переместить хранилище мэйнфреймов в Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Архивация и восстановление

Поддержанием собственного аварийного восстановления может оказаться дорогим проектом. Azure доступны в развертывании и экономичного варианта [резервного копирования](/azure/backup/backup-introduction-to-azure-backup), [восстановления](/azure/site-recovery/site-recovery-overview), и [избыточности](/azure/storage/common/storage-redundancy) на локальном или региональные или с помощью географической избыточности.

## <a name="azure-government-for-mainframe-migrations"></a>Azure для государственных организаций для миграция решений для мэйнфреймов

Многие государственного сектора хотели переместить их больших ЭВМ к более современные гибкую платформу. Microsoft Azure для государственных организаций — это физически отделенные экземпляр глобальную платформу Microsoft Azure — в пакет для систем правительства федерального, штатов и местным. Он предоставляет уровень безопасности мирового класса, защиты и службы обеспечения соответствия требованиям специально для правительственных учреждений США и их партнеров.

Azure для государственных организаций, полученные центром временное, чтобы выполнить (P-ATO) за высокая степень влияния FedRAMP для систем, которым требуется такой тип среды.

Чтобы приступить к работе, скачайте [облако Microsoft Azure для государственных организаций для приложений для мейнфреймов](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Дальнейшие действия

Попросите наших [партнеров](partner-workloads.md) помогут вам выполнить миграцию и повторного размещения ваших приложений для мейнфреймов. Подробные инструкции о выборе одного из партнерских решений, см. в разделе [Alliance модернизации платформы](https://www.platformmodernization.org/pages/mainframe.aspx) веб-сайта.

См. также:

- [Технические документы по темам мэйнфреймов](mainframe-white-papers.md)
- [Переход с мэйнфреймов](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Устранение неполадок](/azure/virtual-machines/troubleshooting/)
- [Мифы о больших ЭВМ к миграции в Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
