---
title: Настройка IBM Db2 HADR на виртуальных машинах Azure (VM) на RHEL Документы Майкрософт
description: Установить высокую доступность IBM Db2 LUW на виртуальных машинах Azure (VMs) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.openlocfilehash: c6a230f6abeab45c56aab2db40b8b1defcc06d90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598703"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Обеспечение высокого уровня доступности IBM DB2 LUW на виртуальных машинах Azure в Red Hat Enterprise Linux Server

IBM Db2 для Linux, UNIX и Windows (LUW) в [конфигурации высокой доступности и аварийного восстановления (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) состоит из одного узла, который работает в экземпляре первичной базы данных, и по крайней мере одного узла, который выполняет вторичный экземпляр базы данных. Изменения в экземпляре первичной базы данных реплицируются в экземпляр вторичной базы данных синхронно или синхронно, в зависимости от конфигурации. 

В этой статье описывается, как развертывать и настраивать виртуальные машины Azure (VMs), устанавливать кластерную платформу и устанавливать IBM Db2 LUW с конфигурацией HADR. 

Статья не охватывает, как установить и настроить IBM Db2 LUW с помощью установки программного обеспечения HADR или SAP. Чтобы помочь вам выполнить эти задачи, мы предоставляем ссылки на руководства по установке SAP и IBM. В этой статье основное внимание уделяется частям, специфичным для среды Azure. 

Поддерживаемые версии IBM Db2 10,5 и более поздние, как описано в SAP note [1928533].

Перед началом установки смотрите следующие заметки и документацию SAP:

| Примечание SAP | Описание |
| --- | --- |
| [1928533] | Приложения SAP в Azure: поддерживаемые продукты и типы Azure VM |
| [2015553] | SAP на Azure: предпосылки поддержки |
| [2178632] | Ключевые показатели мониторинга для SAP на Azure |
| [2191498] | SAP на Linux с Azure: Улучшенный мониторинг |
| [2243692] | Linux на Azure (IaaS) VM: проблемы с лицензией SAP |
| [2002167] | Red Hat Enterprise Linux 7.x: установка и обновление |
| [2694118] | Red Hat Предприятие Linux HA Надстройка на Azure |
| [1999351] | Устранение неполадок, связанных с расширенным мониторингом Azure для SAP |
| [2233094] | DB6: SAP-приложения на Azure, которые используют IBM Db2 для Linux, UNIX и Windows - дополнительная информация |
| [1612105] | DB6: часто задаваемые вопросы на Db2 с HADR |


| Документация | 
| --- |
| [SAP Сообщество Вики](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): имеет все необходимые SAP примечания для Linux |
| [Планирование и внедрение виртуальных машин Azure для SAP на руководстве по Linux][planning-guide] |
| [Развертывание виртуальных машин Azure для SAP на Linux][deployment-guide] (эта статья) |
| [Развертывание системы управления базами данных Virtual Machines Azure (DBMS) для SAP на руководстве по Linux][dbms-guide] |
| [Контрольный список для планирования и развертывания рабочей нагрузки SAP в Azure][azr-sap-plancheck] |
| [Обзор высокой доступности Дополнения для Red Hat Предприятие Linux 7][rhel-ha-addon] |
| [Администрирование надстройки для обеспечения высокой доступности][rhel-ha-admin] |
| [Справочник по надстройке для обеспечения высокой доступности][rhel-ha-ref] |
| [Политики поддержки для кластеров высокой доступности RHEL — виртуальные машины Microsoft Azure как члены кластера][rhel-azr-supp]
| [Установка и настройка кластера высокой доступности Red Hat Enterprise Linux 7.4 (и более поздних версий) в Microsoft Azure][rhel-azr-inst]
| [Развертывание виртуальных машин IBM Db2 Виртурные машины DBMS для рабочей нагрузки SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [Политика поддержки кластеров высокой доступности RHEL - Управление IBM Db2 для Linux, Unix и Windows в кластере][rhel-db2-supp]



## <a name="overview"></a>Обзор
Для достижения высокой доступности IBM Db2 LUW с HADR устанавливается по крайней мере на двух виртуальных машинах Azure, которые развернуты в [наборе доступности Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) или в [зонах доступности Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 

Следующая графика отображает настройку двух серверов баз данных Azure VMs. Оба сервера баз данных Azure VMs имеют собственное хранилище и работают. В HADR в одном экземпляре базы данных в одном из ВМ Azure роль основного экземпляра. Все клиенты подключены к первичной инстанции. Все изменения в транзакциях баз данных сохраняются локально в журнале транзакций Db2. Поскольку записи журналов транзакций сохраняются локально, записи передаются через TCP/IP в экземпляр базы данных на втором сервере базы данных, сервере ожидания или резервном экземпляре. Резервный экземпляр обновляет локальную базу данных, перекаив аяды на передаваемые записи журналов транзакций. Таким образом, резервный сервер синхронизируется с основным сервером.

HADR — это только функция репликации. Он не имеет обнаружения сбоев и не автоматического поглощения или отказобъекта. Поглощение или перевод на сервер ожидания должен быть инициирован вручную администратором базы данных. Для автоматического обнаружения поглощения и сбоев можно использовать функцию кластеризации Linux Pacemaker. Pacemaker отслеживает два экземпляра сервера баз данных. При сбое сервера первичной базы данных Pacemaker инициирует *автоматическое* поглощение HADR резервным сервером. Pacemaker также гарантирует, что виртуальный IP-адрес будет назначен новому основному серверу.

![Обзор высокой доступности IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Чтобы серверы приложений SAP подключались к основной базе данных, вам необходимо виртуальное имя хоста и виртуальный IP-адрес. В случае сбоя серверы приложений SAP подключаются к новой основной экземпляру базы данных. В среде Azure [балансиватор нагрузки Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) должен использовать виртуальный IP-адрес так, как это требуется для HADR IBM Db2. 

Чтобы помочь вам в полной мере понять, как IBM Db2 LUW с HADR и Pacemaker вписывается в высокодоступную настройку системы SAP, следующее изображение представляет обзор высокодоступной настройки системы SAP на основе базы данных IBM Db2. Эта статья охватывает только IBM Db2, но содержит ссылки на другие статьи о том, как настроить другие компоненты системы SAP.

![IBM DB2 Высокая доступность полный обзор среды](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Обзор необходимых шагов на высоком уровне
Для развертывания конфигурации IBM Db2 необходимо следовать следующим шагам:

  + Планируйте свою среду.
  + Развертывание VMs.
  + Обновление RHEL Linux и настройка файловых систем.
  + Установите и назначайте Pacemaker.
  + Настройка [кластера хуклепов][glusterfs] или [файлов Azure NetApp][anf-rhel]
  + Установите [ASCS/ERS на отдельном кластере.][ascs-ha-rhel]
  + Установка базы данных IBM Db2 с опцией распределенной/высокой доступности (SWPM).
  + Установите и создайте вторичный узла базы данных и экземпляр, а также нанастройки HADR.
  + Подтвердите, что HADR работает.
  + Примените конфигурацию Pacemaker для управления IBM Db2.
  + Наверстойируйте балансируера загрузки Azure.
  + Установка первичных и диалоговые серверы приложений.
  + Проверьте и адаптируйте конфигурацию серверов приложений SAP.
  + Выполните тесты на отказ и поглощение.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Планируйте инфраструктуру Azure для размещения IBM Db2 LUW с HADR

Завершите процесс планирования перед выполнением развертывания. Планирование создает основу для развертывания конфигурации Db2 с HADR в Azure. Ключевые элементы, которые должны быть частью планирования Для IMB Db2 LUW (база данных, входящем в среду SAP), перечислены в следующей таблице:

| Раздел | Краткое описание |
| --- | --- |
| Определение групп ресурсов Azure | Группы ресурсов, в которых развертываются VM, VNet, Azure Load Balancer и другие ресурсы. Может быть существующим или новым. |
| Виртуальная сеть / Определение Subnet | В случае развертывания VM для IBM Db2 и Azure Load Balancer. Может быть существующим или вновь созданным. |
| Виртуальные машины, хпарянивая IBM Db2 LUW | Размер VM, хранение, сеть, IP-адрес. |
| Виртуальное имя хозяина и виртуальный IP для базы данных IBM Db2| Виртуальное IP или имя хоста, используемое для подключения серверов приложений SAP. **db-virt-hostname**, **db-virt-ip**. |
| Лазурное фехтование | Предотвратить предотвращение раздвоений мозговых ситуаций. |
| Azure Load Balancer | Использование Basic или Standard (рекомендуется), порт зонда для базы данных Db2 (наша рекомендация 62500) **зонд-порт**. |
| Разрешение имен| Как разрешение имен работает в среде. Высоко рекомендуется обслуживание DNS. Файл локальных хостов можно использовать. |
    
Для получения дополнительной информации о Linux Pacemaker в Azure, [см. Настройка Pacemaker на Red Hat Enterprise Linux в Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Развертывание на Red Hat Enterprise Linux

Ресурсный агент IBM Db2 LUW включен в Red Hat Enterprise Linux Server HA Addon. Для настройки, описанной в этом документе, следует использовать Red Hat Enterprise Linux для SAP. Рынок Azure содержит изображение для Red Hat Enterprise Linux 7.4 для SAP или выше, которое можно использовать для развертывания новых виртуальных машин Azure. Будьте в курсе различных моделей поддержки или обслуживания, которые предлагаются Red Hat через Azure Marketplace при выборе vM-изображения в Azure VM Marketplace.

### <a name="hosts-dns-updates"></a>Хосты: Обновления DNS
Составьте список всех имен хоста, включая имена виртуальных хостов, и обновите свои DNS-серверы, чтобы обеспечить правильное ip-адрес для разрешения имен хоста. Если DNS-сервер не существует или вы не можете обновить и создать записи DNS, необходимо использовать локальные файлы хоста отдельных VMs, которые участвуют в этом сценарии. Если вы используете записи файлов-хоста, убедитесь, что записи применяются ко всем vMs в среде системы SAP. Тем не менее, мы рекомендуем использовать DNS, который в идеале распространяется на Azure


### <a name="manual-deployment"></a>Развертывание вручную

Убедитесь, что выбранная ОС поддерживается IBM/SAP для IBM Db2 LUW. Список поддерживаемых версий ОС для выпусков Azure VMs и Db2 доступен в SAP note [1928533]. Список релизов ОС по отдельным версиям Db2 доступен в Матрице доступности продуктов SAP. Мы настоятельно рекомендуем минимум Red Hat Enterprise Linux 7.4 для SAP из-за улучшения производительности, связанного с Azure, в этой или более поздних версиях Red Hat Enterprise Linux.

1. Создайте или выберите группу ресурсов.
1. Создайте или выберите виртуальную сеть и подсеть.
1. Создайте набор доступности Azure или разместите зону доступности.
    + Для набора доступности установите домены максимального обновления до 2.
1. Создание виртуальной машины 1.
    + Используйте Red Hat Enterprise Linux для изображения SAP в Azure Marketplace.
    + Выберите набор доступности Azure, созданный на шаг 3, или выберите Зону доступности.
1.  Создание виртуальной машины 2.
    + Используйте Red Hat Enterprise Linux для изображения SAP в Azure Marketplace.
    + Выберите набор доступности Azure, созданный в шаге 3, или выберите Зону доступности (не ту же зону, что и в шаге 3).
1. Добавьте диски данных к виртуальным вам, а затем проверьте рекомендацию настройки файловой системы в статье [IBM Db2 Azure Virtual Machines DBMS для рабочей нагрузки SAP.][dbms-db2]

## <a name="create-the-pacemaker-cluster"></a>Создание кластера Pacemaker
    
Чтобы создать базовый кластер Pacemaker для этого сервера IBM Db2, см. [Настройка Pacemaker на Red Hat Enterprise Linux в Azure.][rhel-pcs-azr] 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Установка среды IBM Db2 LUW и SAP

Перед началом установки среды SAP на основе IBM Db2 LUW просмотрите следующую документацию:

+ Документация по Azure
+ Документация SAP
+ Документация IBM

Ссылки на эту документацию приведены во вступительном разделе этой статьи.

Проверьте руководства по установке SAP об установке приложений на основе NetWeaver на IBM Db2 LUW.
Вы можете найти руководства на портале Справки SAP, используя [руководство по установке SAP Finder.][sap-instfind]

Можно уменьшить количество руководств, отображаемых на портале, установив следующие фильтры:
- Я хочу: "Установка новой системы"
- Моя база данных: "IBM Db2 для Linux, Unix и Windows"
- Дополнительные фильтры для версий SAP NetWeaver, конфигурации стека или операционной системы

#### <a name="red-hat-firewall-rules"></a>Правила брандмауэра Red Hat
Red Hat Enterprise Linux имеет брандмауэр включен по умолчанию. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Подсказки установки для установки IBM Db2 LUW с HADR

Настройка основного экземпляра базы данных IBM Db2 LUW:

- Используйте опцию высокой доступности или распределенную опцию.
- Установите экземпляр SAP ASCS/ERS и базы данных.
- Возьмите резервную часть недавно установленной базы данных.

> [!IMPORTANT] 
> Запишите "Порт связи базы данных", установленный во время установки. Для обоих экземпляров базы данных он должен быть одинаковым.
>![Определение порта SAP SWPM](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Настройки IBM Db2 HADR для Azure

   При использовании фехтования Azure Pacemaker установите следующие параметры:

   - Продолжительность однорангового окна HADR (секунды) (HADR_PEER_WINDOW)  
   - Значение тайм-аута HADR (HADR_TIMEOUT)

Мы рекомендуем предыдущие параметры на основе первоначального тестирования на отказ/поглощение. Это обязательно, что вы тестируете для надлежащей функциональности failover и поглощения с этими параметрами параметров. Поскольку отдельные конфигурации могут варьироваться, параметры могут потребовать корректировки. 

> [!NOTE]
> Специфический для IBM Db2 с конфигурацией HADR с обычным запуском: вторичный или резервный экземпляр базы данных должен быть запущен, прежде чем вы сможете запустить экземпляр первичной базы данных.

   
> [!NOTE]
> Для установки и конфигурации, специфичных для Azure и Pacemaker: Во время процедуры установки через SAP Software Software Software Manager возникает явный вопрос о высокой доступности ДЛЯ IBM Db2 LUW:
>+ Не выбирайте **IBM Db2 pureScale**.
>+ Не выбирайте **установку IBM Tivoli System Automation для мультиплатформ.**
>+ Не выбирайте **файлы конфигурации кластера Generate.**
>![SAP SWPM - Варианты DB2 HA](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Для настройки сервера базы данных Standby с помощью однородной системы SAP выполните следующие действия:

1. Выберите опцию **копирования системы** > **целевых систем** > **Распределенная** > **база данных экземпляра.**
1. В качестве метода копирования выберите **однородную систему,** чтобы можно было использовать резервную копию для восстановления резервного копирования в экземпляре резервного сервера.
1. Когда вы достигнете шага выхода для восстановления базы данных для однородной копии системы, выйдите из установки. Восстановление базы данных из резервного копирования основного узла. Все последующие этапы установки уже выполнены на сервере первичной базы данных.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Правила брандмауэра Red Hat для DB2 HADR
Добавьте правила брандмауэра, чтобы трафик на DB2 и между DB2 для работы HADR:
+ Порт связи базы данных. При использовании разделов, добавить эти порты тоже.
+ Порт HADR (значение параметра DB2 HADR_LOCAL_SVC)
+ Порт зонда Azure
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>ПРОВЕРКА IBM Db2 HADR
Для демонстрационных целей и процедур, описанных в этой статье, база данных SID является **ID2**.

После настройки HADR и статуса PEER и CONNECTED на первичных и резервных узлах выполните следующую проверку:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Конфигурация Db2 Pacemaker

При использовании Pacemaker для автоматического сбоя в случае сбоя узла необходимо настроить экземпляры Db2 и Pacemaker соответственно. Этот раздел описывает этот тип конфигурации.

Следующие элементы являются прификсированными либо:

- **(А)**: Применимо ко всем узлам
- **(** Применяется только к узлам 1 
- **(** Применяется только к узлам 2

**Предпосылки** для конфигурации Pacemaker:
1. Выключите оба сервера баз\<данных с пользователем db2 sid> с db2stop.
1. Измените среду оболочки\<для db2 sid> пользователя на */bin/ksh:*
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Конфигурация кардиостимулятора

**Конфигурация** кардиостимулятора IBM Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**Создание** ресурсов IBM Db2:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**Запустите** ресурсы IBM Db2:
* Вывести Pacemaker из режима обслуживания.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**Убедитесь,** что статус кластера в порядке и что все ресурсы запущены. Не важно, на каком узел работают ресурсы.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Онлайн: аз-idb01 аз-idb02

Полный список ресурсов:

 rsc_st_azure (stonith:fence_azure_arm): Начат az-idb01 Мастер / Slave Set: Db2_HADR_ID2-мастер "Db2_HADR_ID2" Мастера: " аз-idb01 " Slaves: аз-idb02 " Ресурсная группа: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::Heartbeat:IPaddr2): Начатый az-idb01 nc_db2id2_ID2 (ocf::heartbeat:az

Статус Daemon: corosync: активный/отключенный кардиостимулятор: активный/отключенный PCsd: активный/включенный
</pre>

> [!IMPORTANT]
> Вы должны управлять экземпляром Pacemaker, сгруппированным Db2, используя инструменты Pacemaker. Если вы используете команды db2, такие как db2stop, Pacemaker обнаруживает действие как сбой ресурса. Если вы выполняете техническое обслуживание, вы можете поместить узлы или ресурсы в режим обслуживания. Pacemaker приостанавливает мониторинг ресурсов, и вы можете использовать обычные команды администрирования db2.


### <a name="configure-azure-load-balancer"></a>Настройка Azure Load Balancer
Для настройки баланса загрузки Azure рекомендуется использовать [SKU Standard Load Balancer,](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) а затем следующим образом;

> [!NOTE]
> Стандартный балансовый баланс нагрузки SKU имеет ограничения доступа к общедоступным IP-адресам из узлов под балансом нагрузки. В статье [«Общественное соединение конечных точек» для виртуальных машин с использованием баланса стандартной нагрузки Azure в сценариях высокой доступности SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) описывается, как дать этим узлам доступ к общедоступным IP-адресам



1. Создание пула IP-адреса:

   а. На портале Azure откройте балансовик загрузки Azure, выберите **пул IP-адреса,** а затем выберите **Добавить.**

   b. Введите название нового пула IP-адреса (например, **Db2-соединение).**

   c. Установите **назначение** **в Static**и введите **IP-адрес Virtual-IP,** определенный в начале.

   d. Нажмите кнопку **ОК**.

   д) Когда пул интерфейсных IP-адресов будет создан, запишите его IP-адрес.

1. Создание бэк-эндпула:

   а. На портале Azure откройте балансовик загрузки Azure, выберите **пулы бэкэнда,** а затем выберите **Добавить.**

   b. Введите название нового бэк-энда (например, **Db2-бэкэнд).**

   c. Щелкните **Добавить виртуальную машину**.

   d. Выберите набор доступности или виртуальные машины, хпаряющие базу данных IBM Db2, созданные на предыдущем этапе.

   д) Выберите виртуальные машины кластера IBM Db2.

   е) Нажмите кнопку **ОК**.

1. Создайте зонд здоровья:

   а. На портале Azure откройте балансовик загрузки Azure, выберите **зонды работоспособности**и **выберите Добавить.**

   b. Введите название нового зонда здоровья (например, **Db2-л.с.).**

   c. Выберите **TCP** в качестве протокола и порт **62500**. Держите значение **Interval,** установленное до **5,** и сохраните значение **нездорового порога** до **2**.

   d. Нажмите кнопку **ОК**.

1. Создание правил балансировки нагрузки:

   а. На портале Azure откройте балансовик загрузки Azure, выберите **правила балансировки нагрузки,** а затем выберите **Добавить.**

   b. Введите название нового правила балансора нагрузки (например, **Db2-SID).**

   c. Выберите IP-адрес переднего конца, пул бэк-энда и зонд работоспособности, созданный ранее (например, **Db2-frontend).**

   d. Держите **протокол** в **TCP**и введите *порт базы данных communication port.*

   д) Увеличьте **время ожидания** до 30 минут.

   е) Убедитесь в том, чтобы **включить плавающий IP**.

   ж. Нажмите кнопку **ОК**.

Добавить правило брандмауэра для порта **зонда:**
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Вносить изменения в профили SAP, чтобы использовать виртуальный IP для подключения
Для подключения к основной экземпляру конфигурации HADR слой приложения SAP должен использовать виртуальный IP-адрес, который вы определили и настроили для баланса загрузки Azure. Требуются следующие изменения:

/sapmnt/\<SID>/профиль/DEFAULT. Pfl
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Установка первичных и диалоговые серверы приложений

При установке первичных и диалоговые серверы приложений в отношении конфигурации Db2 HADR используйте выбранное для конфигурации имя виртуального хоста. 

Если вы выполнили установку до создания конфигурации Db2 HADR, внося изменения, описанные в предыдущем разделе, и следующие для стеков SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Проверка URL-адресов ABAP-Java или Java-систем JDBC

Используйте инструмент J2EE Config для проверки или обновления URL-адреса JDBC. Поскольку инструмент J2EE Config является графическим инструментом, необходимо установить X-сервер:
 
1. Войти на основной сервер приложения экземпляра J2EE и выполнить:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. В левой раме выберите **магазин безопасности.**
1. В нужном кадре `jdbc/pool/\<SAPSID>/url`выберите ключ.
1. Измените имя хоста в URL JDBC на имя виртуального хоста.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Нажмите кнопку **Добавить**.
1. Чтобы сохранить изменения, выберите значок диска в левом верхнем углу.
1. Закройте инструмент конфигурации.
1. Перезапустите экземпляр Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Настройка архивирования журнала для установки HADR
Для настройки архива журнала Db2 для установки HADR мы рекомендуем настроить как основную, так и в режиме ожидания базу данных, чтобы иметь возможность автоматического поиска журналов из всех мест архива журнала. Начальная и в режиме ожидания база данных должна иметь возможность извлекать файлы архива журналов из всех мест архива журнала, в которые может быть архивирован файлы журнала. 

Архивирование журналов выполняется только основной базой данных. При изменении ролей HADR серверов базы данных или при сбое новая основная база данных отвечает за архивирование журналов. Если вы настроили несколько местоположений архива журналов, ваши журналы могут быть заархивированы дважды. В случае локального или удаленного догоняющего, вам также придется вручную копировать архивные журналы со старого основного сервера в active log location нового основного сервера.

Мы рекомендуем настроить общую долю NFS или GlusterFS, где журналы пишутся из обоих узлов. Доля NFS или GlusterFS должна быть очень доступной. 

Для транспортировки или каталога профилей можно использовать существующие высокодоступные акции NFS или GlusterFS. Дополнительные сведения см. в разделе:

- [GlusterFS на виртуальных машинах Azure с Red Hat Enterprise Linux для SAP NetWeaver][glusterfs] 
- [Высокая доступность SAP NetWeaver на M Ms Azure на Red Hat Enterprise Linux с файлами Azure NetApp для приложений SAP][anf-rhel]
- [Файлы Сети Приложений Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (для создания акций NFS)

## <a name="test-the-cluster-setup"></a>Проверка настройки кластера

В этом разделе описывается, как можно протестировать настройку Db2 HADR. Каждый тест предполагает, что IBM Db2 первичный работает на виртуальной машине *az-idb01.* Пользователь с привилегиями судо или корень (не рекомендуется) должны быть использованы.

Первоначальный статус для всех тестовых случаев объясняется здесь: (crm_mon -r или pcs статус)

- **Статус PCs** — это моментальный снимок состояния Pacemaker во время выполнения 
- **crm_mon -r** - это непрерывный выход статуса Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

Исходный статус в системе SAP задокументирован в обзоре > конфигурации транзакций DBACOCKPIT >, как показано на следующем изображении:

![DBACockpit - Предварительная миграция](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Тестовый захват IBM Db2


> [!IMPORTANT] 
> Перед началом теста убедитесь, что:
> * Pacemaker не имеет каких-либо неудачных действий (статус PCS).
> * Нет ограничений местоположения (остатки миграционного теста)
> * Работает синхронизация IBM Db2 HADR. Проверьте с пользователем db2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Мигрируйте в узла, который выполняет основную базу данных Db2, выполняя следующие команды:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

После завершения миграции выход состояния crm выглядит следующим образом:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

Исходный статус в системе SAP задокументирован в обзоре > конфигурации транзакций DBACOCKPIT >, как показано на следующем изображении:

![DBACockpit - После миграции](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

Миграция ресурсов с помощью "перемещения ресурсов pcs" создает ограничения местоположения. Ограничения местоположения в этом случае препятствуют запуску экземпляра IBM Db2 на az-idb01. Если ограничения местоположения не удаляются, ресурс не может выйти из строя.

Удалите ограничение местоположения и резервный узел будет запущен на az-idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
И статус кластера меняется на:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit - Удалено ограничение местоположения](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Перенести ресурс обратно в *az-idb01* и очистить ограничения местоположения
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **PCs ресурс \< <host>двигаться res_name>:** Создает ограничения местоположения и может вызвать проблемы с поглощением
- **PCs ресурс \<ясно res_name>**: Очищает ограничения местоположения
- **PCs ресурс \<очистки res_name>**: Очищает все ошибки ресурса

### <a name="test-a-manual-takeover"></a>Протестировать ручной захват

Вы можете протестировать ручной захват, остановив сервис Pacemaker на узел *az-idb01:*
<pre><code>systemctl stop pacemaker</code></pre>

статус на *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

После неудачи, вы можете начать службу снова на *az-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Убейте процесс Db2 на узлах, который запускает основную базу данных HADR

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Экземпляр Db2 потерпит неудачу, и Pacemaker переместит мастер-узла и сообщит следующее состояние:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

Pacemaker перезапустит экземпляр первичной базы данных Db2 на том же узлах, или он выйдет из строя к узлам, который работает в экземпляре вторичной базы данных, и сообщается об ошибке.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Убейте процесс Db2 на узлах, который выполняет экземпляр вторичной базы данных

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Узл попадает в неудавшийся заявленный и сообщается об ошибке
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

Экземпляр Db2 перезапускается во второстепенной роли, которую он назначил ранее.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Остановите DB с помощью db2stop силы на узлах, который работает экземпляр первичной базы данных HADR

Как пользователь db2\<sid> выполнить команду db2stop силы:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Обнаружена неисправность:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

Экземпляр вторичной базы данных Db2 HADR получил повышение в основной роли.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Сбой VM, который запускает экземпляр первичной базы данных HADR с "halt"

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

В таком случае Pacemaker обнаружит, что узла, работающего в экземпляре основной базы данных, не отвечает.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

Следующим шагом является проверка ситуации *с мозгом Сплита.* После того, как уцелевший узла определил, что узло, которое в последний раз выработало экземпляр первичной базы данных, не работает, выполняется сбой ресурсов.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


В случае паники ядра, неуграмный узла будет restared фехтованием агентом. После того, как неудавшийся узла вернулся в строй, вы должны запустить кластер кардиостимулятора
<pre><code>sudo pcs cluster start</code></pre> оно начинает экземпляр Db2 в вторичную роль.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Дальнейшие действия
- [Высокодоступная архитектура и сценарии для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Настройка кластера Pacemaker в Red Hat Enterprise Linux в Azure][rhel-pcs-azr]
