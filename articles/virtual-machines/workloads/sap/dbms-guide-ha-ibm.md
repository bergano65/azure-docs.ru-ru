---
title: Настройка IBM Db2 HADR на виртуальных машинах (ВМ) Azure | Документация Майкрософт
description: Обеспечение высокого уровня доступности IBM Db2 LUW на виртуальных машинах (ВМ) Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: 7464ea481d4c95856b78a83a875f2cd24c00705b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503321"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Высокий уровень доступности LUW IBM Db2 на виртуальных машинах Azure в SUSE Linux Enterprise Server с помощью Pacemaker

IBM Db2 для Linux, UNIX и Windows (LUW) в [высокий уровень доступности и аварийное восстановление (HADR) конфигурации](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) состоит из одного узла, где запущен экземпляр базы данных-источника и хотя бы один узел, где запущен экземпляр базы данных-получателя. Изменения в экземпляре базы данных-источника, реплицируются на экземпляр базы данных-получателя синхронно или асинхронно, в зависимости от конфигурации. 

В этой статье описывается развертывание и настройка виртуальных машин (ВМ) Azure, установка платформы кластера и установка LUW IBM Db2 с помощью конфигурации HADR. 

Статье не рассматривается Установка и настройка IBM Db2 LUW при установке программного обеспечения HADR или SAP. Чтобы помочь вам выполнить эти задачи, мы предоставляем ссылки на руководства по установке SAP и IBM. Эта статья посвящена части, которые используются в среде Azure. 

Поддерживаемые версии IBM Db2 10.5 и более поздние версии, как описано в примечании SAP [1928533].

Перед началом установки см. в следующих примечаниях SAP и документацию.

| Примечание к SAP | Описание |
| --- | --- |
| [1928533] | Приложения SAP в Azure: поддерживаемые продукты и типы виртуальных машин Azure |
| [2015553] | SAP в Azure: необходимые компоненты для поддержки |
| [2178632] | Ключевые метрики мониторинга для SAP в Azure |
| [2191498] | SAP на платформе Linux в Azure: расширенный мониторинг |
| [2243692] | Linux в Azure виртуальных Машинах (IaaS): проблемы с лицензированием SAP |
| [1984787] | SUSE Linux Enterprise Server 12 Замечания по установке |
| [1999351] | Устранение неполадок, связанных с расширенным мониторингом Azure для SAP |
| [2233094] | DB6: Приложения SAP в Azure, использующих IBM Db2 для Linux, UNIX и Windows — Дополнительные сведения |
| [1612105] | DB6: Часто задаваемые вопросы о Db2 с HADR |


| Документация | 
| --- |
| [Вики-сайт сообщества SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Имеет все необходимые примечания к SAP для Linux |
| [На виртуальных машинах планированию и внедрению SAP NETWEAVER на платформе Linux][planning-guide] руководство |
| [Развертывание виртуальных машин для SAP NETWEAVER на][deployment-guide] (Эта статья) |
| [Виртуальные машины Azure, базы данных управления system(DBMS) развертывания SAP NetWeaver на][dbms-guide] руководство |
| [Рабочих нагрузок SAP в Azure контрольный список планирования и развертывания][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server для SAP приложений 12 SP3 лучшие рекомендации, руководства по][sles-for-sap-bp] |
| [SUSE Linux Enterprise высокого уровня доступности расширения 12 SP3][sles-ha-guide] |
| [IBM Db2 Azure виртуальные машины по развертыванию СУБД для рабочих нагрузок SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Обзор
Для обеспечения высокой доступности, LUW IBM Db2 с HADR установлен по крайней мере два виртуальных машинах Azure, которые будут развернуты в [группе доступности Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) или по горизонтали [зоны доступности Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Следующие графики отображения настройки виртуальных машин Azure сервера две базы данных. Оба сервера базы данных виртуальных машин Azure, имеют свои собственные хранилище, подключенное и запущены и работают. В HADR один экземпляр базы данных в одной из виртуальных машин Azure имеет роль экземпляра сервера-источника. Все клиенты будут подключены к этой основной экземпляр. Все изменения в транзакции базы данных сохраняются локально в журнале транзакций Db2. Как записи журнала транзакций сохраняются локально, записи передаются по протоколу TCP/IP к экземпляру базы данных — на второй сервер базы данных, резервный сервер или резервный экземпляр. Резервный экземпляр обновляет локальную базу данных, накат переносятся транзакции записи журнала. Таким образом резервный сервер синхронизируется с основным сервером.

HADR является только функциональные возможности репликации. Она имеет не обнаружения сбоев и автоматического перенаправления или отработки отказа средств. Перенаправление или передачи к резервному серверу должна инициироваться вручную администратором базы данных. Чтобы обеспечить автоматическое перенаправление и обнаружение сбоя, можно использовать функцию кластеризации Linux Pacemaker. Pacemaker отслеживает экземпляры сервера две базы данных. Когда экземпляр сервера базы данных-источника в случае сбоя инициирует Pacemaker *автоматического* перенаправление HADR, резервный сервер. Pacemaker также гарантирует, что виртуальный IP-адрес назначен новому основному серверу.

![Общие сведения о высокой доступности IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Для SAP серверов приложений соединиться с базой данных-источником, требуется имени виртуального узла и виртуальный IP-адрес. При отработке отказа серверы приложений SAP будет подключаться к новый экземпляр базы данных-источника. В среде Azure [балансировщика нагрузки Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) необходим для использования виртуального IP-адреса так, требуемом для HADR IBM Db2. 

Чтобы помочь вам лучше понять, как LUW IBM Db2 с HADR и Pacemaker встраивается в высокодоступной конфигурации системы SAP, ниже представлен обзор настройки высокодоступной системы SAP на основе базы данных IBM Db2. В этой статье рассматриваются только IBM Db2, но предоставляет ссылки на другие ресурсы, посвященные настройке других компонентов системы SAP.

![Обзор полную среду высокой доступности IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Общий обзор необходимых действий
Развертывания конфигурации IBM Db2, необходимо выполнить следующие действия:

  + Планирование среды.
  + Разверните виртуальные машины.
  + Обновите SUSE Linux и настройки файловых систем.
  + Установка и настройка Pacemaker.
  + Установка [высокой доступности NFS][nfs-ha].
  + Установка [ASCS/ERS на отдельный кластер][ascs-ha].
  + Установка базы данных IBM Db2 с параметром распределенных и высокий уровень доступности (SWPM).
  + Установите и создайте узел базы данных-получателя и экземпляр и настройте HADR.
  + Убедитесь, что работает HADR.
  + Применение конфигурации Pacemaker управления IBM Db2.
  + Настройте подсистему балансировки нагрузки Azure.
  + Установка основного и серверов приложений диалогового окна.
  + Проверьте и адаптировать конфигурацию серверов приложений SAP.
  + Выполнение отработки отказа и перехвата тестов.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Планирование инфраструктуры Azure для размещения LUW IBM Db2 с HADR

Завершения процесса планирования, до выполнения развертывания. Планирование закладывает основу для развертывания конфигурации из Db2 с HADR в Azure. В следующей таблице перечислены ключевые элементы, которые должны быть частью планирования для LUW Db2 IMB (базы данных частью среды SAP).

| Раздел | Краткое описание |
| --- | --- |
| Определять группы ресурсов Azure | Группы ресурсов, где развертывание виртуальной Машины, виртуальной сети, подсистемы балансировки нагрузки Azure и другие ресурсы. Можно выбрать существующую или новую. |
| Виртуальная сеть / определение подсети | Где развертываемых виртуальных машин для IBM Db2 и Azure Load Balancer. Может быть существующую или новую. |
| Размещение IBM Db2 LUW на виртуальных машинах | Размер виртуальной Машины, хранение, сеть, IP-адрес. |
| Имя виртуального узла и виртуальный IP-адрес для базы данных IBM Db2| Виртуальный IP-адрес или имя узла, используемый для подключения серверов приложений SAP. **DB-virt-hostname**, **db-virt-ip**. |
| Azure ограждения | Azure ограждения или ограждения SBD (настоятельно рекомендуется). Метод, чтобы предотвратить удаление разделения вычислительных мощностей блокируется. |
| SBD ВИРТУАЛЬНОЙ МАШИНЫ | Размер виртуальной машины SBD, хранилище, сеть. |
| Azure Load Balancer | Использование Basic или Standard (рекомендуется), порт для базы данных Db2 (мы рекомендуем использовать 62500) пробы **порт пробы**. |
| Разрешение имен| Как разрешение имен работает в среде. Настоятельно рекомендуется использовать службу DNS. Можно использовать локальный файл hosts. |
    
Дополнительные сведения о Linux Pacemaker в Azure, см. в разделе [настройка кластера Pacemaker в SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Развертывание в SUSE Linux

Агент ресурсов для IBM Db2 LUW включается в SUSE Linux Enterprise Server for SAP Applications. Для настройки, описанной в этом документе необходимо использовать сервер Linux SUSE для приложений SAP. Azure Marketplace содержит образ для SUSE Enterprise Server for SAP Applications 12, который можно использовать для развертывания новых виртуальных машин Azure. Имейте в виду различных моделей поддержки или службы, предлагаемые SUSE в Azure Marketplace при выборе образа виртуальной Машины в виртуальной Машине в Azure Marketplace. 

### <a name="hosts-dns-updates"></a>Узлов: Обновления DNS
Создайте список всех имен узлов, включая имена виртуальных узлов и обновите DNS-серверы для включения правильный IP-адрес для разрешения имени узла. Если DNS-сервер не существует или не может обновлять и создавать записи DNS, необходимо использовать локальный узел файлы отдельных виртуальных машин, которые участвуют в этом сценарии. При использовании записей файлы узлов, убедитесь, что записи применяются ко всем виртуальным машинам в среде системы SAP. Тем не менее мы рекомендуем использовать DNS-сервера, в идеальном случае заходят в Azure


### <a name="manual-deployment"></a>Развертывание вручную

Убедитесь, что выбранной ОС поддерживается IBM и SAP для IBM Db2 LUW. Список поддерживаемых версий ОС для виртуальных машин Azure и Db2 выпусков можно найти в примечании SAP [1928533]. Список версий операционной системы, отдельным выпуском Db2 доступна в матрице доступности продуктов SAP. Мы настоятельно рекомендуем не менее SLES 12 SP3 из-за повышение производительности, связанных с Azure в этой или более поздней версии SUSE Linux.

1. Создайте или выберите группу ресурсов.
1. Создайте или выберите виртуальную сеть и подсеть.
1. Создание группы доступности Azure или развернуть зону доступности.
    + Для группы доступности задайте домены обновления максимальное значение 2.
1. Создание виртуальной машины 1.
    + Используйте SLES для SAP образа в Azure Marketplace.
    + Выберите группу доступности Azure, созданную на шаге 3, или выберите зону доступности.
1.  Создание виртуальной машины 2.
    + Используйте SLES для SAP образа в Azure Marketplace.
    + Выберите группу доступности Azure в созданную на шаге 3, или выберите зону доступности (не той же зоне как в шаге 3).
1. Добавление дисков данных к виртуальным машинам и проверьте рекомендации настройки системы файл в этой статье [IBM Db2 Azure виртуальные машины по развертыванию СУБД для рабочей нагрузки SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Создание кластера Pacemaker
    
Чтобы создать базовый кластер Pacemaker для этого сервера IBM Db2, см. в разделе [настройка кластера Pacemaker в SUSE Linux Enterprise Server в Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Установите среду IBM Db2 LUW и SAP

Перед началом установки SAP среду, основанную на IBM Db2 LUW со следующей документацией:

+ Документация по Azure
+ Документации по SAP
+ Документации по IBM

В вводной части в этой статье приводятся ссылки в эту документацию.

Проверьте руководства по установке SAP по установке приложений на основе NetWeaver на IBM Db2 LUW.

Можно найти руководства на портале SAP справки с помощью [Finder руководство по установке SAP][sap-instfind].

Можно уменьшить число структур, которые отображаются на портале, задав следующие фильтры:

- Я хочу: «Установить новую систему»
- Моя база данных: «IBM Db2 для Linux, Unix и Windows»
- Дополнительных фильтров для версий, конфигурация стека или операционной системы SAP NetWeaver

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Установка указания по настройке LUW IBM Db2 с HADR

Чтобы настроить основной экземпляр базы данных IBM Db2 LUW:

- Используйте высокого уровня доступности или распределенные параметр.
- Установите экземпляр SAP ASCS/ERS и база данных.
- Создайте резервную копию только что установленной базы данных.


> [!IMPORTANT] 
> Запишите «взаимодействие с базой данных порт», задаются во время установки. Он должен быть один и тот же номер порта для обоих экземпляров базы данных

Чтобы настроить базу данных на резервный сервер с помощью процедуры копирование однородной системы SAP, выполните следующие действия:

1. Выберите **копирование системы** параметр > **целевых систем** > **распределенных** > **экземпляр базы данных**.
1. Как метод копирования, выберите **однородные системы** таким образом, можно использовать резервное копирование для восстановления резервной копии на резервным экземпляром сервера.
1. Дойдя до выхода шаг, чтобы восстановить базу данных для копирования однородные системы, закройте программу. Восстановите базу данных из резервной копии основной узел. На сервере базы данных-источника уже выполнены все этапы для последующей установки.
1. Настройка HADR для IBM Db2.

   > [!NOTE]
   > Для установки и конфигурации, относящиеся к Azure и Pacemaker. Во время процесса установки через SAP Software Provisioning Manager есть явная вопрос высокий уровень доступности для IBM Db2 LUW:
   >+ Не устанавливайте **pureScale IBM Db2**.
   >+ Не устанавливайте **установить IBM Tivoli системы автоматизации для Multiplatforms**.
   >+ Не устанавливайте **создать файлы конфигурации кластера**.

   При использовании устройство SBD для Linux Pacemaker, задайте следующие параметры Db2 HADR:
   + Длительность периода HADR однорангового узла (в секундах) (HADR_PEER_WINDOW) = 300  
   + Значение времени ожидания HADR (HADR_TIMEOUT) = 60

   Если вы используете агент ограждения Azure Pacemaker, задайте следующие параметры:
   + Длительность периода HADR однорангового узла (в секундах) (HADR_PEER_WINDOW) = 900  
   + Значение времени ожидания HADR (HADR_TIMEOUT) = 60

Мы рекомендуем использовать описанные выше параметры, в зависимости от первоначального тестирования отработки отказа и перенаправление. Это обязательный параметр, проверить правильность работы всех функций отработки отказа и перенаправление с помощью этих параметров. Поскольку отдельные конфигурации могут различаться, параметры требуют корректировки. 

> [!IMPORTANT]
> Относящиеся к IBM Db2 с помощью конфигурации HADR с Обычный запуск: Базы данных-получателя или резервный экземпляр должен быть запущен и работает, перед запуском экземпляра базы данных-источника.

Для демонстрационных целей и процедуры, описанные в этой статье, с помощью базы данных идентификатор безопасности — **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Проверка IBM Db2 HADR
После настройки HADR и находится в состоянии однорангового узла и ПОДКЛЮЧЕНО на основной и резервный узлах, выполните следующую проверку:

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

При использовании Pacemaker для автоматической отработки отказа в случае сбоя узла, необходимо соответствующим образом настроить экземпляры Db2 и Pacemaker. В этом разделе описывается конфигурация данного типа.

Следующие элементы с префиксами::

- **[A]** : Применяется ко всем узлам
- **[1]** : Применяется только к узлу 1 
- **[2]** : Применяется только к узлу 2

**[A]**  Необходимые условия для Pacemaker конфигурации:
1. Завершите работу обоих серверов баз данных с db2 пользователя\<sid > с db2stop.
1. Измените среду оболочки для db2\<sid > пользователю */bin/ksh*. Мы рекомендуем использовать средство Yast. 


### <a name="pacemaker-configuration"></a>Pacemaker конфигурации

**[1]**  Конфигурации HADR конкретных Pacemaker IBM Db2:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Ресурсы создать IBM Db2:
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
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]**  Ресурсы запустить IBM Db2:
* Поместите Pacemaker из режима обслуживания.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Убедитесь, что кластер находится в работоспособном состоянии и что запущены все ресурсы. Не важно какой из узлов на ресурсы.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 узлами
# <a name="5-resources-configured"></a>5 настроенных ресурсов

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [azibmdb01 azibmdb02]

# <a name="full-list-of-resources"></a>Полный список ресурсов:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith sbd (stonith:external / sbd): Azibmdb02 по началу работы
#  <a name="resource-group-gipdb2ptrptr"></a>Resource Group: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Azibmdb02 по началу работы
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat: ничего):      Azibmdb02 по началу работы
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Набор, ведущий/ведомый: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Мастеры: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Создание подчиненных модулей: [azibmdb01]
</pre>

> [!IMPORTANT]
> Необходимо управлять Pacemaker кластеризованного экземпляра Db2 с помощью средств Pacemaker. При использовании команд db2, например db2stop Pacemaker обнаруживает это действие как сбой ресурса. При выполнении обслуживания, можно поместить узлов или ресурсы в режим обслуживания. Pacemaker приостанавливает ресурсы мониторинга, а затем можно использовать обычный db2 администрирования команды.


### <a name="configure-azure-load-balancer"></a>Настройка Azure Load Balancer
Чтобы настроить балансировщик нагрузки Azure, мы рекомендуем использовать [Azure SKU Standard подсистемы балансировки нагрузки](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) и выполните следующую процедуру.

1. Создайте интерфейсный пул IP-адресов:

   a. На портале Azure, подсистему балансировки нагрузки Azure, выберите **внешних IP-адресов**, а затем выберите **добавить**.

   2\. Введите имя нового пула IP-адрес внешнего интерфейса (например, **соединителя Db2**).

   c. Задайте **назначения** для **статический**и введите IP-адрес **виртуальных IP-** в начале.

   d. Нажмите кнопку **ОК**.

   д. Когда пул интерфейсных IP-адресов будет создан, запишите его IP-адрес.

1. Создание пула серверной части:

   a. На портале Azure, подсистему балансировки нагрузки Azure, выберите **серверные пулы**, а затем выберите **добавить**.

   2\. Введите имя нового пула серверной части (например, **Db2-backend**).

   c. Щелкните **Добавить виртуальную машину**.

   d. Выберите группу доступности или виртуальные машины, на котором размещена база данных IBM Db2, созданные на предыдущем шаге.

   д. Выберите виртуальные машины кластера IBM Db2.

   Е. Нажмите кнопку **ОК**.

1. Создайте пробу работоспособности:

   a. На портале Azure, подсистему балансировки нагрузки Azure, выберите **пробы работоспособности**и выберите **добавить**.

   2\. Введите имя новой пробы работоспособности (например, **Db2-hp**).

   c. Выберите **TCP** как протокол и порт **62500**. Сохранить **интервал** значение **5**и сохранить **порог состояния неработоспособности** значение **2**.

   d. Нажмите кнопку **ОК**.

1. Создайте правила балансировки нагрузки:

   a. На портале Azure, подсистему балансировки нагрузки Azure, выберите **правила балансировки нагрузки**, а затем выберите **добавить**.

   2\. Введите имя нового правила балансировки нагрузки (например, **Db2 SID**).

   c. Выберите интерфейсный IP-адрес, внутренний пул и пробу работоспособности, который был создан ранее (например, **Db2-frontend**).

   d. Сохранить **протокола** присвоено **TCP**и введите порт *порта взаимодействия с базой данных*.

   д. Увеличьте **время ожидания** до 30 минут.

   Е. Не забудьте **включить плавающий IP-адрес**.

   ж. Нажмите кнопку **ОК**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Вносить изменения в профили SAP будет использоваться виртуальный IP-адрес для подключения
Для подключения к основной экземпляр конфигурации HADR, SAP уровень приложения должен использовать виртуальный IP-адрес, определенный и настроенное для Azure Load Balancer. Необходимы следующие изменения:

/sapmnt/\<SID >/профилей/по умолчанию. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Установка основного и серверов приложений диалоговое окно

При установке первичного и серверы приложений диалоговое окно конфигурации Db2 HADR, используйте виртуальный узел имя, которое вы выбрали для конфигурации. 

Если вы выполнили установку, прежде чем вы создали конфигурацию Db2 HADR, внесите изменения, как описано в предыдущем разделе и следующим образом для стеков SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Проверьте стек ABAP с Java или Java, систем URL-адреса JDBC

Используйте средство конфигурации J2EE для проверки или обновить URL-адреса JDBC. Так как средство настройки J2EE — это графическое средство, необходимо иметь X установлен сервер:
 
1. Войдите на основной сервер приложений J2EE экземпляра и выполните:   `sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. В левом окне выберите **хранилища безопасности**.
1. В правой области выберите ключаjdbc/пул/\<SAPSID > / URL-адрес.
1. Измените имя узла в URL-адреса JDBC на имя виртуального узла.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Выберите **Добавить**.
1. Чтобы сохранить изменения, щелкните значок дискеты в левом верхнем углу.
1. Закройте средство настройки.
1. Перезапустите экземпляр Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Настройка архивации журнала для программы установки HADR
Чтобы настроить Db2 журнала архивирования для программы установки HADR, мы рекомендуем настроить основной и резервной базы данных, чтобы иметь возможность получения автоматического ведения журнала из всех расположений архив журнала. Основной и резервной базы данных должен иметь возможность получения архивные файлы журнала из всех расположений архив журнала, с каким либо из базы данных экземпляров может архивировать файлы журналов. 

Архивации журнала осуществляется только базы данных-источника. При изменении роли HADR серверов баз данных, или если происходит сбой, несет ответственность за архивации журналов новой базы данных-источника. Если вы настроили несколько расположения архива журналов, журналов может архивироваться дважды. В случае локальный или удаленный захват может потребоваться вручную скопировать архивированные журналы из старого сервера-источника в активный журнал расположение нового сервера-источника.

Рекомендуется выполнить настройку общей папке NFS, в которую записываются журналы с обоих узлов. В общий ресурс NFS должен обладать высокой доступностью. 

Можно использовать существующие общие папки NFS высокой доступности для транспортов или каталог профиля. Дополнительные сведения можно найти в разделе

- [Высокий уровень доступности для NFS на виртуальных машинах Azure в SUSE Linux Enterprise Server][nfs-ha] 
- [Высокий уровень доступности для SAP NetWeaver на виртуальных машинах Azure в SUSE Linux Enterprise Server с файлами NetApp Azure для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Служба файлов Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (для создания общих ресурсов NFS)


## <a name="test-the-cluster-setup"></a>Проверка настройки кластера

В этом разделе описывается, как вы можете проверить конфигурацию Db2 HADR. *Для каждого теста предполагается, что вы вошли качестве привилегированного пользователя* и IBM Db2 основной работает под управлением *azibmdb01* виртуальной машины.

Здесь подробно описаны начальное состояние для всех тестовых случаев: (crm_mon - r или crm status)

- **состояние CRM** представляет собой моментальный снимок состояния Pacemaker во время выполнения 
- **crm_mon - r** непрерывной выводится состояние Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

Исходное состояние в системе SAP описан в DBACOCKPIT для транзакции > настройки > Общие сведения, как показано на следующем рисунке:

![DBACockpit - Pre миграции](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Перенаправление теста IBM Db2


> [!IMPORTANT] 
> Перед началом теста убедитесь, что:
> * Pacemaker нет все завершившиеся сбоем действия (crm status).
> * Нет никаких ограничений расположение (leftovers теста миграции)
> * Работоспособности синхронизации IBM Db2 HADR. Проверка с помощью db2 пользователя\<ИД безопасности > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Миграция узла, на котором работает база данных-источник Db2, выполнив следующую команду:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

После завершения миграции вывод статуса crm выглядит как:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

Исходное состояние в системе SAP описан в DBACOCKPIT для транзакции > настройки > Общие сведения, как показано на следующем рисунке:

![DBACockpit - после миграции](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Миграции ресурсов с «перенос ресурсов crm» устанавливаются ограничения расположения. Следует удалить ограничения расположения. Если ограничения расположения, не удаляются, ресурс нельзя восстановить размещение, или могут возникнуть нежелательные takeovers. 

Перенос ресурса к *azibmdb01* и снимите ограничения расположения
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **перенос ресурсов CRM \<res_name > <host>:** Устанавливаются ограничения расположения и может вызвать проблемы с перенаправление
- **Очистка ресурсов CRM \<res_name >** : Удаляет ограничения расположения
- **Очистка ресурсов CRM \<res_name >** : Удаляет все ошибки ресурса

### <a name="test-the-fencing-agent"></a>Проверить конфигурацию агента ограждения

В этом случае мы тестируем ограждения SBD, который мы рекомендуем выполнить при использовании SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Узел кластера *azibmdb01* необходимо перезагрузить компьютер. Планируется переместить в первичной роли HADR IBM Db2 *azibmdb02*. Когда *azibmdb01* оперативный, Db2, экземпляра планируется переместить в роли экземпляра базы данных-получателя. 

Если на после перезагрузки первый основной службы Pacemaker не запускается автоматически, не забудьте запустить его вручную с помощью:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Тестирование вручную перенаправление

Вы можете проверить перенаправление вручную, остановив службу Pacemaker на *azibmdb01* узла:
<pre><code>service pacemaker stop</code></pre>

status on *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

После отработки отказа, вы можете снова запустить службу на *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Завершить процесс Db2 на узле, на котором работает база данных-источник HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Экземпляр Db2 это ведет к сбою, и сообщает Pacemaker следующие состояния:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker перезапустит экземпляр базы данных-источника Db2 на одном узле, или он выполнит отработку отказа на узел, на котором выполняется экземпляр базы данных-получателя и возвращается ошибка.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Завершить процесс Db2 на узле, где запущен экземпляр базы данных-получателя

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

Узел получает не удалось выполнить into заявленным и сообщение об ошибке
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

Во вторичной роли, которую ей назначено перед перезапускается экземпляр Db2.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Остановить DB через force db2stop на узле, где запущен экземпляр базы данных-источника HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Как пользователь db2\<sid > выполните команду db2stop force:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Обнаружен сбой
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

Экземпляр базы данных-получателя Db2 HADR распространяется в основную роль
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Завершить работу виртуальной Машины, перезапуск на узле, где запущен экземпляр базы данных-источника HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker переведет вторичном экземпляре роли экземпляра сервера-источника. Старый первичного экземпляра будут перемещены в вторичной роли после виртуальной Машины и полностью восстановить все службы после перезагрузки виртуальной Машины:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Аварийное завершение работы виртуальной Машины, которое будет выполняться экземпляр базы данных-источника HADR с «halt»

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

В таком случае Pacemaker определит, что узел, на котором выполняется экземпляр базы данных-источника не отвечает.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

следующим шагом является проверка *разделения вычислительных мощностей* ситуации. После этого узла определил, что узел последнего запуска экземпляра базы данных-источника не работает, выполняется отработка отказа ресурсов.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


В случае «остановка» узла, неисправного узла должна быть перезапущена с помощью средств управления Azure (на портале Azure, PowerShell или Azure CLI). После сбоя узла будет восстановлен, он запускает экземпляр Db2 в роль вторичной.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Дальнейшие действия
- [Архитектура высокого уровня доступности и сценарии для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Настройка кластера Pacemaker в SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

