---
title: Оценка вариантов переноса данных с устройств StorSimple серий 5000–7000 | Документация Майкрософт
description: Общие сведения о вариантах переноса данных с устройств StorSimple серий 5000–7000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: d51891c076f1784fbddb88bfaed28ac1d889afdc
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227769"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Варианты переноса данных с устройств StorSimple серий 5000–7000 

> [!IMPORTANT]
> On July 9, 2019 the StorSimple 5000/7000 series will reach end of support (EOS) status. Мы рекомендуем клиентам устройств StorSimple серий 5000 и 7000 перейти на один из альтернативных вариантов, описанных в этом документе.

[Поддержка](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) устройств StorSimple серий 5000–7000 будет прекращена в июле 2019 г. Клиенты, использующие StorSimple серий 5000–7000, имеют возможность перейти на другие гибридные службы Azure. В этой статье описываются гибридные варианты, доступные для переноса данных в Azure. 

## <a name="migration-options"></a>Варианты переноса

Для клиентов, использующих StorSimple серий 5000–7000, доступны варианты с использованием Azure или сторонних решений.

### <a name="azure-options"></a>Варианты с использованием Azure

#### <a name="upgrade-to-storsimple-8000-series"></a>Обновление до хранилища StorSimple серии 8000

Перейдите на хранилище StorSimple серии 8000 и продолжайте использовать платформу StorSimple.  Этот вариант обновления требует, чтобы пользователи заменили свои устройства серий 5000–7000 на устройства серии 8000. Перенос данных из устройств серий 5000–7000 происходит с помощью средства миграции. После успешного завершения миграции устройства StorSimple серии 8000 будут продолжать распределять данные по уровням в хранилище BLOB-объектов Azure. 

Дополнительные сведения о том, как перенести данные на устройство StorSimple серии 8000, см. в статье [Перенос данных с устройства StorSimple серий 5000–7000 на устройство серии 8000](storsimple-8000-migrate-from-5000-7000.md).

#### <a name="migrate-to-azure-file-sync"></a>Миграция в службу "Синхронизация файлов Azure"

Это совершенно новый вариант миграции, который позволяет клиентам хранить файловые ресурсы организации в службе "Синхронизация файлов Azure". Затем эти файловые ресурсы централизуются для локального доступа с помощью службы "Синхронизация файлов Azure" (AFS). AFS можно развернуть на узле Windows Server. Фактический перенос данных выполняется с помощью средства миграции или путем перемещения копии узла.

Дополнительные сведения о том, как перенести данные в службу "Синхронизация файлов Azure", см. в статье [Варианты переноса данных с устройств StorSimple серий 5000–7000](storsimple-5000-7000-afs-migration.md).

### <a name="third-party-options"></a>Сторонние решения

#### <a name="migrate-to-panzura-freedom-nas"></a>Перенос в Panzura Freedom NAS

Пользователи StorSimple 5000–7000 могут выполнить миграцию в Panzura Freedom NAS, чтобы хранить свои данные в Azure. Panzura Freedom предоставляет решение NAS, охватывающее центры обработки данных, филиалы, общедоступные и частные облака. Решение позволяет выполнять локальные, гибридные и облачные рабочие процессы с данными для NFS, SMB и мобильных клиентов. 

Такой вариант миграции поддерживается Panzura, и пользователи могут начать с создания запроса в техническую поддержку по миграции на веб-сайте [Panzura](https://panzura.com/storsimple-migration/).

#### <a name="migrate-to-cohesity"></a>Перенос в Cohesity

Cohesity позволяет выполнять миграцию данных из текущего хранилища StorSimple 5000–7000 на платформу данных Cohesity в Azure. Платформа данных Cohesity является программно-определяемым решением в масштабах Интернета, которое объединяет в себе файлы, резервные копии, объекты и виртуальные машины в одно облачное решение. После миграции на платформу данных можно выполнять управление, защиту, подготовку данных и приложений из облака в ядро с помощью панели из стекла. С помощью Cohesity появляется возможность начать сразу с трех узлов. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.


## <a name="migration---frequently-asked-questions"></a>Часто задаваемые вопросы о переносе

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>В. Когда наступает дата окончания поддержки для устройств StorSimple серий 5000 и 7000? 

О. [Дата окончания поддержки](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) устройств StorSimple серий 5000–7000 наступит в июле 2019 г. После июля 2019 года корпорация Майкрософт больше не сможет обеспечивать поддержку оборудования и программного обеспечения этих устройств. Мы настоятельно рекомендуем начать разработку плана по переносу данных с ваших устройств.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>В. Что происходит с данными, которые хранятся в Azure?  

О. Вы можете продолжать использовать данные в Azure после их перемещения в новую службу. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>В. Что происходит с данными, которые хранятся локально на устройстве StorSimple? 

О. Данные, которые хранятся локально на устройстве, можно скопировать в новую службу, как описано в документах о переносе.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>В. Что произойдет, если я хочу сохранить устройство StorSimple серии 5000 или 7000? 

О. Хотя службы могут продолжать работу, корпорация Майкрософт больше не будет обеспечивать поддержку оборудования и программного обеспечения. Для обеспечения непрерывности бизнес-процессов настоятельно рекомендуется выполнить миграцию.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>В. Какие варианты доступны для переноса данных с устройств StorSimple серий 5000–7000? 

О. В зависимости от сценария для пользователей устройств StorSimple серий 5000–7000 доступны следующие варианты переноса. 

 - **Обновление до хранилища StorSimple серии 8000**. Используйте этот вариант, если вы хотите продолжать работу на платформе StorSimple. 
 - **Перенос в службу "Синхронизация файлов Azure"** . Используйте этот вариант, если необходимо переключиться в собственный формат Azure. Службу "Синхронизация файлов Azure" можно использовать для централизованного управления файловыми ресурсами. 

Вы можете обратиться в службу поддержки Майкрософт, чтобы обсудить варианты переноса, которые не перечислены здесь.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>В. Поддерживается ли перенос данных в другие решения для хранения?

О. Да. Поддерживается перенос на другие решения для хранения с использованием копии узла данных.

### <a name="q-is-migration-supported-by-microsoft"></a>В. Поддерживается ли перенос корпорацией Майкрософт? 

О. Перенос данных с устройств серий 5000–7000 — полностью поддерживаемая операция. Более того, корпорация Майкрософт рекомендует обратиться в службу поддержки перед началом миграции. Сейчас миграции — это интерактивная операция. Если вы намерены перенести данные из устройств StorSimple серий 5000 –7000, [откройте запрос в службу поддержки](storsimple-8000-contact-microsoft-support.md).

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>В. Какова модель ценообразования для обоих вариантов миграции?

О. Стоимость переноса зависит от выбранного варианта. Хотя само перемещение бесплатно, если вы решите выполнить обновление до StorSimple серии 8000, будет включена плата за аппаратное устройство. 

Аналогичным образом при использовании службы "Синхронизация файлов Azure" может взиматься плата за подписку. В каждом случае клиентам также придется оплачивать текущие расходы на хранение. Для оценки стоимости обратитесь к следующим разделам: 
- [Цены на StorSimple](https://azure.microsoft.com/pricing/details/storsimple/)  
- [Цены на AFS]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>В.  Сколько времени занимает процесс переноса данных?

О. Время переноса данных зависит от объема данных и выбранного варианта обновления. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>В. Какова дата окончания поддержки StorSimple серии 8000?

О. Даты окончания поддержки StorSimple серии 8000 публикуется [здесь](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).


## <a name="next-steps"></a>Дальнейшие действия
 - [Перенос данных с устройства StorSimple серий 5000–7000 на устройство серии 8000](storsimple-8000-migrate-from-5000-7000.md).
 - [Migrate data from StorSimple 5000-7000 series to Azure File Sync](storsimple-5000-7000-afs-migration.md) (Перенос данных с устройств StorSimple серий 5000–7000 в службу "Синхронизация файлов Azure")
