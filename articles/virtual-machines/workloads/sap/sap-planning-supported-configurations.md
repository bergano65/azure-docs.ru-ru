---
title: 'SAP на Azure: поддерживаемые сценарии с Azure VMs'
description: Виртуальные машины Azure поддерживают сценарии с рабочей нагрузкой SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 564c648a550b41017ffc684ca19ff03612fc63d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137634"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Нагрузка SAP на виртуальную машину Azure поддерживает сценарии
Проектирование архитектуры систем SAP NetWeaver, Business one `Hybris` или S/4HANA в Azure открывает множество различных возможностей для различных архитектур и инструментов, используемых для получения масштабируемого, эффективного и высокодоступного развертывания. Хотя в зависимости от операционной системы или DBMS используется, Есть ограничения. Кроме того, не все сценарии, которые поддерживаются в помещениях, поддерживаются одинаково в Azure. Этот документ будет вести через поддерживаемые невысокой доступности конфигураций и высокой доступности конфигураций и архитектур, использующих Azure VMs исключительно. Для сценариев, [поддерживаемых HANA Large Instances,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)проверьте сценарии поддержки статьи [для HANA Large Instances.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario) 


## <a name="2-tier-configuration"></a>2-ярусная конфигурация
Конфигурация SAP 2-Tier считается построенной из комбинированного слоя SAP DBMS и слоя приложения, который работает на том же сервере или VM-устройстве. Второй уровень считается слоем пользовательского интерфейса. В случае 2-уровня конфигурации слой приложений DBMS и SAP разделяет ресурсы Azure VM. В результате необходимо настроить различные компоненты таким образом, чтобы они не конкурировали за ресурсы. Вы также должны быть осторожны, чтобы не переподписывать ресурсы VM. Такая конфигурация не обеспечивает высокой доступности, помимо [соглашений уровня обслуживания Azure](https://azure.microsoft.com/support/legal/sla/) различных компонентов Azure.

Графическое представление такой конфигурации может выглядеть следующим образом:

![Простая 2-ярусная конфигурация](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Такие конфигурации поддерживаются Windows, Red Hat, SUSE и Oracle Linux для dBMS-систем S'L Server, Oracle, Db2, maxDB и SAP ASE для производственных и непроизводственных кейсов. Для SAP HANA как DBMS такие типы конфигураций поддерживаются только для непроизводственных случаев. Это включает в себя случай развертывания [Azure HANA Больших инстанций,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) а также.
Для всех комбинаций OS/DBMS, поддерживаемых на Azure, этот тип конфигурации поддерживается. Однако необходимо установить конфигурацию компонентов DBMS и SAP таким образом, чтобы компоненты DBMS и SAP не конкурировали за ресурсы памяти и процессора и тем самым превышали физические доступные ресурсы. Это должно быть сделано путем ограничения памяти DBMS разрешено выделить. Также необходимо ограничить расширенную память SAP в экземплярах приложений. Также необходимо контролировать потребление процессора VM в целом, чтобы убедиться, что компоненты не максимизируют ресурсы процессора. 

> [!NOTE]
> Для производственных систем SAP мы рекомендуем дополнительные конфигурации высокой доступности и возможных аварийных ситуаций, как описано позднее в этом документе


## <a name="3-tier-configuration"></a>3-ярусная конфигурация
В таких конфигурациях вы разделяете слой приложения SAP и слой DBMS на различные VMs. Обычно это делается для более крупных систем и из соображений более гибкой работы на ресурсах уровня приложения SAP. В самой простой настройке нет высокой доступности за пределами [соглашений уровня обслуживания Azure](https://azure.microsoft.com/support/legal/sla/) различных компонентов Azure. 

Графическое представление выглядит следующим:

![Простая 2-ярусная конфигурация](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Этот тип конфигурации поддерживается на Windows, Red Hat, SUSE и Oracle Linux для dBMS-систем S'L Server, Oracle, Db2, SAP HANA, maxDB и SAP ASE для производственных и непроизводственных кейсов. Это конфигурация развертывания по умолчанию для [больших экземпляров Azure HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) Для упрощения мы не провели различия между экземплярами SAP Central Services и SAP диалогов в уровне приложения SAP. В этой простой 3-уровневой конфигурации не будет высокой защиты доступности для SAP Central Services.

> [!NOTE]
> Для производственных систем SAP мы рекомендуем дополнительные конфигурации высокой доступности и возможных аварийных ситуаций, как описано позднее в этом документе


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Несколько экземпляров DBMS на единицу крупной инстанции VM или HANA
В этом типе конфигурации размещается несколько экземпляров DBMS в azure VM или HANA Large Instance. Мотивация может заключаться в том, чтобы иметь меньше операционных систем для обслуживания и с этим снижение затрат. Другими мотивами может быть большая гибкость и эффективность путем совместного использования ресурсов более крупного подразделения VM или HANA Large Instance между несколькими экземплярами DBMS. До сих пор эти конфигурации отображались в основном для непроизводственных систем.

Конфигурация, как это может выглядеть следующим:

![Несколько экземпляров DBMS в одной единице](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Этот тип развертывания DBMS поддерживается для:

- SQL Server в Windows
- IBM Db2. Найдите подробную информацию в статье [Несколько экземпляров (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Для Оракула. Для получения подробной информации см. [примечание поддержки SAP #1778431](https://launchpad.support.sap.com/#/notes/1778431) и связанные с ними примечания SAP
- Для SAP HANA поддерживается несколько экземпляров на одном VM, SAP вызывает этот метод развертывания MCOS. Для получения подробной информации см. статью SAP «Несколько систем SAP HANA на одном хосте (MCOS)» (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Запуская несколько экземпляров базы данных на одном узлах, необходимо убедиться, что различные экземпляры не конкурируют за ресурсы и тем самым превышают физические пределы ресурсов VM. Это особенно верно для памяти, где вам нужно ограничить память любой из экземпляров обмена VM может выделить. Это также может быть верно для ресурсов процессора различных экземпляров базы данных могут использовать. Все упомянутые DBMS имеют конфигурации, которые позволяют ограничивать распределение памяти и ресурсов процессора на уровне экземпляра.
Для поддержки такой конфигурации для Azure VMs предполагается, что диски или тома, используемые для файлов данных и журналов/повторов баз данных, управляемых различными экземплярами, являются отдельными. Другими словами, данные или файлы журналов/переделок баз данных, управляемых другими экземплярами DBMS, не должны совместно с теми же дисками или объемами. 

Конфигурация диска для HANA Large Instances настроена и подробно описана в [поддерживаемых сценариях для HANA Large Instances.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) 

> [!NOTE]
> Для производственных систем SAP мы рекомендуем дополнительные конфигурации высокой доступности и возможных аварийных ситуаций, как описано в настоящем документе. VMs с несколькими экземплярами DBMS не поддерживаются конфигурациями высокой доступности, описанными позднее в этом документе.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Несколько экземпляров SAP Dialog в одном VM
Во многих случаях несколько экземпляров диалогов были развернуты на голых металлических серверах или даже в vMs,хау в частных облаках. Причиной таких конфигураций было адаптировать определенные экземпляры диалога SAP к определенным рабочим нагрузкам, бизнес-функциям или типам рабочей нагрузки. Причиной неисправления этих экземпляров в отдельные ВМ были усилия по техническому обслуживанию и эксплуатации операционной системы. Или во многих случаях расходы в случае, если хостер или оператор VM просит ежемесячную плату за ВМ работает и администрируется. В Azure сценарий размещения нескольких экземпляров sAP-диалогов в рамках одного VM, который мы поддерживали для производственных и непроизводственных целей на операционных системах Windows, Red Hat, SUSE и Oracle Linux. Параметр ядра SAP PHYS_MEMSIZE, доступный на Windows и современных ядрах Linux, должен быть установлен, если несколько экземпляров SAP Application Server работают на одном VM. Также рекомендуется ограничить расширение расширенной памяти SAP на операционных системах, таких как Windows, где осуществляется автоматический рост расширенной памяти SAP. Это можно сделать с параметром `em/max_size_MB`профиля SAP.

В конфигурации 3-tier, где несколько экземпляров sAP диалогов, запускаемых в Azure VMs, могут выглядеть следующими:

![Несколько экземпляров DBMS в одной единице](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Для упрощения мы не провели различия между экземплярами SAP Central Services и SAP диалогов в уровне приложения SAP. В этой простой 3-уровневой конфигурации не будет высокой защиты доступности для SAP Central Services. Для производственных систем не рекомендуется оставлять SAP Central Services без защиты. Для сведения о так называемых многоsid конфигурациях вокруг SAP Central Instances и высокой доступности таких многоsid конфигураций, см. более поздние разделы этого документа.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Высокая защита доступности для слоя SAP DBMS
При развертывании производственных систем SAP необходимо учитывать горячий резервный тип конфигураций высокой доступности. Особенно в случае SAP HANA, где данные должны быть загружены в память, прежде чем получить полную производительность и масштабируемость обратно, заживление службы Azure не является идеальной мерой для высокой доступности. 

В целом корпорация Майкрософт поддерживает только конфигурации высокой доступности и пакеты программного обеспечения, описанные в разделе рабочей нагрузки SAP в docs.microsoft.com. Вы можете прочитать то же заявление в sAP примечание [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Корпорация Майкрософт не будет предоставлять поддержку другим сторонним программным платформам с высокой доступностью, которые не документированы корпорацией Майкрософт в связи с рабочей нагрузкой SAP. В таких случаях сторонний поставщик платформы высокой доступности является поддерживающей стороной для конфигурации высокой доступности, которая должна быть вовлечена вами как клиентом в процесс поддержки. Исключения будут упомянуты в этой статье. 

В целом корпорация Майкрософт поддерживает ограниченный набор конфигураций высокой доступности на m Ms Azure или подразделениях HANA Large Instances. Для поддерживаемых сценариев HANA Large Instances прочитайте [сценарии поддержки документа для крупных инстанций HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)

Для VMs Azure следующие конфигурации высокой доступности поддерживаются на уровне DBMS:

- Репликация системы SAP HANA на основе Linux Pacemaker на SUSE и Red Hat. Смотрите подробные статьи:
    - [Обеспечение высокого уровня доступности SAP HANA на виртуальных машинах Azure в SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
    - [Обеспечение высокого уровня доступности SAP HANA в виртуальных машинах Azure в Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)
- SAP HANA масштабирует сяздные n'm конфигурации с помощью [файлов Azure NetApp](https://azure.microsoft.com/services/netapp/) на SUSE и Red Hat. Подробности перечислены в следующих статьях:
    - [Развертывание системы масштабирования SAP HANA с резервным узлам на Вазуре VMs с помощью файлов Azure NetApp на сервере SUSE Linux Enterprise Server.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
    - [Развертывание системы масштабирования SAP HANA с резервным узлам на VMs Azure с помощью файлов Azure NetApp на Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- Кластер S'L Server Failover основан на файловых службах Windows Scale-Out. Хотя рекомендация для производственных систем заключается в использовании S'L Server Always On вместо кластеризации. Сервер всегда на сервере S'L обеспечивает лучшую доступность с помощью отдельного хранилища. Подробности описаны в этой статье: 
    - [Наверсвыше нанастройки кластера s'L Server на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)
- Сервер всегда поддерживается с помощью операционной системы Windows для сервера S'L на Azure. Это рекомендация по умолчанию для производственных экземпляров сервера S'L в Azure. Подробности описаны в следующих статьях:
    - [Представляем группы доступности сервера S'L Всегда на настройках на виртуальных машинах Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
    - [Настройка группы доступности AlwaysOn на виртуальных машинах Azure в разных регионах](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
    - [Наиндикируйте балансера нагрузки для группы всегда на наличие в Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener)
- Oracle Data Guard для Windows и Oracle Linux. Подробную информацию для Oracle Linux можно найти в этой статье:
    - [Реализация Oracle Data Guard на виртуальной машине Azure под управлением Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
- IBM Db2 HADR на SUSE и RHEL Подробная документация для SUSE и RHEL с использованием Pacemaker предоставляется здесь:
    - [Высокая доступность IBM Db2 LUW на Облачных Вымизированных на SUSE Linux Enterprise Server с Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
    - [Обеспечение высокого уровня доступности IBM DB2 LUW на виртуальных машинах Azure в Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)
- Конфигурация SAP ASE и SAP maxDB, описанная в следующих документах:
    - [Рабочие нагрузки SAP на Виртуальных машинах Azure. Руководство по развертыванию СУБД SAP ASE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
    - [Развертывание SAP MaxDB, liveCache и сервера содержимого на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)
- Сценарии высокой доступности HANA Large Instances подробно описаны в:
    - [Поддерживаемые сценарии для HANA Large Instances- HSR с STONITH для высокой доступности](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability)
    - [Поддерживаемые сценарии для HANA Больших Инстанций - Хост авто сбой (1'1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11)

> [!IMPORTANT]
> Ни в одном из описанных выше сценариев мы поддерживаем конфигурации нескольких экземпляров DBMS в одном VM. В каждом случае можно использовать только один экземпляр базы данных на ВМ и защищен ы описанными методами высокой доступности. Защита нескольких экземпляров DBMS в одном и том же кластере Windows или Pacemaker **не** поддерживается на данный момент. Кроме того, Oracle Data Guard поддерживается только для одного экземпляра в случаях развертывания VM. 

Различные системы баз данных позволяют размещать несколько баз данных в одном экземпляре DBMS. Как и в случае sAP HANA, несколько баз данных могут быть размещены в нескольких контейнерах баз данных (MDC). В случаях, когда эти конфигурации мультибаз работают в рамках одного ресурса кластера failover, эти конфигурации поддерживаются. Конфигурации, которые не поддерживаются, являются случаями, когда потребуется несколько кластерных ресурсов. Что касается конфигураций, в которых можно определить несколько групп доступности серверов, то в одном экземпляре s-L Server.


![Конфигурация DBMS HA](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

В зависимости от DBMS an/or операционных систем, компоненты, такие как балансиватор нагрузки Azure, могут или не могут потребоваться как часть архитектуры решения. 

Специально для maxDB конфигурация хранилища должна быть другой. В maxDB файлы данных и журналов должны быть размещены в общем хранилище для конфигураций высокой доступности. Только в случае maxDB общее хранилище поддерживается для высокой доступности. Для всех остальных dBMS отдельные стеки хранения на узла являются единственными поддерживаемыми конфигурациями диска.

Известно, что существуют и другие платформы высокой доступности, которые также работают в Microsoft Azure. Однако корпорация Майкрософт не тестирует эти платформы. Если вы хотите создать конфигурацию высокой доступности с помощью этих инфраструктур, вам нужно будет работать с поставщиком этого программного обеспечения, чтобы:

- Разработка архитектуры развертывания
- Развертывание архитектуры
- Поддержка архитектуры

> [!IMPORTANT]
> Microsoft Azure Marketplace предлагает множество мягких приборов, которые предоставляют решения для хранения данных поверх родного хранилища Azure. Эти мягкие приборы могут быть использованы для создания акций NFS, которые теоретически могут быть использованы в sAP HANA масштабирования развертывания, где резервный узло требуется. По разным причинам ни одна из этих средств хранения данных не поддерживается ни для одного из развертываний DBMS корпорацией Майкрософт и SAP на Azure. Развертывание DBMS на sMB-акциях на данный момент не поддерживается. Развертывание DBMS на акциях NFS ограничено акциями NFS 4.1 в [файлах NetApp Azure.](https://azure.microsoft.com/services/netapp/)


## <a name="high-availability-for-sap-central-service"></a>Высокая доступность для центрального сервиса SAP
SAP Central Services — это вторая точка сбоя конфигурации SAP. В результате необходимо также защищать эти процессы центральных служб. Предложение поддерживается и документируется для рабочей нагрузки SAP, выглядит следующим образом:

- Кластерный сервер Windows Failover с использованием служб файлов с масштабом Windows для sapmnt и глобального каталога транспорта. Подробности описаны в статье:
    - [Кластеризация экземпляра SAP ASCS/SCS в отказоустойчивом кластере Windows с помощью файлового ресурса в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
    - [Подготовка высокодоступной инфраструктуры Azure для SAP с помощью отказоустойчивого кластера Windows и файлового ресурса для экземпляров SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share).
- Кластерный сервер Windows Failover использует долю SMB на основе [файлов Azure NetApp](https://azure.microsoft.com/services/netapp/) для sapmnt и глобального каталога транспорта. Подробности перечислены в статье:
    - [Высокая доступность SAP NetWeaver на MMs Azure на Windows с файлами Azure NetApp (SMB) для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
- Windows Failover кластерный сервер `Datakeeper`на основе SIOS . Несмотря на то, что корпорация Майкрософт задокументирована, вам необходимы отношения поддержки с SIOS, так что вы можете взаимодействовать с поддержкой SIOS при использовании этого решения. Подробности описаны в статье:
    - [Кластеризация экземпляра SAP ASCS/SCS в отказоустойчивом кластере Windows с помощью общего диска кластера в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - [Подготовка инфраструктуры Azure для SAP высокого уровня доступности с помощью отказоустойчивого кластера Windows и общего диска для SAP ASCS/SC](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- Pacemaker на операционной системе SUSE с созданием высокодоступной `drdb` доли NFS с использованием двух SUSE VMs и для репликации файлов. Подробности задокументированы в статье
    - [Руководство по обеспечению высокого уровня доступности виртуальных машин Azure для SAP NetWeaver на SUSE Linux Enterprise Server для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
    - [Обеспечение высокого уровня доступности NFS на виртуальных машинах Azure в SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Операционная система Pacemaker SUSE с использованием акций NFS, предоставленных [Azure NetApp Files.](https://azure.microsoft.com/services/netapp/) Подробности задокументированы в
    - [Высокая доступность SAP NetWeaver на M-м для Azure на корпоративном сервере SUSE Linux с файлами Azure NetApp для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- Pacemaker на операционной системе Red Hat `glusterfs` с долей NFS, размещенных на кластере. Подробности можно найти в статьях
    - [Обеспечение высокого уровня доступности SAP NetWeaver в виртуальных машинах Azure с Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)
    - [`GlusterFS`на Azure VMs на Red Hat Enterprise Linux для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
- Pacemaker на операционной системе Red Hat с долей NFS размещены на [Azure NetApp Файлов](https://azure.microsoft.com/services/netapp/). Подробности описаны в статье
    - [Лазурные виртуальные машины высокой доступности для SAP NetWeaver на Red Hat Enterprise Linux с Azure NetApp Файлы для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

Из перечисленных решений вам необходимы отношения поддержки с `Datakeeper` SIOS для поддержки продукта и взаимодействия с SIOS непосредственно в случае проблем. В зависимости от того, как вы лицензируемые Windows, Red Hat и/или SUSE OS, вы также можете быть обязаны иметь контракт на поддержку с поставщиком ОС, чтобы иметь полную поддержку перечисленных конфигураций высокой доступности.

Конфигурация также может отображаться следующим образом:

![Конфигурация DBMS и ASCS HA](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

С правой стороны графики отображается высокодоступная служба SAP Central Services. Помимо того, что sAP Central услуги защищены с failover кластера рамки, которые могут не в состоянии в случае проблемы, есть необходимость в высокодоступной NFS или SMB доля, или Windows общий диск, чтобы убедиться, sapmnt и глобальный каталог транспорта доступны независимо от существования одного VM. Дополнительные некоторые решения, такие как Windows Failover Cluster Server и Pacemaker, потребуют от баланса нагрузки Azure для направления или перенаправления трафика на здоровый узел.

В показанном списке нет упоминания об операционной системе Oracle Linux. Oracle Linux не поддерживает Pacemaker в качестве кластерной платформы. Если вы хотите развернуть систему SAP на Oracle Linux и вам нужна платформа высокой доступности для Oracle Linux, вам нужно работать со сторонними поставщиками. Одним из поставщиков является SIOS с их набором защиты для Linux, который поддерживается SAP на Azure. Для получения дополнительной информации читайте sAP примечание [#1662610 - Подробная информация о поддержке SIOS Protection Suite для Linux](https://launchpad.support.sap.com/#/notes/1662610) для получения более подробной информации.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Поддерживаемое хранилище со сценариями SAP Central Services, перечисленными выше
Поскольку только подмножество типов хранения Azure предоставляет высокодоступные nFS или SMB-акции, качество которых в наших сценариях кластера SAP Central Services список поддерживаемых типов хранения

- Кластерный сервер Windows Failover с файловым сервером Стаким масштабом может быть развернут на всех родных типах хранения данных Azure, за исключением файлов NetApp Azure. Тем не менее, рекомендация заключается в использовании Premium Storage из-за превосходного уровня обслуживания соглашений в пропускной связи и IOPS.
- Кластерный сервер Windows Failover с SMB в файлах NetApp Azure поддерживается в файлах Azure NetApp. Доли SMB в службах Azure File **НЕ** поддерживаются на данный момент.
- Кластерный сервер Windows Failover с общим `Datakeeper` диском Windows на основе SIOS может быть развернут на всех родных типах хранения Данных Azure, за исключением файлов NetApp Azure. Тем не менее, рекомендация заключается в использовании Premium Storage из-за превосходного уровня обслуживания соглашений в пропускной связи и IOPS.
- SUSE или Red Hat Pacemaker, использующие акции NFS в файлах Azure NetApp, поддерживается в файлах NetApp Azure. 
- SUSE Pacemaker `drdb` с помощью конфигурации между двумя VMs поддерживается с помощью наиболее родных типов хранения Azure, за исключением файлов NetApp Azure. Тем не менее, рекомендация заключается в использовании Premium Storage из-за превосходного уровня обслуживания соглашений в пропускной связи и IOPS.
- Red Hat Pacemaker, использующий `glusterfs` для предоставления доли NFS, поддерживается с помощью наиболее родных типов хранения Azure, за исключением файлов NetApp Azure. Тем не менее, рекомендация заключается в использовании Premium Storage из-за превосходного уровня обслуживания соглашений в пропускной связи и IOPS.

> [!IMPORTANT]
> Microsoft Azure Marketplace предлагает множество мягких приборов, которые предоставляют решения для хранения данных поверх родного хранилища Azure. Эти мягкие приборы могут быть использованы для создания NFS или SMB акций, а также, что теоретически может быть использован в failover кластерных SAP Центральных услуг, а также. Эти решения не поддерживаются непосредственно для рабочей нагрузки SAP корпорацией Майкрософт. Если вы решили использовать такое решение для создания своей доли NFS или SMB, поддержка конфигурации SAP Central Service должна быть предоставлена третьей стороной, владеющей программным обеспечением в мягком приборе для хранения данных.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Кластеры Сбой SAP Central Services сбой
Чтобы уменьшить количество vMs, необходимых в больших ландшафтах SAP, SAP позволяет запускать экземпляры SAP Central Services нескольких различных систем SAP в конфигурации кластера сбоя. Представьте себе случаи, когда у вас есть 30 или более систем производства NetWeaver или S/4HANA. Без кластеризации с несколькими SID эти конфигурации потребуют 60 или более vMs в 30 или более конфигурациях кластеров Windows или Pacemaker. Кроме того, dBMS failover кластеров необходимо. Развертывание нескольких центральных служб SAP в двух узлах в конфигурации кластера сбоя может значительно сократить количество высочесок. Однако развертывание нескольких экземпляров SAP Central служб в одной конфигурации кластера двух узлов также имеет некоторые недостатки. Проблемы вокруг одного VM в конфигурации кластера применяются к нескольким системам SAP. Обслуживание гостевой ОС, работающей в конфигурации кластера, требует большей координации, так как это касается нескольких производственных систем SAP. Такие инструменты, как SAP LaMa, не поддерживают мультиSID кластеризации в процессе клонирования системы.

В Azure для операционной системы Windows с ENSA1 и ENSA2 поддерживается конфигурация кластера с несколькими SID. Рекомендация не объединять старую архитектуру Enqueue Replication Service (ENSA1) с новой архитектурой (ENSA2) в одном многосидском кластере. Подробная информация о такой архитектуре задокументирована в статьях

- [Обеспечение высокого уровня доступности с несколькими идентификаторами безопасности для экземпляра SAP ASCS/SCS с помощью отказоустойчивой кластеризации Windows Server и общего диска в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk) 
- [Обеспечение высокого уровня доступности экземпляра ASCS/SCS с несколькими ИД безопасности с помощью отказоустойчивой кластеризации Windows Server и файлового ресурса в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share) 

Для SUSE поддерживается также многосидский кластер на базе Pacemaker. Пока конфигурация поддерживается для:

- Максимум пять экземпляров SAP ASCS/SCS
- Старая архитектура репликации сервера enqueue (ENSA1)
- Конфигурация кластера 2 узла Pacemaker

Конфигурация задокументирована в [Руководстве SAP NetWeaver на Azure VMs на SUSE Linux Enterprise Server для многостворентных приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)

Кластер мультиSID с сервером Enqueue Replication схематично выглядит

![Конфигурация DBMS и ASCS HA](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>Сценарии масштабирования SAP HANA
Сценарии масштабирования SAP HANA поддерживаются для подмножества сертифицированных HANA VMs Azure, перечисленных в [аппаратном каталоге SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Все ВМ с пометкой "Да" в столбце "Кластеризация" могут быть использованы для масштабирования OLAP или S/4HANA. Конфигурации без ожидания поддерживаются типами хранения Azure: 

- Премиум-хранилище Azure, включая акселератор записи Azure для громкости /hana/log
- [Диск (цен. категория "Ультра")](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Конфигурации масштабирования SAP HANA для OLAP или S/4HANA с резервным узелом (ы) поддерживаются исключительно с помощью Совместного использования NFS в файлах NetApp Azure.

Для получения дополнительной информации о точных конфигурациях хранения с резервным узлами или без нее проверьте статьи:

- [Конфигурации хранилища виртуальных машин SAP HANA в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 
- [Развертывание системы масштабирования SAP HANA с резервным узлами на VMs Azure с помощью файлов Azure NetApp на сервере SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Развертывание системы масштабирования SAP HANA с резервным узлам на VMs Azure с помощью файлов Azure NetApp на Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Заметка поддержки SAP #2080991](https://launchpad.support.sap.com/#/notes/2080991)

Для получения подробной информации о конфигурациях HANA Large Instances, поддерживаемых HANA, применяется следующая документация:

- [Поддерживаемые сценарии масштабирования HANA Large Instances с резервным режимом](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby)
- [Поддерживаемые сценарии масштабирования HANA Large Instances без ожидания](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Сценарий аварийного восстановления
Поддерживается множество сценариев аварийного восстановления. Мы определяем архитектуры disaster как архитектуры, которые должны компенсировать полное отключение областей Azure. Это означает, что нам нужна цель аварийного восстановления, чтобы быть другой областью Azure в качестве цели для выполнения вашего ландшафта SAP. Мы разделяем методы и конфигурации в слое DBMS и не-DBMS слое. 

### <a name="dbms-layer"></a>Слой DBMS
Для слоя DBMS поддерживаются конфигурации с использованием механизмов репликации dBMS, таких как Always On, Oracle Data Guard, Db2 HADR, SAP ASE Always-On или репликация системы HANA. В таких случаях поток репликации является асинхронным, а не синхронным, как в типичных сценариях высокой доступности, которые развертываются в одном регионе Azure. Типичный пример такой поддерживаемой конфигурации аварийного восстановления DBMS описан в статье [SAP HANA доступности в регионах Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions#combine-availability-within-one-region-and-across-regions) Второй график в этом разделе описывает сценарий с HANA в качестве примера. Все основные базы данных, поддерживаемые для приложений SAP, могут быть развернуты в таком сценарии.

Поддерживается использование меньшего VM в качестве целевого экземпляра в зоне аварийного восстановления, так как VM не испытывает полного трафика рабочей нагрузки. При этом необходимо учитывать следующие соображения:

- Меньшие типы VM не позволяют, что многие диски прилагается, чем меньше VMs
- Меньшие VMs имеют меньшую пропускную емкость сети и хранилища
- Повторное размеры в семьях VM может быть проблемой, когда различные ВМ собираются в одном наборе доступности Azure или когда повторное увеличение размера должно произойти между семейством M-Series и семейством Mv2 vMs
- CPU и потребление памяти для экземпляра базы данных, будучи в состоянии получить поток изменений с минимальной задержкой и достаточно ежемое количество ресурсов процессора и памяти, чтобы применить эти изменения с минимальной задержкой к данным  

Более подробную информацию об ограничениях различных размеров VM можно найти [здесь](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) 

Другим поддерживаемым методом развертывания цели DR является установка второго экземпляра DBMS на VM, который выполняет непроизводственный экземпляр DBMS непроизводственного экземпляра SAP. Это может быть немного более сложным, так как вам нужно выяснить, что на память, cPU ресурсов, пропускной способности сети, и пропускная способность хранения необходимо для конкретных целевых экземпляров, которые должны функционировать в качестве основного экземпляра в сценарии DR. Особенно в HANA настоятельно рекомендуется настроить экземпляр, который функционирует как цель DR на общий узел, чтобы данные не были предварительно загружены в целевой экземпляр DR.

Для сценариев HANA Large Instance DR проверьте следующие документы:

- [Единый узла с DR с использованием репликации хранилища](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication)
- [Единый узла с DR (многоцелевым) с использованием репликации хранилища](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Единый узла с DR (многоцелевым) с использованием репликации хранилища](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Высокая доступность с HSR и DR с репликацией хранилища](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication)
- [Масштабирование с ПОМОЩЬю DR с помощью репликации хранилища](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication)
- [Одиночный узла с DR с использованием HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr)
- [Одиночный узла HSR к DR (оптимизация затрат)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized)
- [Высокая доступность и аварийное восстановление с помощью HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr)
- [Высокая доступность и аварийное восстановление с помощью HSR (оптимизация затрат)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Масштабирование с DR с использованием HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr)

> [!NOTE]
> Использование [восстановления сайта Azure](https://azure.microsoft.com/services/site-recovery/) не тестировалось для развертывания DBMS под рабочей нагрузкой SAP. В результате он не поддерживается для dBMS слой систем SAP на данный момент времени. Другие методы репликации microsoft и SAP, которые не перечислены, не поддерживаются. Использование стороннего программного обеспечения для репликации dBMS-уровня систем SAP между различными регионами Azure должно поддерживаться поставщиком программного обеспечения и не будет поддерживаться через каналы поддержки Майкрософт и SAP. 
 
## <a name="non-dbms-layer"></a>Слой, не связанный с DBMS
Для уровня приложения SAP и возможных необходимых акций или мест хранения два основных сценария используются клиентами:

- Цели аварийного восстановления во втором регионе Azure не используются для производства или непроизводственных целей. В этом сценарии в идеале не развернуты миги, которые функционируют в качестве цели аварийного восстановления, а изображение и изменения в изображениях производственного слоя приложения SAP реплицируются в область аварийного восстановления. Функциональностью, которая может выполнить такую задачу, является [восстановление сайта Azure.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-move-overview) Восстановление сайта Azure поддерживает сценарии репликации azure-to-Azure. 
- Целями аварийного восстановления являются vMs, которые фактически используются непроизводственными системами. Весь ландшафт SAP распределен по двум различным регионам Azure с производственными системами, как правило, в одном регионе и непроизводственными системами в другом регионе. Во многих развертываниях клиентов клиент имеет непроизводственную систему, эквивалентную производственной системе. Заказчик имеет предустановленные экземпляры производственного применения на непроизводственных системах уровня приложения. В случае неудачи непроизводственные экземпляры будут закрыты, виртуальные названия производственных VMs переместились на непроизводственные Виртуальные компании (после назначения новых IP-адресов в DNS), а предустановленные производственные экземпляры начинают работу

### <a name="sap-central-services-clusters"></a>Кластеры центральных служб SAP
Кластеры SAP Central Services, использующие общие диски (Windows), sMB-акции (Windows) или nFS-акции, немного сложнее воспроизвести. Что на стороне Windows, то репликация системы хранения данных Windows является возможным решением. На Linux rsync является жизнеспособным решением.



## <a name="non-supported-scenario"></a>Неподдерживаемый сценарий
Существует список сценариев, которые не поддерживаются для рабочей нагрузки SAP на архитектурах Azure. **Не поддерживаемые** средства SAP и Microsoft не смогут поддерживать эти конфигурации и должны будут отложить в конечном итоге участие третьей стороны, которая предоставила программное обеспечение для создания таких архитектур. Две из этих категорий:

- Хранение мягкой техники: Существует ряд хранения мягкой техники, предлагаемой на рынке Azure. Некоторые поставщики предлагают собственную документацию о том, как использовать эти устройства для хранения на Azure, связанные с программным обеспечением SAP. Поддержка конфигураций или развертываний, связанных с такими хранилищами, должна обеспечиваться поставщиком этих мягких приборов хранения. Этот факт также проявляется в [примечании поддержки SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Платформы высокой доступности: только Pacemaker и Windows Server Failover Cluster поддерживают сяокопительную доступность для рабочей нагрузки SAP в Azure. Как упоминалось ранее, решение `Datakeeper` SIOS описано и задокументировано корпорацией Майкрософт. Тем не менее компоненты `Datakeeper` СИОС должны поддерживаться через СИОС в качестве поставщика, предоставляющего эти компоненты. SAP также перечислила другие сертифицированные рамки высокой доступности в различных примечаниях SAP. Некоторые из них были сертифицированы сторонним поставщиком для Azure. Тем не менее, поддержка конфигураций с использованием этих продуктов должна быть предоставлена поставщиком продукта. Различные поставщики имеют различную интеграцию в процессы поддержки SAP. Прежде чем принять решение об использовании продукта в конфигурациях SAP, развернутых в Azure, следует уточнить, какой процесс поддержки лучше всего подходит для конкретного поставщика.
- Общие дисковые кластеры, в которых файлы баз данных находятся на общих дисках, не поддерживаются, за исключением maxDB. Для всех других баз данных поддерживаемое решение состоит в том, чтобы иметь отдельные места хранения вместо доли SMB или NFS или общего диска для настройки сценариев высокой доступности

Другие сценарии, которые не поддерживаются, являются такими сценариями, как:

- Сценарии развертывания, которые вводят большую задержку сети между уровнем приложения SAP и уровнем SAP DBMS в общей архитектуре `Hybris`SAP, как показано в NetWeaver, S/4HANA и, например, . В том числе:
    - Развертывание одного из уровней на предпосылке, в то время как другой уровень развернут в Azure
    - Развертывание уровня применения SAP системы в другом регионе Azure, чем уровень DBMS
    - Развертывание одного уровня в центрах обработки данных, расположенных совместно с Azure, и другого уровня в Azure, за исключением тех случаев, когда такие шаблоны архитектуры предоставляются народным службой Azure
    - Развертывание сетевых виртуальных приборов между уровнем приложения SAP и слоем DBMS
    - Использование хранилища, размещенного в центрах обработки данных, расположенных совместно с центром обработки данных Azure для уровня SAP DBMS или глобального каталога транспорта SAP
    - Развертывание двух слоев с двумя различными поставщиками облачных технологий. Например, развертывание уровня DBMS в облачной инфраструктуре Oracle и уровня приложений в Azure
- Конфигурации кластеров Multi-Instance HANA Pacemaker
- Конфигурации кластера Windows с общими дисками через SOFS или SMB на БАЗАх данных SAP, поддерживаемых в Windows. Вместо этого мы рекомендуем использовать нативную репликацию высокой доступности определенных баз данных и использовать отдельные стеки хранения данных
- Развертывание баз данных SAP, поддерживаемых на Linux, с файлами баз данных, расположенными в долях NFS поверх ANF, за исключением SAP HANA
- Развертывание Oracle DBMS на любой другой гостевой ОС, кроме Windows и Oracle Linux. Смотрите также [примечание поддержки SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Сценарий (ы), что мы не тестировали и, следовательно, не имеют опыта работы со списком, как:

- Восстановление сайта Azure, реплицируя VMS-слоя по слою DBMS. В результате мы рекомендуем использовать наирующую асинхронную функциональность репликации базы данных для потенциальной конфигурации аварийного восстановления
 

## <a name="next-steps"></a>Next Steps
Ознакомьте следующие шаги в [планировании и реализации виртуальных машин Azure для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)




  



