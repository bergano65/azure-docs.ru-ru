---
title: Масштаб SAP HANA с резервным режимом с файлами Azure NetApp на RHEL Документы Майкрософт
description: Руководство по высокой доступности для SAP NetWeaver на Red Hat Enterprise Linux с файлами Azure NetApp для приложений SAP
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
ms.date: 01/10/2020
ms.author: radeltch
ms.openlocfilehash: ace30b044682cb4e62a0f81da90f21a77be1edd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372968"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Развертывание системы масштабирования SAP HANA с резервным узлам на VMs Azure с помощью файлов Azure NetApp на Red Hat Enterprise Linux 

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2455582
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


В этой статье описывается, как развернуть высокодоступную систему SAP HANA в масштабной конфигурации с резервным режимом на виртуальных машинах Azure Red Hat Enterprise Linux (VMs) с помощью [файлов Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) для общих объемов хранения.  

В конфигурациях примеров, команд установки и так далее экземпляр HANA равен **03,** а идентификатор системы HANA — **HN1.** Примеры основаны на HANA 2.0 SP4 и Red Hat Enterprise Linux для SAP 7.6. 

Прежде чем начать, обратитесь к следующим примечаниям и документам SAP:

* [Документация файлов Сети Приложений Azure][anf-azure-doc] 
* SAP Примечание [1928533] включает в себя:  
  * Список размеров Azure VM, которые поддерживаются для развертывания программного обеспечения SAP
  * важные сведения о доступных ресурсах для каждого размера виртуальной машины Azure;
  * сведения о поддерживаемом программном обеспечении SAP и сочетаниях операционных систем и баз данных;
  * Необходимая версия ядра SAP для Windows и Linux на Microsoft Azure
* SAP Note [2015553]: Списки предпосылок для развертывания программного обеспечения SAP, поддерживаемого SAP, в Azure
* SAP Примечание (2002167) рекомендовал настройки ОС для Red Hat Enterprise Linux
* примечание к SAP [2009879], содержащее рекомендации по SAP HANA для Red Hat Enterprise Linux;
* SAP Note [2178632]: Содержит подробную информацию обо всех показателях мониторинга, сообщаемых для SAP в Azure
* SAP Примечание [2191498]: Содержит необходимую версию агента SAP Host для Linux в Azure
* SAP Примечание [2243692]: Содержит информацию о лицензировании SAP на Linux в Azure
* SAP Note [1999351]: Содержит дополнительную информацию об устранении неполадок для расширенного мониторинга Azure для SAP
* SAP Примечание [1900823]: Содержит информацию о требованиях хранения SAP HANA
* [SAP Сообщество Вики](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Содержит все необходимые заметки SAP для Linux
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Linux в Azure][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide]
* Общая документация по RHEL
  * [Общие сведения о надстройке для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Администрирование надстройки для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Справочник по надстройке для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Предприятие Linux Руководство по сети](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Документация RHEL, специфичная для Azure:
  * [Установка SAP HANA в Red Hat Enterprise Linux для использования в Microsoft Azure](https://access.redhat.com/solutions/3193782)
* [Приложения NetApp SAP от Microsoft Azure с помощью файлов NetApp Azure][anf-sap-applications-azure]


## <a name="overview"></a>Обзор

Одним из методов достижения высокой доступности HANA является настройка автоматического сбоя. Чтобы настроить автоматическое сбой хоста, вы добавляете одну или несколько виртуальных машин в систему HANA и настраиваете их в резервные узлы. При сбой активного узла автоматически забирает резервный узла. В представленной конфигурации с виртуальными машинами Azure вы достигаете автоматического сбоя, используя [NFS на Файлах NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)  

> [!NOTE]
> Резервный узла требует доступа ко всем объемам баз данных. Объемы HANA должны быть установлены в виде объемов NFSv4. Улучшенный механизм блокировки файла на основе аренды в протоколе NFSv4 используется для `I/O` ограждения. 

> [!IMPORTANT]
> Чтобы создать поддерживаемую конфигурацию, необходимо развернуть данные и объемы журналов HANA в виде томов NFSv4.1 и смонтировать их с помощью протокола NFSv4.1. Конфигурация автоотказа hanA с резервным узелом не поддерживается NFSv3.

![Общие сведения о высоком уровне доступности SAP NetWeaver](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

На предыдущей диаграмме, которая соответствует рекомендациям сети SAP HANA, три подсети представлены в одной виртуальной сети Azure: 
* Для общения с клиентами
* Для связи с системой хранения
* Для внутренней межузловой связи HANA

Объемы NetApp Azure находятся в отдельной подсети, [делегированной файлам NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

Для этой конфигурации примера подсетями являются:  

  - `client`10.9.1.0/26  
  - `storage`10.9.3.0/26  
  - `hana`10.9.2.0/26  
  - `anf`10.9.0.0/26 (делегированная подсеть в файлы NetApp Azure)

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

   При развертывании томов не забудьте выбрать версию **NFSv4.1.** Развертывание томов в специально отведенной [подсети](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp Files. 
   
   Имейте в виду, что ресурсы Azure NetApp Files и MMs Azure должны находиться в той же виртуальной сети Azure или в заглянунных виртуальных сетях Azure. Например, HN1-data-mnt00001, **HN1**-log-mnt00001 и так далее, являются именами громкости и nfs://10.9.0.4/**HN1-data-mnt00001,** nfs://10.9.0.4/**HN1-log-mnt00001**и так далее, являются файловые пути для объемов файлов Azure NetApp Files. **HN1**  

   * объем **HN1**-данные-mnt00001 (nfs://10.9.0.4/**HN1**-data-mnt00001)
   * объем **HN1**-данные-mnt00002 (nfs://10.9.0.4/**HN1**-data-mnt00002)
   * объем **HN1**-log-mnt00001 (nfs://10.9.0.4/**HN1**-log-mnt00001)
   * объем **HN1**-log-mnt00002 (nfs://10.9.0.4/**HN1**-log-mnt00002)
   * объем **HN1**-общий (nfs://10.9.0.4/**HN1**-общий)
   
   В этом примере мы использовали отдельный том файлов Azure NetApp для каждого объема данных и журналов HANA. Для более оптимизированной для затрат конфигурации на небольших или непродуктивных системах можно разместить все крепления данных на одном томе, а все журналы монтируются на другом одном томе.  

### <a name="important-considerations"></a>Важные сведения

Создавая файлы Azure NetApp для масштабирования SAP HANA со сценарием стояния узлов, будьте в курсе следующих важных соображений:

- Бассейн минимальной вместимости составляет 4 тебибайта (TiB).  
- Минимальный размер объема составляет 100 гибибайт (GiB).
- Файлы Azure NetApp и все виртуальные машины, на которых будут установлены объемы файлов Azure NetApp, должны находиться в той же виртуальной сети Azure или в [виртуальных сетях](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) в том же регионе.  
- Выбранная виртуальная сеть должна иметь подсеть, делегированную файлам NetApp Azure.
- Пропускная часть объема файлов Azure NetApp является функцией квоты объема и уровня обслуживания, как это зафиксировано на [уровне обслуживания для файлов NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) При размере объемов NETApp HANA Azure убедитесь, что полученная пропускная связь соответствует требованиям системы HANA.  
- С [помощью экспортной политики](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)Azure NetApp Files вы можете управлять разрешенными клиентами, типом доступа (читать, читать только и так далее). 
- Функция Azure NetApp Files еще не осведомлена о зоне. В настоящее время функция не развернута во всех зонах доступности в регионе Azure. Имейте в виду потенциальные последствия задержки в некоторых регионах Azure.  

> [!IMPORTANT]
> Для рабочих нагрузок SAP HANA низкая задержка имеет решающее значение. Работайте с представителем корпорации Майкрософт, чтобы убедиться, что виртуальные машины и объемы файлов NetApp Azure развертываются в непосредственной близости.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Размер для базы данных HANA в файлах NetApp Azure

Пропускная часть объема файлов Azure NetApp зависит от размера объема и уровня обслуживания, как это зафиксировано на [уровне обслуживания для файлов NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 

При проектировании инфраструктуры для SAP в Azure следует знать о некоторых минимальных требованиях SAP к хранению, которые преобразуются в минимальные характеристики пропускной способности:

- Прочитайте на /hana/log 250 мегабайт в секунду (Mb/s) с размерами 1-Mb I/O.  
- Прочитайте активность не менее 400 МБ/с для /hana/data для размеров 16-Mb и 64-MB I/O.  
- Напишите активность не менее 250 МБ/с для /hana/data с размерами 16-МБ и 64-МБ вв/о. 

[Ограничения пропускной промедляя сети Azure NetApp на](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 1 TiB квоты объема:
- Премиум уровень хранения - 64 MiB/s  
- Ультра-уровень хранения - 128 MiB/s  

Для удовлетворения требований SAP минимальной пропускной способности данных и журнала, а также руководящих принципов для /hana/shared, рекомендуемые размеры будут:

| Том | Размер<br>Премиум уровень хранения | Размер<br>Ультра уровень хранения | Поддерживаемый протокол NFS |
| --- | --- | --- | --- |
| /hana/log/ | 4 ТиБ | 2 ТиБ | v4.1 |
| hana/data; | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | 1xRAM на 4 рабочих узла | 1xRAM на 4 рабочих узла | v3 или v4.1 |

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

   а. Используйте изображение Linux Red Hat Enterprise в галерее Azure, поддерживаемое для SAP HANA. В этом примере мы использовали изображение RHEL-SAP-HA 7.6.  

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
    
    д) Нажмите кнопку **Сохранить**. 
 
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
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **Добавить** сетевой маршрут, чтобы связь с файлами Azure NetApp проходила через сетевой интерфейс хранения данных.  

   В этом примере будет использоваться `Networkmanager` настройка дополнительного сетевого маршрута. Следующие инструкции предполагают, что `eth1`интерфейс сети хранения данных .  
   Во-первых, определите `eth1`имя соединения для устройства. В этом примере имя `eth1` `Wired connection 1`соединения для устройства.  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Затем назначаем дополнительный маршрут в делегированную сеть Azure NetApp Files через `eth1`.  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Перезагрузите VM для активации изменений.  

3. **Установка** клиентского пакета NFS.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **Подготовьте** ОС для запуска SAP HANA на Azure NetApp с Помощью NFS, как описано в [SAP HANA на NetApp AFF Systems с руководством по конфигурации NFS.](https://www.netapp.com/us/media/tr-4435.pdf) Создание файла конфигурации */etc/sysctl.d/netapp-hana.conf* для настроек конфигурации NetApp.  

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
    net.core.netdev_max_backlog = 300000 net.ipv4.tcp_slow_start_after_idle=0 net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **Создайте** файл конфигурации */etc/sysctl.d/ms-az.conf* с дополнительными настройками оптимизации.  

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

5. **Отрегулируйте** настройки sunrpc, как это рекомендовано в [SAP HANA на NetApp AFF Systems с помощью руководства по конфигурации NFS.](https://www.netapp.com/us/media/tr-4435.pdf)  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **Красная** шляпа для конфигурации HANA.

    Напередите RHEL, как описано в SAP Note [2292690], [2455582], [2593824] и <https://access.redhat.com/solutions/2447641>.

    > [!NOTE]
    > Если установка HANA 2.0 SP04 вам потребуется установить пакет, `compat-sap-c++-7` как описано в SAP примечание [2593824], прежде чем вы можете установить SAP HANA. 

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
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
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
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Для получения более подробной `nfs4_disable_idmapping` информации о том, как изменить параметр см. https://access.redhat.com/solutions/1749883

6. **Смонтировать** общие объемы файлов NetApp Azure.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **Смонтировать** узлы конкретных томов на **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **Смонтировать** узлы конкретных томов на **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **Смонтировать** узлы конкретных томов на **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **Удостоверьте,** что все тома HANA смонтированы с протокольной версией **NFSv4.**  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
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
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **(2), (3)** Изменение владельца SAP `data` HANA и `log` каталогов на **объявление hn1.**   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** Отвражайте брандмауэр временно, чтобы он не мешал установке HANA. Вы можете повторно включить его после установки HANA. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>Установка HANA

1. **Установите** SAP HANA, следуя инструкциям в [руководстве по установке и обновлению SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). В этом примере мы устанавливаем масштаб SAP HANA с мастером, одним работником и одним резервным узлом.  

   а. Запустите программу **hdblcm** из каталога программного обеспечения установки HANA. Используйте `internal_network` параметр и передайте адресное пространство для подсети, которая используется для внутренней межузловой связи HANA.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. В подсказке введите следующие значения:

     * Для **выбора действия:** введите **1** (для установки)
     * Для **дополнительных компонентов для установки**: введите **2, 3**
     * Для пути установки: нажмите Enter (по умолчанию /hana/shared)
     * Для **локального имени хоста**: нажмите Введите, чтобы принять значение по умолчанию
     * **Под Вы хотите добавить хосты в систему?**: введите **y**
     * Для **запятой разделенных имен хоста, чтобы добавить:** введите **hanadb2, hanadb3**
     * Для **имени корневого пользователя** :root»: нажмите Enter, чтобы принять значение по умолчанию
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
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **Добавьте** отображение хоста, чтобы гарантировать, что IP-адреса клиента используются для общения с клиентами. Добавьте `public_host_resolution`раздел и добавьте соответствующие IP-адреса из клиентской подсети.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **Перезапустите** SAP HANA для активации изменений.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **Убедитесь,** что интерфейс клиента будет использовать IP-адреса из `client` подсети для связи.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Для получения информации о том, как проверить конфигурацию, см. SAP Примечание [2183363 - Конфигурация внутренней сети SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

5. **Повторновключить** брандмауэр.  
   - Остановить HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - Повторное включение брандмауэра
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - Откройте необходимые порты брандмауэра

       > [!IMPORTANT]
       > Создание правил брандмауэра, чтобы позволить HANA межузловой связи и клиентского трафика. Требуемые порты перечислены в разделе [Порты TCP/IP для всех продуктов SAP](https://help.sap.com/viewer/ports). Следующие команды являются лишь примером. В этом сценарии с использованным системным номером 03.

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - Начало HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Чтобы оптимизировать SAP HANA для базового хранилища файлов Azure NetApp, установите следующие параметры SAP HANA:

   - `max_parallel_io_requests`**128 г.**
   - `async_read_submit` **on**
   - `async_write_submit_active` **on**
   - `async_write_submit_blocks`**все**

   Для получения дополнительной информации [см. SAP HANA на NetApp AFF Systems с NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf). 

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
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
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
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
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
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. Выполнить следующие команды в виде **adm hn1**на активном узлах, который в данном случае является **hanadb1:**  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Резервный узлы **hanadb3** возьмут на себя в качестве главного узла. Вот состояние ресурса после завершения теста на отказ:  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
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
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
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

   d. Опять же, убить имя сервера на в настоящее время активный мастер-узла (то есть, на **узлах hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Узлы **hanadb1** возобновят роль главного узла. После завершения теста на отказ, статус будет выглядеть следующим образом:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
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
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
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
* Дополнительные сведения об обеспечении высокого уровня доступности и планировании аварийного восстановления SAP HANA в Azure (крупные экземпляры) см. в [этой статье](hana-overview-high-availability-disaster-recovery.md).
* Чтобы узнать, как установить высокую доступность и планировать аварийное восстановление SAP HANA на Azure VMs, [см.][sap-hana-ha]
