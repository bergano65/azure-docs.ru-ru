---
title: Масштаб SAP HANA с резервным режимом с файлами Azure NetApp на SLES Документы Майкрософт
description: Руководство по высокой доступности для SAP NetWeaver на корпоративном сервере SUSE Linux с файлами Azure NetApp для приложений SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: radeltch
ms.openlocfilehash: b584341931299e408b596bd6a66d1da4580cfffe
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754776"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>Развертывание системы масштабирования SAP HANA с резервным узлами на VMs Azure с помощью файлов Azure NetApp на сервере SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


В этой статье описывается, как развернуть высокодоступную систему SAP HANA в масштабной конфигурации с резервным режимом на виртуальных машинах Azure (VMs) с помощью [файлов Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) для общих объемов хранения.  

В конфигурациях примеров, команд установки и так далее экземпляр HANA равен **03,** а идентификатор системы HANA — **HN1.** Примеры основаны на HANA 2.0 SP4 и SUSE Linux Enterprise Server для SAP 12 SP4. 

Прежде чем начать, обратитесь к следующим примечаниям и документам SAP:

* [Документация файлов Сети Приложений Azure][anf-azure-doc] 
* SAP Примечание [1928533] включает в себя:  
  * Список размеров Azure VM, которые поддерживаются для развертывания программного обеспечения SAP
  * важные сведения о доступных ресурсах для каждого размера виртуальной машины Azure;
  * сведения о поддерживаемом программном обеспечении SAP и сочетаниях операционных систем и баз данных;
  * Необходимая версия ядра SAP для Windows и Linux на Microsoft Azure
* SAP Note [2015553]: Списки предпосылок для развертывания программного обеспечения SAP, поддерживаемого SAP, в Azure
* SAP Примечание [2205917]: Содержит рекомендуемые настройки ОС для SUSE Linux Enterprise Server для SAP-приложений
* SAP Примечание [1944799]: Содержит руководящие принципы SAP для SUSE Linux Enterprise Server для SAP приложений
* SAP Note [2178632]: Содержит подробную информацию обо всех показателях мониторинга, сообщаемых для SAP в Azure
* SAP Примечание [2191498]: Содержит необходимую версию агента SAP Host для Linux в Azure
* SAP Примечание [2243692]: Содержит информацию о лицензировании SAP на Linux в Azure
* SAP Примечание [1984787]: Содержит общую информацию о SUSE Linux Enterprise Server 12
* SAP Note [1999351]: Содержит дополнительную информацию об устранении неполадок для расширенного мониторинга Azure для SAP
* SAP Примечание [1900823]: Содержит информацию о требованиях хранения SAP HANA
* [SAP Сообщество Вики](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Содержит все необходимые заметки SAP для Linux
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Linux в Azure][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide]
* [SUSE SAP HA Best Practice Guides][suse-ha-guide]: Содержит всю необходимую информацию для настройки NetWeaver High Availability и SAP HANA System Replication на месте (для использования в качестве общего базового; они предоставляют гораздо более подробную информацию)
* [Заметки о выпуске расширения SUSE высокого уровня доступности 12 SP3][suse-ha-12sp3-relnotes]
* [Приложения NetApp SAP от Microsoft Azure с помощью файлов NetApp Azure][anf-sap-applications-azure]


## <a name="overview"></a>Обзор

Одним из методов достижения высокой доступности HANA является настройка автоматического сбоя. Чтобы настроить автоматическое сбой хоста, вы добавляете одну или несколько виртуальных машин в систему HANA и настраиваете их в резервные узлы. При сбой активного узла автоматически забирает резервный узла. В представленной конфигурации с виртуальными машинами Azure вы достигаете автоматического сбоя, используя [NFS на Файлах NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)  

> [!NOTE]
> Резервный узла требует доступа ко всем объемам баз данных. Объемы HANA должны быть установлены в виде объемов NFSv4. Улучшенный механизм блокировки файла на основе аренды в протоколе NFSv4 используется для `I/O` ограждения. 

> [!IMPORTANT]
> Чтобы создать поддерживаемую конфигурацию, необходимо развернуть данные и объемы журналов HANA в виде томов NFSv4.1 и смонтировать их с помощью протокола NFSv4.1. Конфигурация автоотказа hanA с резервным узелом не поддерживается NFSv3.

![Общие сведения о высоком уровне доступности SAP NetWeaver](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

На предыдущей диаграмме, которая соответствует рекомендациям сети SAP HANA, три подсети представлены в одной виртуальной сети Azure: 
* Для общения с клиентами
* Для связи с системой хранения
* Для внутренней межузловой связи HANA

Объемы NetApp Azure находятся в отдельной подсети, [делегированной файлам NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

Для этой конфигурации примера подсетями являются:  

  - `client`10.23.0.0/24  
  - `storage`10.23.2.0/24  
  - `hana`10.23.3.0/24  
  - `anf`10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Настройка инфраструктуры файлов NetApp Azure 

Прежде чем приступить к настройке инфраструктуры Azure NetApp Files, ознакомьтесь с [документацией Файлов NetApp Azure.][anf-azure-doc] 

Файлы Сети Azure доступны в нескольких [регионах Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Проверьте, предлагает ли выбранный вами регион Azure файлы NetApp.  

Для получения информации о наличии файлов NetApp Azure в регионе Azure [см.][anf-avail-matrix]  

Перед развертыванием файлов Azure NetApp запросите на борт файлы Azure NetApp, [зарегистрировавшись для получения инструкций][anf-register]по файлам NetApp Azure. 

### <a name="deploy-azure-netapp-files-resources"></a>Развертывание ресурсов файлов NetApp Azure  

Следующие инструкции предполагают, что вы уже развернули [виртуальную сеть Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Ресурсы и виртуальные файлы Azure NetApp Files, на которых будут смонтированы ресурсы Файлов Нетapp Azure, должны быть развернуты в той же виртуальной сети Azure или в виртуальных сетях Azure.  

1. Если вы еще не развернули ресурсы, [запросите бортовые запросы в Файлы NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  

2. Создайте учетную запись NetApp в выбранном регионе Azure, следуя инструкциям в [создании учетной записи NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  

3. Нарань пула емкости файлов Сети Azure, следуя инструкциям в [настройке пула емки файлов Сети Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)  

   Архитектура HANA, представленная в этой статье, использует единый пул емкости файлов Azure NetApp на уровне *Ultra Service.* Для рабочих нагрузок HANA в Azure рекомендуется использовать [уровень файлов](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)Сети Ultra *или* *Premium.*  

4. Делегируйте подсеть в файлы NetApp Azure, как описано в инструкциях [по делегированию подсети в Файлы NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

5. Развертывание объемов файлов NetApp Azure, следуя инструкциям в [создании тома NFS для файлов NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)  

   При развертывании томов не забудьте выбрать версию **NFSv4.1.** В настоящее время доступ к NFSv4.1 требует дополнительного белого списка. Развертывание томов в специально отведенной [подсети](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp Files. 
   
   Имейте в виду, что ресурсы Azure NetApp Files и MMs Azure должны находиться в той же виртуальной сети Azure или в заглянунных виртуальных сетях Azure. Например, HN1-data-mnt00001, **HN1**-log-mnt00001 и так далее, являются именами громкости и nfs://10.23.1.5/**HN1-data-mnt00001,** nfs://10.23.1.4/**HN1-log-mnt00001**и так далее, являются файловые пути для объемов файлов Azure NetApp Files. **HN1**  

   * объем **HN1**-данные-mnt00001 (nfs://10.23.1.5/**HN1**-data-mnt00001)
   * объем **HN1**-данные-mnt00002 (nfs://10.23.1.6/**HN1**-данные-mnt00002)
   * объем **HN1**-log-mnt00001 (nfs://10.23.1.4/**HN1**-log-mnt00001)
   * объем **HN1**-log-mnt00002 (nfs://10.23.1.6/**HN1**-log-mnt00002)
   * объем **HN1**-общий (nfs://10.23.1.4/**HN1**-общий)
   
   В этом примере мы использовали отдельный том файлов Azure NetApp для каждого объема данных и журналов HANA. Для более оптимизированной для затрат конфигурации на небольших или непродуктивных системах можно разместить все крепления данных и все журналы на одном томе.  

### <a name="important-considerations"></a>Важные сведения

Создавая файлы Azure NetApp для SAP NetWeaver в архитектуре высокой доступности SUSE, будьте осведомлены о следующих важных соображениях:

- Бассейн минимальной вместимости составляет 4 тебибайта (TiB).  
- Минимальный размер объема составляет 100 гибибайт (GiB).
- Файлы Azure NetApp и все виртуальные машины, на которых будут установлены объемы файлов Azure NetApp, должны находиться в той же виртуальной сети Azure или в [виртуальных сетях](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) в том же регионе.  
- Выбранная виртуальная сеть должна иметь подсеть, делегированную файлам NetApp Azure.
- Пропускная часть объема файлов Azure NetApp является функцией квоты объема и уровня обслуживания, как это зафиксировано на [уровне обслуживания для файлов NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) При размере объемов NETApp HANA Azure убедитесь, что полученная пропускная связь соответствует требованиям системы HANA.  
- С [помощью экспортной политики](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)Azure NetApp Files вы можете управлять разрешенными клиентами, типом доступа (читать, читать только и так далее). 
- Функция Azure NetApp Files еще не осведомлена о зоне. В настоящее время функция не развернута во всех зонах доступности в регионе Azure. Имейте в виду потенциальные последствия задержки в некоторых регионах Azure.  
-  

> [!IMPORTANT]
> Для рабочих нагрузок SAP HANA низкая задержка имеет решающее значение. Работайте с представителем корпорации Майкрософт, чтобы убедиться, что виртуальные машины и объемы файлов NetApp Azure развертываются в непосредственной близости.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Размер для базы данных HANA в файлах NetApp Azure

Пропускная часть объема файлов Azure NetApp зависит от размера объема и уровня обслуживания, как это зафиксировано на [уровне обслуживания для файлов NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 

При проектировании инфраструктуры для SAP в Azure следует знать о некоторых минимальных требованиях SAP к хранению, которые преобразуются в минимальные характеристики пропускной способности:

- Включить чтение-запись на /hana/log 250 мегабайт в секунду (Mb/s) с 1-МБ ВВ/О размеров.  
- Включить считывание деятельности по крайней мере 400 МБ /с для /hana/data для 16-Mb и 64-MB I/O размеров.  
- Включить активность записи не менее 250 МБ/с для /hana/data с размерами 16-Mb и 64-MB I/O. 

[Ограничения пропускной промедляя сети Azure NetApp на](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 1 TiB квоты объема:
- Премиум уровень хранения - 64 MiB/s  
- Ультра-уровень хранения - 128 MiB/s  

Для удовлетворения требований SAP минимальной пропускной способности данных и журнала, а также руководящих принципов для /hana/shared, рекомендуемые размеры будут:

| Том | Размер<br>Премиум уровень хранения | Размер<br>Ультра уровень хранения | Поддерживаемый протокол NFS |
| --- | --- | --- | --- |
| /hana/log/ | 4 ТиБ | 2 ТиБ | v4.1 |
| hana/data; | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | Макс (512 ГБ, 1xRAM) на 4 рабочих узлов | Макс (512 ГБ, 1xRAM) на 4 рабочих узлов | v3 или v4.1 |

Конфигурация SAP HANA для макета, представленного в этой статье, с использованием уровня Azure NetApp Files Ultra Storage, будет:

| Том | Размер<br>Ультра уровень хранения | Поддерживаемый протокол NFS |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 ТиБ | v4.1 |
| /hana/log/mnt00002 | 2 ТиБ | v4.1 |
| /hana/data/mnt00001 | 3.2 TiB | v4.1 |
| /hana/data/mnt00002 | 3.2 TiB | v4.1 |
| /hana/shared | 2 ТиБ | v3 или v4.1 |

> [!NOTE]
> Приведенные здесь рекомендации по размеру файлов Сети Azure предназначены для удовлетворения минимальных требований, которые SAP рекомендует для поставщиков инфраструктуры. В реальных сценариях развертывания клиентов и рабочих нагрузок этих размеров может оказаться недостаточно. Используйте эти рекомендации в качестве отправной точки и адаптируйтесь в зависимости от требований вашей конкретной рабочей нагрузки.  

> [!TIP]
> Вы можете изменить размер объемов Файлов Azure NetApp динамически, не *разобревая* объемы, останавливая виртуальные машины или останавливая SAP HANA. Такой подход позволяет гибко удовлетворять как ожидаемые, так и непредвиденные потребности в пропускной связи приложения.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Развертывание виртуальных машин Linux через портал Azure

Сначала необходимо создать объемы файлов Сети Azure. Затем сделайте следующие шаги:
1. Создавайте [подсети виртуальной сети Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) в [виртуальной сети Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 
1. Развертывание VMs. 
1. Создайте дополнительные сетевые интерфейсы и прикрепите сетевые интерфейсы к соответствующим ВМ.  

   Каждая виртуальная машина имеет три сетевых интерфейса, которые`client` `storage` соответствуют трем подсетям виртуальных сетевых сетей Azure ( и `hana`). 

   Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)  

> [!IMPORTANT]
> Для рабочих нагрузок SAP HANA низкая задержка имеет решающее значение. Чтобы добиться низкой задержки, работайте с представителем корпорации Майкрософт, чтобы убедиться, что виртуальные машины и объемы файлов NetApp Azure будут развернуты в непосредственной близости. При [посадке на новую систему SAP HANA,](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) использующего файлы SAP HANa Azure NetApp, отправьте необходимую информацию. 
 
Следующие инструкции предполагают, `client` `storage` что вы уже создали группу ресурсов, виртуальную сеть Azure и `hana`три подсети виртуальной сети Azure: и . При развертывании вс-чаевых выберите клиентскую подсеть, чтобы интерфейс клиентской сети был основным интерфейсом на VMs. Вам также нужно будет настроить явный маршрут к файлам Azure NetApp, делегированной подсети через шлюз подсети хранения. 

> [!IMPORTANT]
> Убедитесь, что выбранная Вами ОС сертифицирована SAP для SAP HANA по конкретным типам VM, которые вы используете. Для получения списка сертифицированных типов VM и ОС SAP HANA для этих типов перейдите на сертифицированный сайт [платформ IaaS, сертифицированный SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Нажмите на детали перечисленного типа VM, чтобы получить полный список sAP HANA-поддерживаемых оС-релизов для этого типа.  

1. Создайте набор доступности для SAP HANA. Убедитесь в том, чтобы установить домен макс обновления.  

2. Создайте три виртуальных машины **(hanadb1,** **hanadb2,** **hanadb3**) делая следующие шаги:  

   а. Используйте изображение SLES4SAP в галерее Azure, поддерживаемое для SAP HANA. В этом примере мы использовали изображение SLES4SAP 12 SP4.  

   b. Выберите набор доступности, созданный ранее для SAP HANA.  

   c. Выберите клиентскую подсеть виртуальной сети Azure. Выберите [Ускоренную сеть](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  

   При развертывании виртуальных машин автоматически генерируется имя сетевого интерфейса. В этих инструкциях по простоте мы будем ссылаться на автоматически генерируемые сетевые интерфейсы, которые прикрепляются к клиенту Azure виртуальной сети подсети, как **hanadb1-клиент**, **hanadb2-клиент**, и **hanadb3-клиент**. 

3. Создайте три сетевых интерфейса, по `storage` одному для каждой виртуальной машины, для виртуальной сетевой подсети (в этом примере, **hanadb1-storage,** **hanadb2-storage**и **hanadb3-storage).**  

4. Создайте три сетевых интерфейса, по `hana` одному для каждой виртуальной машины, для виртуальной сетевой подсети (в этом примере **hanadb1-hana,** **hanadb2-hana**и **hanadb3-hana).**  

5. Прикрепите недавно созданные виртуальные сетевые интерфейсы к соответствующим виртуальным машинам, сделав следующие шаги:  

    а. Перейдите на виртуальную машину на [портале Azure](https://portal.azure.com/#home).  

    b. В левом стеле выберите **виртуальные машины.** Фильтр на имя виртуальной машины (например, **hanadb1),** а затем выберите виртуальную машину.  

    c. В панели **Обзора** выберите **Stop,** чтобы разложить виртуальную машину.  

    d. Выберите **Сеть**, а затем прикрепить сетевой интерфейс. В списке выпадающих **сетевых интерфейсов Attach** выберите `hana` уже созданные сетевые интерфейсы для подсетей и подсетей. `storage`  
    
    д) Щелкните **Сохранить**. 
 
    е) Повторите шаги b через e для остальных виртуальных машин (в нашем примере, **hanadb2** и **hanadb3).**
 
    ж. Оставьте виртуальные машины в остановленном состоянии на данный момент. Далее мы включим [ускоренную сеть](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) для всех недавно подключенных сетевых интерфейсов.  

6. Включите ускоренную сеть для дополнительных сетевых интерфейсов для `storage` подсетей и `hana` подсетей, выдыхая следующие действия:  

    а. Откройте [облачную оболочку Azure](https://azure.microsoft.com/features/cloud-shell/) на [портале Azure.](https://portal.azure.com/#home)  

    b. Выполняйте следующие команды, чтобы обеспечить ускоренную сеть для дополнительных `storage` `hana` сетевых интерфейсов, которые прикрепляются к подсетям и подсетям.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Запустите виртуальные машины, сделав следующие шаги:  

    а. В левом стеле выберите **виртуальные машины.** Фильтр на имя виртуальной машины (например, **hanadb1),** а затем выберите его.  

    b. В панели **обзора** выберите **Start**.  

## <a name="operating-system-configuration-and-preparation"></a>Конфигурация и подготовка операционной системы

Инструкции в следующих разделах прикрепляются к одному из следующих:
* **(А)**: Применимо ко всем узлам
* **(** Применяется только к узлам 1
* **(** Применяется только к узлам 2
* **(** применяется только к узлам 3

Настроить и подготовить ОС, сделав следующие шаги:

1. **Поддерживайте** файлы хоста на виртуальных машинах. Включите записи для всех подсетей. Для этого примера `/etc/hosts` были добавлены следующие записи.  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **Изменение** параметров DHCP и облачной конфигурации для сетевого интерфейса для хранения, чтобы избежать непреднамеренных изменений хост-имен.  

    Следующие инструкции предполагают, что `eth1`интерфейс сети хранения данных . 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **Добавить** сетевой маршрут, чтобы связь с файлами Azure NetApp проходила через сетевой интерфейс хранения данных.  

    Следующие инструкции предполагают, что `eth1`интерфейс сети хранения данных .  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    Перезагрузите VM для активации изменений.  

3. **Подготовьте** ОС для запуска SAP HANA на NetApp Systems с помощью NFS, как описано в [приложениях NetApp SAP на Microsoft Azure с помощью файлов NetApp Azure.][anf-sap-applications-azure] Создание файла конфигурации */etc/sysctl.d/netapp-hana.conf* для настроек конфигурации NetApp.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **Создайте** файл конфигурации */etc/sysctl.d/ms-az.conf* с microsoft для настроек конфигурации Azure.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

4. **Отрегулируйте** настройки sunrpc, как это рекомендуется в [приложениях NetApp SAP в Microsoft Azure с помощью файлов Azure NetApp.][anf-sap-applications-azure]  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Монтировать объемы файлов NetApp Azure

1. **Создайте** точки крепления для объемов базы данных HANA.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **Создайте** каталоги для узлов для /usr/sap на **HN1-shared.**  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **Проверить** настройки домена NFS. Убедитесь, что домен настроен как домен Azure NetApp **`defaultv4iddomain.com`** Files по умолчанию, т.е. отображение **настроено на никто.**  

    > [!IMPORTANT]
    > Убедитесь в том, чтобы `/etc/idmapd.conf` установить домен NFS на VM, чтобы **`defaultv4iddomain.com`** соответствовать конфигурации домена по умолчанию в Azure NetApp Files: . Если есть несоответствие между конфигурацией домена на клиенте NFS (т.е. VFS) и сервере NFS, т.е. конфигурацией Azure NetApp, то разрешения на файлы `nobody`в объемах Azure NetApp, установленные на VMs, будут отображаться как :  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **Проверить** `nfs4_disable_idmapping`. Она должна быть установлена на **Y**. Чтобы создать структуру `nfs4_disable_idmapping` каталога, где находится, выполните команду крепления. Вы не сможете вручную создать каталог под /sys/modules, потому что доступ зарезервирован для ядра / драйверов.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **Создайте** группу SAP HANA и пользователя вручную. Идентиматы для групповых sapsys и пользователь **hn1**adm должны быть установлены на те же идентиматы, которые предоставляются во время посадки. (В этом примере иные иудальные данные установлены на **1001**.) Если итоговые иудимы не установлены правильно, вы не сможете получить доступ к объемам. Идентиматы для групповых sapsys и учетных записей пользователей **hn1**adm и sapadm должны быть одинаковыми на всех виртуальных машинах.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **Смонтировать** общие объемы файлов NetApp Azure.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **Смонтировать** узлы конкретных томов на **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **Смонтировать** узлы конкретных томов на **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **Смонтировать** узлы конкретных томов на **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **Удостоверьте,** что все тома HANA смонтированы с протокольной версией **NFSv4.**  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Установка  

В этом примере для развертывания SAP HANA в масштабирующей конфигурации с резервным узлом с Azure мы использовали HANA 2.0 SP4.  

### <a name="prepare-for-hana-installation"></a>Подготовка к установке HANA

1. **Перед** установкой HANA установите корневой пароль. Вы можете отключить корневой пароль после завершения установки. Выполнение `root` в `passwd`качестве команды .  

2. **Убедитесь,** что вы можете войти в систему через SSH для **hanadb2** и **hanadb3**, без запроса на пароль.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **Установите** дополнительные пакеты, которые необходимы для HANA 2.0 SP4. Для получения дополнительной информации см. Примечание SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **(2), (3)** Изменение владельца SAP `data` HANA и `log` каталогов на **объявление hn1.**   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Установка HANA

1. **Установите** SAP HANA, следуя инструкциям в [руководстве по установке и обновлению SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). В этом примере мы устанавливаем масштаб SAP HANA с мастером, одним работником и одним резервным узлом.  

   а. Запустите программу **hdblcm** из каталога программного обеспечения установки HANA. Используйте `internal_network` параметр и передайте адресное пространство для подсети, которая используется для внутренней межузловой связи HANA.  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. В подсказке введите следующие значения:

     * Для **выбора действия:** введите **1** (для установки)
     * Для **дополнительных компонентов для установки**: введите **2, 3**
     * Для пути установки: нажмите Enter (по умолчанию /hana/shared)
     * Для **локального имени хоста**: нажмите Введите, чтобы принять значение по умолчанию
     * **Под Вы хотите добавить хосты в систему?**: введите **y**
     * Для **запятой разделенных имен хоста, чтобы добавить:** введите **hanadb2, hanadb3**
     * Для **имени корневого пользователя** :root»: нажмите Enter, чтобы принять значение по умолчанию
     * Для **корневого пароля пользователя**: введите пароль корневого пользователя
     * Для ролей для хоста hanadb2: введите **1** (для работника)
     * Для **Host Failover Group** для хоста hanadb2 «по умолчанию»: нажмите Enter, чтобы принять значение по умолчанию
     * Для **номера раздела хранения** для хоста hanadb2 (<<assign automatically>>): нажмите Enter, чтобы принять значение по умолчанию
     * Для **рабочей группы** для хоста hanadb2 «по умолчанию»: нажмите Введите, чтобы принять значение по умолчанию
     * Для **выбора ролей** для хоста hanadb3: введите **2** (для ожидания)
     * Для **Host Failover Group** для хоста hanadb3 «по умолчанию»: нажмите Enter, чтобы принять значение по умолчанию
     * Для **рабочей группы** для хоста hanadb3 «по умолчанию»: нажмите Enter, чтобы принять значение по умолчанию
     * Для **идентификатора системы SAP HANA:** введите **HN1**
     * Для **номера экземпляра** : введите **03**
     * Для **локальной группы принимающих работников** (по умолчанию): нажмите Enter, чтобы принять значение по умолчанию
     * Для **выбора системы использования / Введите индекс :** введите **4** (для пользовательских)
     * Для **определения местоположения объемов данных** (/hana/data/DATA/HN1): нажмите Enter, чтобы принять значение по умолчанию
     * Для **определения местоположения объемов журналов** (/hana/log/HN1): нажмите Enter, чтобы принять значение по умолчанию
     * Для **ограничения максимального распределения памяти?** Введите **n**
     * Для **имени хостатора сертификата для хоста hanadb1** (hanadb1): нажмите Enter, чтобы принять значение по умолчанию
     * Для **имени хостатора сертификата для хоста hanadb2** «hanadb2»: нажмите Введите, чтобы принять значение по умолчанию
     * Для **имени хостатора сертификата для хоста hanadb3** (hanadb3): нажмите Enter, чтобы принять значение по умолчанию
     * Для **системного администратора (hn1adm) Пароль:** введите пароль
     * Для **пользователя базы данных системы (системы) Пароль**: введите пароль системы
     * Для **подтверждения системы базы данных пользователя (системы) Пароль**: введите пароль системы
     * Для **перезагрузки системы после перезагрузки машины?** Введите **n** 
     * **Для Вы хотите продолжить (y/n)**: проверить резюме, и если все выглядит хорошо, введите **y**


2. **Проверить** global.ini  

   Отображение global.ini и убедитесь, что конфигурация для внутренней межузловой связи SAP HANA на месте. Проверьте раздел **связи.** Он должен иметь адресное `hana` пространство `listeninterface` для подсети, и должен быть установлен на `.internal`. Проверьте **раздел internal_hostname_resolution.** Он должен иметь IP-адреса для виртуальных машин `hana` HANA, которые принадлежат к подсети.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **Добавьте** отображение хоста, чтобы гарантировать, что IP-адреса клиента используются для общения с клиентами. Добавьте `public_host_resolution`раздел и добавьте соответствующие IP-адреса из клиентской подсети.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **Перезапустите** SAP HANA для активации изменений.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **Убедитесь,** что интерфейс клиента будет использовать IP-адреса из `client` подсети для связи.  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Для получения информации о том, как проверить конфигурацию, см. SAP Примечание [2183363 - Конфигурация внутренней сети SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

6. Чтобы оптимизировать SAP HANA для базового хранилища файлов Azure NetApp, установите следующие параметры SAP HANA:

   - `max_parallel_io_requests`**128 г.**
   - `async_read_submit`**на**
   - `async_write_submit_active`**на**
   - `async_write_submit_blocks`**все**

   Для получения дополнительной информации смотрите [приложения NetApp SAP на Microsoft Azure с помощью файлов NetApp Azure.][anf-sap-applications-azure] 

   Начиная с систем SAP HANA 2.0, вы `global.ini`можете установить параметры в . Для получения дополнительной информации см. SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Для системных версий SPS12 SPS12 и ранее эти параметры могут быть установлены во время установки, как описано в SAP Note [2267798.](https://launchpad.support.sap.com/#/notes/2267798)  

7. Хранилище, используемое файлами Azure NetApp, имеет ограничение размера файла в 16 терабайт (ТБ). SAP HANA не неявно осведомлена об ограничении хранилища и не будет автоматически создавать новый файл данных при установлении предела размера файла в 16 ТБ. Поскольку SAP HANA пытается увеличить количество файлов за 16 ТБ, эта попытка приведет к ошибкам и, в конечном итоге, к сбою сервера индекса. 

   > [!IMPORTANT]
   > Чтобы предотвратить попытки SAP HANA увеличить объем файлов данных за пределы [16-ТБ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) подсистемы хранения данных, установите следующие параметры в `global.ini`.  
   > - datavolume_striping - правда
   > - datavolume_striping_size_gb 15000 Для получения дополнительной информации см. SAP Примечание [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Будьте в курсе SAP Примечание [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Тест SAP HANA сбой 

1. Имитировать аварию узла на рабочем узлах SAP HANA. Выполните следующие действия. 

   а. Перед тем, как смоделировать сбой узла, запустите следующие команды в качестве adm **hn1,** чтобы захватить состояние среды:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Чтобы имитировать сбой узла, запустите следующую команду в качестве корня на узло рабочего, который в этом случае является **hanadb2:**  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Мониторинг системы для завершения сбоя. Когда сбой завершен, захватстатус, который должен выглядеть следующим образом:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Когда узла переживаешь паника ядра, избегайте `kernel.panic` задержек с сбой SAP HANA, установив до 20 секунд на *всех* виртуальных машинах HANA. Конфигурация выполнена `/etc/sysctl`в . Перезагрузите виртуальные машины, чтобы активировать изменение. Если это изменение не выполняется, отказ может занять 10 или более минут, когда узла испытывает паника ядра.  

2. Убейте сервер имен, сделав следующее:

   а. Перед тестом проверьте состояние среды, запустив следующие команды в виде **adm hn1:**  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. Выполнить следующие команды в виде **adm hn1**на активном узлах, который в данном случае является **hanadb1:**  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Резервный узлы **hanadb3** возьмут на себя в качестве главного узла. Вот состояние ресурса после завершения теста на отказ:  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
        # Check the landscape status
        python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
        | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
        |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
        |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
        | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
        | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
        | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
        | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Перезапустите экземпляр HANA на **hanadb1** (т.е. на той же виртуальной машине, где был убит сервер имен). Узла **hanadb1** воссоединит окружающую среду и сохранит свою роль в режиме ожидания.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   После того, как SAP HANA начал на **hanadb1**, ожидать следующего статуса:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Опять же, убить имя сервера на в настоящее время активный мастер-узла (то есть, на **узлах hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Узлы **hanadb1** возобновят роль главного узла. После завершения теста на отказ, статус будет выглядеть следующим образом:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   д) Запустите SAP HANA на **hanadb3**, который будет готов служить в качестве резервного узла.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   После того, как SAP HANA начал на **hanadb3**, статус выглядит следующим образом:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Дальнейшие действия

* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Azure][deployment-guide]
* [Развертывание СУБД виртуальных машин Azure для SAP][dbms-guide]
* Чтобы узнать, как установить высокую доступность и планировать аварийное восстановление SAP HANA на Azure VMs, [см.][sap-hana-ha]
