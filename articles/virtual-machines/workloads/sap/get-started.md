---
title: Начало работы с SAP на Вымотра Azure (ru) Документы Майкрософт
description: Узнайте о решениях SAP, которые работают на виртуальных машинах (Виртуальных машинах) в Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/27/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4c708b6e872bfc54371aa38ee1472d27dcb00952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373049"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Используйте Azure для размещения и запуска сценариев рабочей нагрузки SAP

При использовании Microsoft Azure можно надежно запускать критически важные для миссии рабочие нагрузки и сценарии SAP на масштабируемой, совместимой и проверенной на предприятии платформе. Вы получаете масштабируемость, гибкость и экономию затрат Azure. Благодаря расширенному партнерству между microsoft и SAP вы можете запускать приложения SAP в сценариях разработки, тестирования и производства в Azure и полностью поддерживаться. От SAP NetWeaver до SAP S/4HANA, SAP BI на Linux и от Windows и от SAP HANA до S'L, мы покрыли вас.

Помимо размещения сценариев SAP NetWeaver с различными DBMS в Azure, можно размещать другие сценарии рабочей нагрузки SAP, такие как SAP BI на Azure. 

Уникальность Azure для SAP HANA – это предложение, которое отличает Azure. Для того, чтобы обеспечить размещение большего количества памяти и cPU, требующих ресурсов SAP сценариев, которые включают SAP HANA, Azure предлагает использование ориентированных на клиента голо-металлического оборудования. Используйте это решение для выполнения развертываний SAP HANA, для чего требуется до 24 ТБ (120-ТБ-масштаб) памяти для S/4HANA или другой рабочей нагрузки SAP HANA. 

Хостинг сценариев рабочей нагрузки SAP в Azure также может создавать требования интеграции идентификационных данных и единого входного. Эта ситуация может возникнуть при использовании Active Directory Azure (Azure AD) для подключения различных компонентов SAP и sAP программного обеспечения как службы (SaaS) или предложений платформы как службы (PaaS). Список таких сценариев интеграции и единого ввоза с объектами Azure AD и SAP описан и документирован в разделе «Интеграция идентификационных данных AAD SAP и единый ввод в систему».

## <a name="changes-to-the-sap-workload-section"></a>Изменения в разделе рабочей нагрузки SAP
Изменения в документах в разделе SAP на рабочей нагрузке Azure перечислены в конце этой статьи. Записи в журнале изменений хранятся около 180 дней.

## <a name="you-want-to-know"></a>Вы хотите знать,
Если у вас есть конкретные вопросы, мы укаем вас на конкретные документы или потоки в этом разделе стартовой страницы. Вы хотите знать:

- Какие подразделения Azure VMs и HANA Large Instance поддерживаются, для которых выпускается программное обеспечение SAP и какие версии операционной системы. Прочитайте документ [О том, что программное обеспечение SAP поддерживается для развертывания Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) для получения ответов и процесса поиска информации
- Какие сценарии развертывания SAP поддерживаются с помощью Azure VMs и HANA Large Instances. Информацию об поддерживаемых сценариях можно найти в документах:
    - [Нагрузка SAP на виртуальную машину Azure поддерживает сценарии](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Поддерживаемые сценарии для крупной инстанции HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- Какие службы Azure, типы ВМ Azure и службы хранения данных Azure доступны в различных регионах Azure, проверьте продукты сайта, [доступные по регионам](https://azure.microsoft.com/global-infrastructure/services/) 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA в Azure (крупные экземпляры)

Серия документов ведет вас через SAP HANA на Azure (Большие инстанции) или для краткости, HANA Large Instances. Для получения информации о HANA Большие инстанции начать с документа [Обзор и архитектура SAP HANA на Azure (Большие инстанции)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) и пройти через соответствующую документацию в разделе HANA Большой инстанции



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA на виртуальных машинах Azure
Этот раздел документации охватывает различные аспекты SAP HANA. В качестве предварительного условия вы должны ознакомиться с основными службами Azure, предоставляющие элементарные службы Azure IaaS. Итак, вам нужны знания о вычислениях, хранении и сети Azure. Многие из этих предметов обрабатываются в руководстве по [планированию Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)связанном с SAP NetWeaver. 

Для получения информации о HANA на Azure см.

- [Конфигурации инфраструктуры SAP HANA и работа с ней в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Обеспечение высокого уровня доступности для SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Высокий уровень доступности SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Руководство по резервному копированию для SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver развернут на виртуальных машинах Azure
В этом разделе перечислены документация по планированию и развертыванию для SAP NetWeaver и Business One на Azure. В документации основное внимание уделяется основам и использованию баз данных, не связанных с HANA, с рабочей нагрузкой SAP на Azure. Документы и статьи для высокой доступности также являются основой для высокой доступности HANA в Azure, таких как:

- [Руководство по планированию Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [SAP Business One на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Защитите многофункционта SAP NetWeaver, используя восстановление сайта](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Соединитель SAP LaMa для Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Для получения информации о базах данных, не относясь к HANA, в рамках рабочей нагрузки SAP на Azure см.

- [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver на виртуальных машинах Azure. Руководство по развертыванию СУБД SQL Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Рабочие нагрузки SAP на Виртуальных машинах Azure. Руководство по развертыванию СУБД Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Рабочие нагрузки SAP на Виртуальных машинах Azure. Руководство по развертыванию СУБД IBM DB2](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Рабочие нагрузки SAP на Виртуальных машинах Azure. Руководство по развертыванию СУБД SAP ASE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Развертывание SAP MaxDB, Live Cache и сервера содержимого на MMs Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Подробнее о базах данных SAP HANA на Azure читайте в разделе «SAP HANA на виртуальных машинах Azure».

Для получения информации о высокой доступности рабочей нагрузки SAP на Azure см.

- [Руководство по обеспечению высокого уровня доступности SAP NetWeaver на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

В этом документе указывается на различные другие архитектуры и сценарийные документы. В более поздних сценариях предоставляются ссылки на подробные технические документы, объясняющие развертывание и конфигурацию различных методов высокой доступности. Различные документы, которые показывают, как установить и настроить высокую доступность для sAP NetWeaver рабочей нагрузки охватывают Linux и Windows операционных систем.


Для получения информации об интеграции между службами Active Directory Azure (Azure AD) и SAP и единым ввозам см.:

- [Руководство по интеграции Azure Active Directory с SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с приложением SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. Интеграция Azure Active Directory с SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Ваша среда S/4HANA: Fiori Launchpad SAML одиночный знак с Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Информацию об интеграции служб Azure в компоненты SAP можно узнать:

- [Использование SAP HANA в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery и SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Использование соединителя SAP BW в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Возможность интеграции данных SAP HANA и Business Warehouse в Фабрике данных Azure](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Журнал изменений
- 03/27/2020: Изменение [высокой доступности для SAP NW на Azure VMs на SLES с помощью ПРИЛОЖЕНИй SAP для выравнивания](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) вариантов крепления файловой системы с NetApp TR-4746 (удалите опцию синхронизации)
- 03/26/2020: Изменение [высокой доступности для SAP NetWeaver на Azure VMs на SLES мульти-SID руководство,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) чтобы добавить ссылку на NetApp TR-4746
- 03/26/2020: Изменение [высокой доступности для SAP NetWeaver на Azure VMs на SLES для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Высокая доступность для SAP NetWeaver на Azure VMs на SLES с Azure NetApp Файлы для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Высокая доступность для NFS на Azure VMs на SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Высокая доступность для SAP NetWeaver на Azure VMs на SLES мульти-SID руководство](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid), [Высокая доступность для SAP NetWeaver на Azure VMs на RHEL для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) и [высокая доступность для SAP NetWeaver на Azure VMs на RHEL с Azure Файлы NetApp для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) для обновления диаграмм и уточнения инструкций по созданию резервного резерва azure Load Balancer
- 03/19/2020: Основные изменения документа [Быстрый запуск: Ручная установка одного экземпляра SAP HANA на Лазурном виртуальных машинах](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) для [установки SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 03/17/2020: Изменение настройки [Pacemaker на SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) для удаления настройки конфигурации SBD, которая больше не является необходимой
- 03/16/2020: Уточнение сценария сертификации столбцов в сертифицированной платформе SAP HANA IaaS в [программном обеспечении SAP поддерживается для развертывания Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 03/11/2020: Изменение [рабочей нагрузки SAP на виртуальном компьютере Azure поддерживает сценарии](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) для уточнения нескольких баз данных в поддержке экземпляров DBMS
- 03/11/2020: Изменение [в планировании и внедрении виртуальных машин Azure для SAP NetWeaver,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) объясняющих поколение 1 и поколение 2 VMs
- 03/10/2020: Изменение [конфигураций виртуальной машины SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) для уточнения реальных существующих пределов пропускной способности ANF
- 03/09/2020: Изменение [высокой доступности для SAP NetWeaver на Azure VMs на SUSE Linux Enterprise Server для sAP-приложений](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Высокая доступность для SAP NetWeaver на Azure VMs на сервере SUSE Linux Enterprise Server с Azure NetApp Файлы для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Высокая доступность для NFS на Azure VMs на SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Настройка Pacemaker на SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [Высокая доступность IBM Db2 LUW на Azure VMs на SUSE Linux Enterprise Server с Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), [Высокая доступность SAP HANA на Azure VMs на SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) и [высокая доступность для SAP NetWeaver на Azure VMs на sLES multi-SID руководство](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) по обновлению кластерных ресурсов с ресурсным агентом azure-lb 
- 03/05/2020: Изменения структуры и изменения содержимого для регионов Azure и виртуальных машин Azure в [планировании и реализации виртуальных машин Azure для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020: Изменение [высокой доступности для SAP NW на Azure VMs на SLES с помощью ANF для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) для изменения в более эффективную компоновку объема ANF
- 03/01/2020: Переработанное [руководство по резервному копированию для SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) для включения службы резервного копирования Azure. Уменьшенное и конденсированное содержимое резервного [копирования SAP HANA Azure на уровне файла](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) и удаление третьего документа, касаемого резервного копирования через снимок диска. Содержимое обрабатывается в руководстве по резервному копированию для SAP HANA на виртуальных машинах Azure 
- 02/27/2020: Изменение [высокой доступности для SAP NW на Azure VMs на SLES для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Высокая доступность для SAP NW на Azure VMs на SLES с ПОМОЩЬю ANF для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) и [высокая доступность для SAP NetWeaver на Azure VMs на SLES multi-SID руководство](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) по настройке "на отказе" параметр
- 02/26/2020: Изменение [конфигураций виртуальной машины SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) для уточнения выбора файловой системы для HANA на Azure
- 02/26/2020: Изменение [архитектуры высокой доступности и сценариев для SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) чтобы включить ссылку на HA для SAP NetWeaver на Azure VMs на руководстве RHEL с несколькими SID
- 02/26/2020: Изменение [высокой доступности для SAP NW на Azure VMs на SLES для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Высокая доступность для SAP NW на Azure VMs на SLES с ПОМОЩЬю ANF для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Az Высокая доступность для SAP NetWeaver на RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) и [Azure VMs с высокой доступностью для SAP NetWeaver на RHEL с azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) для удаления заявления о том, что кластер multi-SID ASCS/ERS не поддерживается
- 02/26/2020: Выпуск [высокой доступности для SAP NetWeaver на Azure VMs на руководстве RHEL multi-SID,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) чтобы добавить ссылку на руководство sUSE с мульти-SID кластера
- 02/25/2020: Изменение [архитектуры высокой доступности и сценариев для SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) чтобы добавить ссылки на новые статьи HA
- 02/25/2020: Изменение [высокой доступности IBM Db2 LUW на Azure VMs на SUSE Linux Enterprise Server с Pacemaker,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) чтобы указать на документ, который описывает доступ к публичной конечной точке с балансером Standard Azure Load
- 02/21/2020: Полный пересмотр статьи [SAP ASE Azure Виртуальные машины DBMS развертывания для sAP рабочей нагрузки](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 02/21/2020: Изменение [конфигурации виртуального устройства хранения sAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) для представления новой рекомендации в размере полосы для /hana/data и добавления настройки планировщика ввос/эксплуатации
- 02/21/2020: Изменения в документах HANA Large Instance для представления недавно сертифицированных СКУ S224 и S224m
- 02/21/2020: Изменение высокой [доступности Для SAP NetWeaver на RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) и Azure VMs высокой [доступности SAP NetWeaver на RHEL с](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) помощью файлов Azure NetApp для корректировки ограничений кластера для архитектуры репликации серверов Enqueue (ENSA2)
- 02/20/2020: Изменение [высокой доступности для SAP NetWeaver на Azure VMs на sLES multi-SID руководство,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) чтобы добавить ссылку на SUSE мульти-SID кластера руководство
- 02/13/2020: Изменения в [планировании и внедрении виртуальных машин Azure для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) для реализации ссылок на новые документы
- 02/13/2020: Добавлен новый документ [SAP рабочей нагрузки на Azure виртуальный компьютер поддерживается сценарий](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 02/13/2020: Добавлен новый [документ, который поддерживает программное обеспечение SAP для развертывания Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020: Изменение [высокой доступности IBM Db2 LUW на vMs Azure на Linux Server Red Hat Enterprise,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) чтобы указать на документ, описывающий доступ к общедоступной конечной точке с балансером Standard Azure Load
- 02/13/2020: Добавьте новые типы VM в [сертификаты и конфигурации SAP, работающие на Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020: Добавление новых заметок поддержки SAP [на загруженности SAP в Azure: контрольный список планирования и развертывания](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020: Изменение [высокой доступности Для SAP NetWeaver на RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) и Azure VMs высокой [доступности для SAP NetWeaver на RHEL с azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) для выравнивания тайм-аутов кластерных ресурсов с рекомендациями по тайм-ауту Red Hat
- 02/11/2020: Выпуск [SAP HANA о миграции Azure Large Instance в виртуальные машины Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: Изменение [подключения конечных точек для VMs с использованием Azure Standard ILB в сценариях SAP HA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) для обновления образца скриншота NSG
- 02/03/2020: Изменение [высокой доступности для SAP NW на Azure VMs на SLES для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) и [высокая доступность для SAP NW на Azure VMs на SLES с помощью ANF для приложений SAP для](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) удаления предупреждения об использовании тире в именах кластерных узлов на SLES
- 01/28/2020: Изменение [высокой доступности SAP HANA на Azure VMs на RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) для приведения тайм-аутов кластерных ресурсов SAP HANA в соответствие с рекомендациями тайм-аута Red Hat
- 01/17/2020: Изменение [групп размещения близости Azure для оптимальной задержки сети с приложениями SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) для изменения раздела перемещения существующих VMs в группу размещения непосредственного размещения
- 01/17/2020: Изменение [конфигураций рабочей нагрузки SAP с зонами доступности Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) чтобы указать на процедуру, которая автоматизирует измерения задержки между зонами доступности
- 01/16/2020: Изменения в [том, как установить и настроить SAP HANA (Большие инстанции) на Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) для адаптации релизов ОС к аппаратным каталогам HANA IaaS
- 01/16/2020: Изменения в [высокой доступности для SAP NetWeaver на Azure VMs на SLES multi-SID руководство](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) для добавления инструкций для sAP систем, используя архитектуру сервера enqueue 2 (ENSA2)
- 01/10/2020: Изменения в [масштабе SAP HANA с резервным узлом на Azure VMs с файлами Azure NetApp на SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) и в [масштабе SAP HANA с резервным узлом на Azure VMs с файлами Azure NetApp на RHEL,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) чтобы добавить инструкции о том, как вносить `nfs4_disable_idmapping` изменения постоянными.
- 01/10/2020: Изменения в [высокой доступности для SAP NetWeaver на Azure VMs на SLES с Azure NetApp Files для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) и в [Azure Virtual Machines высокой доступности для SAP NetWeaver на RHEL с Azure NetApp Файлы для приложений SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) чтобы добавить инструкции, как монтировать Azure NetApp Файлы NFSv4 томов.
- 12/23/2019: Выпуск [высокой доступности для SAP NetWeaver на Azure VMs на sLES мульти-SID руководство](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019: Выпуск [sAP HANA масштабирования с резервным узлами на Azure VMs с файлами Azure NetApp на RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/21/2019: Изменения в [масштабе SAP HANA с резервным узлом на Azure VMs с Azure NetApp Files на SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) для упрощения конфигурации отображения NFS ID и изменения рекомендуемого основного сетевого интерфейса для упрощения реулятики.
- 11/15/2019: Незначительные изменения в [высокой доступности для SAP NetWeaver на SUSE Linux Enterprise Server с Azure NetApp Files для приложений SAP](high-availability-guide-suse-netapp-files.md) и [высокая доступность для SAP NetWeaver на Red Hat Enterprise на Red Hat Enterprise с приложениями Azure NetApp для приложений SAP для](high-availability-guide-rhel-netapp-files.md) уточнения ограничений размера емкости и удаления заявления о том, что поддерживается только версия NFSv3.
- 11/12/2019: Выпуск [высокой доступности для SAP NetWeaver на Windows с Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: Изменения в [высокой доступности SAP HANA на Azure VMs на SUSE Linux Enterprise Server](sap-hana-high-availability.md), [Настройка репликации системы SAP HANA на виртуальных машинах Azure (VMs)](sap-hana-high-availability-rhel.md), [Azure Virtual Machines высокой доступности для SAP NetWeaver на SUSE Linux Enterprise Server для sAP-приложений,](high-availability-guide-suse.md) [Azure Virtual Machines высокой доступности для SAP NetWeaver на SUSE Linux Enterprise Server с Azure NetApp Files](high-availability-guide-suse-netapp-files.md), [Azure Virtual Machines высокой доступности для SAP NetWeaver на Red Hat Enterprise Linux ](high-availability-guide-rhel.md), [Azure Виртуальные машины высокая доступность для SAP NetWeaver на Red Hat Enterprise Linux с Azure NetApp Файлов](high-availability-guide-rhel-netapp-files.md), [Высокая доступность для NFS на Azure VMs на SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS на Azure VMs на Red Hat Enterprise Linux для SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) рекомендовать стандартный балансер нагрузки Azure  
- 11/08/2019: Изменения в [планировании рабочей нагрузки SAP и контрольном списке развертывания](sap-deployment-checklist.md) для уточнения рекомендации по шифрованию  
- 11/04/2019: Изменения в [настройке Pacemaker на SUSE Linux Enterprise Server в Azure](high-availability-guide-suse-pacemaker.md) для создания кластера непосредственно с одноразовой конфигурацией  
- 10/29/2019: Выпуск [общедоступного подключения конечных точек для виртуальных машин с использованием балансировора стандартной нагрузки Azure в сценариях высокой доступности SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: Изменения в [конфигурациях виртуального хранения данных SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) и [масштабs-аутs SAP HANA с резервным узлом на Azure VMs с файлами Azure NetApp на SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) для уточнения протокола NFS для /hana/shared volume
- 10/22/2019: Изменение [высокой доступности для SAP NetWeaver на Azure VMs на SUSE Linux Enterprise Server для sAP-приложений](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [высокая доступность для SAP NetWeaver на Azure VMs на сервере SUSE Linux Enterprise Server с Azure NetApp Файлы для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Высокая доступность для NFS на Azure VMs на SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Настройка Pacemaker на SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [Высокая доступность IBM Db2 LUW на Azure VMs на SUSE Linux Enterprise Server с Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), и [высокая доступность SAP HANA на M-ми-ми-предправленияaz Azure Linux на сервере SUSE Linux для](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) упрочнения обнаружения нагрузки и баланса Azure
- Изменяет раздел и раздел заголовка ANF в [конфигурациях виртуальной машины SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: Выпуск [sAP HANA масштабирования с резервным узлами на Azure VMs с файлами Azure NetApp на SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: Исправить сломанные ссылки в [резервном копировании и восстановить](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: Изменение минимальной рекомендуемой ОС с SLES 12 SP3 на SLES 12 SP4 в [высокой доступности IBM Db2 LUW на Azure VMs на SUSE Linux Enterprise Server с Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: Изменения в конфигурациях хранения дисков Ultra и внедрение [конфигураций виртуальных машинных хранилищ SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: Изменение графики [групп размещения близости Azure для оптимальной задержки сети с приложениями SAP,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) чтобы получить больше ясности
- 10/01/2019: Изменение [конфигураций и операций инфраструктуры SAP HANA в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) для исправления инструкций вокруг высокодоступной доли NFS для /hana/shared. 
- 09/28/2019: Изменение в [настройке Pacemaker на Red Hat Enterprise Linux в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) для уточнения SBD как механизм ограждения не поддерживается на кластерах RHEL  
- 09/17/2019: Изменение руководства по планированию и развертыванию NetWeaver для унификации условий вокруг расширения VM для SAP  
- 08/22/2019: Изменения в [настройке Pacemaker на SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) для обновления URL-адресов для создания пользовательских ролей  
- 08/16/2019: Изменения в [настройке Pacemaker на Red Hat Enterprise Linux в Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) чтобы напомнить клиентам об обновлении действий в пользовательской роли, при обновлении к новой версии агента забора Azure  
- 08/15/2019: Изменения в [конфигурациях виртуальной машины SAP HANA Azure, отражающие](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) общую доступность ультрадискового диска (ранее Ultra SSD)
- 08/01/2019: Изменения в [настройке Pacemaker на SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) для интеграции изменений специально для SLES 15 


