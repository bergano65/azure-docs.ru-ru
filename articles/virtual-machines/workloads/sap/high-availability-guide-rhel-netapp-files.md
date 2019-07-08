---
title: Azure виртуальные машины высокой доступности SAP NetWeaver в Red Hat Enterprise Linux с файлами Azure NetApp | Документация Майкрософт
description: Обеспечение высокого уровня доступности SAP NetWeaver в виртуальных машинах Azure с Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503582"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Azure виртуальные машины высокой доступности SAP NetWeaver в Red Hat Enterprise Linux с файлами NetApp Azure для приложений SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

В этой статье описывается развертывание виртуальных машин, Настройка виртуальных машин, установка платформы кластера и установка высокодоступной системы SAP NetWeaver 7.50, с помощью [NetApp службы файлов Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
В примерах конфигурации, командах установки и т. д. Экземпляр ASCS номер 00, экземпляр ERS номер 01, основного экземпляра приложений (PAS) — 02 и экземпляр приложения (AAS) — 03 — Используется QAS идентификатор системы SAP. 

На уровне базы данных не рассматриваются в этой статье.  

Прежде всего прочитайте следующие примечания и документы SAP:

* [Документация по Azure файлы NetApp][anf-azure-doc] 
* примечание к SAP [1928533], которое содержит:
  * список размеров виртуальных машин Azure, поддерживаемых для развертывания ПО SAP;
  * важные сведения о доступных ресурсах для каждого размера виртуальной машины Azure;
  * сведения о поддерживаемом программном обеспечении SAP и сочетаниях операционных систем и баз данных;
  * сведения о требуемой версии ядра SAP для Windows и Linux в Microsoft Azure.

* примечание к SAP [2015553], в котором описываются предварительные требования к SAP при развертывании программного обеспечения SAP в Azure;
* примечание к SAP [2002167], содержащее рекомендуемые параметры ОС для Red Hat Enterprise Linux;
* примечание к SAP [2009879], содержащее рекомендации по SAP HANA для Red Hat Enterprise Linux;
* примечание к SAP [2178632], содержащее подробные сведения обо всех доступных метриках мониторинга для SAP в Azure;
* примечание к SAP [2191498], содержащее сведения о необходимой версии агента SAP Host Agent для Linux в Azure;
* примечание к SAP [2243692], содержащее сведения о лицензировании SAP в Linux в Azure;
* примечание к SAP [1999351], содержащее дополнительные сведения об устранении неполадок, связанных с расширением для расширенного мониторинга Azure для SAP;
* [вики-сайт сообщества SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), содержащий все необходимые примечания к SAP для Linux;
* [На виртуальных машинах планированию и внедрению SAP NETWEAVER на платформе Linux][planning-guide]
* [Развертывание виртуальных машин для SAP в Linux][deployment-guide]
* [Развертывание программного обеспечения SAP на платформе Linux для Azure виртуальные машины СУБД][dbms-guide]
* [SAP Netweaver в кластере pacemaker](https://access.redhat.com/articles/3150081)
* Общая документация по RHEL
  * [Общие сведения о надстройке для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Администрирование надстройки для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Справочник по надстройке для обеспечения высокой доступности](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Настройка ASCS/ERS для SAP Netweaver с автономными ресурсами в RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Настройка S/4HANA SAP ASCS/ERS с сервером постановки в очередь автономных 2 (ENSA2) в Pacemaker на RHEL ](https://access.redhat.com/articles/3974941)
* Документация по RHEL для Azure:
  * [Политики поддержки для кластеров высокой доступности RHEL — виртуальные машины Microsoft Azure как члены кластера](https://access.redhat.com/articles/3131341)
  * [Установка и настройка кластера высокой доступности Red Hat Enterprise Linux 7.4 (и более поздних версий) в Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp приложения SAP в Microsoft Azure, использующих службу файлов Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Обзор

Высокий уровень availability(HA) для центральные службы SAP Netweaver требует общее хранилище.
Чтобы добиться этого в Red Hat Linux до сих требовалось построить отдельный кластер высокодоступной GlusterFS. 

Теперь можно достичь SAP Netweaver высокого уровня ДОСТУПНОСТИ с помощью общего хранилища, развернутых в Azure файлы NetApp. С помощью Azure NetApp файлы для общего хранилища устраняет необходимость в дополнительных [GlusterFS кластера](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). Pacemaker по-прежнему необходим для обеспечения высокой ДОСТУПНОСТИ из центра services(ASCS/SCS) SAP Netweaver.

![Общие сведения о высоком уровне доступности SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS и база данных SAP HANA используют виртуальное имя узла и виртуальные IP-адреса. Балансировщику нагрузки в Azure нужен виртуальный IP-адрес. Ниже показана конфигурация подсистемы балансировки нагрузки с front отдельный IP-адресов для (A) SCS и ERS.

> [!IMPORTANT]
> Кластеризации с несколькими ИД безопасности из SAP ASCS/ERS с номером Red Hat Linux, как гостевой ОС на виртуальных машинах Azure — **не поддерживается**. С несколькими ИД безопасности кластеризации описывается установка нескольких экземпляров SAP ASCS/ERS при использовании разные идентификаторы безопасности в одном кластере Pacemaker.

### <a name="ascs"></a>(A)SCS

* Конфигурация внешнего интерфейса:
  * IP-адрес 192.168.14.9
* Конфигурация серверной части:
  * подключена к основным сетевым интерфейсам всех виртуальных машин, которые должны быть частью кластера (A)SCS/ERS.
* Порт пробы:
  * порт 620<strong>&lt;nr&gt;</strong>.
* Правила балансировки нагрузки
  * 32<strong>&lt;nr&gt;</strong> TCP;
  * 36<strong>&lt;nr&gt;</strong> TCP;
  * 39<strong>&lt;nr&gt;</strong> TCP;
  * 81<strong>&lt;nr&gt;</strong> TCP;
  * 5<strong>&lt;nr&gt;</strong>13 TCP;
  * 5<strong>&lt;nr&gt;</strong>14 TCP;
  * 5<strong>&lt;nr&gt;</strong>16 TCP.

### <a name="ers"></a>ERS

* Конфигурация внешнего интерфейса:
  * IP-адрес 192.168.14.10
* Конфигурация серверной части:
  * подключена к основным сетевым интерфейсам всех виртуальных машин, которые должны быть частью кластера (A)SCS/ERS.
* Порт пробы:
  * Порт 621<strong>&lt;nr&gt;</strong>.
* Правила балансировки нагрузки
  * 32<strong>&lt;nr&gt;</strong> TCP;
  * 33<strong>&lt;nr&gt;</strong> TCP;
  * 5<strong>&lt;nr&gt;</strong>13 TCP;
  * 5<strong>&lt;nr&gt;</strong>14 TCP;
  * 5<strong>&lt;nr&gt;</strong>16 TCP.

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Настройка инфраструктуры NetApp службы файлов Azure 

SAP NetWeaver требует общее хранилище для каталога профиля и транспорта.  Прежде чем продолжить настройку инфраструктуры файлы Azure NetApp, ознакомьтесь с [документации по службе файлов Azure NetApp][anf-azure-doc]. Проверьте, если в выбранном регионе Azure предлагает NetApp службы файлов Azure. По следующей ссылке представлены сведения о доступности NetApp файлов Azure по регионам Azure: [Azure NetApp файлы доступности по регионам Azure][anf-avail-matrix].

Служба файлов Azure NetApp доступен в нескольких [регионов Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Перед развертыванием службы файлов Azure NetApp, запросить подключение к службе файлов Azure NetApp, следуя [регистрации инструкции файлы Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Развертывание ресурсов Azure NetApp файлов  

Предполагается, что вы уже развернули [виртуальной сети Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Ресурсы службы файлов Azure NetApp и виртуальные машины, куда будет подключен ресурсы Azure NetApp файлы должны быть развернуты в той же виртуальной сети Azure или в пиринговых виртуальных сетях Azure.  

1. Если вы еще не сделали, запросить [адаптации к службе файлов Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Создайте учетную запись NetApp в выбранном регионе Azure, следуя [инструкции по созданию учетной записи NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Настройте пул емкость NetApp службы файлов Azure, следуя [инструкции по настройке службы файлов Azure NetApp емкость пула](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Архитектура SAP Netweaver, представленных в этой статье использует один пул файлов NetApp Azure емкость, номер SKU уровня "премиум". Мы рекомендуем использовать SKU "премиум" файлы Azure NetApp для рабочей нагрузки приложения SAP Netweaver в Azure.  
4. Делегировать подсети, к файлам Azure NetApp, как описано в разделе [инструкции делегировать подсети в службу файлов Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Развертывание службы файлов Azure NetApp тома, в соответствии с [инструкции по созданию тома для службы файлов Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Выполнять развертывание томов в назначенным файлам Azure NetApp [подсети](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Имейте в виду, что ресурсы службы файлов Azure NetApp и виртуальные машины Azure должны находиться в той же виртуальной сети Azure, или в пиринговых виртуальных сетях Azure. В этом примере мы используем два тома службы файлов Azure NetApp: sap<b>QAS</b> и transSAP. Пути к файлам, которые должны быть подключены к соответствующей точки подключения, /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys, д.  

   1. тома sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. тома sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. тома sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. тома sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. transSAP тома (nfs://192.168.24.4/transSAP)
   6. тома sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. тома sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
В этом примере мы использовали для демонстрации использования файлов Azure NetApp NetApp службы файлов Azure для всех файловых систем SAP Netweaver. Файловые системы SAP, не требует подключения через NFS можно также развернуть в качестве [хранилища дисков Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . В этом примере <b>a-e</b> должны находиться в Azure файлы NetApp и <b>f-g</b> (то есть/usr/sap/<b>QAS</b>/D<b>02</b>, /usr/sap/<b>QAS </b>/D<b>03</b>) может быть развернут как хранилище дисков Azure. 

### <a name="important-considerations"></a>Важные сведения

При рассмотрении NetApp службы файлов Azure для SAP Netweaver на SUSE высокодоступная архитектура, необходимо учитывать следующие важные моменты.

- Минимальная емкость пула — 4 Тиб. Размер емкости пула должен быть кратно 4 Тиб.
- Минимальное том содержит 100 Гиб
- Служба файлов Azure NetApp и все виртуальные машины, куда будет подключен тома файлов Azure NetApp, должны находиться в той же виртуальной сети Azure или в [пиринговые виртуальные сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) в одном регионе. Теперь поддерживается Azure файлы NetApp доступ через ПИРИНГ виртуальных сетей в одном регионе. Azure NetApp доступ через глобальный пиринг не поддерживается.
- Выбранной виртуальной сети должен иметь подсеть, делегировать NetApp службы файлов Azure.
- Служба файлов Azure NetApp в настоящее время поддерживает только NFSv3 
- Служба файлов Azure NetApp предоставляет [экспортировать политику](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): разрешенных клиентов, можно управлять тип доступа (чтение и запись, только для чтения и т. д.). 
- Компонент Azure NetApp файлы еще не поясе. В настоящее время функции Azure NetApp файлов не был развернут во всех зонах доступности в регионе Azure. Имейте в виду потенциальные последствия задержку в некоторых регионах Azure. 

## <a name="setting-up-ascs"></a>Настройка (A)SCS

В этом примере ресурсы развернуты вручную с помощью [портала Azure](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Развертывание Linux вручную с помощью портала Azure

Сначала необходимо создать тома службы файлов Azure NetApp. Разверните виртуальные машины. После этого следует создать подсистему балансировки нагрузки и использовать виртуальные машины во внутренних пулах.

1. Создание подсистемы балансировки нагрузки (внутренней).  
   1. Создайте IP-адреса внешнего интерфейса.
      1. IP-адрес 192.168.14.9 для ASCS
         1. Откройте подсистему балансировки нагрузки, выберите пул внешних IP-адресов и щелкните "Добавить".
         1. Введите имя нового пула IP-адрес внешнего интерфейса (например **внешнего интерфейса. ОЦЕНКА КАЧЕСТВА. ASCS**)
         1. Параметра "Назначение" на статический и введите IP-адрес (например **192.168.14.9**)
         1. Нажмите кнопку "ОК"
      1. IP-адрес 192.168.14.10 для ASCS ERS
         * Повторите предыдущие шаги в разделе «» для создания IP-адрес для ERS (например **192.168.14.10** и **внешнего интерфейса. ОЦЕНКА КАЧЕСТВА. ERS**)
   1. Создайте внутренние пулы.
      1. Создайте внутренний пул для ASCS.
         1. Выберите подсистему балансировки нагрузки, щелкните "Серверные пулы" и нажмите кнопку "Добавить".
         1. Введите имя нового внутреннего пула (например **серверной части. Оценка Качества**)
         1. Щелкните "Добавить виртуальную машину".
         1. Выберите группу доступности, созданную ранее для ASCS 
         1. Выберите виртуальные машины кластера (A)SCS.
         1. Нажмите кнопку "ОК"
   1. Создайте пробы работоспособности.
      1. Порт 620**00** для ASCS
         1. Выберите подсистему балансировки нагрузки, щелкните "Зонды работоспособности" и нажмите кнопку "Добавить".
         1. Введите имя новой пробы работоспособности (например **работоспособности. ОЦЕНКА КАЧЕСТВА. ASCS**)
         1. Выберите протокол TCP, порт 620**00**, интервал, равный 5, и порог состояния неработоспособности, равный 2.
         1. Нажмите кнопку "ОК"
      1. Порт 621**01** для ASCS ERS
            * Повторите предыдущие шаги в разделе «c», чтобы создать пробу работоспособности для ERS (например, 621**01** и **работоспособности. ОЦЕНКА КАЧЕСТВА. ERS**)
   1. Правила балансировки нагрузки
      1. TCP 32**00** для ASCS
         1. Подсистему балансировки нагрузки правила балансировки нагрузки и щелкните "Добавить"
         1. Введите имя нового правила балансировки нагрузки (например **балансировки нагрузки. ОЦЕНКА КАЧЕСТВА. ASCS.3200**)
         1. Выберите внешний IP-адрес для ASCS, внутренний пул и пробу работоспособности, созданные ранее (например **внешнего интерфейса. ОЦЕНКА КАЧЕСТВА. ASCS**)
         1. Оставьте выбранным протокол **TCP** и введите порт **3200**.
         1. Увеличьте время ожидания до 30 минут.
         1. **Не забудьте включить плавающий IP-адрес**.
         1. Нажмите кнопку "ОК"
      1. Дополнительные порты для ASCS
         * Повторите предыдущие шаги в разделе «d» порты 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 и TCP-порт для ASCS
      1. Дополнительные порты для ASCS ERS
         * Повторите предыдущие шаги в разделе «d» для портов 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 и TCP для ASCS ERS


> [!IMPORTANT]
> Не включайте отметки времени TCP на виртуальных машинах Azure, размещенных за Azure Load Balancer. Включение TCP отметки времени вызовет пробы работоспособности, переход на другой. Задайте для параметра **net.ipv4.tcp_timestamps** для **0**. Дополнительные сведения см. [пробы работоспособности подсистемы балансировки нагрузки](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Создание кластера Pacemaker

Следуйте указаниям в статье [Настройка кластера Pacemaker в Red Hat Enterprise Linux в Azure](high-availability-guide-rhel-pacemaker.md), чтобы создать базовый кластер Pacemaker для этого (A)SCS-сервера.

### <a name="prepare-for-sap-netweaver-installation"></a>Подготовка к установке SAP NetWeaver

Ниже приведены элементы с префиксами: **[A]**  — применяется ко всем узлам, **[1**] — применяется только к узлу 1, **[2]**  — применяется только к узлу 2.

1. **[A]** Установите разрешения имен.

   Можно использовать DNS-сервер или внести изменения в файл /etc/hosts на всех узлах. В этом примере показано, как использовать файл /etc/hosts.
   Замените IP-адрес и имя узла в следующих командах.

    ```
    sudo vi /etc/hosts
    ```

   Вставьте следующие строки в /etc/hosts. Измените IP-адрес и имя узла в соответствии со своей средой.

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]**  Каталоги SAP создайте тома службы файлов Azure NetApp.  
   Подключить временно NetApp службы файлов Azure на одном из виртуальных машин и создать каталоги SAP (пути к файлам).  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp

1. **[A]** Create the shared directories

   ```
   sudo mkdir -p/sapmnt/QAS sudo mkdir -p /usr/sap/trans sudo mkdir -p /usr/sap/QAS/SYS sudo mkdir -p /usr/sap/QAS/ASCS00 sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr + я chattr QAS sudo/sapmnt / + я /usr/sap/trans sudo chattr + я /usr/sap/QAS/SYS sudo chattr + я /usr/sap/QAS/ASCS00 sudo chattr + я /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>Загружены подключаемые модули: пакетов поддержки языков, артикул, поиска disabled репозиториев
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Repodata — более 2 недель. Установить yum cron? Или запустите: быстрый makecache yum
   # <a name="installed-packages"></a>Установленные пакеты
   # <a name="name---------resource-agents-sap"></a>Name        : resource-agents-sap
   # <a name="arch---------x8664"></a>Arch: x86_64
   # <a name="version------395"></a>Версия: 3.9.5
   # <a name="release------124el7"></a>Выпуск: 124.el7
   # <a name="size---------100-k"></a>Размер: 100 тыс.
   # <a name="repo---------installed"></a>Репозиторий: установлено
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>Из репозитория: rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>Сводка. Агенты ресурсов кластера SAP и скрипт соединителя
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>URL-АДРЕС: https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>Лицензия: GPLv2 +
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>Описание: Агенты ресурсов SAP и интерфейс скрипта соединителя с
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>: Чтобы разрешить экземпляров SAP для управления в кластере pacemaker
   #          <a name="-environment"></a>: среда.
   ```


1. **[A]** Add mount entries

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Добавьте следующие строки в fstab, сохраните и закройте
    192.168.24.5:/sapQAS/sapmntQAS/sapmnt/QAS nfs rw, трудно, rsize = 65536, wsize = 65536, vers = 3 192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw, трудно, rsize = 65536, wsize = 65536, vers = 3 192.168.24.4:/transSAP /usr/sap/trans nfs rw, трудно, rsize = 65536, wsize = 65536, vers = 3
   ```

   Mount the new shares

   ```
   sudo mount -  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Задайте для свойства ResourceDisk.EnableSwap значение y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Создание и использование файла подкачки на диске ресурсов.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Задать размер файла ПОДКАЧКИ со свойством ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>Свободное пространство диска ресурсов зависит от размера виртуальной машины. Убедитесь, что не присвоили значение, слишком велик. Вы можете проверить подкачки с помощью команды swapon
   # <a name="size-of-the-swapfile"></a>Размер файла подкачки.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   При перезапуске службы waagent sudo
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   anftstsapcl2 резервный узел ПК sudo
   
   создания ресурса ПК sudo fs_QAS_ASCS device='192.168.24.5:/sapQAS/usrsapQASascs файловой системы \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' \
     --группе g QAS_ASCS
   
   создания ресурса ПК sudo vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --группе g QAS_ASCS
   
   sudo ПК ресурсов создайте nc_QAS_ASCS балансировки нагрузки azure порт = 62000 \
     --группе g QAS_ASCS
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   состояние ПК sudo
   
   # <a name="node-anftstsapcl2-standby"></a>Узел anftstsapcl2: резервный
   # <a name="online--anftstsapcl1-"></a>Online: [anftstsapcl1]
   #
   # <a name="full-list-of-resources"></a>Полный список ресурсов:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure (stonith:fence_azure_arm):      Anftstsapcl1 по началу работы
   #  <a name="resource-group-g-qasascs"></a>Группа ресурсов: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl1 по началу работы
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Anftstsapcl1 по началу работы
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl1 по началу работы
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Разрешите доступ к SWPM. Это правило не является окончательным. Если вы перезагрузите компьютер, необходимо повторно запустить команду.
   брандмауэр sudo-cmd--зоны = public---port = 4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00 sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   sudo ПК узел unstandby anftstsapcl2 sudo ПК узел резервный anftstsapcl1
   
   создания ресурса ПК sudo fs_QAS_AERS device='192.168.24.5:/sapQAS/usrsapQASers файловой системы \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' \
    --группе g QAS_AERS

   создания ресурса ПК sudo vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --группе g QAS_AERS
   
   sudo ПК ресурсов создайте nc_QAS_AERS порт балансировки нагрузки azure = 62101 \
    --группе g QAS_AERS
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   состояние ПК sudo
   
   # <a name="node-anftstsapcl1-standby"></a>Узел anftstsapcl1: резервный
   # <a name="online--anftstsapcl2-"></a>Online: [anftstsapcl2]
   #
   # <a name="full-list-of-resources"></a>Полный список ресурсов:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure (stonith:fence_azure_arm):      Anftstsapcl2 по началу работы
   #  <a name="resource-group-g-qasascs"></a>Группа ресурсов: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl2 по началу работы
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):      К работе anftstsapcl2 <
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl2 по началу работы
   #  <a name="resource-group-g-qasaers"></a>Группа ресурсов: g-QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl2 по началу работы
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS (ocf::heartbeat:azure-lb):      Anftstsapcl2 по началу работы
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Anftstsapcl2 по началу работы
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Разрешите доступ к SWPM. Это правило не является окончательным. Если вы перезагрузите компьютер, необходимо повторно запустить команду.
   брандмауэр sudo-cmd--зоны = public---port = 4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01 sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Изменение команды перезапуска для команды запуска
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 = локального pf=$(_PF) $(_EN)
   Start_Program_01 = локального pf=$(_PF) $(_EN)
   
   # <a name="add-the-keep-alive-parameter"></a>Добавьте параметр keep-alive
   enque/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Изменение команды перезапуска для команды запуска
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # <a name="remove-autostart-from-ers-profile"></a>удалить автозапуска из профиля ERS
   # <a name="autostart--1"></a>Автоматический запуск = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>Изменения конфигурации системы Linux
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>На узле, где установлен ASCS закомментируйте следующую строку
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>Значение LD_LIBRARY_PATH = / usr/sap/QAS/ASCS00/exe: значение LD_LIBRARY_PATH$; экспортировать значение LD_LIBRARY_PATH; /USR/SAP/QAS/ASCS00/exe/sapstartsrv pf = / usr/sap/QAS/SYS/профилей/QAS_ASCS00_anftstsapvh - D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>На узле, где установлен ERS закомментируйте следующую строку
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>Значение LD_LIBRARY_PATH = / usr/sap/QAS/ERS01/exe: значение LD_LIBRARY_PATH$; экспортировать значение LD_LIBRARY_PATH; /USR/SAP/QAS/ERS01/exe/sapstartsrv pf = / usr/sap/QAS/ERS01/профилей/QAS_ERS01_anftstsapers - D -u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   sudo pcs property set maintenance-mode=true
   
    создания ресурса ПК sudo rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --группе g QAS_ASCS
   
    создания ресурса ПК sudo rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --группе g QAS_AERS
      
    совместное размещение ограничение ПК sudo добавить g-QAS_AERS с g-QAS_ASCS-5000 sudo ПК ограничение расположения rsc_sap_QAS_ASCS00 правило оценкой = 2000 runs_ers_QAS eq 1 sudo ПК ограничение порядка g QAS_ASCS и g QAS_AERS вид = необязательно симметричное = false
    
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    sudo pcs property set maintenance-mode=true
    
    создания ресурса ПК sudo rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    --группе g QAS_ASCS
   
    создания ресурса ПК sudo rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --группе g QAS_AERS
      
    совместное размещение ограничение ПК sudo добавить g-QAS_AERS g-QAS_ASCS-5000 sudo ПК ограничение порядка g QAS_ASCS и g QAS_AERS вида = необязательно симметричное = false
   
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    состояние ПК sudo
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>Online: [anftstsapcl1 anftstsapcl2]
    #
    # <a name="full-list-of-resources"></a>Полный список ресурсов:
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure (stonith:fence_azure_arm):      Anftstsapcl2 по началу работы
    #  <a name="resource-group-g-qasascs"></a>Группа ресурсов: g-QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Anftstsapcl2 по началу работы
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Anftstsapcl2 по началу работы
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       Anftstsapcl2 по началу работы
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 по началу работы
    #  <a name="resource-group-g-qasaers"></a>Группа ресурсов: g-QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):    Anftstsapcl1 по началу работы
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS (ocf::heartbeat:azure-lb):      Anftstsapcl1 по началу работы
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS (ocf::heartbeat:IPaddr2):       Anftstsapcl1 по началу работы
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 по началу работы
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>Порт пробы для ASCS
   брандмауэра sudo-cmd - зоны = public — добавить порт = 62000/tcp--брандмауэра постоянных sudo-cmd - зоны = public — добавить порт = брандмауэра sudo 62000/tcp-cmd--зоны = public — добавить порт = 3200/tcp--брандмауэра постоянных sudo-cmd--зоны = public — добавить порт = sudo 3200/tcp брандмауэра cmd--зоны = public — добавить порт = 3600/tcp--брандмауэра постоянных sudo-cmd - зоны = public — добавить порт = 3600/tcp sudo брандмауэра cmd - зоны = public — добавить порт = 3900/tcp--брандмауэра постоянных sudo-cmd--зоны = public — добавить порт = sudo 3900/tcp брандмауэра cmd - зоны = public — добавить порт = 8100/tcp--брандмауэра постоянных sudo-cmd - зоны = public — добавить порт = брандмауэра sudo 8100/tcp-cmd--зоны = public — добавить порт = 50013/tcp--брандмауэра постоянных sudo-cmd--зоны = public — добавить порт = sudo 50013/tcp брандмауэра cmd--зоны = public — добавить порт = 50014/tcp--брандмауэра постоянных sudo-cmd--зоны = public — добавить порт = брандмауэра sudo 50014/tcp-cmd - зоны = public — добавить порт = 50016/tcp--брандмауэра постоянных sudo-cmd - зоны = public — добавить порт = 50016/tcp
   # <a name="probe-port-of-ers"></a>Порт пробы ERS
   брандмауэра sudo-cmd - зоны = public — добавить порт = 62101/tcp--брандмауэра постоянных sudo-cmd--зоны = public — добавить порт = брандмауэра sudo 62101/tcp-cmd--зоны = public — добавить порт = 3301/tcp--брандмауэра постоянных sudo-cmd - зоны = public — добавить порт = sudo 3301/tcp брандмауэра cmd--зоны = public — добавить порт = 50113/tcp--брандмауэра постоянных sudo-cmd--зоны = public — добавить порт = брандмауэра sudo 50113/tcp-cmd - зоны = public — добавить порт = 50114/tcp--брандмауэра постоянных sudo-cmd - зоны = public — добавить порт = sudo 50114/tcp брандмауэр cmd--зоны = public — добавить порт = 50116/tcp--брандмауэра постоянных sudo-cmd - зоны = public — добавить порт = 50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   sudo vi/etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>IP-адрес конфигурации внешнего интерфейса подсистемы балансировки нагрузки для SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>IP-адрес конфигурации внешнего интерфейса подсистемы балансировки нагрузки для SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapa01 anftstsapers 192.168.14.7 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   sudo mkdir -p/sapmnt/QAS sudo mkdir -p /usr/sap/trans

   sudo chattr + я chattr QAS sudo/sapmnt / + я /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Add mount entries  

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Добавьте следующие строки в fstab, сохраните и закройте
   192.168.24.5:/sapQAS/sapmntQAS/sapmnt/QAS nfs rw, трудно, rsize = 65536, wsize = 65536, vers = 3 192.168.24.4:/transSAP /usr/sap/trans nfs rw, трудно, rsize = 65536, wsize = 65536, vers = 3
   ```

   Mount the new shares

   ```
   sudo mount -
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D02 sudo chattr + я /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Добавьте следующую строку в fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw, трудно, rsize = 65536, wsize = 65536, vers = 3
   
   # <a name="mount"></a>Подключение
   sudo mount -
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D03 sudo chattr + я /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Добавьте следующую строку в fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw, трудно, rsize = 65536, wsize = 65536, vers = 3
   
   # <a name="mount"></a>Подключение
   sudo mount -
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Задайте для свойства ResourceDisk.EnableSwap значение y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Создание и использование файла подкачки на диске ресурсов.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Задать размер файла ПОДКАЧКИ со свойством ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>Свободное пространство диска ресурсов зависит от размера виртуальной машины. Убедитесь, что не присвоили значение, слишком велик. Вы можете проверить подкачки с помощью команды swapon
   # <a name="size-of-the-swapfile"></a>Размер файла подкачки.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   При перезапуске службы waagent sudo
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   Список hdbuserstore
   ```

   This should list all entries and should look similar to
   ```
   ДАННЫЕ файла: /home/qasadm/.hdb/anftstsapa01/SSFS_HDB. ФАЙЛ ключа DAT: /home/qasadm/.hdb/anftstsapa01/SSFS_HDB. КЛЮЧ
   
   ENV КЛЮЧА ПО УМОЛЧАНИЮ: ПОЛЬЗОВАТЕЛЬ 192.168.14.4:30313: SAPABAP1 БАЗЫ ДАННЫХ: ОЦЕНКА КАЧЕСТВА
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   su - SET DEFAULT hdbuserstore qasadm qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 по началу работы
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~] rsc_sap_QAS_ASCS00 перемещение # ПК ресурсов
   
   [root@anftstsapcl1 ~] # ПК ресурсов снимите rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>Удалить завершившиеся сбоем действия для ERS, произошедшей в ходе миграции
   [root@anftstsapcl1 ~] rsc_sap_QAS_ERS01 очистки ресурсов ПК #
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl2: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 по началу работы
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl2: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 по началу работы
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~] # echo b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   Online: [anftstsapcl1 anftstsapcl2]
   
   Полный список ресурсов:
   
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 по началу работы
   
   Завершившиеся сбоем действия:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~] rsc_sap_QAS_ERS01 очистки ресурсов ПК #
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 по началу работы
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 по началу работы
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~] # установку ms.sapQAS | xargs kill -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~] rsc_sap_QAS_ASCS00 очистки ресурсов ПК # [root@anftstsapcl1 ~] rsc_sap_QAS_ERS01 очистки ресурсов ПК #
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl2: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 по началу работы
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl2: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl1 по началу работы
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~] # установку en.sapQAS | xargs kill -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] rsc_sap_QAS_ASCS00 очистки ресурсов ПК # [root@anftstsapcl2 ~] rsc_sap_QAS_ERS01 очистки ресурсов ПК #
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 по началу работы
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 по началу работы
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~] # установку er.sapQAS | xargs kill -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] rsc_sap_QAS_ERS01 очистки ресурсов ПК #
   ```

   Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 по началу работы
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 по началу работы
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~] # установку -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>59545 sapstartsrv
   
   [root@anftstsapcl1 ~] # kill-9 59545
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure (stonith:fence_azure_arm):      К работе группы ресурсов anftstsapcl1: fs_QAS_ASCS g QAS_ASCS (ocf::heartbeat:Filesystem):    К работе anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):      Начато anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   К работе группы ресурсов anftstsapcl1: fs_QAS_AERS g QAS_AERS (ocf::heartbeat:Filesystem):    К работе anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):      Начато anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):       К работе anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 по началу работы
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
