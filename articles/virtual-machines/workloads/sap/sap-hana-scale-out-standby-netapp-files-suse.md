---
title: Развертывание SAP HANA масштабируемой системы с резервным узлом на виртуальных машинах Azure с помощью Azure NetApp Files на SUSE Linux Enterprise Server | Документация Майкрософт
description: Рекомендации по обеспечению высокого уровня доступности для SAP NetWeaver на SUSE Linux Enterprise Server с Azure NetApp Files для приложений SAP
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
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 7fb7294cc6f7918b4c6a3afa9e3c9dc7f44504e1
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014950"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>Развертывание SAP HANA масштабируемой системы с резервным узлом на виртуальных машинах Azure с помощью Azure NetApp Files на SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


В этой статье описывается, как развернуть высокодоступную систему SAP HANA в конфигурации с горизонтальным масштабированием в режиме ожидания на виртуальных машинах Azure с помощью [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) для томов общего хранилища.  

В примерах конфигураций, команд установки и т. д. экземпляр HANA имеет значение **03** , а идентификатор системы Hana — **HN1**. Примеры основаны на HANA 2,0 SP4 и SUSE Linux Enterprise Server для SAP 12 SP4. 

Прежде чем начать, ознакомьтесь со следующими примечаниями и документацией по SAP:

* [Документация по Azure NetApp Files][anf-azure-doc] 
* Примечание SAP [1928533] включает в себя:  
  * Список размеров виртуальных машин Azure, которые поддерживаются при развертывании программного обеспечения SAP.
  * важные сведения о доступных ресурсах для каждого размера виртуальной машины Azure;
  * сведения о поддерживаемом программном обеспечении SAP и сочетаниях операционных систем и баз данных;
  * Требуемая версия ядра SAP для Windows и Linux на Microsoft Azure
* Примечание SAP [2015553]. список необходимых компонентов для развертываний программного обеспечения SAP, поддерживаемых SAP в Azure
* Примечание SAP [2205917]: содержит рекомендуемые параметры ос для SuSE Linux Enterprise Server для приложений SAP.
* Примечание SAP [1944799]: содержит рекомендации sap для SuSE Linux Enterprise Server для приложений SAP.
* Примечание SAP [2178632]: содержит подробные сведения обо всех метриках мониторинга, сообщаемых для SAP в Azure.
* Примечание SAP [2191498]: содержит обязательную версию агента узла SAP для Linux в Azure.
* Примечание SAP [2243692]: содержит сведения о лицензировании SAP в Linux в Azure.
* Примечание SAP [1984787]: содержит общие сведения о SUSE Linux Enterprise Server 12
* Примечание SAP [1999351]. содержит дополнительные сведения об устранении неполадок для расширения расширенного мониторинга Azure для SAP.
* Примечание SAP [1900823]: содержит сведения о требованиях к хранилищу SAP HANA
* [Вики-сайт сообщества SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)содержит все необходимые примечания SAP для Linux.
* [Планирование и реализация виртуальных машин Azure для SAP в Linux][planning-guide]
* [Развертывание виртуальных машин Azure для SAP в Linux][deployment-guide]
* [Развертывание СУБД на виртуальных машинах Azure для SAP в Linux][dbms-guide]
* [Рекомендации по использованию SUSE SAP с рекомендациями][suse-ha-guide]: содержит все необходимые сведения для настройки высокой доступности и SAP HANA репликации системы в локальной среде (для использования в качестве общего базового показателя). они предоставляют более подробную информацию.
* [Заметки о выпуске расширения высокой доступности SUSE 12 SP3][suse-ha-12sp3-relnotes]
* [NetApp приложений SAP на Microsoft Azure с помощью Azure NetApp Files][anf-sap-applications-azure]
* [SAP HANA в системах NetApp с сетевой файловой системой (NFS)](https://www.netapp.com/us/media/tr-4435.pdf): руководство по настройке, в котором содержатся сведения о настройке SAP HANA с помощью Azure NFS на платформе NetApp


## <a name="overview"></a>Обзор

Одним из способов достижения высокого уровня доступности HANA является Настройка автоматической отработки отказа узла. Чтобы настроить автоматическую отработку отказа узла, добавьте одну или несколько виртуальных машин в систему HANA и настройте их в качестве резервных узлов. При сбое активного узла происходит автоматический переход на резервный узел. В представленной конфигурации с виртуальными машинами Azure вы достигают автоматической отработки отказа с помощью [NFS на Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

> [!NOTE]
> Для резервного узла требуется доступ ко всем томам базы данных. Тома HANA должны быть подключены как тома NFSv4. Улучшенный механизм блокировки на основе аренды файлов в протоколе NFSv4 используется для `I/O` ограждения. 

> [!IMPORTANT]
> Чтобы создать поддерживаемую конфигурацию, необходимо развернуть тома HANA и log в виде томов Нфсв 4.1 и подключить их с помощью протокола Нфсв 4.1. Конфигурация автоматической отработки отказа узла HANA с резервным узлом не поддерживается для NFSv3.

![Общие сведения о высоком уровне доступности SAP NetWeaver](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

На предыдущей схеме, которая соответствует SAP HANA рекомендациях по сети, три подсети представлены в одной виртуальной сети Azure: 
* Для связи с системой хранения данных
* Для внутреннего обмена данными между узлами HANA
* Для связи с клиентом

Тома NetApp для Azure находятся в отдельной подсети, [делегированной Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

Для этого примера конфигурации подсети:  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Настройка инфраструктуры Azure NetApp Files 

Прежде чем продолжить настройку Azure NetApp Files инфраструктуры, ознакомьтесь с [документацией по Azure NetApp Files][anf-azure-doc]. 

Azure NetApp Files доступен в нескольких [регионах Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Проверьте, предлагает ли выбранный регион Azure Azure NetApp Files.  

Сведения о доступности Azure NetApp Files по регионам Azure см. в разделе [доступность Azure NetApp Files по регионам Azure][anf-avail-matrix].  

Перед развертыванием Azure NetApp Files запросите подключение к Azure NetApp Files, перейдя к [инструкциям Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Развертывание ресурсов Azure NetApp Files  

В следующих инструкциях предполагается, что вы уже развернули [виртуальную сеть Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Azure NetApp Files ресурсы и виртуальные машины, на которых будут подключены Azure NetApp Files ресурсы, должны быть развернуты в одной виртуальной сети Azure или в одноранговых виртуальных сетях Azure.  

1. Если вы еще не развернули ресурсы, запросите подключение [к Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Создайте учетную запись NetApp в выбранном регионе Azure, следуя инструкциям в разделе [Создание учетной записи NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Настройте пул емкости Azure NetApp Files, следуя инструкциям в разделе [Настройка пула емкости Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  

   Архитектура HANA, представленная в этой статье, использует один пул емкости Azure NetApp Files на уровне *Ultra Service* . Для рабочих нагрузок HANA в Azure рекомендуется использовать [уровень обслуживания](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)Azure NetApp Files *Ultra* или *Premium* .  

4. Делегируйте подсеть Azure NetApp Files, как описано в инструкциях в разделе [Делегирование подсети для Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Разверните Azure NetApp Files тома, следуя инструкциям в разделе [Создание тома NFS для Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  

   При развертывании томов обязательно выберите версию **нфсв 4.1** . В настоящее время доступ к Нфсв 4.1 требует дополнительных список разрешений. Разверните тома в указанной [подсети](https://docs.microsoft.com/rest/api/virtualnetwork/subnets) Azure NetApp Files. 
   
   Помните, что ресурсы Azure NetApp Files и виртуальные машины Azure должны находиться в одной виртуальной сети Azure или в одноранговых виртуальных сетях Azure. Например, **HN1**-Data-Mnt00001, **HN1**-log-mnt00001 и т. д. — это имена томов и NFS://10.23.1.5/**HN1**-Data-mnt00001, NFS://10.23.1.4/**HN1**-log-mnt00001, и т. д. — это пути к файлам для Azure NetApp Filesных томов.  

   * Volume **HN1**-Data-mnt00001 (NFS://10.23.1.5/**HN1**-Data-mnt00001)
   * Volume **HN1**-Data-mnt00002 (NFS://10.23.1.6/**HN1**-Data-mnt00002)
   * Volume **HN1**-log-mnt00001 (NFS://10.23.1.4/**HN1**-log-mnt00001)
   * Volume **HN1**-log-mnt00002 (NFS://10.23.1.6/**HN1**-log-mnt00002)
   * Volume **HN1**-Shared (NFS://10.23.1.4/**HN1**— Shared)
   
   В этом примере мы использовали отдельный том Azure NetApp Files для каждого тома HANA и данных журнала. Для более производительной конфигурации, оптимизированной для небольших или непродуктивных систем, можно разместить все подключения к данным и все журналы на одном томе.  

### <a name="important-considerations"></a>Важные сведения

При создании Azure NetApp Files для SAP NetWeaver в архитектуре высокого уровня доступности SUSE учитывайте следующие важные моменты.

- Пул минимальных мощностей — 4 тебибитес (тиб).  
- Минимальный размер тома — 100 гибибайтах (гиб).
- Azure NetApp Files и все виртуальные машины, на которых будут подключаться Azure NetApp Filesные тома, должны находиться в одной виртуальной сети Azure или в [одноранговой виртуальной сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) в том же регионе.  
- Выбранная виртуальная сеть должна иметь подсеть, делегированную Azure NetApp Files.
- Пропускная способность тома Azure NetApp Files является функцией квоты тома и уровня обслуживания, как описано в статье [об уровне обслуживания для Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). При определении размера томов HANA Azure NetApp убедитесь, что полученная пропускная способность соответствует требованиям к системе HANA.  
- С помощью [политики экспорта](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)Azure NetApp Files можно управлять разрешенными клиентами, типом доступа (чтение и запись, только чтение и т. д.). 
- Функция Azure NetApp Files еще не поддерживает зоны. Сейчас эта функция не развернута во всех зонах доступности в регионе Azure. Учитывайте возможные последствия задержки в некоторых регионах Azure.  
-  

> [!IMPORTANT]
> Для SAP HANA рабочих нагрузок низкая задержка очень важна. Поработайте с представителями корпорации Майкрософт, чтобы убедиться, что виртуальные машины и Azure NetApp Filesные тома развернуты в близком близком отношении.  

> [!IMPORTANT]
> Идентификатор пользователя для **SID**ADM и идентификатор группы для `sapsys` на виртуальных машинах должны соответствовать конфигурации в Azure NetApp Files. В случае несоответствия между идентификаторами виртуальных машин и конфигурацией Azure NetApp разрешения на доступ к файлам на томах NetApp в Azure, подключенных к виртуальным машинам, будут отображаться как `nobody`. При подключении [новой системы](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) для Azure NetApp Files не забудьте указать правильные идентификаторы.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Изменение размера базы данных HANA на Azure NetApp Files

Пропускная способность Azure NetApp Filesого тома — это функция размера тома и уровня обслуживания, как описано в статье [об уровне обслуживания для Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

При проектировании инфраструктуры для SAP в Azure необходимо учитывать некоторые минимальные требования к хранилищу SAP, которые переводятся в характеристики минимальной пропускной способности.

- Включите чтение и запись в/Hana/log с 250 МБ в секунду (МБ/с) с размером операций ввода-вывода в 1 МБ.  
- Включите операцию чтения не менее 400 МБ/с для/Hana/Data для размеров операций ввода-вывода 16 МБ и 64-МБ.  
- Включите действие записи не менее 250 МБ/с для/Hana/Data с размерами операций ввода-вывода 16 МБ и 64-МБ. 

[Azure NetApp Files ограничения пропускной способности](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) в 1 Тиб квоты тома:
- Уровень хранилища Premium-64 MiB/s  
- Ultra Storage уровня — 128 MiB/с  

Чтобы удовлетворить минимальные требования к пропускной способности SAP для данных и журналов, а также рекомендации для/Hana/Shared, рекомендуется использовать следующие размеры:

| Том | Размер<br>Уровень хранилища Premium | Размер<br>Уровень Ultra Storage | Поддерживаемый протокол NFS |
| --- | --- | --- | --- |
| /Hana/log | 4 ТиБ | 2 тиб | Версия 4.1 |
| hana/data; | 6,3 тиб | 3,2 тиб | Версия 4.1 |
| /hana/shared | Максимум (512 ГБ, 1xRAM) на 4 рабочих узла | Максимум (512 ГБ, 1xRAM) на 4 рабочих узла | V3 или версия 4.1 |

Конфигурация SAP HANA для макета, представленного в этой статье с использованием Azure NetApp Files Ultra Storage уровня, будет выглядеть следующим образом:

| Том | Размер<br>Уровень Ultra Storage | Поддерживаемый протокол NFS |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 тиб | Версия 4.1 |
| /hana/log/mnt00002 | 2 тиб | Версия 4.1 |
| /hana/data/mnt00001 | 3,2 тиб | Версия 4.1 |
| /hana/data/mnt00002 | 3,2 тиб | Версия 4.1 |
| /hana/shared | 2 тиб | V3 или версия 4.1 |

> [!NOTE]
> Azure NetApp Files рекомендации по выбору размера, указанные здесь, предназначены для удовлетворения минимальных требований, которые SAP рекомендует для поставщиков инфраструктуры. В реальных развертываниях клиентов и сценариях рабочей нагрузки эти размеры могут быть недостаточно. Используйте эти рекомендации в качестве отправной точки и адаптируйте их в зависимости от требований конкретной рабочей нагрузки.  

> [!TIP]
> Вы можете динамически изменять размер Azure NetApp Files томов без *отключения* томов, отключения виртуальных машин или завершения SAP HANA. Такой подход обеспечивает гибкость для удовлетворения ожидаемых и непредвиденных требований к пропускной способности приложения.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Развертывание виртуальных машин Linux с помощью портал Azure

Сначала необходимо создать Azure NetApp Files тома. Выполните следующие действия.
1. Создайте [подсети виртуальной сети Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) в [виртуальной сети Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). 
1. Разверните виртуальные машины. 
1. Создайте дополнительные сетевые интерфейсы и Подключите сетевые интерфейсы к соответствующим виртуальным машинам.  

   Каждая виртуальная машина имеет три сетевых интерфейса, которые соответствуют трем подсетям виртуальной сети Azure (`storage`, `hana`и `client`). 

   Дополнительные сведения см. [в статье Создание виртуальной машины Linux в Azure с несколькими сетевыми картами](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> Для SAP HANA рабочих нагрузок низкая задержка очень важна. Чтобы обеспечить низкую задержку, обратитесь к своему специалисту корпорации Майкрософт, чтобы убедиться, что виртуальные машины и Azure NetApp Filesные тома развернуты в близком близком отношении. При [адаптации новой SAP HANAной системы](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) , которая использует SAP HANA Azure NetApp Files, отправьте необходимые сведения. 
 
В следующих инструкциях предполагается, что вы уже создали группу ресурсов, виртуальную сеть Azure и три подсети виртуальной сети Azure: `storage`, `hana`и `client`. При развертывании виртуальных машин выберите подсеть хранилища, чтобы сетевой интерфейс хранилища был основным интерфейсом на виртуальных машинах. Если это невозможно, настройте явный маршрут к Azure NetApp Files делегированной подсети через шлюз подсети хранилища. 

> [!IMPORTANT]
> Убедитесь, что выбранная ОС является сертифицированной SAP для SAP HANA на конкретных типах виртуальных машин, которые вы используете. Список сертифицированных типов виртуальных машин и выпусков ОС для этих типов SAP HANA см. на веб-сайте [SAP HANA сертифицированные платформы IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Щелкните сведения о списке типа виртуальной машины, чтобы получить полный список поддерживаемых SAP HANA выпусков ОС для этого типа.  

1. Создайте группу доступности для SAP HANA. Убедитесь, что задан максимальный домен обновления.  

2. Создайте три виртуальные машины (**hanadb1**, **hanadb2**, **hanadb3**), выполнив следующие действия.  

   a. Используйте образ SLES4SAP в коллекции Azure, который поддерживается для SAP HANA. В этом примере мы использовали образ с пакетом обновления 4 (SP4) SLES4SAP 12.  

   Б. Выберите группу доступности, созданную ранее для SAP HANA.  

   c. Выберите подсеть виртуальной сети Azure хранилища. Выберите [Ускоренная сеть](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  

   При развертывании виртуальных машин имя сетевого интерфейса создается автоматически. Мы будем называть сетевые интерфейсы, подключенные к подсети хранилища виртуальной сети Azure, как **hanadb1-Storage**, **hanadb2**и **hanadb3-Storage**. 

3. Создайте три сетевых интерфейса, по одному для каждой виртуальной машины, для подсети `hana` виртуальной сети (в этом примере это **hanadb1-Hana**, **hanadb2-Hana**и **hanadb3-Hana**).  

4. Создайте три сетевых интерфейса, по одному для каждой виртуальной машины, для подсети `client` виртуальной сети (в этом примере это **hanadb1-Client**, **hanadb2-Client**и **hanadb3-Client**).  

5. Подключите только что созданные виртуальные сетевые интерфейсы к соответствующим виртуальным машинам, выполнив следующие действия.  

    a. Перейдите к виртуальной машине в [портал Azure](https://portal.azure.com/#home).  

    Б. В левой области выберите **виртуальные машины**. Выполните фильтрацию по имени виртуальной машины (например, **hanadb1**), а затем выберите виртуальную машину.  

    c. В области **Обзор** выберите пункт **прерывать** , чтобы освободить виртуальную машину.  

    d. Выберите **сеть**, а затем подключите сетевой интерфейс. В раскрывающемся списке **Подключить сетевой интерфейс** выберите уже созданные сетевые интерфейсы для подсетей `hana` и `client`.  
    
    д. Щелкните **Сохранить**. 
 
    f. Повторите шаги с b по e для оставшихся виртуальных машин (в нашем примере это **hanadb2** и **hanadb3**).
 
    g. Оставить виртуальные машины в остановленном состоянии сейчас. Далее мы разберем функцию [ускорения сети](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) для всех вновь подключенных сетевых интерфейсов.  

6. Включите ускоренную сеть для дополнительных сетевых интерфейсов `hana` и `client` подсетей, выполнив следующие действия.  

    a. Откройте [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) в [портал Azure](https://portal.azure.com/#home).  

    Б. Выполните следующие команды, чтобы включить ускоренную сеть для дополнительных сетевых интерфейсов, подключенных к `hana` и `client` подсетям.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>

7. Запустите виртуальные машины, выполнив следующие действия.  

    a. В левой области выберите **виртуальные машины**. Выполните фильтрацию по имени виртуальной машины (например, **hanadb1**), а затем выберите ее.  

    Б. В области **Обзор** выберите **Запуск**.  

## <a name="operating-system-configuration-and-preparation"></a>Настройка и подготовка операционной системы

Инструкции в следующих разделах начинаются с одной из следующих версий:
* **[A]** : применимо ко всем узлам
* **[1]** : применимо только к узлу 1
* **[2]** : применимо только к узлу 2
* **[3]** : применимо только к узлу 3

Настройте и подготовьте ОС, выполнив следующие действия.

1. **[A]** сохраните файлы узлов на виртуальных машинах. Включить записи для всех подсетей. В `/etc/hosts` для этого примера были добавлены следующие записи.  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** измените параметры DHCP и Cloud Configuration, чтобы избежать непреднамеренного изменения имени узла.  

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** ПОДготовьте операционную систему для запуска SAP HANA в системах NetApp с помощью NFS, как описано в разделе [SAP HANA в NetApp АФФ Systems with NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf). Создайте файл конфигурации */ЕТК/сисктл.д/нетапп-хана.конф* для параметров конфигурации NetApp.  

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

4. **[A]** создайте файл конфигурации */ЕТК/сисктл.д/МС-аз.конф* с параметром конфигурации Microsoft для Azure.  

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

4. **[A]** настройте параметры сунрпк, как это было рекомендовано в разделе [SAP HANA on NetApp АФФ Systems by NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf).  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Подключение томов Azure NetApp Files

1. **[A]** Создайте точки подключения для томов базы данных Hana.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Создайте каталоги узлов для/usr/SAP на **HN1**-Shared.  

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

3. **[A]** проверьте параметр домена NFS. Убедитесь, что домен настроен как **`localdomain`** и для сопоставления установлено значение **никто**.  

    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** отключить сопоставление идентификаторов NFSv4. Чтобы создать структуру каталогов, в которой находится `nfs4_disable_idmapping`, выполните команду mount. Вы не сможете вручную создать каталог в/СИС/модулес, так как доступ зарезервирован для ядра или драйверов.  

    <pre><code>
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>`

5. **[A]** создайте группу SAP HANA и пользователя вручную. Для идентификаторов групп sapsys и User **HN1**ADM должны быть заданы одинаковые идентификаторы, которые предоставляются во время адаптации. (В этом примере идентификаторы имеют значение **1001**.) Если идентификаторы не заданы правильно, доступ к томам будет невозможен. Идентификаторы групп sapsys и учетных записей пользователей **HN1**ADM и sapadm должны быть одинаковыми на всех виртуальных машинах.  

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

6. **[A]** подключите общие Azure NetApp Files тома.  

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

7. **[1]** Подключите тома конкретного узла к **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Подключите тома конкретного узла к **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Подключите тома конкретного узла к **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** убедитесь, что все тома Hana подключены с помощью протокола NFS версии **NFSv4**.  

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

В этом примере для развертывания SAP HANA в конфигурации с горизонтальным масштабированием с резервным узлом Azure мы использовали HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Подготовка к установке HANA

1. **[A]** перед установкой Hana задайте корневой пароль. После завершения установки можно отключить корневой пароль. Команда EXECUTE AS `root` `passwd`.  

2. **[1]** убедитесь, что вы можете войти с помощью SSH в **hanadb2** и **hanadb3**без запроса пароля.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** установите дополнительные пакеты, необходимые для Hana 2,0 SP4. Дополнительные сведения см. в разделе SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2], [3]** Измените владельца SAP HANA `data` и каталогов `log` на **HN1**ADM.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Установка HANA

1. **[1]** установите SAP Hana, следуя инструкциям из руководства по [установке и обновлению SAP HANA 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). В этом примере мы устанавливаем SAP HANA горизонтальное масштабирование с помощью главного, одного рабочего и одного резервного узла.  

   a. Запустите программу **hdblcm** из каталога с установочным программным обеспечением Hana. Используйте параметр `internal_network` и передайте адресное пространство для подсети, используемое для обмена данными между узлами HANA.  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   Б. В командной строке введите следующие значения:

     * Для **выберите действие**: введите **1** (для установки).
     * Дополнительные **компоненты для установки**: введите **2, 3**
     * Для пути установки: нажмите клавишу ВВОД (по умолчанию —/Hana/Shared).
     * Для **имени локального узла**: нажмите клавишу ВВОД, чтобы принять значение по умолчанию
     * В разделе **вы хотите добавить узлы в систему?** : введите **y** .
     * Для **добавления имен узлов с разделителями-запятыми**: введите **hanadb2, hanadb3**
     * Для **корневого имени пользователя** [Root]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию
     * Для **пароля привилегированного пользователя**: введите пароль привилегированного пользователя.
     * Для ролей узла hanadb2: введите **1** (для рабочей роли)
     * Для **узла Группа отработки отказа** для узла hanadb2 [default]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию.
     * Для **номера раздела хранилища** для узла hanadb2 [<<assign automatically>>]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию
     * Для **рабочей группы** для узла hanadb2 [default]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию.
     * Для **выбора ролей** для узла hanadb3: введите **2** (для режима ожидания)
     * Для **узла Группа отработки отказа** для узла hanadb3 [default]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию.
     * Для **рабочей группы** для узла hanadb3 [default]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию.
     * Для **SAP HANA идентификатора системы**: введите **HN1** .
     * **Номер экземпляра** [00]: введите **03**
     * Для **рабочей группы локального узла** [default]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию
     * Для **выбора использования системы/введите индекс [4]** : введите **4** (для настраиваемого)
     * Для **расположения томов данных** [/Hana/Data/HN1]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию
     * Для **расположения томов журнала** [/Hana/log/HN1]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию
     * Для **ограничения максимального выделения памяти?** [n]: введите **n**
     * Для **имени узла сертификата для узла hanadb1** [hanadb1]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию
     * Для **имени узла сертификата для узла hanadb2** [hanadb2]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию
     * Для **имени узла сертификата для узла hanadb3** [hanadb3]: нажмите клавишу ВВОД, чтобы принять значение по умолчанию
     * **Пароль для системного администратора (hn1adm)** : введите пароль.
     * Для **пользователя системной базы данных (системный) пароль**: введите пароль системы.
     * Для **подтверждения системного пароля пользователя системной базы данных**: введите пароль системы
     * **Перезапустить систему после перезагрузки компьютера?** [n]: введите **n** 
     * **Чтобы продолжить (y/n)** : Проверьте сводку и, если все выглядит хорошо, введите **y** .


2. **[1]** проверьте Global. ini  

   Отобразите Global. ini и убедитесь, что конфигурация внутреннего SAP HANA взаимодействия между узлами. Проверьте раздел **связи** . Он должен иметь адресное пространство для подсети `hana`, а `listeninterface` должно быть установлено в значение `.internal`. Проверьте раздел **internal_hostname_resolution** . Он должен иметь IP-адреса виртуальных машин HANA, принадлежащих к подсети `hana`.  

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

3. **[1]** добавьте сопоставление узла, чтобы обеспечить использование IP-адресов клиента для связи с клиентами. Добавьте раздел `public_host_resolution`и добавьте соответствующие IP-адреса из подсети клиента.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** перезапустите SAP Hana, чтобы активировать изменения.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** убедитесь, что клиентский интерфейс будет использовать IP-адреса из подсети `client` для обмена данными.  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Сведения о том, как проверить конфигурацию, см. в разделе SAP Note [2183363 — Настройка внутренней сети SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

6. Чтобы оптимизировать SAP HANA для базового хранилища Azure NetApp Files, задайте следующие параметры SAP HANA.

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **на**
   - `async_write_submit_active` **на**
   - `async_write_submit_blocks` **все**

   Дополнительные сведения см. [в разделе SAP HANA on NETAPP АФФ Systems by NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf). 

   Начиная с SAP HANA 2,0, параметры можно задать в `global.ini`. Дополнительные сведения см. в разделе SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Для SAP HANA 1,0 Systems SPS12 и более ранних версий эти параметры можно задать во время установки, как описано в разделе SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Хранилище, используемое Azure NetApp Files, имеет ограничение размера файла, равное 16 терабайтам (ТБ). SAP HANA не является неявно осведомленностью об ограничении хранилища и не создает новый файл данных при достижении предельного размера файла в 16 ТБ. Так как SAP HANA пытается увеличить размер файла за пределами 16 ТБ, эта попытка приведет к ошибкам и, в конце концов, в случае сбоя сервера индекса. 

   > [!IMPORTANT]
   > Чтобы SAP HANA не пытаться увеличивать файлы данных за [пределами 16 ТБ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) подсистемы хранения, задайте следующие параметры в `global.ini`.  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 дополнительные сведения см. в разделе SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Обратите внимание на Примечание SAP [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Тестовая отработка отказа SAP HANA 

1. Имитация сбоя узла на рабочем узле SAP HANA. Выполните следующие действия. 

   a. Прежде чем имитировать сбой узла, выполните следующие команды в качестве **HN1**ADM, чтобы записать состояние среды:  

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

   Б. Для имитации сбоя узла выполните следующую команду в качестве корневого узла рабочей роли, **hanadb2** в этом случае:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Наблюдение за системой для завершения отработки отказа. После завершения отработки отказа запишите состояние, которое должно выглядеть следующим образом:  

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
   > При возникновении тревоги ядра не следует задерживаться SAP HANA отработки отказа, установив `kernel.panic` на 20 секунд на *всех* виртуальных машинах Hana. Настройка выполняется в `/etc/sysctl`. Перезагрузите виртуальные машины, чтобы активировать изменение. Если это изменение не выполняется, отработка отказа может занять 10 или более минут, если на узле возникла авария ядра.  

2. Удалите сервер имен, выполнив следующие действия.

   a. Перед тестированием Проверьте состояние среды, выполнив следующие команды в качестве **HN1**ADM:  

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

   Б. Выполните следующие команды в качестве **HN1**ADM на активном главном узле, **hanadb1** в этом случае:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Резервный узел **hanadb3** перейдет в качестве главного узла. Ниже приведено состояние ресурса после завершения теста отработки отказа.  

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

   c. Перезапустите экземпляр HANA на **hanadb1** (то есть на той же виртуальной машине, где был уничтожен сервер имен). Узел **hanadb1** будет повторно присоединиться к среде и сохранит свою резервную роль.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   После запуска SAP HANA в **hanadb1**, должно быть указано следующее состояние:  

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

   d. Опять же, остановите сервер имен на активном в данный момент главном узле (то есть на узле **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Узел **hanadb1** возобновит роль главного узла. После завершения теста отработки отказа состояние будет выглядеть следующим образом:

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

   д. Запустите SAP HANA в **hanadb3**, который будет готов к работе в качестве резервного узла.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   После запуска SAP HANA в **hanadb3**состояние выглядит следующим образом:  

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

## <a name="next-steps"></a>Дополнительная информация

* [Планирование и реализация виртуальных машин Azure для SAP][planning-guide]
* [Развертывание виртуальных машин Azure для SAP][deployment-guide]
* [Развертывание СУБД виртуальных машин Azure для SAP][dbms-guide]
* Дополнительные сведения об обеспечении высокого уровня доступности и планировании аварийного восстановления SAP HANA в Azure (крупные экземпляры) см. в [этой статье](hana-overview-high-availability-disaster-recovery.md).
* Сведения о том, как установить высокий уровень доступности и спланировать аварийное восстановление SAP HANA на виртуальных машинах Azure, см. в статье [высокий уровень доступности SAP HANA на виртуальные машины Azure][sap-hana-ha].
