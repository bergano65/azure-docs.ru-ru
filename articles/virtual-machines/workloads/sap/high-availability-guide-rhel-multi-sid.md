---
title: Azure VMs высокой доступности для SAP NW на RHEL мульти-SID руководство Документы Майкрософт
description: Обеспечение высокого уровня доступности SAP NetWeaver в виртуальных машинах Azure с Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/24/2020
ms.author: radeltch
ms.openlocfilehash: 4f1bfd58e27f0cd677980ff9351d32d91a68e3e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247441"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Высокая доступность для SAP NetWeaver на Azure VMs на Red Hat Enterprise Linux для sAP-приложений с несколькими SID-руководства

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

В этой статье описывается, как развернуть несколько высокодоступных систем SAP NetWeaver (т.е. мульти-SID) в двух узловом кластере на Azure VMs с Red Hat Enterprise Linux для приложений SAP.  

В конфигурациях примеров команды установки и т.д. три системы SAP NetWeaver 7.50 развернуты в одном кластере высокой доступности узлов. SID-системы SAP:
* **NW1**: ASCS экземпляр номер **00** и виртуальное имя хозяина **msnw1ascs**; ERS экземпляр номер **02** и виртуальное имя хозяина **msnw1ers**.  
* **NW2**: ASCS экземпляр номер **10** и виртуальный хостимя **msnw2ascs**; ERS экземпляр номер **12** и виртуальное имя хозяина **msnw2ers**.  
* **NW3**: ASCS экземпляр номер **20** и виртуальный хостимя **msnw3ascs**; ERS экземпляр номер **22** и виртуальное имя хозяина **msnw3ers**.  

Статья не охватывает уровень базы данных и развертывание акций SAP NFS. В примерах этой статьи мы используем объем [файлов Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) **sapMSID** для акций NFS, предполагая, что объем уже развернут. Мы также предполагаем, что объем файлов Azure NetApp используется с помощью протокола NFSv3 и что для кластерных ресурсов для экземпляров ASCS и ERS систем SAP NW1, NW2 и NW3 существуют следующие пути файлов:  

* объем sapMSID (nfs://10.42.0.4/sapmnt<b>NW1)</b>
* объем sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ascs)
* объем sapMSID (nfs://10.42.0.4/usrsap<b>sys NW1)</b>
* объем sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* объем sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* объем sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ascs)
* объем sapMSID (nfs://10.42.0.4/usrsap<b>nw2</b>sys)
* объем sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* объем sapMSID (nfs://10.42.0.4/sapmnt<b>NW3)</b>
* объем sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* объем sapMSID (nfs://10.42.0.4/usrsap<b>сис NW3)</b>
* объем sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

Прежде чем начать, обратитесь к следующим SAP Примечания и документы в первую очередь:

* примечание к SAP [1928533], которое содержит:
  * список размеров виртуальных машин Azure, поддерживаемых для развертывания ПО SAP;
  * важные сведения о доступных ресурсах для каждого размера виртуальной машины Azure;
  * сведения о поддерживаемом программном обеспечении SAP и сочетаниях операционных систем и баз данных;
  * сведения о требуемой версии ядра SAP для Windows и Linux в Microsoft Azure.
* [Документация файлов Сети Приложений Azure][anf-azure-doc]
* примечание к SAP [2015553], в котором описываются предварительные требования к SAP при развертывании программного обеспечения SAP в Azure;
* примечание к SAP [2002167], содержащее рекомендуемые параметры ОС для Red Hat Enterprise Linux;
* примечание к SAP [2009879], содержащее рекомендации по SAP HANA для Red Hat Enterprise Linux;
* примечание к SAP [2178632], содержащее подробные сведения обо всех доступных метриках мониторинга для SAP в Azure;
* примечание к SAP [2191498], содержащее сведения о необходимой версии агента SAP Host Agent для Linux в Azure;
* примечание к SAP [2243692], содержащее сведения о лицензировании SAP в Linux в Azure;
* примечание к SAP [1999351], содержащее дополнительные сведения об устранении неполадок, связанных с расширением для расширенного мониторинга Azure для SAP;
* [вики-сайт сообщества SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), содержащий все необходимые примечания к SAP для Linux;
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Linux в Azure][deployment-guide]
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по развертыванию СУБД][dbms-guide]
* [SAP Netweaver в кластере pacemaker](https://access.redhat.com/articles/3150081)
* Общая документация по RHEL
  * [Общие сведения о надстройке для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Администрирование надстройки для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Справочник по надстройке для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Настройка ASCS/ERS для SAP Netweaver с автономными ресурсами в RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Налажить SAP S/4HANA ASCS/ERS с автономным сервером Enqueue 2 (ENSA2) в Pacemaker на RHEL](https://access.redhat.com/articles/3974941)
* Документация RHEL, специфичная для Azure:
  * [Политики поддержки для кластеров высокой доступности RHEL — виртуальные машины Microsoft Azure как члены кластера](https://access.redhat.com/articles/3131341)
  * [Установка и настройка кластера высокой доступности Red Hat Enterprise Linux 7.4 (и более поздних версий) в Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Приложения NetApp SAP от Microsoft Azure с помощью файлов NetApp Azure][anf-sap-applications-azure]

## <a name="overview"></a>Обзор

Виртуальные машины, которые участвуют в кластере должны быть размером, чтобы иметь возможность запускать все ресурсы, в случае сбоя происходит. Каждый SAP SID может выполнить неудачу независимо друг от друга в кластере с высокой доступностью с несколькими SID.  

Для достижения высокой доступности SAP NetWeaver требуется высокодоступная акция. В этой документации мы представляем примеры с акциями SAP, развернутыми в [объемах NFS Файлов NFS Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) Также можно разместить акции на высокодоступном [кластере GlusterFS,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)который может быть использован несколькими системами SAP.  

![Общие сведения о высоком уровне доступности SAP NetWeaver](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Поддержка мультиsid кластеризации SAP ASCS/ERS с Red Hat Linux в качестве гостевой операционной системы в Azure VMs ограничена **пятью** SAP SID в одном кластере. Каждый новый SID увеличивает сложность. Смесь SAP Enqueue Replication Server 1 и Enqueue Replication Server 2 в одном кластере **не поддерживается.** Кластеризация Multi-SID описывает установку нескольких экземпляров SAP ASCS/ERS с различными СИД в одном кластере Pacemaker. В настоящее время мультиsid кластеризации поддерживается только для ASCS / ERS.  

> [!TIP]
> Мультиsid кластеризации SAP ASCS /ERS является решение с более высокой сложностью. Это сложнее в реализации. Она также включает в себя более высокие административные усилия, при проведении работ по техническому обслуживанию (например, исправление ОС). Перед тем, как приступить к реализации, потребуется время, чтобы тщательно спланировать развертывание и все вовлеченные компоненты, такие как VMs, nFS-монтажи, VIP-персоны, конфигурации балансодержателя нагрузки и так далее.  

SAP NetWeaver ASCS, SAP NetWeaver SCS и SAP NetWeaver ERS используют виртуальные хост-имена и виртуальные IP-адреса. Балансировщику нагрузки в Azure нужен виртуальный IP-адрес. Мы рекомендуем использовать [стандартный балансер нагрузки.](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)  

В следующем списке показана конфигурация балансиста нагрузки (A)SCS и ERS для этого примера кластера с несколькими SID с тремя системами SAP. Вам понадобится отдельный ip-адрес, зонды работоспособности и правила балансировки нагрузки для каждого экземпляра ASCS и ERS для каждого из SID. Назначаем все ВМ, которые являются частью кластера ASCS/ASCS, одному пулу бэкэнда одного ILB.  

### <a name="ascs"></a>(A)SCS

* Конфигурация внешнего интерфейса:
  * IP-адрес для NW1: 10.3.1.50
  * IP-адрес для NW2: 10.3.1.52
  * IP-адрес для NW3: 10.3.1.54

* Порты зонда
  * Порт 620<strong>&lt;&gt;nr</strong>, поэтому для NW1, NW2, и NW3 зонд порты 620**00**, 620**10** и 620**20**
* Правила балансировки нагрузки - создайте по одному для каждого экземпляра, то есть NW1/ASCS, NW2/ASCS и NW3/ASCS.
  * При использовании Standard Load Balancer выберите **порты HA**
  * При использовании Basic Load Balancer создайте правила балансировки нагрузки для следующих портов
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 36<strong>&lt;&gt; nr</strong> TCP
    * 39<strong>&lt;&gt; nr</strong> TCP
    * 81<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>ERS

* Конфигурация внешнего интерфейса:
  * IP-адрес для NW1 10.3.1.51
  * IP-адрес для NW2 10.3.1.53
  * IP-адрес для NW3 10.3.1.55

* Порт пробы:
  * Порт 621<strong>&lt;&gt;nr</strong>, поэтому для NW1, NW2, и N3 зонд порты 621**02**, 621**12** и 621**22**
* Правила балансировки нагрузки - создайте по одному для каждого экземпляра, то есть NW1/ERS, NW2/ERS и NW3/ERS.
  * При использовании Standard Load Balancer выберите **порты HA**
  * При использовании Basic Load Balancer создайте правила балансировки нагрузки для следующих портов
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 33<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* Конфигурация серверной части:
  * подключена к основным сетевым интерфейсам всех виртуальных машин, которые должны быть частью кластера (A)SCS/ERS.

> [!Note]
> Когда ВМ без общедоступных IP-адресов помещаются в пул бэкэнда внутреннего (без публичного IP-адреса) балансируев нагрузки Standard Azure, не будет исходящих подключений к Интернету, если не будет выполнена дополнительная конфигурация, позволяющая осуществлять реаутирование в конечные точки общего пользования. Для получения подробной информации о том, как достичь исходящего подключения, смотрите [подключение к конечным точкам для виртуальных машин с помощью баланса azure Standard Load balancer в сценариях высокой доступности SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

> [!IMPORTANT]
> Не включайте метки времени TCP на M-м ими Azure, размещенных за Azure Load Balancer. Включение меток времени TCP приведет к сбою зондов работоспособности. Установить параметр **net.ipv4.tcp_timestamps** до **0**. Для получения подробной информации [см.](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

## <a name="sap-shares"></a>Акции SAP

SAP NetWeaver требует совместного хранения для транспортировки, каталога профилей и так далее. Для высокодоступной системы SAP важно иметь высокодоступные акции. Вам нужно будет определиться с архитектурой для ваших акций SAP. Одним из вариантов является развертывание акций на [объемах NFS Файлов Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)  С помощью файлов Azure NetApp вы получите встроенную высокую доступность для акций SAP NFS.

Другой вариант заключается в создании [GlusterFS на Azure VMs на Red Hat Enterprise Linux для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs), которые могут быть общими между несколькими системами SAP. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Развертывание первой системы SAP в кластере

Теперь, когда вы определились с архитектурой для акций SAP, разместите первую систему SAP в кластере после соответствующей документации.

* При использовании объемов NFS NFS, azure NetApp Files NFS, следуйте высокой [доступности Azure VMs для SAP NetWeaver на Red Hat Enterprise Linux с Azure NetApp Files для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)  
* При использовании кластера GlusterFS следуйте [glusterFS на Azure VMs на Red Hat Enterprise Linux для SAP NetWeaver.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)  

Перечисленные выше документы помогут вам пройти через шаги по подготовке необходимой инфраструктуры, созданию кластера, подготовке ОС для запуска приложения SAP.  

> [!TIP]
> Всегда проверяйте сбой над функциональностью кластера после развертывания первой системы, прежде чем добавлять дополнительные SAP SID-файлы в кластер. Таким образом, вы будете знать, что функциональность кластера работает, прежде чем добавить сложность дополнительных систем SAP в кластер.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Развертывание дополнительных систем SAP в кластере

В этом примере мы предполагаем, что система **NW1** уже была развернута в кластере. Мы покажем, как развернуть в кластере системы SAP **NW2** и **NW3.** 

Следующие элементы прикрепляются либо **с «А»,** применимыми ко всем узлам, и это **применимо** только к узлам 1 или **No2,** применимо только к уму 2.

### <a name="prerequisites"></a>Предварительные требования 

> [!IMPORTANT]
> Прежде чем следовать инструкциям по развертыванию дополнительных систем SAP в кластере, следуйте инструкциям по развертыванию первой системы SAP в кластере, поскольку есть шаги, которые необходимы только во время развертывания первой системы.  

Эта документация предполагает, что:
* Кластер Pacemaker уже настроен и запущен.  
* По крайней мере одна система SAP (пример ASCS / ERS) уже развернута и работает в кластере.  
* Функциональность сбоя кластера была протестирована.  
* Развернуты акции NFS для всех систем SAP.  

### <a name="prepare-for-sap-netweaver-installation"></a>Подготовка к установке SAP NetWeaver

1. Добавьте конфигурацию для недавно развернутой системы (т.е. **NW2,** **NW3**) к существующему балансиру грузоподъемности Azure, следуя инструкциям [Deploy Azure Load Balancer вручную через портал Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#deploy-linux-manually-via-azure-portal) Отрегулируйте IP-адреса, порты зонда работоспособности, правила балансировки нагрузки для вашей конфигурации.  

2. **Разрешение** на настройку для дополнительных систем SAP. Вы можете использовать DNS-сервер или изменить `/etc/hosts` на всех узлах. В этом примере `/etc/hosts` показано, как использовать файл.  Адаптируйте IP-адреса и имена хоста к среде. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **Создайте** общие каталоги для дополнительных систем **NW2** и **NW3** SAP, которые вы развертываете в кластере. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **Добавьте** записи крепления для /sapmnt/SID и /usr/sap/SID/SYS файловых систем для дополнительных систем SAP, которые вы развертываете в кластере. В этом примере **NW2** и **NW3**.  

   Обновление `/etc/fstab` файла с файловыми системами для дополнительных систем SAP, которые вы развертываете в кластере.  

   * При использовании файлов Azure NetApp следуйте инструкциям [здесь](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#prepare-for-sap-netweaver-installation)  
   * При использовании кластера GlusterFS следуйте инструкциям [здесь](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel#prepare-for-sap-netweaver-installation)  

### <a name="install-ascs--ers"></a>Установка ASCS / ERS

1. Создание виртуальных кластерных ресурсов зонда IP и работоспособности для экземпляров ASCS дополнительных систем SAP, развертываемых в кластере. Пример, показанный здесь для **NW2** и **NW3** ASCS, используя NFS на Лазурном Приложении Файлов томов с протоколом NFSv3.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Убедитесь, что статус кластера в порядке и что все ресурсы запущены. Не важно, на каком узле выполняются ресурсы.  

2. **[1]** Установите SAP NetWeaver ASCS.  

   Установите SAP NetWeaver ASCS в качестве корня, используя виртуальное хостимя, которое отображает IP-адрес конфигурации фронтера нагрузки для ASCS. Например, для системы **NW2**виртуальное хостимя <b>msnw2ascs,</b> <b>10.3.1.52</b> и номер экземпляра, который вы использовали для зонда балансора нагрузки, например <b>10</b>. Для системы **NW3**, виртуальный хостимя <b>msnw3ascs</b>, <b>10.3.1.54</b> и номер экземпляра, который вы использовали для зонда балансора нагрузки, например <b>20</b>. Запишите, на каком кластерном узлах установлен ASCS для каждого SAP SID.  

   Чтобы разрешить непривилегированному пользователю подключаться к SAPinst, можно использовать параметр SAPINST_REMOTE_ACCESS_USER. Для установки SAP можно использовать параметр SAPINST_USE_HOSTNAME, используя виртуальное имя хоста.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Если установка не может создать субфолдер в /usr/sap/**SID**/ASCS**Instance ,** попробуйте установить владельца, чтобы **sid**adm и группы sapsys из ASCS**Instance и** повторить попытку.

3. **Создайте** виртуальные кластерные ресурсы IP и зондов для ERS-экземпляра дополнительной системы SAP, развертываемых в кластере. Пример, показанный здесь для **NW2** и **NW3** ERS, используя NFS на лазурных netApp файлов объемов с протоколом NFSv3.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Убедитесь, что статус кластера в порядке и что все ресурсы запущены.  

   Затем убедитесь, что ресурсы вновь созданной группы ERS работают на кластерном узеле, напротив кластерного узла, где был установлен экземпляр ASCS для той же системы SAP.  Например, если NW2 ASCS `rhelmsscl1`был установлен на , то убедитесь, что группа NW2 ERS работает на `rhelmsscl2`.  Вы можете перенести группу `rhelmsscl2` NW2 ERS, запустив следующую команду для одного из кластерных ресурсов в группе: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** Установите SAP NetWeaver ERS.

   Установите SAP NetWeaver ERS в качестве корня на другом узлах, используя виртуальное хостимя, которое отображает IP-адрес конфигурации фронтового баланса нагрузки для ERS. Например, для системы **NW2,** виртуальное имя хоста будет <b>msnw2ers</b>, <b>10.3.1.53</b> и номер экземпляра, который вы использовали для зонда балансора нагрузки, например <b>12</b>. Для системы **NW3**, виртуальное имя хозяина <b>msnw3ers</b>, <b>10.3.1.55</b> и номер экземпляра, который вы использовали для зонда балансора нагрузки, например <b>22</b>. 

   Чтобы разрешить непривилегированному пользователю подключаться к SAPinst, можно использовать параметр SAPINST_REMOTE_ACCESS_USER. Для установки SAP можно использовать параметр SAPINST_USE_HOSTNAME, используя виртуальное имя хоста.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Используйте SWPM SP 20 PL 05 или более поздней версии. Более ранние версии задают разрешения неправильно, и установка завершится с ошибкой.

   Если установка не может создать субфолдер в /usr/sap/**NW2**/ERS**Instance**, попробуйте установить владельца, чтобы **sid**adm и группы sapsys из папки ERS**Instance и** повторить.

   Если необходимо было перенести группу ERS недавно развернутой системы SAP в другой кластерный узла, не забудьте удалить ограничение местоположения для группы ERS. Можно удалить ограничение, запустив следующую команду (пример приведен для систем SAP **NW2** и **NW3).** Убедитесь в том, чтобы удалить временные ограничения для того же ресурса, который использовался в команде для перемещения группы кластеров ERS.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **Адаптируйте** профили экземпляров ASCS/SCS и ERS для недавно установленной системы SAP (ы). Приведенный ниже пример предназначен для NW2. Вам нужно будет адаптировать профили ASCS/SCS и ERS для всех экземпляров SAP, добавленных в кластер.  
 
   * Профиль ASCS/SCS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter
      enque/encni/set_so_keepalive = true
      ```

   * Профиль ERS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** Изменение файла /usr/sap/sapservices

   Чтобы предотвратить запуск экземпляров скриптом запуска sapinit, все экземпляры, управляемые Pacemaker, должны быть прокомментированы из `/usr/sap/sapservices` файла.  Приведенный ниже пример предназначен для систем SAP **NW2** и **NW3.**  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **Создайте** кластерные ресурсы SAP для недавно установленной системы SAP.  

   При использовании архитектуры сервера enqueue 1 (ENSA1) определите ресурсы для систем SAP **NW2** и **NW3** следующим образом:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP представила поддержку сервера enqueue 2, включая репликацию, по состоянию на SAP NW 7.52. Начиная с ABAP Platform 1809, сервер enqueue 2 устанавливается по умолчанию. Смотрите Примечание SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) для поддержки сервера enqueue 2.
   При использовании архитектуры сервера enqueue 2[(ENSA2),](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)определите ресурсы для систем SAP **NW2** и **NW3** следующим образом:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Если вы переходите из старой версии и переходите на сервер enqueue 2, см. Примечание SAP [2641019.](https://launchpad.support.sap.com/#/notes/2641019) 

   > [!NOTE]
   > Тайм-ауты в вышеуказанной конфигурации являются лишь примерами и, возможно, должны быть адаптированы к конкретной настройке SAP. 

   Убедитесь, что состояние кластера — "ОК" и что запущены все ресурсы. Не важно, на каком узле выполняются ресурсы.
   Следующий пример показывает состояние ресурсов кластера после добавления в кластер систем SAP **NW2** и **NW3.** 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **Добавление** правил брандмауэра для ASCS и ERS на обоих узлах.  В приведенном ниже примере показаны правила брандмауэра для систем SAP **NW2** и **NW3.**  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Продолжить установку SAP 

Завершите установку SAP:

* [Подготовка серверов приложений SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Установка экземпляра DBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#install-database)
* [Установка основного сервера приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#sap-netweaver-application-server-installation)
* Установка одного или нескольких дополнительных экземпляров применения SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Протестная установка кластера multi-SID

Следующие тесты представляют собой подмножество тестовых случаев в лучших руководствах Red Hat. Они включены для вашего удобства. Для полного списка кластерных тестов, ссылка на следующую документацию:

* При использовании объемов NFS NFS, azure NetApp Files, следуйте высокой [доступности Azure VMs для SAP NetWeaver на RHEL с azure NetApp Files для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* При использовании `GlusterFS`высокодоступных, следуйте [Azure VMs высокой доступности для SAP NetWeaver на RHEL для приложений SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)  

Всегда читайте руководства Red Hat по рекомендациям и выполняйте все дополнительные тесты, которые могли быть добавлены.  
Представленные тесты находятся в двухузайном многоsid-кластере с тремя установленными системами SAP.  

1. Вручную перенести экземпляр ASCS. На примере показано, что экземпляр ASCS для системы SAP NW3.

   Состояние ресурсов перед запуском теста:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Выполняй следующие команды в качестве корня для переноса экземпляра NW3 ASCS.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Состояние ресурсов после теста:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Имитирование сбоя узла

   Состояние ресурсов перед запуском теста:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Выполнить следующую команду в качестве корня на узлах, где работает по крайней мере один экземпляр ASCS. В этом примере мы выполнили команду на `rhelmsscl1`, где asCS экземпляры для NW1, NW2 и NW3 работают.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   Статус после испытания, и после того, как узла, который был разбился снова, должен выглядеть следующим образом.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Если есть сообщения для неисправных ресурсов, очистите состояние неисправных ресурсов. Пример:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Дальнейшие действия

* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Azure][deployment-guide]
* [Развертывание СУБД виртуальных машин Azure для SAP][dbms-guide]
* Дополнительные сведения об установке высокого уровня доступности и плана для аварийного восстановления SAP HANA на виртуальных машинах Azure см. в статье [Высокий уровень доступности SAP HANA на виртуальных машинах Azure][sap-hana-ha].
