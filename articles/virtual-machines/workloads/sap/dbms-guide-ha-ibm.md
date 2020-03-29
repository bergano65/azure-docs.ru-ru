---
title: Настройка IBM Db2 HADR на виртуальных машинах Azure (VMs) Документы Майкрософт
description: Установить высокую доступность IBM Db2 LUW на виртуальных машинах Azure (VM).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: juergent
ms.openlocfilehash: 614ac8b651224a3b6ec605a6bffd520449a1d793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926747"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Высокая доступность IBM Db2 LUW на Облачных Вымизированных на SUSE Linux Enterprise Server с Pacemaker

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
| [1984787] | SUSE LINUX Enterprise Server 12: примечания к установке |
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
| [SUSE Linux Enterprise Server для SAP-приложений 12 лучших практик SP4][sles-for-sap-bp] |
| [SUSE Linux Предприятие Высокое расширение доступности 12 SP4][sles-ha-guide] |
| [Развертывание виртуальных машин IBM Db2 Виртурные машины DBMS для рабочей нагрузки SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Обзор
Для достижения высокой доступности IBM Db2 LUW с HADR устанавливается по крайней мере на двух виртуальных машинах Azure, которые развернуты в [наборе доступности Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) или в [зонах доступности Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 

Следующая графика отображает настройку двух серверов баз данных Azure VMs. Оба сервера баз данных Azure VMs имеют собственное хранилище и работают. В HADR в одном экземпляре базы данных в одном из ВМ Azure роль основного экземпляра. Все клиенты подключены к этому основному экземпляру. Все изменения в транзакциях баз данных сохраняются локально в журнале транзакций Db2. Поскольку записи журналов транзакций сохраняются локально, записи передаются через TCP/IP в экземпляр базы данных на втором сервере базы данных, сервере ожидания или резервном экземпляре. Резервный экземпляр обновляет локальную базу данных, перекаив аяды на передаваемые записи журналов транзакций. Таким образом, резервный сервер синхронизируется с основным сервером.

HADR — это только функция репликации. Он не имеет обнаружения сбоев и не автоматического поглощения или отказобъекта. Поглощение или перевод на сервер ожидания должен быть инициирован вручную администратором базы данных. Для автоматического обнаружения поглощения и сбоев можно использовать функцию кластеризации Linux Pacemaker. Pacemaker отслеживает два экземпляра сервера баз данных. При сбое сервера первичной базы данных Pacemaker инициирует *автоматическое* поглощение HADR резервным сервером. Pacemaker также гарантирует, что виртуальный IP-адрес будет назначен новому основному серверу.

![Обзор высокой доступности IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Чтобы серверы приложений SAP подключались к основной базе данных, вам необходимо виртуальное имя хоста и виртуальный IP-адрес. В случае сбоя серверы приложений SAP подключаются к новой основной экземпляру базы данных. В среде Azure [балансиватор нагрузки Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) должен использовать виртуальный IP-адрес так, как это требуется для HADR IBM Db2. 

Чтобы помочь вам в полной мере понять, как IBM Db2 LUW с HADR и Pacemaker вписывается в высокодоступную настройку системы SAP, следующее изображение представляет обзор высокодоступной настройки системы SAP на основе базы данных IBM Db2. Эта статья охватывает только IBM Db2, но содержит ссылки на другие статьи о том, как настроить другие компоненты системы SAP.

![IBM DB2 Высокая доступность полный обзор среды](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Обзор необходимых шагов на высоком уровне
Для развертывания конфигурации IBM Db2 необходимо следовать следующим шагам:

  + Планируйте свою среду.
  + Развертывание VMs.
  + Обновление SUSE Linux и настройка файловых систем.
  + Установите и назначайте Pacemaker.
  + Установите [высокодоступные NFS][nfs-ha].
  + Установите [ASCS/ERS на отдельном кластере.][ascs-ha]
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
| Лазурное фехтование | Лазурное ограждение или ограждение SBD (рекомендуется). Метод, чтобы избежать раскола мозга ситуаций. |
| SBD VM | SBD виртуальный размер машины, хранения, сети. |
| Azure Load Balancer | Использование Basic или Standard (рекомендуется), порт зонда для базы данных Db2 (наша рекомендация 62500) **зонд-порт**. |
| Разрешение имен| Как разрешение имен работает в среде. Высоко рекомендуется обслуживание DNS. Файл локальных хостов можно использовать. |
    
Для получения дополнительной информации о Linux Pacemaker в Azure, [см.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

## <a name="deployment-on-suse-linux"></a>Развертывание на SUSE Linux

Ресурсный агент IBM Db2 LUW включен в sUSE Linux Enterprise Server для SAP-приложений. Для настройки, описанной в этом документе, необходимо использовать SUSE Linux Server для SAP Applications. Рынок Azure содержит изображение для SUSE Enterprise Server для SAP Applications 12, которое можно использовать для развертывания новых виртуальных машин Azure. Будьте в курсе различных моделей поддержки или обслуживания, которые предлагаются SUSE через Azure Marketplace при выборе vM-изображения в Azure VM Marketplace. 

### <a name="hosts-dns-updates"></a>Хосты: Обновления DNS
Составьте список всех имен хоста, включая имена виртуальных хостов, и обновите свои DNS-серверы, чтобы обеспечить правильное ip-адрес для разрешения имен хоста. Если DNS-сервер не существует или вы не можете обновить и создать записи DNS, необходимо использовать локальные файлы хоста отдельных VMs, которые участвуют в этом сценарии. Если вы используете записи файлов-хоста, убедитесь, что записи применяются ко всем vMs в среде системы SAP. Тем не менее, мы рекомендуем использовать DNS, который в идеале распространяется на Azure


### <a name="manual-deployment"></a>Развертывание вручную

Убедитесь, что выбранная ОС поддерживается IBM/SAP для IBM Db2 LUW. Список поддерживаемых версий ОС для выпусков Azure VMs и Db2 доступен в SAP note [1928533]. Список релизов ОС по отдельным версиям Db2 доступен в Матрице доступности продуктов SAP. Мы настоятельно рекомендуем минимум SLES 12 SP4 из-за улучшения производительности, связанного с Azure, в этой или более поздних версиях SUSE Linux.

1. Создайте или выберите группу ресурсов.
1. Создайте или выберите виртуальную сеть и подсеть.
1. Создайте набор доступности Azure или разместите зону доступности.
    + Для набора доступности установите домены максимального обновления до 2.
1. Создание виртуальной машины 1.
    + Используйте SLES для изображения SAP в Azure Marketplace.
    + Выберите набор доступности Azure, созданный на шаг 3, или выберите Зону доступности.
1.  Создание виртуальной машины 2.
    + Используйте SLES для изображения SAP в Azure Marketplace.
    + Выберите набор доступности Azure, созданный в шаге 3, или выберите Зону доступности (не ту же зону, что и в шаге 3).
1. Добавьте диски данных к виртуальным вам, а затем проверьте рекомендацию настройки файловой системы в статье [IBM Db2 Azure Virtual Machines DBMS для рабочей нагрузки SAP.][dbms-db2]

## <a name="create-the-pacemaker-cluster"></a>Создание кластера Pacemaker
    
Чтобы создать базовый кластер Pacemaker для этого [Set up Pacemaker on SUSE Linux Enterprise Server in Azure][sles-pacemaker]сервера IBM Db2, см. 

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

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Подсказки установки для установки IBM Db2 LUW с HADR

Настройка основного экземпляра базы данных IBM Db2 LUW:

- Используйте опцию высокой доступности или распределенную опцию.
- Установите экземпляр SAP ASCS/ERS и базы данных.
- Возьмите резервную часть недавно установленной базы данных.


> [!IMPORTANT] 
> Запишите "Порт связи базы данных", установленный во время установки. Это должно быть один и тот же номер порта для обоих экземпляров базы данных

Для настройки сервера базы данных Standby с помощью однородной системы SAP выполните следующие действия:

1. Выберите опцию **копирования системы** > **целевых систем** > **Распределенная** > **база данных экземпляра.**
1. В качестве метода копирования выберите **однородную систему,** чтобы можно было использовать резервную копию для восстановления резервного копирования в экземпляре резервного сервера.
1. Когда вы достигнете шага выхода для восстановления базы данных для однородной копии системы, выйдите из установки. Восстановление базы данных из резервного копирования основного узла. Все последующие этапы установки уже выполнены на сервере первичной базы данных.
1. Настройка HADR для IBM Db2.

   > [!NOTE]
   > Для установки и конфигурации, специфичных для Azure и Pacemaker: Во время процедуры установки через SAP Software Software Software Manager возникает явный вопрос о высокой доступности ДЛЯ IBM Db2 LUW:
   >+ Не выбирайте **IBM Db2 pureScale**.
   >+ Не выбирайте **установку IBM Tivoli System Automation для мультиплатформ.**
   >+ Не выбирайте **файлы конфигурации кластера Generate.**

   При использовании устройства SBD для Linux Pacemaker установите следующие параметры Db2 HADR:
   + Продолжительность однорангового окна HADR (секунды) (HADR_PEER_WINDOW)  
   + Значение тайм-аута HADR (HADR_TIMEOUT)

   При использовании фехтования Azure Pacemaker установите следующие параметры:
   + Продолжительность однорангового окна HADR (секунды) (HADR_PEER_WINDOW)  
   + Значение тайм-аута HADR (HADR_TIMEOUT)

Мы рекомендуем предыдущие параметры на основе первоначального тестирования на отказ/поглощение. Это обязательно, что вы тестируете для надлежащей функциональности failover и поглощения с этими параметрами параметров. Поскольку отдельные конфигурации могут варьироваться, параметры могут потребовать корректировки. 

> [!IMPORTANT]
> Специфический для IBM Db2 с конфигурацией HADR с обычным запуском: вторичный или резервный экземпляр базы данных должен быть запущен, прежде чем вы сможете запустить экземпляр первичной базы данных.

Для демонстрационных целей и процедур, описанных в этой статье, база данных SID является **PTR**.

#### <a name="ibm-db2-hadr-check"></a>ПРОВЕРКА IBM Db2 HADR
После настройки HADR и статуса PEER и CONNECTED на первичных и резервных узлах выполните следующую проверку:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Конфигурация Db2 Pacemaker

При использовании Pacemaker для автоматического сбоя в случае сбоя узла необходимо настроить экземпляры Db2 и Pacemaker соответственно. Этот раздел описывает этот тип конфигурации.

Следующие элементы являются прификсированными либо:

- **(А)**: Применимо ко всем узлам
- **(** Применяется только к узлам 1 
- **(** Применяется только к узлам 2

**Предпосылки** для конфигурации Pacemaker:
1. Выключите оба сервера баз\<данных с пользователем db2 sid> с db2stop.
1. Измените среду оболочки\<для db2 sid> пользователя *на /bin/ksh.* Мы рекомендуем использовать инструмент Yast. 


### <a name="pacemaker-configuration"></a>Конфигурация кардиостимулятора

> [!IMPORTANT]
> Недавнее тестирование выявило ситуации, когда netcat перестает отвечать на запросы из-за невыполненной работы и ограничения обработки только одного соединения. Ресурс netcat перестает слушать запросы баланса Azure Load, и плавающий IP становится недоступен.  
> Для существующих кластеров Pacemaker, мы рекомендовали в прошлом заменить netcat с socat. В настоящее время мы рекомендуем использовать ресурсный агент azure-lb, который является частью ресурсо-агентов пакета, со следующими требованиями к версии пакета:
> - Для SLES 12 SP4/SP5 версия должна быть не менее ресурсо-агентов -4.3.018.a7fb5035-3.30.1.  
> - Для SLES 15/15 SP1 версия должна быть не менее ресурсо-агентов - 4.3.0184.6ee15eb2-4.13.1.  
>
> Обратите внимание, что изменение потребует краткого простоя.  
> Для существующих кластеров Pacemaker, если конфигурация уже была изменена на использование socat, как описано в [Azure Load-Balancer Detection Hardening,](https://www.suse.com/support/kb/doc/?id=7024128)нет необходимости немедленно переключаться на ресурсный агент Azure-lb.

**Конфигурация** кардиостимулятора IBM Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**Создание** ресурсов IBM Db2:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**Запустите** ресурсы IBM Db2:
* Вывести Pacemaker из режима обслуживания.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**Убедитесь,** что статус кластера в порядке и что все ресурсы запущены. Не важно, на каком узел работают ресурсы.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 узла настроены
# <a name="5-resources-configured"></a>5 ресурсов настроены

# <a name="online--azibmdb01-azibmdb02-"></a>Онлайн: азиббб01 azibmdb02

# <a name="full-list-of-resources"></a>Полный список ресурсов:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd (stonith:external/sbd): Запущенный azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Ресурсная группа: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (ocf::heartbeat:IPaddr2): Запущенный azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat:azure-lb): Запущенный azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Мастер/Рабский набор: msl_Db2_db2ptr_PTR (rsc_Db2_db2ptr_PTR)
#      <a name="masters--azibmdb02-"></a>Мастера: азиббб02
#      <a name="slaves--azibmdb01-"></a>Рабы: азиббб01
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
 
1. Войти на основной сервер приложения экземпляра J2EE и выполнить:`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. В левой раме выберите **магазин безопасности.**
1. В нужном кадре выберите ключ jdbc/pool/\<SAPSID>/url.
1. Измените имя хоста в URL JDBC на имя виртуального хоста.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Нажмите кнопку **Добавить**.
1. Чтобы сохранить изменения, выберите значок диска в левом верхнем углу.
1. Закройте инструмент конфигурации.
1. Перезапустите экземпляр Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Настройка архивирования журнала для установки HADR
Для настройки архива журнала Db2 для установки HADR мы рекомендуем настроить как основную, так и в режиме ожидания базу данных, чтобы иметь возможность автоматического поиска журналов из всех мест архива журнала. Начальная и в режиме ожидания база данных должна иметь возможность извлекать файлы архива журналов из всех мест архива журнала, в которые может быть архивирован файлы журнала. 

Архивирование журналов выполняется только основной базой данных. При изменении ролей HADR серверов базы данных или при сбое новая основная база данных отвечает за архивирование журналов. Если вы настроили несколько местоположений архива журналов, ваши журналы могут быть заархивированы дважды. В случае локального или удаленного догоняющего, вам также придется вручную копировать архивные журналы со старого основного сервера в active log location нового основного сервера.

Мы рекомендуем настроить общую долю NFS, где журналы написаны из обоих узлов. Доля NFS должна быть очень доступной. 

Существующие высокодоступные акции NFS можно использовать для транспортировки или каталога профилей. Дополнительные сведения см. в разделе:

- [Обеспечение высокого уровня доступности NFS на виртуальных машинах Azure в SUSE Linux Enterprise Server][nfs-ha] 
- [Высокая доступность SAP NetWeaver на M-м для Azure на корпоративном сервере SUSE Linux с файлами Azure NetApp для SAP-приложений](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Файлы Сети Приложений Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (для создания акций NFS)


## <a name="test-the-cluster-setup"></a>Проверка настройки кластера

В этом разделе описывается, как можно протестировать настройку Db2 HADR. *Каждый тест предполагает, что вы вошли в систему в качестве корня пользователя* и IBM Db2 основной работает на *azibmdb01* виртуальной машины.

Первоначальный статус для всех тестовых случаев объясняется здесь: (crm_mon -r или crm статус)

- **crm —** это моментальный снимок состояния Pacemaker во время выполнения 
- **crm_mon -r** - это непрерывный выход статуса Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Исходный статус в системе SAP задокументирован в обзоре > конфигурации транзакций DBACOCKPIT >, как показано на следующем изображении:

![DBACockpit - Предварительная миграция](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Тестовый захват IBM Db2


> [!IMPORTANT] 
> Перед началом теста убедитесь, что:
> * Pacemaker не имеет каких-либо неудачных действий (статус crm).
> * Нет ограничений местоположения (остатки миграционного теста)
> * Работает синхронизация IBM Db2 HADR. Проверьте с пользователем db2\<sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Мигрируйте в узла, который выполняет основную базу данных Db2, выполняя следующие команды:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

После завершения миграции выход состояния crm выглядит следующим образом:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Исходный статус в системе SAP задокументирован в обзоре > конфигурации транзакций DBACOCKPIT >, как показано на следующем изображении:

![DBACockpit - После миграции](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Миграция ресурсов с помощью "мигрировать ресурсов crm" создает ограничения местоположения. Ограничения расположения должны быть удалены. Если ограничения местоположения не удаляются, ресурс не может выйти из строя или вы можете испытывать нежелательные поглощения. 

Перенести ресурс обратно в *azibmdb01* и очистить ограничения местоположения
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **Crm ресурс \< \<мигрирует res_name>> хоста:** Создает ограничения местоположения и может вызвать проблемы с поглощением
- **crm ресурс \<ясно res_name>: **Очищает ограничения местоположения
- **crm очистка \<ресурса res_name>: **Очищает все ошибки ресурса

### <a name="test-the-fencing-agent"></a>Испытать фехтование агента

В этом случае мы тестируем sBD ограждение, которое мы рекомендуем вам делать при использовании SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Кластерный узел *azibmdb01* должен быть перезагружен. IBM Db2 основной роли HADR будет перемещен в *azibmdb02*. Когда *azibmdb01* снова в сети, экземпляр Db2 перейдет в роли вторичной инстанции базы данных. 

Если служба Pacemaker не запускается автоматически на перезагруженном бывшем первичном, не забудьте запустить его вручную с:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Протестировать ручной захват

Вы можете протестировать ручной захват, остановив сервис Pacemaker на *узеле azibmdb01:*
<pre><code>service pacemaker stop</code></pre>

статус на *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

После неудачи, вы можете начать службу снова на *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Убейте процесс Db2 на узлах, который запускает основную базу данных HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Экземпляр Db2 потерпит неудачу, и Pacemaker сообщит о следующем состоянии:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker перезапустит экземпляр первичной базы данных Db2 на том же узлах, или он выйдет из строя к узлам, который работает в экземпляре вторичной базы данных, и сообщается об ошибке.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Убейте процесс Db2 на узлах, который выполняет экземпляр вторичной базы данных

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Узл попадает в неудавшийся заявленный и сообщается об ошибке
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Экземпляр Db2 перезапускается во второстепенной роли, которую он назначил ранее.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Остановите DB с помощью db2stop силы на узлах, который работает экземпляр первичной базы данных HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Как пользователь db2\<sid> выполнить команду db2stop силы:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Обнаружена неисправность
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Экземпляр вторичной базы данных Db2 HADR получил повышение в основной роли
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Crash VM с перезапуском на узле, который запускает экземпляр первичной базы данных HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker будет способствовать вторичной инстанции в основной роли экземпляра. Старый основной экземпляр перейдет во второстепенную роль после vM и полностью восстановлены все службы после перезагрузки VM:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Сбой VM, который запускает экземпляр первичной базы данных HADR с "halt"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

В таком случае Pacemaker обнаружит, что узла, работающего в экземпляре основной базы данных, не отвечает.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Следующим шагом является проверка ситуации *с мозгом Сплита.* После того, как уцелевший узла определил, что узло, которое в последний раз выработало экземпляр первичной базы данных, не работает, выполняется сбой ресурсов.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


В случае «остановки» узла неузеля необходимо перезапустить с помощью инструментов управления Azure (на портале Azure, PowerShell или Azure CLI). После того, как неузлый вернулся в онлайн, он запускает экземпляр Db2 во второстепенную роль.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Дальнейшие действия
- [Высокодоступная архитектура и сценарии для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Настройка Pacemaker на Корпоративном сервере SUSE Linux в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

