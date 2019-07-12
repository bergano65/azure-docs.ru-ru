---
title: Настройка IBM Db2 HADR на виртуальных машинах Azure (ВМ) на RHEL | Документация Майкрософт
description: Обеспечение высокого уровня доступности IBM Db2 LUW на виртуальных машинах (ВМ) RHEL.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/10/2019
ms.author: juergent
ms.openlocfilehash: b26a66eaee3a107c37d64541ec6b832331a3e2c9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812133"
---
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2233094]: https://launchpad.support.sap.com/#/notes/2233094
[1612105]: https://launchpad.support.sap.com/#/notes/1612105
[2694118]: https://launchpad.support.sap.com/#/notes/2694118


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
[anf-rhel]:(https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Высокий уровень доступности LUW IBM Db2 на виртуальных машинах Azure в Red Hat Enterprise Linux Server

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
| [2002167] | Red Hat Enterprise Linux 7.x: установка и обновление |
| [2694118] | Дополнение HA Red Hat Enterprise Linux в Azure |
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
| [Общие сведения об этой надстройки высокого уровня доступности для Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Администрирование надстройки для обеспечения высокой доступности][rhel-ha-admin] |
| [Справочник по надстройке для обеспечения высокой доступности][rhel-ha-ref] |
| [Политики поддержки для кластеров высокой доступности RHEL — виртуальные машины Microsoft Azure как члены кластера][rhel-azr-supp]
| [Установка и настройка кластера высокой доступности Red Hat Enterprise Linux 7.4 (и более поздних версий) в Microsoft Azure][rhel-azr-inst]
| [IBM Db2 Azure виртуальные машины по развертыванию СУБД для рабочих нагрузок SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR 10.5][db2-hadr-10.5] |
| [Политика поддержки для RHEL высокой доступности кластеров: управление IBM Db2 для Linux, Unix и Windows в кластере][rhel-db2-supp]



## <a name="overview"></a>Обзор
Для обеспечения высокой доступности, LUW IBM Db2 с HADR установлен по крайней мере два виртуальных машинах Azure, которые будут развернуты в [группе доступности Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) или по горизонтали [зоны доступности Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Следующие графики отображения настройки виртуальных машин Azure сервера две базы данных. Оба сервера базы данных виртуальных машин Azure, имеют свои собственные хранилище, подключенное и запущены и работают. В HADR один экземпляр базы данных в одной из виртуальных машин Azure имеет роль экземпляра сервера-источника. Все клиенты будут подключены к основной реплике. Все изменения в транзакции базы данных сохраняются локально в журнале транзакций Db2. Как записи журнала транзакций сохраняются локально, записи передаются по протоколу TCP/IP к экземпляру базы данных — на второй сервер базы данных, резервный сервер или резервный экземпляр. Резервный экземпляр обновляет локальную базу данных, накат переносятся транзакции записи журнала. Таким образом резервный сервер синхронизируется с основным сервером.

HADR является только функциональные возможности репликации. Она имеет не обнаружения сбоев и автоматического перенаправления или отработки отказа средств. Перенаправление или передачи к резервному серверу должна инициироваться вручную администратором базы данных. Чтобы обеспечить автоматическое перенаправление и обнаружение сбоя, можно использовать функцию кластеризации Linux Pacemaker. Pacemaker отслеживает экземпляры сервера две базы данных. Когда экземпляр сервера базы данных-источника в случае сбоя инициирует Pacemaker *автоматического* перенаправление HADR, резервный сервер. Pacemaker также гарантирует, что виртуальный IP-адрес назначен новому основному серверу.

![Общие сведения о высокой доступности IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Для SAP серверов приложений соединиться с базой данных-источником, требуется имени виртуального узла и виртуальный IP-адрес. При отработке отказа серверы приложений SAP будет подключаться к новый экземпляр базы данных-источника. В среде Azure [балансировщика нагрузки Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) необходим для использования виртуального IP-адреса так, требуемом для HADR IBM Db2. 

Чтобы помочь вам лучше понять, как LUW IBM Db2 с HADR и Pacemaker встраивается в высокодоступной конфигурации системы SAP, ниже представлен обзор настройки высокодоступной системы SAP на основе базы данных IBM Db2. В этой статье рассматриваются только IBM Db2, но предоставляет ссылки на другие ресурсы, посвященные настройке других компонентов системы SAP.

![Обзор полную среду высокой доступности IBM DB2](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Общий обзор необходимых действий
Развертывания конфигурации IBM Db2, необходимо выполнить следующие действия:

  + Планирование среды.
  + Разверните виртуальные машины.
  + Обновите RHEL Linux и настройки файловых систем.
  + Установка и настройка Pacemaker.
  + Программа установки [glusterfs кластера][glusterfs] or [Azure NetApp Files][anf-rhel]
  + Установка [ASCS/ERS на отдельный кластер][ascs-ha-rhel].
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
| Azure ограждения | Метод, чтобы предотвратить удаление разделения вычислительных мощностей блокируется. |
| Azure Load Balancer | Использование Basic или Standard (рекомендуется), порт для базы данных Db2 (мы рекомендуем использовать 62500) пробы **порт пробы**. |
| Разрешение имен| Как разрешение имен работает в среде. Настоятельно рекомендуется использовать службу DNS. Можно использовать локальный файл hosts. |
    
Дополнительные сведения о Linux Pacemaker в Azure, см. в разделе [настройка кластера Pacemaker в Red Hat Enterprise Linux в Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Развертывание в Red Hat Enterprise Linux

Агент ресурсов для IBM Db2 LUW включается в Red Hat Enterprise Linux Server высокого уровня ДОСТУПНОСТИ дополнительный компонент. Для настройки, описанной в этом документе следует использовать Red Hat Enterprise Linux для SAP. Azure Marketplace содержит изображение для Red Hat Enterprise Linux 7.4 для SAP или более поздней версии, можно использовать для развертывания новых виртуальных машин Azure. Имейте в виду различных моделей поддержки или службы, предлагаемые Red Hat в Azure Marketplace при выборе образа виртуальной Машины в виртуальной Машине в Azure Marketplace.

### <a name="hosts-dns-updates"></a>Узлов: Обновления DNS
Создайте список всех имен узлов, включая имена виртуальных узлов и обновите DNS-серверы для включения правильный IP-адрес для разрешения имени узла. Если DNS-сервер не существует или не может обновлять и создавать записи DNS, необходимо использовать локальный узел файлы отдельных виртуальных машин, которые участвуют в этом сценарии. При использовании записей файлы узлов, убедитесь, что записи применяются ко всем виртуальным машинам в среде системы SAP. Тем не менее мы рекомендуем использовать DNS-сервера, в идеальном случае заходят в Azure


### <a name="manual-deployment"></a>Развертывание вручную

Убедитесь, что выбранной ОС поддерживается IBM и SAP для IBM Db2 LUW. Список поддерживаемых версий ОС для виртуальных машин Azure и Db2 выпусков можно найти в примечании SAP [1928533]. Список версий операционной системы, отдельным выпуском Db2 доступна в матрице доступности продуктов SAP. Мы настоятельно рекомендуем Red Hat Enterprise Linux 7.4 для SAP не менее из-за повышение производительности, связанных с Azure в этой или более поздней версии Red Hat Enterprise Linux.

1. Создайте или выберите группу ресурсов.
1. Создайте или выберите виртуальную сеть и подсеть.
1. Создание группы доступности Azure или развернуть зону доступности.
    + Для группы доступности задайте домены обновления максимальное значение 2.
1. Создание виртуальной машины 1.
    + Используйте Red Hat Enterprise Linux для SAP образа в Azure Marketplace.
    + Выберите группу доступности Azure, созданную на шаге 3, или выберите зону доступности.
1.  Создание виртуальной машины 2.
    + Используйте Red Hat Enterprise Linux для SAP образа в Azure Marketplace.
    + Выберите группу доступности Azure в созданную на шаге 3, или выберите зону доступности (не той же зоне как в шаге 3).
1. Добавление дисков данных к виртуальным машинам и проверьте рекомендации настройки системы файл в этой статье [IBM Db2 Azure виртуальные машины по развертыванию СУБД для рабочей нагрузки SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Создание кластера Pacemaker
    
Чтобы создать базовый кластер Pacemaker для этого сервера IBM Db2, см. в разделе [настройка кластера Pacemaker в Red Hat Enterprise Linux в Azure][rhel-pcs-azr]. 

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

#### <a name="red-hat-firewall-rules"></a>Правила брандмауэра Red Hat
Red Hat Enterprise Linux имеет брандмауэр включен по умолчанию. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Установка указания по настройке LUW IBM Db2 с HADR

Чтобы настроить основной экземпляр базы данных IBM Db2 LUW:

- Используйте высокого уровня доступности или распределенные параметр.
- Установите экземпляр SAP ASCS/ERS и база данных.
- Создайте резервную копию только что установленной базы данных.

> [!IMPORTANT] 
> Запишите «взаимодействие с базой данных порт», задаются во время установки. Он должен быть один и тот же номер порта для обоих экземпляров базы данных.
>![Определение порта SAP SWPM](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Параметры IBM Db2 HADR для Azure

   Если вы используете агент ограждения Azure Pacemaker, задайте следующие параметры:

   - Длительность периода HADR однорангового узла (в секундах) (HADR_PEER_WINDOW) = 240  
   - Значение времени ожидания HADR (HADR_TIMEOUT) = 45

Мы рекомендуем использовать описанные выше параметры, в зависимости от первоначального тестирования отработки отказа и перенаправление. Это обязательный параметр, проверить правильность работы всех функций отработки отказа и перенаправление с помощью этих параметров. Поскольку отдельные конфигурации могут различаться, параметры требуют корректировки. 

> [!NOTE]
> Относящиеся к IBM Db2 с помощью конфигурации HADR с Обычный запуск: Базы данных-получателя или резервный экземпляр должен быть запущен и работает, перед запуском экземпляра базы данных-источника.

   
> [!NOTE]
> Для установки и конфигурации, относящиеся к Azure и Pacemaker. Во время процесса установки через SAP Software Provisioning Manager есть явная вопрос высокий уровень доступности для IBM Db2 LUW:
>+ Не устанавливайте **pureScale IBM Db2**.
>+ Не устанавливайте **установить IBM Tivoli системы автоматизации для Multiplatforms**.
>+ Не устанавливайте **создать файлы конфигурации кластера**.
>![SAP SWPM - параметры DB2 высокого уровня ДОСТУПНОСТИ](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Чтобы настроить базу данных на резервный сервер с помощью процедуры копирование однородной системы SAP, выполните следующие действия:

1. Выберите **копирование системы** параметр > **целевых систем** > **распределенных** > **экземпляр базы данных**.
1. Как метод копирования, выберите **однородные системы** таким образом, можно использовать резервное копирование для восстановления резервной копии на резервным экземпляром сервера.
1. Дойдя до выхода шаг, чтобы восстановить базу данных для копирования однородные системы, закройте программу. Восстановите базу данных из резервной копии основной узел. На сервере базы данных-источника уже выполнены все этапы для последующей установки.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Red Hat правила брандмауэра для DB2 HADR
Добавьте правила брандмауэра, разрешающее трафик к DB2, а также между DB2 для HADR для работы:
+ Порт связи базы данных. При использовании секций также добавьте эти порты.
+ HADR порт (значение параметра DB2 HADR_LOCAL_SVC)
+ Порт пробы Azure
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>Проверка IBM Db2 HADR
Для демонстрационных целей и процедуры, описанные в этой статье, с помощью базы данных идентификатор безопасности — **ID2**.

После настройки HADR и находится в состоянии однорангового узла и ПОДКЛЮЧЕНО на основной и резервный узлах, выполните следующую проверку:

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

При использовании Pacemaker для автоматической отработки отказа в случае сбоя узла, необходимо соответствующим образом настроить экземпляры Db2 и Pacemaker. В этом разделе описывается конфигурация данного типа.

Следующие элементы с префиксами::

- **[A]** : Применяется ко всем узлам
- **[1]** : Применяется только к узлу 1 
- **[2]** : Применяется только к узлу 2

**[A]**  Необходимые условия для Pacemaker конфигурации:
1. Завершите работу обоих серверов баз данных с db2 пользователя\<sid > с db2stop.
1. Измените среду оболочки для db2\<sid > пользователю */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Pacemaker конфигурации

**[1]**  Конфигурации HADR конкретных Pacemaker IBM Db2:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]**  Ресурсы создать IBM Db2:
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

**[1]**  Ресурсы запустить IBM Db2:
* Поместите Pacemaker из режима обслуживания.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]**  Убедитесь, что кластер находится в работоспособном состоянии и что запущены все ресурсы. Не важно какой из узлов на ресурсы.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [az-idb01 az-idb02]

Полный список ресурсов:

 rsc_st_azure (stonith:fence_azure_arm):      Набор, ведущий/ведомый работы az-idb01: Мастеры Db2_HADR_ID2-master [Db2_HADR_ID2]: подчиненные модули [az-idb01]: [az-idb02] группа ресурсов: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2):       К работе az idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb):      Начато az-idb01

Состояние управляющей программы: corosync: pacemaker активный "или" отключено: pcsd активный "или" отключено: активный или не включен
</pre>

> [!IMPORTANT]
> Необходимо управлять Pacemaker кластеризованного экземпляра Db2 с помощью средств Pacemaker. При использовании команд db2, например db2stop Pacemaker обнаруживает это действие как сбой ресурса. При выполнении обслуживания, можно поместить узлов или ресурсы в режим обслуживания. Pacemaker приостанавливает ресурсы мониторинга, а затем можно использовать обычный db2 администрирования команды.


### <a name="configure-azure-load-balancer"></a>Настройка Azure Load Balancer
Чтобы настроить балансировщик нагрузки Azure, мы рекомендуем использовать [Azure SKU Standard подсистемы балансировки нагрузки](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) и выполните следующую процедуру.

1. Создайте интерфейсный пул IP-адресов:

   1\. На портале Azure, подсистему балансировки нагрузки Azure, выберите **внешних IP-адресов**, а затем выберите **добавить**.

   2\. Введите имя нового пула IP-адрес внешнего интерфейса (например, **соединителя Db2**).

   В. Задайте **назначения** для **статический**и введите IP-адрес **виртуальных IP-** в начале.

   Г. Нажмите кнопку **ОК**.

   Д. Когда пул интерфейсных IP-адресов будет создан, запишите его IP-адрес.

1. Создание пула серверной части:

   1\. На портале Azure, подсистему балансировки нагрузки Azure, выберите **серверные пулы**, а затем выберите **добавить**.

   2\. Введите имя нового пула серверной части (например, **Db2-backend**).

   В. Щелкните **Добавить виртуальную машину**.

   Г. Выберите группу доступности или виртуальные машины, на котором размещена база данных IBM Db2, созданные на предыдущем шаге.

   Д. Выберите виртуальные машины кластера IBM Db2.

   f. Нажмите кнопку **ОК**.

1. Создайте пробу работоспособности:

   1\. На портале Azure, подсистему балансировки нагрузки Azure, выберите **пробы работоспособности**и выберите **добавить**.

   2\. Введите имя новой пробы работоспособности (например, **Db2-hp**).

   В. Выберите **TCP** как протокол и порт **62500**. Сохранить **интервал** значение **5**и сохранить **порог состояния неработоспособности** значение **2**.

   Г. Нажмите кнопку **ОК**.

1. Создайте правила балансировки нагрузки:

   1\. На портале Azure, подсистему балансировки нагрузки Azure, выберите **правила балансировки нагрузки**, а затем выберите **добавить**.

   2\. Введите имя нового правила балансировки нагрузки (например, **Db2 SID**).

   В. Выберите интерфейсный IP-адрес, внутренний пул и пробу работоспособности, который был создан ранее (например, **Db2-frontend**).

   Г. Сохранить **протокола** присвоено **TCP**и введите порт *порта взаимодействия с базой данных*.

   Д. Увеличьте **время ожидания** до 30 минут.

   f. Не забудьте **включить плавающий IP-адрес**.

   ж. Нажмите кнопку **ОК**.

**[A]**  Добавить правило брандмауэра для порта пробы:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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
 
1. Войдите на основной сервер приложений J2EE экземпляра и выполните:
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. В левом окне выберите **хранилища безопасности**.
1. В правой области выберите ключа jdbc/пул / \<SAPSID>"/ URL".
1. Измените имя узла в URL-адреса JDBC на имя виртуального узла.
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. Выберите **добавить**.
1. Чтобы сохранить изменения, щелкните значок дискеты в левом верхнем углу.
1. Закройте средство настройки.
1. Перезапустите экземпляр Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Настройка архивации журнала для программы установки HADR
Чтобы настроить Db2 журнала архивирования для программы установки HADR, мы рекомендуем настроить основной и резервной базы данных, чтобы иметь возможность получения автоматического ведения журнала из всех расположений архив журнала. Основной и резервной базы данных должен иметь возможность получения архивные файлы журнала из всех расположений архив журнала, с каким либо из базы данных экземпляров может архивировать файлы журналов. 

Архивации журнала осуществляется только базы данных-источника. При изменении роли HADR серверов баз данных, или если происходит сбой, несет ответственность за архивации журналов новой базы данных-источника. Если вы настроили несколько расположения архива журналов, журналов может архивироваться дважды. В случае локальный или удаленный захват может потребоваться вручную скопировать архивированные журналы из старого сервера-источника в активный журнал расположение нового сервера-источника.

Рекомендуется настроить общую папку NFS или файловая система Gluster, в которую записываются журналы с обоих узлов. Для общей папки NFS или файловая система Gluster должно быть высокодоступной. 

Можно использовать существующие общие папки NFS высокой доступности или файловая система Gluster для транспортов или каталог профиля. Дополнительные сведения можно найти в разделе

- [GlusterFS на виртуальных машинах Azure с Red Hat Enterprise Linux для SAP NetWeaver][glusterfs] 
- [Высокий уровень доступности для SAP NetWeaver на виртуальных машинах Azure в Red Hat Enterprise Linux с файлами NetApp Azure для приложений SAP][anf-rhel]
- [Служба файлов Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (для создания общих ресурсов NFS)

## <a name="test-the-cluster-setup"></a>Проверка настройки кластера

В этом разделе описывается, как вы можете проверить конфигурацию Db2 HADR. Для каждого теста предполагается основной IBM Db2 выполняется на *az idb01* виртуальной машины. Необходимо использовать пользователя с привилегиями sudo или корневой (не рекомендуется).

Здесь подробно описаны начальное состояние для всех тестовых случаев: (crm_mon - r или ПК status)

- **состояние ПК** представляет собой моментальный снимок состояния Pacemaker во время выполнения 
- **crm_mon - r** непрерывной выводится состояние Pacemaker

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

Исходное состояние в системе SAP описан в DBACOCKPIT для транзакции > настройки > Общие сведения, как показано на следующем рисунке:

![DBACockpit - Pre миграции](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Перенаправление теста IBM Db2


> [!IMPORTANT] 
> Перед началом теста убедитесь, что:
> * Pacemaker нет все завершившиеся сбоем действия (статус ПК).
> * Нет никаких ограничений расположение (leftovers теста миграции)
> * Работоспособности синхронизации IBM Db2 HADR. Проверка с помощью db2 пользователя\<ИД безопасности > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Миграция узла, на котором работает база данных-источник Db2, выполнив следующую команду:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

После завершения миграции вывод статуса crm выглядит как:
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

Исходное состояние в системе SAP описан в DBACOCKPIT для транзакции > настройки > Общие сведения, как показано на следующем рисунке:

![DBACockpit - после миграции](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

При переносе ресурсов с «перемещение ресурсов ПК» устанавливаются ограничения расположения. В этом случае ограничения расположения препятствуют запущенного экземпляра IBM Db2 на az idb01. Если ограничения расположения, не удаляются, ресурс нельзя восстановить размещение.

Удаление ограничения расположения и резервный узел будет запущен на az idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
И состояние кластера изменяется на:
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

![Ограничить удаленные расположения DBACockpit-](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Перенос ресурса к *az idb01* и снимите ограничения расположения
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **Перемещение ресурсов ПК \<res_name > <host>:** Устанавливаются ограничения расположения и может вызвать проблемы с перенаправление
- **Очистка ресурсов ПК \<res_name >** : Удаляет ограничения расположения
- **Очистка ресурсов ПК \<res_name >** : Удаляет все ошибки ресурса

### <a name="test-a-manual-takeover"></a>Тестирование вручную перенаправление

Вы можете проверить перенаправление вручную, остановив службу Pacemaker на *az idb01* узла:
<pre><code>systemctl stop pacemaker</code></pre>

status on *az-ibdb02*
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

После отработки отказа, вы можете снова запустить службу на *az idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Завершить процесс Db2 на узле, на котором работает база данных-источник HADR

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

Экземпляр Db2 это ведет к сбою и Pacemaker переместит главного узла и отчетов, следующие состояния:

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

Pacemaker перезапустит экземпляр базы данных-источника Db2 на одном узле, или он выполнит отработку отказа на узел, на котором выполняется экземпляр базы данных-получателя и возвращается ошибка.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Завершить процесс Db2 на узле, где запущен экземпляр базы данных-получателя

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

Узел получает не удалось выполнить into заявленным и сообщение об ошибке
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

Во вторичной роли, которую ей назначено перед перезапускается экземпляр Db2.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Остановить DB через force db2stop на узле, где запущен экземпляр базы данных-источника HADR

Как пользователь db2\<sid > выполните команду db2stop force:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Обнаружен сбой:

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

Экземпляр базы данных-получателя Db2 HADR распространяется в основную роль.
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


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Аварийное завершение работы виртуальной Машины, которое будет выполняться экземпляр базы данных-источника HADR с «halt»

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

В таком случае Pacemaker определит, что узел, на котором выполняется экземпляр базы данных-источника не отвечает.

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

следующим шагом является проверка *разделения вычислительных мощностей* ситуации. После этого узла определил, что узел последнего запуска экземпляра базы данных-источника не работает, выполняется отработка отказа ресурсов.

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


В случае авария ядра неисправного узла будет restared агента ограждения. После сбоя узла будет восстановлена, необходимо запустить кластер pacemaker с
<pre><code>sudo pcs cluster start</code></pre> он запускает экземпляр Db2 в роль вторичной.

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

## <a name="next-steps"></a>Следующие шаги
- [Архитектура высокого уровня доступности и сценарии для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Настройка кластера Pacemaker в Red Hat Enterprise Linux в Azure][rhel-pcs-azr]
