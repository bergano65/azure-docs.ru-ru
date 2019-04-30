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
ms.openlocfilehash: 3c1d0e252b5c658ab6da2b3932918f05ba651d52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835979"
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
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Высокий уровень доступности LUW IBM Db2 на виртуальных машинах Azure в SUSE Linux Enterprise Server с помощью Pacemaker

IBM Db2 LUW (Linux, Unix и Windows) в [конфигурации HADR](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) состоят из одного узла, где запущен экземпляр базы данных-источника и хотя бы один узел, где запущен экземпляр базы данных-получателя. Изменения в экземпляре базы данных-источника реплицируются на экземпляр базы данных-получателя синхронно или асинхронно, зависимые от конфигурации. 

В этой статье описывается развертывание и настройка виртуальных машин, установка платформы кластера, установите и настройте IBM Db2 LUW в конфигурации HADR. Статье не объясняется, как установить и настроить IBM Db2 LUW при установке программного обеспечения HADR или SAP. Ссылки на руководства по установке SAP и IBM предоставляются по выполнению этих задач. Нацелено на части, которые используются в среду Azure. 

Поддерживаемые версии IBM Db2 10.5 и более поздних версий, как описано в Примечание SAP #[1928533].

Прочтите следующие примечания SAP и документацию, прежде чем приближается установки:

| Примечание к SAP | Описание |
| --- | --- |
| [1928533] | Приложения SAP в Azure: поддерживаемые продукты и типы виртуальных машин Azure |
| [2015553] | SAP в Microsoft Azure: необходимые компоненты для поддержки |
| [2178632] | Ключевые метрики мониторинга для SAP в Microsoft Azure |
| [2191498] | SAP на платформе Linux в Azure: расширенный мониторинг |
| [2243692] | Linux на виртуальной машине Microsoft Azure (IaaS): проблемы с лицензированием SAP |
| [1984787] |SUSE Linux Enterprise Server 12 Замечания по установке |
| [1999351] |Устранение неполадок, связанных с расширенным мониторингом Azure для SAP |
| [2233094] |DB6: Приложения SAP в Azure с помощью IBM Db2 для Linux, UNIX и Windows — Дополнительные сведения |
| [1612105] |DB6: Часто задаваемые вопросы о Db2 высокий уровень доступности, аварийное восстановление (HADR) |


| Документация | 
| --- |
| [Вики-САЙТ сообщества SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) имеет все необходимые примечания к SAP для Linux |
| [На виртуальных машинах планированию и внедрению SAP NETWEAVER на платформе Linux] [ planning-guide] руководство |
| [Развертывание виртуальных машин для SAP NETWEAVER на] [ deployment-guide] (Эта статья) |
| [Развертывание программного обеспечения SAP на платформе Linux для Azure виртуальные машины СУБД] [ dbms-guide] руководство |
| [Рабочих нагрузок SAP в Azure контрольный список планирования и развертывания][azr-sap-plancheck] |
| [рекомендации по SUSE Linux Enterprise Server for SAP Applications версии 12 с пакетом обновления 3 (SP3)][sles-for-sap-bp]: |
| [SUSE Linux Enterprise высокого уровня доступности расширения 12 SP3][sles-ha-guide] |
| [IBM Db2 Azure виртуальные машины по развертыванию СУБД для рабочих нагрузок SAP][dbms-db2] |
| [Аварийное восстановление IBM Db2 высокой доступности 11.1][db2-hadr-11.1] |
| [Аварийное восстановление IBM Db2 высокой доступности R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Обзор
Для обеспечения высокой доступности, LUW IBM Db2 с HADR установлен по крайней мере два виртуальных машинах Azure, которые будут развернуты в [группе доступности Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) или по горизонтали [зоны доступности Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). Рисунки ниже показано, Настройка двух виртуальных машин Azure сервера базы данных. Оба сервера базы данных виртуальных машин Azure, имеют свои собственные хранилище, подключенное и запущены и работают. В HADR один экземпляр базы данных в одной из виртуальных машин Azure имеет роль экземпляра сервера-источника. Все клиенты будут подключены к этой основной экземпляр. Все изменения в транзакции базы данных сохраняются локально в журнале транзакций Db2. Как записи журнала транзакций сохраняются локально, записи передаются по протоколу TCP/IP к экземпляру базы данных — на второй сервер базы данных, резервный сервер или резервный экземпляр. Резервный экземпляр обновляет локальную базу данных, накат переносятся транзакции записи журнала. Поэтому резервный сервер синхронизируется с основным сервером.

HADR является только функциональные возможности репликации. Она имеет не обнаружения сбоев и автоматического перенаправления или отработки отказа средств. Перенаправление или передачи, выделенной для должна инициироваться вручную администратором базы данных. Чтобы обеспечить автоматическое перенаправление и обнаружение сбоя, можно использовать кластеров Linux Pacemaker. Pacemaker осуществляет мониторинг серверов и экземпляров двух баз данных. Когда основной экземпляр сервера базы данных или аварийно завершает работу, инициирует Pacemaker **автоматического** перенаправление HADR, резервный сервер и также гарантирует, что виртуальный IP-адрес назначен новому основному серверу.

![Общие сведения о IBM Db2 высокой доступности](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Серверы приложений для подключения к базе данных-источнике SAP должны виртуальное имя узла и виртуальный IP-адрес. В случае отработки отказа серверов приложений SAP подключится к новый экземпляр базы данных-источника. В среде Azure [Azure Load Balancer](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) необходим для использования виртуального IP-адреса так, необходимые для HADR IBM Db2. 

Чтобы полностью понять, как LUW IBM Db2 с HADR и Pacemaker встраивается в конфигурации высокой доступности системы SAP, в следующем рисунке представлен обзор настройки высокодоступной системы SAP на основе базы данных IBM Db2. В этой статье рассматриваются только IBM Db2 и ссылки на другие статьи по настройке других компонентов системы SAP.

![IBM DB2 высокого уровня ДОСТУПНОСТИ полный обзор среды](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-steps-needed"></a>Общий обзор шагов, необходимых
Для развертывания конфигурации IBM Db2, необходимо рассмотреть следующие действия:

  + Планирование среды
  + Развертывание виртуальных машин
  + Обновить SUSE Linux и настроить файловые системы
  + Установка и настройка Pacemaker
  + Установка [NFS высокой доступности][nfs-ha]
  + Установка [ASCS/ERS на отдельный кластер][ascs-ha] 
  + Установка базы данных IBM Db2 с параметром распределенных и высокий уровень доступности (SWPM)
  + Установка/создать узел базы данных-получателя и экземпляром и настроить HADR
  + Убедитесь, что работает HADR
  + Применить конфигурацию Pacemaker для управления IBM Db2
  + Настройка Azure Load Balancer 
  + Установите основной + диалоговое окно серверов приложений
  + Проверка и адаптировать конфигурацию для серверов приложений SAP
  + Выполните отработку отказа / проверяет перенаправление



## <a name="planning-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Планирование инфраструктуры Azure для размещения LUW IBM Db2 с HADR

Выполните план перед выполнением развертывания. Он создает основу для развертывания конфигурации из Db2 с HADR в Azure. Ключевые элементы, которые должны быть частью планирования для LUW Db2 IMB (базы данных частью среды SAP).

| Раздел | Краткое описание |
| --- | --- |
| Определение групп ресурсов Azure | Группы ресурсов, где развертывание виртуальной Машины, виртуальной сети, подсистемы балансировки нагрузки Azure и другие ресурсы. Можно выбрать существующую или новую |
| Виртуальная сеть / определение подсети | Где виртуальных машин для IBM Db2 и Azure Load Balancer получаете для развертывания. Может быть существующую или новую |
| Размещение IBM Db2 LUW на виртуальных машинах | Размер виртуальной Машины, хранение, сеть, IP-адрес |
| Имя виртуального узла & виртуальный IP-адрес для базы данных IBM Db2| Виртуальный IP-адрес или имя узла, которое используется для подключения серверов приложений SAP. **DB-virt-hostname**, **db-virt-ip** |
| Azure ограждения | Azure ограждения или ограждения SBD (настоятельно рекомендуется). Метод, чтобы предотвратить удаление разделения вычислительных мощностей запрещен |
| SBD ВИРТУАЛЬНОЙ МАШИНЫ | Размер SBD виртуальной машины, хранилище, сеть |
| Балансировщик нагрузки Azure | Использование Basic или Standard (рекомендуется), порт для базы данных Db2 (мы рекомендуем использовать 62500) пробы **порт пробы** |
| Разрешение имени| Как разрешение имен работает в среде. Настоятельно рекомендуется использовать службу DNS. Можно использовать локальный файл hosts |
    
Дополнительные сведения об использовании Linux Pacemaker в Azure можно найти в следующих статьях:

- [Настройка кластера Pacemaker в SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)



## <a name="deployment-on-suse-linux"></a>Развертывание в SUSE Linux

Агент ресурсов для IBM Db2 LUW включается в SUSE Linux Enterprise Server for SAP Applications. Для установки, описанные в этом документе является обязательным, используемая на сервер Linux SUSE для приложений SAP. Azure Marketplace содержит образ для SUSE Enterprise Server for SAP Applications 12, который можно использовать для развертывания новых виртуальных машин Azure. Имейте в виду моделей разных/служба поддержки, предлагаемых SUSE, через Azure галереи, при выборе образа виртуальной Машины в коллекции виртуальных Машин Azure. 

### <a name="hosts---dns-updates"></a>Размещает — обновление DNS
Создайте список всех имен узлов, включая имен виртуальных узлов и обновите DNS-серверы для включения правильный IP-адрес для разрешения имени узла. В случае DNS-сервер не существует, или вы не сможете обновлять и создавать записи DNS, необходимо использовать локальные узлы файлы отдельных виртуальных машин, которые участвуют в этом сценарии. В случае с помощью записи файлов узла, необходимо убедиться, что записи применяются ко всем виртуальным машинам в среде системы SAP. Однако рекомендуется использовать DNS-сервера, в идеале расширяются в Azure


### <a name="manual-deployment"></a>Развертывание вручную

Убедитесь, что выбранной ОС поддерживается IBM и SAP для IBM Db2 LUW. Список поддерживаемых версий ОС для виртуальных машин Azure и Db2 выпусков можно найти в примечании SAP [1928533]. Список версий операционной системы в отдельные версии Db2 доступна в матрице доступности продуктов SAP. Настоятельно рекомендуется не менее SLES 12 SP3 из-за улучшения производительности, связанные с Azure в этой или более поздней версии SUSE Linux.

1. Создайте или выберите группу ресурсов
2. Создание и выбор виртуальной сети и подсети
3. Создание группы доступности Azure и развертывание в зоне доступности
    + Доступность задать — значение max обновления доменов 2
4. Создание виртуальной машины 1.
    + Использовать SLES для SAP изображения в коллекции Azure
    + Выберите Azure группы доступности, созданной в шаге 3, или выберите зоне доступности
5.  Создание виртуальной машины 2.
    + Использовать SLES для SAP изображения в коллекции Azure
    + Выберите группу, созданный на шаге 3 доступности Azure. или выберите зону доступности - не той же зоне, что и в шаге 3.
6. Добавление дисков данных к виртуальным машинам — проверьте рекомендации настройки файловой системы в этой статье [IBM Db2 Azure виртуальные машины по развертыванию СУБД для рабочих нагрузок SAP][dbms-db2]

## <a name="create-the-pacemaker-cluster"></a>Создание кластера Pacemaker
    
Выполните действия, описанные в [настройка кластера Pacemaker в SUSE Linux Enterprise Server в Azure][sles-pacemaker] создать базовый кластер Pacemaker для этого сервера IBM Db2. 

## <a name="install-ibm-db2-luw-and-sap-environment"></a>Установка IBM Db2 LUW и среду SAP

Перед началом установки SAP среду, основанную на IBM Db2 LUW, просмотрите (ссылками в начале этой статьи):

+ Документация по Azure
+ Документации по SAP
+ Документации по IBM

Проверьте manual(s) установки из SAP по установке приложений на основе NetWeaver на IBM Db2 LUW.

Можно найти руководства по использованию портала SAP помогают [Finder руководство по установке SAP][sap-instfind]

Вы можете фильтровать поиск для сокращения количества руководств, доступных с помощью настройки фильтров:

+ Я хочу: «Установить новую систему»
+ Моя база данных: «IBM Db2 для Linux, Unix и Windows»
+ Дополнительных фильтров для версий, конфигурация стека или операционной системы SAP Netweaver.

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Установка указания по настройке LUW IBM Db2 с HADR

Настройка основного экземпляра базы данных IBM Db2 LUW:

- Использовать высокий уровень доступности или распределенные параметр
- Установите экземпляр SAP ASCS/ERS и база данных
- Создать резервную копию только что установленной базы данных


> [!IMPORTANT] 
> Запишите «порт связи базы данных» во время установки. Он должен быть один и тот же номер порта для обоих экземпляров базы данных

Для настройки базы данных на резервный сервер, с помощью процедуры копирования однородной системы SAP, выполните следующие действия:

  - Использовать системный параметр копирования — целевой экземпляр базы данных - распределенных - систем.
  - Как метод копирования выберите копирование однородной системы таким образом, можно использовать резервное копирование, восстановление резервной копии на резервный сервер/экземпляр
  - Дойдя до выхода шаг, чтобы восстановить базу данных для копирования однородные системы, закройте программу. Восстановите базу данных из резервной копии основной узел. Уже были выполнены все этапы для последующей установки на сервере базы данных-источника
- Настройка HADR для IBM Db2

> [!NOTE]
> Установка и настройка Azure и Pacemaker. Во время процесса установки через SAP Software Provisioning Manager есть явные вопрос с обеспечением высокой доступности IBM Db2 LUW:
>+ Не устанавливайте pureScale IBM Db2
>+ Не устанавливайте флажок «установить IBM Tivoli системы автоматизации для Multiplatforms
>+ Не устанавливайте флажок «Создать файлы конфигурации кластера»

> [!NOTE]
>При использовании устройств SBD для Linux Pacemaker, задайте параметры Db2 HADR
>+ Длительность периода HADR однорангового узла (в секундах) (HADR_PEER_WINDOW) = 300  
>+ Значение времени ожидания HADR (HADR_TIMEOUT) = 60

> [!NOTE]
>С помощью агента ограждения Azure Pacemaker:
>+ Длительность периода HADR однорангового узла (в секундах) (HADR_PEER_WINDOW) = 900  
>+ Значение времени ожидания HADR (HADR_TIMEOUT) = 60

Параметры, рекомендуется использовать в зависимости от первоначального тестирования отработки отказа и перенаправление. Это обязательный параметр, чтобы проверить правильное функционирование перенаправление и отработки отказа с помощью этих параметров. Так как отдельные конфигурации могут отличаться, эти параметры могут потребовать коррекции. 

> [!IMPORTANT]
> Относящиеся к IBM Db2 в конфигурации HADR с Обычный запуск - экземпляр базы данных получателя и резервного должен быть запущен и работает, прежде чем вы сможете запустить экземпляр базы данных-источника.

Для демонстрационных целей и процедурам, описанным в этом документе, с помощью базы данных идентификатор безопасности — **PTR**.

##### <a name="ibm-db2-hadr-check"></a>Проверка HADR IBM Db2
После настройки HADR и состояние однорангового узла и ПОДКЛЮЧЕНО на узлах основного и резервного.

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

При использовании Pacemaker для автоматической отработки отказа в случае сбоя узла, необходимо соответствующим образом настроить экземпляры Db2 и Pacemaker. В этом разделе описываются этот тип конфигурации.

Следующие элементы с префиксами::

- **[A]**  — применяется ко всем узлам
- **[1]**  — применяется только к узлу 1 
- **[2]**  — применяется только к узлу 2.

<!-- Yast is a fixed term in Linux and not a spelling error -->

**[A]**  Необходимые условия для Pacemaker конфигурации:
1. Завершите работу обоих сервера базы данных с помощью db2 пользователя\<sid > с db2stop
2. Среда изменения оболочки для db2\<sid > пользователю «/ bin/ksh» - рекомендуется использовать средство Yast 


### <a name="pacemaker-configuration"></a>Pacemaker конфигурация:

**[1]**  Pacemaker конфигурации IBM Db2 HADR
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Ресурсы создать IBM Db2
<pre><code># Replace **bold strings** with your instance name db2sid, database SID and virtual IP address/Azure Load Balancer

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virutal IP - same as Azure Load Balancer IP
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

**[1]**  Ресурсов запустите IBM Db2 — поместить Pacemaker из режима обслуживания
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Убедитесь, что состояние кластера — ОК и что запущены все ресурсы. Не важно, на каком узле выполняются ресурсы.
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
> Управление Pacemaker, необходимо сделать кластеризованный экземпляр Db2 с помощью средств Pacemaker. С помощью команд db2 (например, db2stop) будет определяться с помощью Pacemaker как сбой ресурса. В случае обслуживания можно поместить узлов или ресурсы в режим обслуживания и Pacemaker приостановит ресурсы мониторинга, и можно использовать обычный db2 администрирования команды.


### <a name="configure-azure-load-balancer"></a>Настройка Azure Load Balancer
Рекомендуется использовать [Azure SKU Standard подсистемы балансировки нагрузки](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

1. Настройка балансировщика нагрузки Azure (с помощью портала Azure). Сначала создайте пула IP-адресов для интерфейсной части.

   1. На портале Azure, подсистему балансировки нагрузки Azure, выберите **внешних IP-адресов**и выберите **добавить**.
   2. Введите имя нового пула IP-адрес внешнего интерфейса (например, **соединителя Db2**).
   3. Задайте **назначения** для **статический** и введите IP-адрес **виртуальных IP-** в начале.
   4. Нажмите кнопку **ОК**.
   5. Когда пул интерфейсных IP-адресов будет создан, запишите его IP-адрес.

2. Следующим шагом является создание пула серверной части:

   1. На портале Azure, подсистему балансировки нагрузки Azure, выберите **серверные пулы**и выберите **добавить**.
   2. Введите имя нового пула серверной части (например, **Db2-backend**).
   3. Щелкните **Добавить виртуальную машину**.
   4. Выберите набор и виртуальных машин доступности, котором размещена база данных IBM Db2, созданный на шаге 3.
   5. Выберите виртуальные машины кластера IBM Db2.
   6. Нажмите кнопку **ОК**.

3. Третий шаг — создание пробы работоспособности:

   1. На портале Azure, подсистему балансировки нагрузки Azure, выберите **пробы работоспособности**и выберите **добавить**.
   2. Введите имя новой пробы работоспособности (например, **Db2-hp**).
   3. Выберите **TCP** как протокол и порт **62500**. Сохраните значение "5" для параметра **Интервал** и значение "2" для параметра **Порог состояния неработоспособности**.
   4. Нажмите кнопку **ОК**.

4. Создайте правила балансировки нагрузки:

   1. На портале Azure, откройте Azure Load Balancer, выберите **правила балансировки нагрузки**и выберите **добавить**.
   2. Введите имя нового правила балансировки нагрузки (например, **Db2 SID**).
   3. Выберите интерфейсный IP-адрес, внутренний пул и пробу работоспособности, который был создан ранее (например, **Db2-frontend**).
   4. Сохранить **протокола** присвоено **TCP**и введите порт *порта взаимодействия с базой данных*.
   5. Увеличьте **время ожидания** до 30 минут.
   6. Не забудьте **включить плавающий IP-адрес**.
   7. Нажмите кнопку **ОК**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Вносить изменения в профили SAP будет использоваться виртуальный IP-адрес для подключения
Уровень приложений SAP должен использовать виртуальный IP-адрес, определенный и настроен для Azure Load Balancer для подключения к основной экземпляр конфигурации HADR. Требуются следующие изменения.

/sapmnt/\<SID >/профилей/по умолчанию. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Установка основного и диалогового приложения серверов

При первичной установке и диалоговое окно приложения серверов от конфигурации Db2 HADR, следует использовать виртуальное имя узла, которое вы выбрали для конфигурации. 

В случае, если вы выполнили установку перед созданием конфигурации Db2 HADR, необходимо внести изменения, как описано в предыдущем абзаце и следующим образом для стеков SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Проверьте стек ABAP с Java или Java, систем URL-адреса JDBC

Используйте средство конфигурации J2EE для проверки или обновить URL-адреса JDBC. Средство настройки J2EE — графическое средство, поэтому необходимо **X server** установлен:
 
1. Войдите на основной сервер приложений J2EE экземпляра и выполните:
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
2. В левом окне выберите хранилища безопасности.
2. В правой области выберите ключаjdbc/пул/<SAPSID>"/ URL".
2. Измените имя узла в URL-адреса JDBC на имя виртуального узла <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
5. Нажмите кнопку Добавить.
5. Чтобы сохранить изменения, щелкните значок дискеты в левом верхнем углу.
5. Закройте средство настройки.
5. Перезапустите экземпляр Java.

## <a name="configuration-of-log-archiving-for-hadr-setup"></a>Конфигурация архивации журнала для программы установки HADR
Чтобы настроить Db2 журнала архивирования для программы установки HADR, мы рекомендуем настроить основной и резервной базы данных, чтобы иметь возможность получения автоматического ведения журнала из всех расположений архив журнала. Основной и резервной базы данных должно быть невозможным получение архивных файлов журнала из всех расположений архив журнала, с каким либо из базы данных экземпляров может архивировать файлы журналов. 

Архивации журнала выполняется только в основной базе данных. При изменении роли HADR серверов баз данных, или если происходит сбой, несет ответственность за архивации журналов новой базы данных-источника. Если вы настроили расположения архива различных журналов, журналов могут архивироваться дважды, и в случае локальный или удаленный захват, может потребоваться вручную скопировать архивированные журналы из старого сервера-источника в активный журнал расположение нового сервера-источника.

Рекомендуется выполнить настройку общей папке NFS, которую записываются журналы с обоих узлов. NFS должен обладать высокой доступностью. 

Можно использовать существующие высокой доступности NFS, используемый для транспортов, каталог профиля. Чтение.

- [Обеспечение высокого уровня доступности NFS на виртуальных машинах Azure в SUSE Linux Enterprise Server][nfs-ha] 
- [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в SUSE Linux Enterprise Server с файлами NetApp Azure для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) по использованию [NetApp службы файлов Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) для создания общих ресурсов NFS


## <a name="test-the-cluster-setup"></a>Проверка настройки кластера

В этом разделе описывается, как вы можете проверить конфигурацию Db2 HADR. **Для каждого теста предполагается, что вы вошли качестве привилегированного пользователя** и IBM Db2 основной работает под управлением **azibmdb01** виртуальной машины.

Здесь подробно описаны начальное состояние для всех тестовых случаев: (crm_mon - r или crm status)

- **состояние CRM** -это состояние Pacemaker моментального снимка во время выполнения 
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

Исходное состояние в системе SAP описан в DBACOCKPIT для транзакции--> конфигурации--> Общие сведения, такие как:

![DBACockpit - Pre миграции](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Перенаправление теста IBM Db2.


> [!IMPORTANT] 
> Перед началом теста убедитесь, что Pacemaker не поддерживает все завершившиеся сбоем действия (crm status) и нет никаких ограничений расположение (leftovers теста миграции) и работоспособности синхронизации IBM Db2 HADR. Проверка с помощью db2 пользователя\<ИД безопасности > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Миграция узла при выполнении основной базы данных Db2, выполнив следующую команду:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

По окончании миграции вывод статуса crm выглядит как:
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

Исходное состояние в системе SAP описан в DBACOCKPIT для транзакции--> конфигурации--> Общие сведения, такие как: ![DBACockpit - после миграции](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Миграции ресурсов с «перенос ресурсов crm» устанавливаются ограничения расположения. Следует удалить ограничения расположения. Если ограничения расположения, не удаляются, ресурс нельзя восстановить размещение, или могут возникнуть нежелательные takeovers. 

Перенос ресурсов к **azibmdb01** и снимите ограничения расположения
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>


- < res_name > перенос ресурсов CRM <host> — устанавливаются ограничения расположения и может вызвать проблемы с перенаправление
- Очистить CRM ресурсов < res_name > - очищает ограничения расположения
- Очистка ресурсов CRM < res_name > — удаляет все ошибки ресурса


### <a name="test-the-fencing-agent"></a>Проверить конфигурацию агента ограждения

В этом случае мы тестируем ограждения SBD, рекомендуется для использования с SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Узел кластера **azibmdb01** необходимо перезагрузить компьютер. IBM Db2 первичной роли HADR планируется переместить **azibmdb02**. Когда **azibmdb01** оперативный, Db2, экземпляра планируется переместить в роли экземпляра базы данных-получателя. 

В случае службу Pacemaker на не начнется автоматически после перезагрузки первый основной не забудьте запустить его вручную с помощью:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Тестирование вручную перенаправление

Вы можете проверить перенаправление вручную, остановив службу Pacemaker на **azibmdb01** узла:
<pre><code>service pacemaker stop</code></pre>

status on **azibmdb02**
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

После отработки отказа, вы можете снова запустить службу на **azibmdb01**.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-db2-process-on-the-node-running-the-hadr-primary-database"></a>Завершить процесс Db2 на узле под управлением базы данных-источника HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

Экземпляр Db2 это ведет к сбою и Pacemaker сообщает следующие состояния:

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

Pacemaker будет перезапустить экземпляр базы данных-источника Db2 на одном узле или отработку отказа на узле под управлением экземпляры базы данных-получателя, а сообщение об ошибке.

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


### <a name="kill-db2-process-on-node-that-runs-the-secondary-database-instance"></a>Завершить процесс Db2 на узле, где запущен экземпляр базы данных-получателя

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

Get экземпляр Db2 перезагрузить вторичной роли, которую ей назначено перед

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



### <a name="stop-db-via-db2stop-force-on-node-running-the-hadr-primary-database-instance"></a>Остановить DB через force db2stop на узле, где работает экземпляр базы данных-источника HADR

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


### <a name="crash-vm-with-restart-on-node-running-the-hadr-primary-database-instance"></a>Завершить работу виртуальной Машины, перезапуск на узле выполняется экземпляр базы данных-источника HADR

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



### <a name="crash-the-vm-running-the-hadr-primary-database-instance-with-halt"></a>Сбой виртуальной Машины, где работает экземпляр базы данных-источника HADR с «halt»

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

В таком случае Pacemaker определит, что узел, где работает экземпляр базы данных-источника не отвечает.

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

следующим шагом является проверка **разделения вычислительных мощностей** ситуации. После этого узел находится в том, что узел, который выполнялся последний экземпляр базы данных-источника, не работает, отработки отказа ресурсов будет выполнен
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


В случае «остановка» на узел, неисправного узла должна быть перезапущена с помощью средств управления Azure (портал, оболочку, необходимую...). Неисправный узел будет запустить экземпляр, Db2 для вторичной роли, после подключения к сети.

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
Обратитесь к этой документации:

- [Архитектура высокого уровня доступности и сценарии для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Настройка кластера Pacemaker в SUSE Linux введите
- pri
- SE сервер в Azure])https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

