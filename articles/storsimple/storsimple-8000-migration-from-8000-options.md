---
title: Варианты миграции данных с устройств серии StorSimple 8000
description: Предоставляет обзор вариантов переноса данных из серии StorSimple 8000.
services: storsimple
author: priestlg
ms.service: storsimple
ms.topic: article
ms.date: 03/25/2020
ms.author: v-grpr
ms.openlocfilehash: f712486c8035bbc913fbd229759f3415a1005449
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438324"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Варианты переноса данных из серии StorSimple 8000

> [!IMPORTANT]
> 31 декабря 2022 года серия StorSimple 8000 достигнет статуса поддержки (EOS). Мы рекомендуем клиентам серии StorSimple 8000 перейти на одну из альтернатив, описанных в документе.

Серия StorSimple 8000 подходит [к концу поддержки](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) в декабре 2022 года. Клиенты, которые работают в серии StorSimple 8000, имеют возможность перейти на другие гибридные сервисы Azure. В этой статье описываются гибридные варианты, доступные для переноса данных в Azure.

## <a name="migration-options"></a>Варианты переноса

Клиенты, использующие серии StorSimple 8000, имеют опции Azure или сторонних.

### <a name="azure-options"></a>Варианты с использованием Azure

#### <a name="migrate-to-azure-file-sync"></a>Миграция в службу "Синхронизация файлов Azure"

Эта совершенно новая опция миграции позволяет клиентам хранить файлы своих организаций в файлах Azure Files. Затем эти файловые ресурсы централизуются для локального доступа с помощью службы "Синхронизация файлов Azure" (AFS). AFS можно развернуть на узле Windows Server. Фактический перенос данных выполняется с помощью средства миграции или путем перемещения копии узла.

Для получения дополнительной информации о том, как перенести данные в Azure File Sync, перейдите на [StorSimple 8100 и 8600 миграции в Azure File Sync.](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)

<!-- 03/25/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
### Third-party options

#### Migrate to Panzura Freedom NAS

StorSimple 5000-7000 customers can choose to migrate to Panzura Freedom NAS to keep their data in Azure. Panzura Freedom solution provides a NAS solution that spans datacenters, offices, public and private clouds. The solution enables local, hybrid, and in-cloud data workflows for NFS, SMB, and mobile clients. 

This migration is supported by Panzura and customers can get started by requesting migration support from the [Panzura website](https://panzura.com/storsimple-migration/).

#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Часто задаваемые вопросы о переносе

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>У. Когда устройства серии StorSimple 8000 достигают конца обслуживания?

A. Серия StorSimple 8000 [завершится в](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) декабре 2022 года. Окончание поддержки означает, что Microsoft больше не сможет оказывать поддержку как аппаратного, так и программного обеспечения этих устройств после декабря 2022 года. Мы настоятельно рекомендуем начать разработку плана по переносу данных с ваших устройств.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>У. Что происходит с данными, которые хранятся в Azure?  

A. Вы можете продолжать использовать данные в Azure после их перемещения в новую службу.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>У. Что происходит с данными, которые хранятся локально на устройстве StorSimple?

A. Данные, которые хранятся локально на устройстве, можно скопировать в новую службу, как описано в документах о переносе.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>У. Что произойдет, если я хочу сохранить мой StorSimple 8000 серии прибор?

A. Хотя службы могут продолжать работу, корпорация Майкрософт больше не будет обеспечивать поддержку оборудования и программного обеспечения. Для обеспечения непрерывности бизнес-процессов настоятельно рекомендуется выполнить миграцию.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>У. Какие возможности доступны для переноса данных с устройств серии StorSimple 8000?

A. В зависимости от сценария, пользователи серии StorSimple 8000 имеют следующие варианты миграции:

* **Перенос в службу "Синхронизация файлов Azure"**. Используйте этот вариант, если необходимо переключиться в собственный формат Azure. Службу "Синхронизация файлов Azure" можно использовать для централизованного управления файловыми ресурсами.

* **Другие варианты**: Вы можете связаться с службой поддержки Майкрософт, чтобы обсудить варианты миграции, не перечисленные здесь.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>У. Поддерживается ли перенос данных в другие решения для хранения?

A. Да. Поддерживается перенос на другие решения для хранения с использованием копии узла данных.

### <a name="q-is-migration-supported-by-microsoft"></a>У. Поддерживается ли перенос корпорацией Майкрософт?

A. Миграция из серии 8000 является полностью поддерживаемой операцией. Более того, корпорация Майкрософт рекомендует обратиться в службу поддержки перед началом миграции. Сейчас миграции — это интерактивная операция. Если вы собираетесь перенести данные с устройства серии StorSimple 8000, [обратитесь в службу поддержки StorSimple.](mailto:storsimp@microsoft.com)

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>У. Какова модель ценообразования для миграции в синхронизацию файлов Azure?

A. При использовании Azure File Sync может применяться абонентская плата за эту услугу. Клиенты также должны будут оплачивать текущие расходы на хранение. Ссылка на [цены AFS]( https://azure.microsoft.com/pricing/details/storage/files/) для оценки.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>У. Сколько времени занимает процесс переноса данных?

A. Время переноса данных зависит от объема данных и выбранного варианта обновления.

## <a name="next-steps"></a>Следующие шаги

* [Перенос данных из серии StorSimple 8000 в синхронизацию файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
