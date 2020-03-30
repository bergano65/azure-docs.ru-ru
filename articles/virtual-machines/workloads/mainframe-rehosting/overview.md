---
title: Реhosting мейнфреймов на виртуальных машинах Azure
description: Перехижив рабочие нагрузки на мэйнфрейм, такие как системы на базе IBM, использующие виртуальные машины (Виртуальные машины) в Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76289804"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Реhosting мейнфреймов на виртуальных машинах Azure

Миграция рабочих нагрузок из сред мейнфрейма в облако позволяет модернизировать инфраструктуру и часто экономить на затратах. Многие рабочие нагрузки можно перенести в Azure с минимальными изменениями кода — например, исправив только имена баз данных.

Вообще говоря, термин *мэйнфрейм* означает большую компьютерную систему. В частности, подавляющее большинство в настоящее время используется IBM System - серверы или IBM плагин-совместимых систем, которые работают MVS, DOS, VSE, OS/390, или z/OS.

Виртуальная машина Azure (VM) используется для изоляции и управления ресурсами для конкретного приложения в одном экземпляре. Для этой цели используются основные кадры, такие как IBM z/OS. Мейнфрейм может использовать один LPAR для региона CICS с сопутствующими программами COBOL и отдельную базу данных LPAR для базы данных IBM Db2. Типичное [n-уровненное приложение в Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) развертывает VMs Azure в виртуальную сеть, которую можно сегментировать в подсети для каждого уровня.

Вазовские ВМ могут запускать среды эмуляции мэйнфреймов и компиляторы, поддерживающие сценарии подъема и смены. Разработка и тестирование часто являются одними из первых рабочих нагрузок, которые перекочевали из мэйнфрейма в среду разработки/тестирования Azure. Общие компоненты сервера, которые можно эмулировать, включают процесс онлайн-транзакций (OLTP), пакет и системы приема данных, как показано на следующем рисунке.

![Среды эмуляции в Azure позволяют запускать системы на основе z/OS.](media/01-overview.png)

Некоторые рабочие нагрузки мэйнфрейма могут быть перенесены в Azure с относительной легкостью, в то время как другие могут быть переразмещены на Azure с помощью решения партнера. Для получения подробных рекомендаций по выбору партнерского решения может помочь [центр миграции Мазурных Руменфреймов.](https://azure.microsoft.com/migration/mainframe/)

## <a name="mainframe-migration"></a>Миграция мейнфреймов

Rehost, восстановить, заменить, или уйти в отставку? IaaS или PaaS? Чтобы определить правильную стратегию миграции для приложения мэйнфрейма, смотрите руководство [по миграции Mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) в Центре архитектуры Azure.

## <a name="micro-focus-rehosting-platform"></a>Платформа реhosting Micro Focus

Micro Focus Enterprise Server является одной из крупнейших доступных платформ реhosting-платформ мейнфрейма. Вы можете использовать его для выполнения рабочих нагрузок z/OS на менее дорогой платформе x86 на Azure.

Чтобы начать работу:

- [Установка корпоративного сервера и корпоративного разработчика на Azure](./microfocus/set-up-micro-focus-azure.md)
- [Настройка CICS BankDemo для корпоративного разработчика на Azure](./microfocus/demo.md)
- [Запуск корпоративного сервера в докерном контейнере на Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame на Azure

TmaxSoft OpenFrame — популярное решение для реhosting-хостинга, используемое в сценариях подъема и смены. Среда OpenFrame в Azure подходит для разработки, демонстраций, тестирования или производственных нагрузок.

Чтобы начать работу:

- [Начало работы с TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Скачать электронную книгу](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM - разработка и тестовая среда (IBM zD&T) создает непроизводственную среду на Azure, которую можно использовать для разработки, тестирования и демонстраций приложений на основе z/OS.

Среда эмуляции в Azure может размещать различные виды экземпляров с помощью контролируемых дистрибутивов разработчиков приложений (ADCD). Вы можете запустить zD&T Personal Edition, zD&T Parallel Sysplex и zD&T Enterprise Edition на Azure и Azure Stack.

Чтобы начать работу:

- [Настройка IBM zD&T 12.0 на Azure](./ibm/install-ibm-z-environment.md)
- [Настройка ADCD на zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale в Azure

Среда IBM DB2 pureScale обеспечивает кластер баз данных для Azure. Он не идентичен исходной среде, но обеспечивает аналогичную доступность и масштаб, как IBM DB2 для z/OS, работающих в параллельной настройке Sysplex.

Чтобы начать работу, смотрите [IBM DB2 pureScale на Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Рекомендации

При переносе рабочих нагрузок мэймфрейма в инфраструктуру Azure в качестве службы (IaaS) можно выбрать несколько типов масштабируемых вычислительных ресурсов по требованию, включая MMs-и., вывихненных Azure. Azure предлагает широкий спектр [Linux](/azure/virtual-machines/linux/overview) и [Windows](/azure/virtual-machines/windows/overview) VMs.

### <a name="compute"></a>Службы вычислений

Вычислительная мощность Azure выгодно отличается от емкости мейнфрейма. Если вы хотите перенести рабочую нагрузку мэйнфрейма в Azure, сравните метрику мейнфрейма в миллион инструкций в секунду (MIPS) с виртуальными процессорами. 

Узнайте, как [переместить вычисления мейнфрейма в Azure.](./concepts/mainframe-compute-azure.md)

### <a name="high-availability-and-failover"></a>Высокий уровень доступности и отработка отказа

Azure предлагает соглашения на основе обязательств на уровне обслуживания (SLAs). Доступность нескольких девяностов — это значение по умолчанию, и SLAs можно оптимизировать с помощью локальной или геолокационной репликации служб. В полном [соглашении об уровне обслуживания Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) поясняется гарантированная доступность Azure в целом.

С Azure IaaS, такимкак как VM, определенные системные функции обеспечивают поддержку сбоя, например, случаи кластеризации сбоев и наборы доступности. При использовании платформы Azure в качестве ресурсов службы (PaaS) платформа автоматически перекладывает сбой. В качестве примеров можно привести [базу данных Azure S'L](/azure/sql-database/sql-database-technical-overview) и [DB Azure Cosmos DB.](/azure/cosmos-db/introduction)

### <a name="scalability"></a>Масштабируемость

Мейнфреймы обычно масштабируются, в то время как облачные среды масштабируются. Azure предлагает широкий спектр [размеров Linux](/azure/virtual-machines/linux/sizes) и [Windows](/azure/virtual-machines/windows/sizes) для удовлетворения ваших потребностей. Облако также масштабируется вверх или вниз, чтобы соответствовать точным спецификациям пользователя. Вычислите мощность, хранение и [масштаб](/azure/architecture/best-practices/auto-scaling) услуг по запросу в рамках модели выставления счетов на основе использования.

### <a name="storage"></a>Хранилище

В облаке у вас есть ряд гибких, масштабируемых вариантов хранения, и вы платите только за то, что вам нужно. В [службе хранилища Azure](/azure/storage/common/storage-introduction) предоставляется хранилище с высокой масштабируемостью для объектов данных, служба файловой системы для облака, надежное хранилище сообщений, а также хранилище NoSQL. Для виртуальных машин управляемые и неуправляемые диски предоставляют постоянное и безопасное дисковое хранилище.

Узнайте, как [переместить хранилище мэйнфрейма в Azure.](./concepts/mainframe-storage-azure.md)

### <a name="backup-and-recovery"></a>Резервное копирование и восстановление

Поддержание собственного места аварийного восстановления может быть дорогостоящим предложением. Azure имеет простые в реализации и экономически эффективные варианты [резервного копирования,](/azure/backup/backup-introduction-to-azure-backup) [восстановления](/azure/site-recovery/site-recovery-overview)и [избыточности](/azure/storage/common/storage-redundancy) на местном или региональном уровне, или через гео-избыточность.

## <a name="azure-government-for-mainframe-migrations"></a>Правительство Azure для миграций мэйнфреймов

Многие субъекты государственного сектора хотели бы перенести свои приложения на мэйнфрейм на более современную, гибкую платформу. Правительство Microsoft Azure — это физически разделенный экземпляр глобальной платформы Microsoft Azure, упакованной в федеральные системы, системы управления штатов и местных органов власти. Она предоставляет услуги мирового класса по обеспечению безопасности, защите и соблюдению нормативных требований специально для правительственных учреждений Соединенных Штатов и их партнеров.

Правительство Azure получило Временную администрацию по эксплуатации (P-ATO) для FedRAMP High Impact для систем, которые нуждаются в такого рода среде.

Чтобы начать работу, загрузите [облако Microsoft Azure Government для приложений мейнфреймов.](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/)

## <a name="next-steps"></a>Дальнейшие действия

Попросите наших [партнеров](partner-workloads.md) помочь вам перенести или перевести ваши приложения на мэйнфрейме. 

См. также

- [Белые документы по темам мейнфрейма](mainframe-white-papers.md)
- [Миграция мейнфреймов](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Устранение неполадок](/azure/virtual-machines/troubleshooting/)
- [Демистификация мэйнфрейма для миграции Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
