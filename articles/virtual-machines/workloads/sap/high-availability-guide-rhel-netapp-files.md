---
title: Azure VMs высокой доступности для SAP NW на RHEL с Azure NetApp Файлов Документы Майкрософт
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
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 11119d193cd08944bdff4737e8182cc7bece0abc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351245"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Лазурные виртуальные машины высокой доступности для SAP NetWeaver на Red Hat Enterprise Linux с Azure NetApp Файлы для приложений SAP

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

В этой статье описывается, как развернуть виртуальные машины, настроить виртуальные машины, установить кластерную структуру и установить высокодоступную систему SAP NetWeaver 7.50 с помощью [файлов Azure NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)
В конфигурациях примера команды установки и т.д. Экземпляр ASCS — номер 00, экземпляр ERS — номер 01, экземпляр первичного применения (PAS) — 02, экземпляр приложения (AAS) — 03. Используется Идентификатор sAP System. 

Слой базы данных не подробно описан в этой статье.  

Прежде всего прочитайте следующие примечания и документы SAP:

* [Документация файлов Сети Приложений Azure][anf-azure-doc] 
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

Высокая доступность (HA) для центральных служб SAP Netweaver требует совместного хранения.
Для этого на Red Hat Linux до сих пор необходимо было создать отдельный высокодоступный кластер GlusterFS. 

Теперь можно достичь SAP Netweaver HA с помощью общего хранилища, развернутого в файлах NetApp Azure. Использование файлов Сети Приложений Azure для общего хранилища устраняет необходимость в дополнительном [кластере GlusterFS.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs) Pacemaker по-прежнему необходим для HA центральных служб SAP Netweaver (ASCS/SCS).

![Общие сведения о высоком уровне доступности SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS и база данных SAP HANA используют виртуальное имя узла и виртуальные IP-адреса. Балансировщику нагрузки в Azure нужен виртуальный IP-адрес. Мы рекомендуем использовать [стандартный балансер нагрузки.](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal) В следующем списке показана конфигурация балансоилира нагрузки с отдельными передними ИП для (A)SCS и ERS.

### <a name="ascs"></a>(A)SCS

* Конфигурация внешнего интерфейса:
  * IP-адрес 192.168.14.9
* Порт пробы:
  * Порт 620<strong>&lt;nr&gt;</strong>
* Правила балансировки нагрузки
  * При использовании Standard Load Balancer выберите **порты HA**
  * 32<strong>&lt;&gt; nr</strong> TCP
  * 36<strong>&lt;&gt; nr</strong> TCP
  * 39<strong>&lt;&gt; nr</strong> TCP
  * 81<strong>&lt;&gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>ERS

* Конфигурация внешнего интерфейса:
  * IP-адрес 192.168.14.10
* Порт пробы:
  * Порт 621<strong>&lt;nr&gt;</strong>
* Правила балансировки нагрузки
  * При использовании Standard Load Balancer выберите **порты HA**
  * 32<strong>&lt;&gt; nr</strong> TCP
  * 33<strong>&lt;&gt; nr</strong> TCP
  * 5<strong>&lt;&gt;nr</strong>13 TCP
  * 5<strong>&lt;&gt;nr</strong>14 TCP
  * 5<strong>&lt;&gt;nr</strong>16 TCP

* Конфигурация серверной части:
  * подключена к основным сетевым интерфейсам всех виртуальных машин, которые должны быть частью кластера (A)SCS/ERS.

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Настройка инфраструктуры файлов NetApp Azure 

SAP NetWeaver требует общее хранилище для каталога профиля и транспорта.  Прежде чем приступить к настройке инфраструктуры файлов Azure NetApp, ознакомьтесь с [документацией Файлов NetApp Azure.][anf-azure-doc] Проверьте, предлагает ли выбранный вами регион Azure файлы NetApp. Следующая ссылка показывает наличие файлов NetApp Azure в регионе Azure: [Доступность файлов NetApp Azure в регионе Azure.][anf-avail-matrix]

Файлы NetApp Azure доступны в нескольких [регионах Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Перед развертыванием файлов NetApp Azure запросите на борт файлы NetApp Azure, следуя [инструкциям по файлам Файлов Azure NetApp.][anf-register] 

### <a name="deploy-azure-netapp-files-resources"></a>Развертывание ресурсов файлов NetApp Azure  

Эти шаги предполагают, что вы уже развернули [виртуальную сеть Azure.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Ресурсы Azure NetApp Files и виртуальные технологии, где будут установлены ресурсы Azure NetApp Files, должны быть развернуты в той же виртуальной сети Azure или в виртуальных сетях Azure.  

1. Если вы еще не сделали этого, запросите [борт в файлы NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. Создайте учетную запись NetApp в выбранном регионе Azure, следуя [инструкциям по созданию учетной записи NetApp.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. Наполните пул емкости файлов Azure NetApp, следуя инструкциям о том, [как настроить пул емкости файлов Сети Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)  
Архитектура SAP Netweaver, представленная в этой статье, использует единый пул емкости файлов Azure NetApp, Premium SKU. Мы рекомендуем Azure NetApp Files Premium SKU для рабочей нагрузки приложения SAP Netweaver на Azure.  
4. Делегируйте поднет файлы Azure NetApp, как описано в [инструкциях Делегировать подсеть в Файлы NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

5. Развертывание объемов файлов NetApp Azure, следуя [инструкциям по созданию тома для файлов NetApp Azure.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) Развертывание томов в специально отведенной [подсети](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp Files. Имейте в виду, что ресурсы Azure NetApp Files и MMs Azure должны находиться в той же виртуальной сети Azure или в виртуальных сетях Azure. В этом примере мы используем два тома файлов Azure NetApp: sap<b>qAS</b> и transSAP. Файл пути, которые установлены на соответствующие точки крепления являются /usrsap<b>qas</b>/sapmnt<b>ЗАС</b>, / usrsap<b>кас</b>/usrsap<b>SAS</b>sys и т.д.  

   1. объем сока<b>ЗАС</b> (nfs://192.168.24.5/usrsap<b>зас</b>/сапмнт<b>ЗАС)</b>
   2. объем сока<b>ЗАС</b> (nfs://192.168.24.5/usrsap<b>кас</b>/usrsap<b>ЗАС</b>ас)
   3. объем сока<b>ЗАС</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>ЗАС</b>sys)
   4. объем сока<b>ЗАС</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>ЗАС</b>ers)
   5. объем transSAP (nfs://192.168.24.4/transSAP)
   6. объем сока<b>ЗАС</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>ЗАС</b>па)
   7. объем сока<b>ЗАС</b> (nfs://192.168.24.5/usrsap<b>зас</b>/usrsap<b>ЗАС</b>аас)
  
В этом примере мы использовали файлы Azure NetApp для всех файловых систем SAP Netweaver, чтобы продемонстрировать, как можно использовать файлы NetApp Azure. Файловые системы SAP, которые не должны быть установлены через NFS, также могут быть развернуты в качестве [хранилища дисков Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) В этом примере <b>a-e</b> должны быть на Файлах Azure NetApp и <b>f-g</b> (т.е. /usr/sap/<b>qAS</b>/D<b>02</b>, /usr/sap/QAS /D<b>03)</b>могут быть развернуты в качестве хранения дисков Azure.<b>QAS</b> 

### <a name="important-considerations"></a>Важные сведения

При рассмотрении файлов Azure NetApp для архитектуры SAP Netweaver в архитектуре высокой доступности SUSE следует знать следующие важные соображения:

- Бассейн минимальной вместимости составляет 4 TiB. Размер пула емкости может быть увеличен с шагом в 1 TiB.
- Минимальный объем 100 GiB
- Файлы Azure NetApp и все виртуальные машины, на которых будут монтированы объемы файлов Azure NetApp, должны находиться в той же виртуальной сети Azure или в [виртуальных сетях](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) в том же регионе. Доступ к файлам Azure NetApp через вон водном водном регионе VNET поддерживается. Доступ К сети Azure NetApp через глобальные пиринги пока не поддерживается.
- Выбранная виртуальная сеть должна иметь подсеть, делегированную файлам NetApp Azure.
- Azure NetApp Files предлагает [экспортную политику:](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)вы можете управлять разрешенными клиентами, типдоступа (читай&написать, только читать и т.д.). 
- Функция Azure NetApp Files еще не знает о зоне. В настоящее время функция Azure NetApp Files не развернута во всех зонах доступности в регионе Azure. Имейте в виду потенциальные последствия задержки в некоторых регионах Azure. 
- Объемы файлов Azure NetApp могут быть развернуты в виде томов NFSv3 или NFSv4.1. Оба протокола поддерживаются для уровня приложений SAP (ASCS/ERS, серверы приложений SAP). 

## <a name="setting-up-ascs"></a>Настройка (A)SCS

В этом примере ресурсы были развернуты вручную через [портал Azure.](https://portal.azure.com/#home)

### <a name="deploy-linux-manually-via-azure-portal"></a>Развертывание Linux вручную с помощью портала Azure

Сначала необходимо создать объемы файлов Сети Azure. Развертывание VMs. После этого вы создаете балансиватор нагрузки и используете виртуальные машины в пуле бэкэнда.

1. Создание балансиватора нагрузки (внутренний, стандартный):  
   1. Создайте IP-адреса внешнего интерфейса.
      1. IP-адрес 192.168.14.9 для ASCS
         1. Откройте подсистему балансировки нагрузки, выберите пул внешних IP-адресов и щелкните "Добавить".
         1. Введите название нового пула IP-адреса (например, **передний. ЗАС. АСКС**)
         1. Установите назначение на Static и введите IP-адрес (например, **192.168.14.9**)
         1. Нажмите кнопку "ОК"
      1. IP-адрес 192.168.14.10 для ASCS ERS
         * Повторите вышеперечисленные шаги под "а", чтобы создать IP-адрес для ERS (например, **192.168.14.10** и **передний. ЗАС. ERS**)
   1. Создание внутреннего пула
      1. Выберите подсистему балансировки нагрузки, щелкните "Серверные пулы" и нажмите кнопку "Добавить".
      1. Введите название нового пула бэкэнда (например, **бэкэнд. ЗАС**)
      1. Щелкните "Добавить виртуальную машину".
      1. Выберите Виртуальная машина. 
      1. Выберите виртуальные машины кластера A.SCS и их IP-адреса.
      1. Нажмите "Добавить"
   1. Создайте пробы работоспособности.
      1. Порт 620**00** для ASCS
         1. Выберите подсистему балансировки нагрузки, щелкните "Зонды работоспособности" и нажмите кнопку "Добавить".
         1. Введите название нового зонда здоровья (например, **здоровье. ЗАС. АСКС**)
         1. Выберите протокол TCP, порт 620**00**, интервал, равный 5, и порог состояния неработоспособности, равный 2.
         1. Нажмите кнопку "ОК"
      1. Порт 621**01** для ASCS ERS
            * Повторите вышеперечисленные шаги под "c", чтобы создать зонд здоровья для ERS (например, 621**01** и **здоровья. ЗАС. ERS**)
   1. Правила балансировки нагрузки
      1. Правила балансировки нагрузки для ASCS
         1. Откройте балансиватор нагрузки, выберите правила балансировки нагрузки и нажмите Добавить
         1. Введите название нового правила балансоостатка нагрузки (например, **фунт. ЗАС. АСКС**)
         1. Выберите IP-адрес переднего энда для ASCS, пул бэкэнда и зонд аработоспособности, созданный ранее (например, **фронтэнд. ЗАС. ASCS**, **бэкэнд. ЗАС** и **здоровье. ЗАС. АСКС**)
         1. Выберите **порты HA**
         1. Увеличьте время ожидания до 30 минут.
         1. **Не забудьте включить плавающий IP-адрес**.
         1. Нажмите кнопку "ОК"
         * Повторите выше, чтобы создать правила балансировки нагрузки для ERS (например, **фунт. ЗАС. ERS**)
1. Кроме того, если сценарий требует базового балансиватора нагрузки (внутреннего), выполните следующие действия:  
   1. Создайте IP-адреса внешнего интерфейса.
      1. IP-адрес 192.168.14.9 для ASCS
         1. Откройте подсистему балансировки нагрузки, выберите пул внешних IP-адресов и щелкните "Добавить".
         1. Введите название нового пула IP-адреса (например, **передний. ЗАС. АСКС**)
         1. Установите назначение на Static и введите IP-адрес (например, **192.168.14.9**)
         1. Нажмите кнопку "ОК"
      1. IP-адрес 192.168.14.10 для ASCS ERS
         * Повторите вышеперечисленные шаги под "а", чтобы создать IP-адрес для ERS (например, **192.168.14.10** и **передний. ЗАС. ERS**)
   1. Создание внутреннего пула
      1. Выберите подсистему балансировки нагрузки, щелкните "Серверные пулы" и нажмите кнопку "Добавить".
      1. Введите название нового пула бэкэнда (например, **бэкэнд. ЗАС**)
      1. Щелкните "Добавить виртуальную машину".
      1. Выберите набор доступности, созданный ранее для ASCS 
      1. Выберите виртуальные машины кластера (A)SCS.
      1. Нажмите кнопку "ОК"
   1. Создайте пробы работоспособности.
      1. Порт 620**00** для ASCS
         1. Выберите подсистему балансировки нагрузки, щелкните "Зонды работоспособности" и нажмите кнопку "Добавить".
         1. Введите название нового зонда здоровья (например, **здоровье. ЗАС. АСКС**)
         1. Выберите протокол TCP, порт 620**00**, интервал, равный 5, и порог состояния неработоспособности, равный 2.
         1. Нажмите кнопку "ОК"
      1. Порт 621**01** для ASCS ERS
            * Повторите вышеперечисленные шаги под "c", чтобы создать зонд здоровья для ERS (например, 621**01** и **здоровья. ЗАС. ERS**)
   1. Правила балансировки нагрузки
      1. TCP 32**00** для ASCS
         1. Откройте балансиватор нагрузки, выберите правила балансировки нагрузки и нажмите Добавить
         1. Введите название нового правила балансоостатка нагрузки (например, **фунт. ЗАС. ASCS.3200**)
         1. Выберите IP-адрес переднего энда для ASCS, пул бэкэнда и зонд аработоспособности, созданный ранее (например, **фронтэнд. ЗАС. АСКС**)
         1. Оставьте выбранным протокол **TCP** и введите порт **3200**.
         1. Увеличьте время ожидания до 30 минут.
         1. **Не забудьте включить плавающий IP-адрес**.
         1. Нажмите кнопку "ОК"
      1. Дополнительные порты для ASCS
         * Повторите вышеперечисленные шаги под "d" для портов 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 и TCP для ASCS
      1. Дополнительные порты для ASCS ERS
         * Повторите вышеперечисленные шаги под "d" для портов 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 и TCP для ASCS ERS

      > [!Note]
      > Когда ВМ без общедоступных IP-адресов помещаются в пул бэкэнда внутреннего (без публичного IP-адреса) балансируев нагрузки Standard Azure, не будет исходящих подключений к Интернету, если не будет выполнена дополнительная конфигурация, позволяющая осуществлять реаутирование в конечные точки общего пользования. Для получения подробной информации о том, как достичь исходящего подключения, смотрите [подключение к конечным точкам для виртуальных машин с помощью баланса azure Standard Load balancer в сценариях высокой доступности SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

      > [!IMPORTANT]
      > Не включайте метки времени TCP на M-м ими Azure, размещенных за Azure Load Balancer. Включение меток времени TCP приведет к сбою зондов работоспособности. Установить параметр **net.ipv4.tcp_timestamps** до **0**. Для получения подробной информации [см.](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

## <a name="disable-id-mapping-if-using-nfsv41"></a>Отображение идентификатора (при использовании NFSv4.1)

Инструкции в этом разделе применимы только при использовании объемов файлов Azure NetApp с протоколом NFSv4.1. Выполните конфигурацию на всех ВМ, где будут установлены объемы Azure NetApp Files NFSv4.1.  

1. Проверить настройки домена NFS. Убедитесь, что домен настроен как домен Azure NetApp **`defaultv4iddomain.com`** Files по умолчанию, т.е. отображение **настроено на никто.**  

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
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Для получения более подробной `nfs4_disable_idmapping` информации о том, как изменить параметр см. https://access.redhat.com/solutions/1749883

### <a name="create-pacemaker-cluster"></a>Создание кластера Pacemaker

Следуйте указаниям в статье [Настройка кластера Pacemaker в Red Hat Enterprise Linux в Azure](high-availability-guide-rhel-pacemaker.md), чтобы создать базовый кластер Pacemaker для этого (A)SCS-сервера.

### <a name="prepare-for-sap-netweaver-installation"></a>Подготовка к установке SAP NetWeaver

Следующие элементы прикрепляются либо **с «А»,** применимыми ко всем узлам, и это **применимо** только к узлам 1 или **No2,** применимо только к уму 2.

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

1. **Создание** каталогов SAP в объеме файлов Сети Приложений Azure.  
   Временно смонтировать объем файлов Сети Azure на одном из MMs и создать каталоги SAP (файлы).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
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
    ``` 

1. **[A]** Создайте общие папки.

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **Установка** клиента NFS и другие требования

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Проверка версии resource-agents-sap

   Убедитесь, что версия пакета resource-agents-sap не ниже 3.9.5-124.el7.
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** Добавление записей подключения

   При использовании NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   При использовании NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > При монтаже объемов объемов файлов Azure NetApp обязательно сопоставьте версию протокола NFS. Если объемы файлов Azure NetApp создаются как объемы NFSv3, используйте соответствующую конфигурацию NFSv3. Если объемы файлов Azure NetApp создаются как объемы NFSv4.1, следуйте инструкциям по отображению ID и убедитесь, что используют соответствующую конфигурацию NFSv4.1. В этом примере объемы файлов Azure NetApp были созданы как томы NFSv3.  

   Подключение новых общих папок

   ```
   sudo mount -a  
   ```

1. **[A]** Настройте файл подкачки.

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Перезапустите агент, чтобы активировать изменения:

   ```
   sudo service waagent restart
   ```

1. **[A]** Настройка RHEL

   Настройте RHEL, как описано в примечании к SAP [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Установка SAP NetWeaver ASCS/ERS

1. **[1]** Создайте виртуальный IP-адрес и проверку работоспособности для экземпляра ASCS.

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Убедитесь, что состояние кластера — "ОК" и что запущены все ресурсы. Не важно, на каком узле выполняются ресурсы.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** Установите SAP NetWeaver ASCS.  

   Установите SAP NetWeaver ASCS в качестве корня на первом узлах с помощью виртуального хоста, который отображает IP-адрес конфигурации фронтера нагрузки для ASCS, например <b>anftstsapvh</b>, <b>192.168.14.9</b> и номер экземпляра, который вы использовали для зонда погрузчика, например <b>00.</b>

   Чтобы разрешить непривилегированному пользователю подключаться к SAPinst, можно использовать параметр SAPINST_REMOTE_ACCESS_USER.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Если установка не может создать субфолдер в /usr/sap/**ЗАС**/ASCS**00**, попробуйте установить владельца и группу папки ASCS**00** и повторить попытку.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Создайте виртуальный IP-адрес и проверку работоспособности для экземпляра ERS.

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Убедитесь, что состояние кластера — "ОК" и что запущены все ресурсы. Не важно, на каком узле выполняются ресурсы.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** Установите SAP NetWeaver ERS.  

   Установите SAP NetWeaver ERS в качестве корня на втором узлах, используя виртуальное хостимя, которое отображает IP-адрес конфигурации фронтера нагрузки для ERS, например <b>anftstsapers</b>, <b>192.168.14.10</b> и номер экземпляра, который вы использовали для зонда балансиватора нагрузки, например <b>01.</b>

   Чтобы разрешить непривилегированному пользователю подключаться к SAPinst, можно использовать параметр SAPINST_REMOTE_ACCESS_USER.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Если установка не может создать субфолдер в /usr/sap/**ЗАС**/ERS**01**, попробуйте установить владельца и группу папки ERS**01** и повторить попытку.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Адаптируйте профили экземпляров ASCS/SCS и ERS.

   * Профиль ASCS/SCS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * Профиль ERS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Настройте активность.

   Обмен данными между сервером приложений SAP NetWeaver и ASCS/SCS происходит через программный балансировщик нагрузки. Балансировщик нагрузки отключает неактивные подключения по истечении времени ожидания, которое можно настроить. Чтобы предотвратить это, необходимо установить параметр в профиле SAP NetWeaver ASCS/SCS и изменить настройки системы Linux. Дополнительные сведения см. в [примечании к SAP 1410736][1410736].

   Параметр профиля ASCS/SCS enque/encni/set_so_keepalive уже был добавлен на предыдущем шаге.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Изменение файла /usr/sap/sapservices

   Чтобы предотвратить запуск экземпляров скриптом запуска sapinit, необходимо закомментировать все экземпляры, управляемые Pacemaker, в файле /usr/sap/sapservices. Не следует закомментировать экземпляр SAP HANA, если он будет использоваться с HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Создайте кластерные ресурсы SAP.

   При использовании архитектуры сервера enqueue 1 (ENSA1) определите ресурсы следующим образом:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP представила поддержку сервера enqueue 2, включая репликацию, по состоянию на SAP NW 7.52. Начиная с ABAP Platform 1809, сервер enqueue 2 устанавливается по умолчанию. Смотрите Примечание SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) для поддержки сервера enqueue 2.
   При использовании архитектуры сервера enqueue 2[(ENSA2),](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)установите ресурсагента ресурс-агенты-sap-4.1.1-12.el7.x86_64 или новые и определите ресурсы следующим образом:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Если вы переходите из старой версии и переходите на сервер enqueue 2, см. Примечание SAP [2641322.](https://launchpad.support.sap.com/#/notes/2641322) 

   > [!NOTE]
   > Тайм-ауты в вышеуказанной конфигурации являются лишь примерами и, возможно, должны быть адаптированы к конкретной настройке SAP. 

   Убедитесь, что состояние кластера — "ОК" и что запущены все ресурсы. Не важно, на каком узле выполняются ресурсы.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **Добавление** правил брандмауэра для ASCS и ERS на обоих узлах Добавить правила брандмауэра для ASCS и ERS на обоих узлах.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Подготовка сервера приложений SAP NetWeaver

   Для некоторых баз данных требуется установить экземпляр базы данных на сервере приложений. Для этих случаев подготовьте виртуальные машины сервера приложений к их использованию.  

   В шагах ниже предполагается, что сервер приложений установлен на сервере, отличном от серверов ASCS/SCS и HANA. В противном случае некоторые описанные ниже шаги (например, настройка разрешения имени узла) не требуются.  

   Следующие элементы являются прикрепленными либо **с «А»** - применимыкаки как к PAS, так и к AAS, **«P»** - применимы только к PAS или **«S»** - применимы только к AAS.  

1. **Разрешение** на настройку узла можно либо использовать DNS-сервер, либо изменить /etc/host на всех узлах. В этом примере показано, как использовать файл /etc/hosts.
   Замените IP-адрес и имя узла в следующих командах:  

   ```
   sudo vi /etc/hosts
   ```

   Вставьте следующие строки в /etc/hosts. Измените IP-адрес и имя хоста в соответствии с вашей средой.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **Создайте** каталог sapmnt Создайте каталог sapmnt.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **Установка** клиента NFS и другие требования  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Добавление записей подключения  
   При использовании NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   При использовании NFSv4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Подключение новых общих папок

   ```
   sudo mount -a
   ```

1. **Создайте** и смонтировать каталог PAS  
   При использовании NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   При использовании NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **Создайте** и смонтируйте каталог AAS  
   При использовании NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   При использовании NFSv4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** Настройте файл подкачки.
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Перезапустите агент, чтобы активировать изменения:

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Установка базы данных

В данном примере SAP NetWeaver уже установлен на SAP HANA. Для этой установки можно использовать любую поддерживаемую базу данных. Для получения дополнительной информации о том, как установить SAP HANA в Azure, см. [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533]

1. Запустите установку экземпляра базы данных SAP.

   Установите экземпляр базы данных SAP NetWeaver в качестве корня, используя виртуальное хост-имя, которое отображает IP-адрес конфигурации фронтера нагрузонаселения для базы данных.

   Чтобы разрешить непривилегированному пользователю подключаться к SAPinst, можно использовать параметр SAPINST_REMOTE_ACCESS_USER.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Установка сервера приложений SAP NetWeaver

Для установки сервера приложений SAP выполните следующие действия.

1. Подготовка приложения сервера

   Чтобы подготовить сервер приложений, следуйте инструкциям из раздела [Подготовка сервера приложений SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7).

1. Установите сервер приложений SAP NetWeaver:

   Установите основной и дополнительный сервер приложений SAP NetWeaver:

   Чтобы разрешить непривилегированному пользователю подключаться к SAPinst, можно использовать параметр SAPINST_REMOTE_ACCESS_USER.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Обновите безопасное хранилище SAP HANA.

   Обновите безопасное хранилище SAP HANA, чтобы оно указывало на виртуальное имя настройки репликации системы SAP HANA.

   Чтобы получить список записей, выполните следующую команду как \<sapsid>adm:

   ```
   hdbuserstore List
   ```

   Это позволит вывести список всех записей. Он будет выглядеть так:
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   Выходные данные будут содержать IP-адрес записи по умолчанию, которая указывает на виртуальную машину, а не на IP-адрес балансировщика нагрузки. Эту запись необходимо изменить так, чтобы она указывала на имя виртуального узла балансировщика нагрузки. Убедитесь в том, чтобы использовать тот же порт (**30313** в выходе выше) и название базы данных **(ЗАС** в выходе выше)!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Проверка настройки кластера

1. Миграция экземпляра ASCS вручную

   Состояние ресурсов перед запуском теста:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Выполните следующие команды от имени привилегированного пользователя, чтобы перенести экземпляр ASCS.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Состояние ресурсов после теста:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Имитирование сбоя узла

   Состояние ресурсов перед запуском теста:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Выполните следующую команду в качестве привилегированного пользователя на узле, где выполняется экземпляр ASCS.

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   Состояние после повторного запуска узла должно выглядеть так:

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Выполните следующую команду, чтобы очистить ресурсы со сбоями.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Состояние ресурсов после теста:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Завершение процесса сервера сообщений

   Состояние ресурсов перед запуском теста:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Выполните следующие команды от имени привилегированного пользователя, чтобы определить процесс сервера сообщений и завершить его.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Если вы убьете сервер сообщений только `sapstart`один раз, он будет перезапущен . Если вы завершите работу сервера достаточно много раз, Pacemaker в конечном итоге переместит экземпляр ASCS на другой узел. Выполните следующие команды от имени привилегированного пользователя для очистки состояния ресурсов экземпляра ERS и ASCS после теста.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Состояние ресурсов после теста:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Завершение процесса сервера постановки в очередь

   Состояние ресурсов перед запуском теста:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Выполните следующие команды от имени привилегированного пользователя на узле, где выполняется экземпляр ASCS, чтобы завершить работу сервера постановки в очередь.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   Для экземпляра ASCS должна быть немедленно выполнена отработка отказа с переносом на другой узел. После запуска экземпляра ASCS экземпляр ERS должен также выполнить отработку отказа. Выполните следующие команды от имени привилегированного пользователя для очистки состояния ресурсов экземпляра ERS и ASCS после теста.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Состояние ресурсов после теста:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Завершение процесса сервера постановки в очередь для репликации

   Состояние ресурсов перед запуском теста:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Выполните следующую команду от имени привилегированного пользователя на узле, где выполняется экземпляр ERS, чтобы завершить работу процесса сервера постановки в очередь для репликации.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Если вы запустите команду `sapstart` только один раз, перезапустите процесс. Если вы запустите `sapstart` его достаточно часто, не будет перезапустить процесс и ресурс будет находиться в остановленном состоянии. Выполните следующие команды от имени привилегированного пользователя для очистки состояния ресурсов экземпляра ERS после теста.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Состояние ресурсов после теста:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Завершение процесса постановки sapstartsrv в очередь

   Состояние ресурсов перед запуском теста:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Выполните следующие команды в качестве привилегированного пользователя на узле, где выполняется ASCS.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Процесс sapstartsrv всегда должен перезапускаться с помощью агента ресурсов Pacemaker в рамках мониторинга. Состояние ресурсов после теста:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Дальнейшие действия

* [HA для SAP NW на Azure VMs на RHEL для приложений SAP мульти-SID руководство](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению][planning-guide]
* [Развертывание программного обеспечения SAP на виртуальных машинах Azure][deployment-guide]
* [Развертывание СУБД виртуальных машин Azure для SAP][dbms-guide]
* Дополнительные сведения об обеспечении высокого уровня доступности и планировании аварийного восстановления SAP HANA в Azure (крупные экземпляры) см. в [этой статье](hana-overview-high-availability-disaster-recovery.md).
* Дополнительные сведения об установке высокого уровня доступности и плана для аварийного восстановления SAP HANA на виртуальных машинах Azure см. в статье [Высокий уровень доступности SAP HANA на виртуальных машинах Azure][sap-hana-ha].
