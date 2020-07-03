---
title: Приступая к работе с SAP на виртуальных машинах Azure | Документация Майкрософт
description: Сведения о решениях SAP, которые выполняются на виртуальных машинах (ВМ) в Microsoft Azure
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
ms.date: 05/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eab9db77dee5420ddc5baa9f71bde98fc46ca3f6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196844"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Использование Azure для размещения и запуска сценариев рабочей нагрузки SAP

При использовании Microsoft Azure можно надежно выполнять критически важные рабочие нагрузки и сценарии SAP на масштабируемой и совместимой платформе, проверенной на предприятии. Вы получаете масштабируемость, гибкость и экономичность Azure. Благодаря расширенному взаимоотношениям между корпорацией Майкрософт и SAP вы можете запускать приложения SAP в сценариях разработки и тестирования, а также поддерживать все рабочие сценарии в Azure. Из SAP NetWeaver в SAP S/4HANA, SAP BI в Linux для Windows и SAP HANA в SQL, мы работаем над вами.

Помимо размещения сценариев SAP NetWeaver с разными СУБД в Azure, можно размещать другие сценарии рабочей нагрузки SAP, например SAP BI в Azure. 

Уникальность Azure для SAP HANA — это предложение, которое задает разделение Azure. Чтобы включить размещение большего объема памяти и сценариев SAP с ресурсоемкими ресурсами, которые включают SAP HANA, Azure предлагает использование аппаратно-необычного оборудования без операционной системы. Используйте это решение для запуска SAP HANA развертываний, требующих до 24 ТБ памяти (120 ТБ) для S/4HANA или другой SAP HANA рабочей нагрузки. 

Размещение сценариев рабочей нагрузки SAP в Azure также может привести к созданию требований интеграции удостоверений и единого входа. Такая ситуация может возникать, если вы используете Azure Active Directory (Azure AD) для подключения различных компонентов SAP и программного обеспечения SAP или платформы как услуги (PaaS). Список сценариев интеграции и единого входа с сущностями Azure AD и SAP описан в разделе "Интеграция AAD SAP и единый вход".

## <a name="changes-to-the-sap-workload-section"></a>Изменения в разделе рабочей нагрузки SAP
Изменения в документах в разделе "Рабочая нагрузка SAP в Azure" перечислены в конце этой статьи. Записи в журнале изменений хранятся около 180 дней.

## <a name="you-want-to-know"></a>Вы хотите получить сведения
Если у вас есть определенные вопросы, мы будем указывать на конкретные документы или последовательности в этом разделе начальной страницы. Вы хотите получить информацию:

- Какие виртуальные машины Azure и единицы крупных экземпляров HANA поддерживаются для программных версий SAP и операционных систем. Прочитайте документ [о том, какое программное обеспечение SAP поддерживается для развертывания Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) , для ответов и процесса поиска информации.
- Какие сценарии развертывания SAP поддерживаются с виртуальными машинами Azure и крупными экземплярами HANA. Сведения о поддерживаемых сценариях можно найти в документах:
    - [Рабочая нагрузка SAP в поддерживаемых сценариях виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Поддерживаемые сценарии для крупных экземпляров HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- Какие службы Azure, типы виртуальных машин Azure и службы хранилища Azure доступны в разных регионах Azure, проверьте продукты сайта, [Доступные по регионам](https://azure.microsoft.com/global-infrastructure/services/) . 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA в Azure (крупные экземпляры)

Серия документов поможет вам SAP HANA в Azure (крупные экземпляры) или для коротких экземпляров HANA (крупные экземпляры). Сведения о крупных экземплярах HANA см. в статье [Обзор документов и архитектура SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) и просмотрите соответствующую документацию в разделе крупные экземпляры Hana.



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA на виртуальных машинах Azure
Этот раздел документации охватывает различные аспекты SAP HANA. Предварительные требования должны быть знакомы с основными службами Azure, которые предоставляют простейшие службы Azure IaaS. Итак, вам необходимо знание вычислений, хранилища и сети Azure. Многие из этих субъектов обрабатываются в соответствующем разделе [руководств по планированию Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)для SAP NetWeaver. 

Сведения о HANA в Azure см. в следующих статьях и их подразделах:

- [Конфигурации инфраструктуры SAP HANA и работа с ней в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Обеспечение высокого уровня доступности для SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Высокий уровень доступности SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Инструкции по резервному копированию для SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver, развернутые на виртуальных машинах Azure
В этом разделе приведена документация по планированию и развертыванию для SAP NetWeaver и бизнеса One в Azure. В этой документации основное внимание уделяется основам и использованию баз данных, отличных от HANA, с рабочей нагрузкой SAP в Azure. Документы и статьи для обеспечения высокой доступности также являются основой для обеспечения высокого уровня доступности HANA в Azure, например:

- [Инструкции по планированию Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [SAP Business One на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Защита многоуровневого развертывания приложения SAP NetWeaver с помощью Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Соединитель SAP LaMa для Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Сведения о базах данных, отличных от HANA, в рабочей нагрузке SAP в Azure см. в следующих статьях:

- [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver на виртуальных машинах Azure. Руководство по развертыванию СУБД SQL Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Рабочие нагрузки SAP на Виртуальных машинах Azure. Руководство по развертыванию СУБД Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Рабочие нагрузки SAP на Виртуальных машинах Azure. Руководство по развертыванию СУБД IBM DB2](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Рабочие нагрузки SAP на Виртуальных машинах Azure. Руководство по развертыванию СУБД SAP ASE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Развертывание SAP MaxDB, Live Cache и Content Server на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Сведения о SAP HANA базах данных в Azure см. в разделе "SAP HANA на виртуальных машинах Azure".

Сведения о высокой доступности рабочей нагрузки SAP в Azure см. в следующих статьях:

- [Руководство по обеспечению высокого уровня доступности SAP NetWeaver на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

В этом документе указываются различные документы по архитектуре и сценариям. В последующих сценариях приведены ссылки на подробные технические документы, в которых объясняется развертывание и настройка различных методов высокого уровня доступности. Различные документы, показывающие, как установить и настроить высокий уровень доступности для рабочей нагрузки SAP NetWeaver, охватывают операционные системы Linux и Windows.


Сведения о интеграции между Azure Active Directory (Azure AD) и службами SAP и единым входом см. в следующих статьях:

- [Руководство по интеграции Azure Active Directory с SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с приложением SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. Интеграция Azure Active Directory с SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Среда S/4HANA: Fiori (панель запуска) единый вход SAML с помощью Azure AD.](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Сведения об интеграции служб Azure в компоненты SAP см. в следующих статьях:

- [Использование SAP HANA в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery и SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Использование соединителя SAP BW в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Возможность интеграции данных SAP HANA и Business Warehouse в Фабрике данных Azure](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Журнал изменений

- 05/11/2020: измените [высокий уровень доступности SAP HANA на виртуальных машинах Azure в SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) , чтобы прикрепить ресурс к 0 для ресурса неткат, как это приводит к более рациональному переходу на другой ресурс. 
- 05/05/2020: изменения на [виртуальных машинах Azure по планированию и реализации SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) позволяют выразить, что Gen2 развертывания доступны для "Mv1" семейства виртуальных машин.
- 04/24/2020: изменения [SAP HANA горизонтальном масштабировании с резервным узлом на виртуальных машинах Azure с помощью использовании на SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse), в [SAP HANA масштабирование с резервным узлом на виртуальных машинах Azure с использовании на RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel), [высокий уровень доступности для SAP NetWeaver на ВИРТУАЛЬНЫХ машинах Azure в SLES с](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) использовании и [высокий уровень доступности для SAP NETWEAVER на ВИРТУАЛЬНЫХ машинах Azure в RHEL с использовании](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) , чтобы добавить уточнение, назначаемое
- 04/22/2020: измените [высокий уровень доступности SAP HANA на виртуальных машинах Azure в SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) , чтобы удалить атрибут meta `is-managed` из инструкций, так как он конфликтует с размещением кластера в режиме обслуживания или вне его.
- 04/21/2020: добавлена SQL Azure DB в качестве поддерживаемой СУБД для SAP (Hybris) Commerce Platform 1811 и более поздних версий. в статьях, [какое программное обеспечение SAP поддерживается для развертываний Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) , а также для [сертификации и конфигураций SAP, работающих на Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 04/16/2020: добавлена SAP HANA в качестве поддерживаемой СУБД для коммерческой платформы SAP (Hybris) в статьях, [какое программное обеспечение SAP поддерживается для развертываний Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) , а также для [сертификации и конфигураций SAP, работающих на Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 04/13/2020: исправьте в точные номера выпусков SAP ASE на [виртуальных машинах SAP ASE Azure развертывание СУБД для рабочей нагрузки SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase) .
- 04/07/2020: изменение [настройки Pacemaker для SLES в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) для уточнения Cloud-netconfig-инструкции Azure
- 04/06/2020: изменение [масштаба SAP HANA с резервным узлом на виртуальных машинах Azure с Azure NetApp Files на SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) и в [SAP HANA масштабирования с резервным узлом на виртуальных машинах Azure с Azure NetApp Files на RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) для удаления ссылок на NetApp [tr-4435](https://www.netapp.com/us/media/tr-4746.pdf) (заменено на [tr-4746](https://www.netapp.com/us/media/tr-4746.pdf))
- 03/31/2020: измените [высокий уровень доступности SAP HANA на виртуальных машинах Azure в SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) и [высокий уровень доступности SAP HANA на виртуальных машинах Azure в RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) , чтобы добавить инструкции по указанию размера чередования при создании чередующихся томов.
- 03/27/2020: измените [высокий уровень доступности SAP NW на виртуальных машинах Azure в SLES с использовании для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) , чтобы согласовать параметры подключения файловой системы с NetApp TR-4746 (удалите параметр синхронизации подключения).
- 03/26/2020: изменение [высокого уровня доступности SAP NetWeaver на виртуальных машинах Azure в SLES с несколькими ИД безопасности](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) для добавления ссылки на NetApp TR-4746
- 03/26/2020: изменение [высокого уровня доступности SAP NetWeaver на виртуальных машинах Azure в SLES для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Высокая доступность для SAP NetWeaver на виртуальных машинах Azure в SLES с Azure NETAPP Files для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [высокий уровень доступности NFS на ВИРТУАЛЬНЫХ машинах Azure в SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [высокий уровень доступности SAP NETWEAVER на виртуальных МАШИНАХ Azure в SLES с несколькими ИД безопасности](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid), [высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в RHEL для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) и [высокий уровень доступности для SAP NetWeaver на виртуальных машинах Azure в RHEL с Azure NetApp Files для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) для обновления диаграмм и уточнения инструкций для Azure Load Balancer создания серверного пула
- 03/19/2020: Основная Редакция [краткого руководства по документу: установка SAP HANA с одним экземпляром на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) для [установки SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 03/17/2020: измените [настройку Pacemaker на SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) , чтобы удалить параметр конфигурации SBD, который больше не нужен.
- 03/16/2020. разъяснение сценария сертификации на уровне столбца в SAP HANA сертифицированной платформе IaaS в [том, какое программное обеспечение SAP поддерживается для развертываний Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) .
- 03/11/2020: изменение [рабочей нагрузки SAP в поддерживаемых сценариях виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) для уточнения нескольких баз данных на поддержку каждого экземпляра СУБД
- 03/11/2020: изменение в [планировании и реализации SAP NetWeaver на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) с объяснениями виртуальных машин поколения 1 и поколения 2
- 03/10/2020: измените [SAP HANA конфигурации хранилища виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) , чтобы уточнить реальные ограничения пропускной способности использовании.
- 03/09/2020: изменение [высокого уровня доступности SAP NetWeaver на виртуальных машинах Azure на SUSE Linux Enterprise Server для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Высокая доступность для SAP NetWeaver на виртуальных машинах Azure на SUSE Linux Enterprise Server с Azure NETAPP Files для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [высокий уровень доступности NFS на виртуальных машинах Azure на SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Настройка Pacemaker на SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [Высокая доступность LUW IBM DB2 на виртуальных машинах azure на SUSE Linux Enterprise Server с Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), [высокий уровень доступности SAP HANA на ВИРТУАЛЬНЫХ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) машинах Azure в SuSE Linux Enterprise Server и [высокий уровень доступности для SAP NetWeaver на виртуальных машинах Azure в SLES с несколькими ИД безопасности](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) для обновления ресурсов кластера с помощью агента ресурсов Azure-фунтов 
- 03/05/2020: изменение структуры и изменение содержимого для регионов Azure и виртуальных машин Azure на [виртуальных машинах Azure планирование и реализация SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020: изменение [высокого уровня доступности SAP NW на виртуальных машинах Azure в SLES с использовании для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) с целью изменения более эффективной структуры томов использовании
- 03/01/2020: переработано [руководство по резервному копированию для SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) , чтобы включить Azure Backupную службу. Сокращенное и сжатое содержимое в [SAP HANA Azure Backup на уровне файлов](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) и удален третий документ, в котором рассматривается резервное копирование с помощью моментального снимка диска. Содержимое обрабатывается в разделе "инструкции по резервному копированию" для SAP HANA на виртуальных машинах Azure 
- 02/27/2020: изменение [высокого уровня доступности SAP NW на виртуальных машинах Azure в SLES для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [высокий уровень доступности для SAP NW на виртуальных машинах Azure в SLES с использовании для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) и [высокий уровень доступности для SAP NetWeaver на ВИРТУАЛЬНЫХ машинах Azure в SLES с несколькими ИД безопасности](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) для настройки параметра кластера "при сбое"
- 02/26/2020: измените [SAP HANA конфигурации хранилища виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) , чтобы уточнить вариант выбора файловой системы для Hana в Azure.
- 02/26/2020: измените [архитектуру высокого уровня доступности и сценарии для SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) , включив ссылку на уровень доступности SAP NetWeaver на виртуальных машинах Azure в RHEL с несколькими ИД безопасности.
- 02/26/2020: изменение [высокого уровня доступности SAP NW на виртуальных машинах Azure в SLES для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [высокий уровень доступности для SAP NW на виртуальных машинах Azure в SLES с использовании для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [высокий уровень доступности для SAP NetWeaver на RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) и [виртуальных машинах azure для SAP NetWeaver на RHEL с Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) для удаления инструкции, в которой кластер ASCS/ERS с несколькими ИД безопасности не поддерживается.
- 02/26/2020: выпуск [высокого уровня доступности для SAP NetWeaver на виртуальных машинах Azure в RHEL с несколькими ИД безопасности](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) для добавления ссылки на руководство по кластеру SUSE Multi-SID
- 02/25/2020: измените [архитектуру высокого уровня доступности и сценарии для SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) , чтобы добавить ссылки на более новые статьи высокой доступности
- 02/25/2020: измените [высокий уровень доступности IBM DB2 LUW на виртуальных машинах Azure на SUSE Linux Enterprise Server с Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) для указания документа, описывающего доступ к общедоступной конечной точке с помощью стандартного балансировщика нагрузки Azure.
- 02/21/2020: Полная редакция статьи [SAP ASE Azure Virtual Machines Deployment для рабочей нагрузки SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 02/21/2020: измените [SAP HANA конфигурацию хранилища виртуальных машин Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) , чтобы представить новую рекомендацию в чередующемся размере для/Hana/Data и добавить параметр планировщика ввода-вывода.
- 02/21/2020: изменения в документах крупных экземпляров HANA для представления новых сертифицированных номеров SKU S224 и S224m
- 02/21/2020: изменение [высокой доступности для SAP NetWeaver на](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) виртуальных машинах Azure в RHEL и [Azure для SAP NetWeaver на RHEL с Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) для настройки ограничений кластера для архитектуры сервера постановки в очередь репликации 2 (ENSA2)
- 02/20/2020: изменение [высокого уровня доступности SAP NetWeaver на виртуальных машинах Azure в SLES с несколькими ИД безопасности](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) для добавления ссылки на руководство по использованию кластера SUSE Multi-SID
- 02/13/2020: внесение изменений в [виртуальные машины Azure планирование и реализация SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) для реализации ссылок на новые документы
- 02/13/2020: добавлен новый документ [Рабочая нагрузка SAP в сценарии, поддерживаемом виртуальной машиной Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 02/13/2020: добавлен новый документ [, в котором программное обеспечение SAP поддерживается для развертывания Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) .
- 02/13/2020: измените [высокий уровень доступности IBM DB2 LUW на виртуальных машинах Azure на сервере Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) , чтобы указать документ, описывающий доступ к общедоступной конечной точке с помощью стандартного балансировщика нагрузки Azure.
- 02/13/2020: добавьте новые типы виртуальных машин в [Сертификаты и конфигурации SAP, выполняющиеся на Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020. Добавление новых заметок о поддержке SAP [рабочие нагрузки SAP в Azure: контрольный список для планирования и развертывания](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020: изменение [высокой доступности для SAP NetWeaver на](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) виртуальных машинах Azure в RHEL и [Azure для SAP NetWeaver на RHEL с Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) для согласования времени ожидания ресурсов кластера с рекомендациями по времени ожидания Red Hat
- 02/11/2020: выпуск [SAP HANA для миграции крупных экземпляров Azure на виртуальные машины Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: изменение [подключения к общедоступной конечной точке для виртуальных машин с помощью Azure Standard ilB в сценариях SAP Ha](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) для обновления примера снимка экрана NSG
- 02/03/2020: измените [высокий уровень доступности SAP NW на виртуальных машинах Azure в SLES для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) и [высокий уровень доступности для SAP NW на виртуальных машинах Azure в SLES с использовании для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) , чтобы удалить предупреждение об использовании тире в именах узлов кластеров в SLES
- 01/28/2020: изменение [высокого уровня доступности SAP HANA на виртуальных машинах Azure в RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) для согласования времени ожидания ресурсов кластера SAP HANA с рекомендациями по времени ожидания Red Hat
- 01/17/2020: измените в [группах размещения Azure с учетом расположения, чтобы обеспечить оптимальную задержку в сети с приложениями SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) , чтобы изменить раздел перемещения существующих виртуальных машин в группу размещения с учетом расположения.
- 01/17/2020: измените [конфигурации рабочей нагрузки SAP с зоны доступности Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) , чтобы она указывала на процедуру, которая автоматизирует измерения задержки между зоны доступности
- 01/16/2020. изменение [способа установки и настройки SAP HANA (крупные экземпляры) в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) для адаптации выпусков ОС к каталогу оборудования IaaS для Hana
- 01/16/2020: изменения в [высоком уровне доступности SAP NetWeaver на виртуальных машинах Azure в SLES с несколькими ИД безопасности](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) для добавления инструкций для систем SAP с использованием архитектуры Server 2 для постановки в очередь (ENSA2)
- 01/10/2020: изменения [SAP HANA горизонтальном масштабировании с резервным узлом на виртуальных машинах Azure с Azure NetApp Files на SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) и в [SAP HANA масштабирования с резервным узлом на виртуальных машинах Azure с Azure NetApp Files на RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) , чтобы добавить инструкции по внесению `nfs4_disable_idmapping` изменений в постоянные.
- 01/10/2020: изменения в [высокой доступности SAP NetWeaver на виртуальных машинах Azure в SLES с Azure NetApp Files для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) и на [виртуальных машинах Azure высокий уровень доступности для SAP NetWeaver на RHEL с Azure NETAPP Files для приложений SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) добавьте инструкции по подключению томов Azure NetApp Files NFSv4.
- 12/23/2019: выпуск [высокого уровня доступности для SAP NetWeaver на виртуальных машинах Azure в SLES с несколькими ИД безопасности](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019. выпуск [SAP HANA горизонтального развертывания с резервным узлом на виртуальных машинах Azure с Azure NetApp Files на RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/21/2019: изменения [SAP HANA горизонтальном масштабировании с резервным узлом на виртуальных машинах Azure с Azure NetApp Files на SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) , чтобы упростить настройку сопоставления идентификаторов NFS и изменить Рекомендуемый основной сетевой интерфейс для упрощения маршрутизации.
- 11/15/2019: незначительные изменения в [высоком уровне доступности SAP NetWeaver на SUSE Linux Enterprise Server с Azure NetApp Files для приложений SAP](high-availability-guide-suse-netapp-files.md) и [высокий уровень доступности для sap NetWeaver на Red Hat Enterprise Linux с Azure NETAPP Files для приложений SAP](high-availability-guide-rhel-netapp-files.md) , чтобы уточнить ограничения размера пула ресурсов и удалить инструкцию, так как поддерживается только NFSv3 версия.
- 11/12/2019: выпуск [высокого уровня доступности для SAP NetWeaver в Windows с Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: изменения в [высокой доступности SAP HANA на виртуальных машинах Azure на SUSE Linux Enterprise Server](sap-hana-high-availability.md), [настройте репликацию SAP HANA системы на виртуальных машинах Azure](sap-hana-high-availability-rhel.md), [Высокая доступность виртуальных машин azure для SAP NETWEAVER на SUSE Linux Enterprise Server для приложений SAP](high-availability-guide-suse.md), [Высокая доступность для](high-availability-guide-suse-netapp-files.md)SAP netweaver на виртуальных машинах Azure с Azure NetApp Files, высокая доступность виртуальных машин Azure для SAP NetWeaver на Red Hat Enterprise Linux [,](high-availability-guide-rhel.md)высокая доступность виртуальных SUSE Linux Enterprise Server машин Azure для SAP NETWEAVER на Red Hat Enterprise Linux [с Azure NetApp Files](high-availability-guide-rhel-netapp-files.md), [высокий уровень доступности для NFS](high-availability-guide-suse-nfs.md)на виртуальных машинах Azure в SuSE Linux Enterprise Server, глустерфс на виртуальные машины Azure в Red Hat Enterprise Linux [для SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) .  
- 11/08/2019: изменения в [контрольном списке планирования рабочей нагрузки SAP и рекомендации по развертыванию](sap-deployment-checklist.md) для уточнения рекомендаций по шифрованию  
- 11/04/2019: изменения в [настройке Pacemaker на SUSE Linux Enterprise Server в Azure](high-availability-guide-suse-pacemaker.md) для создания кластера непосредственно с конфигурацией одноадресной рассылки  


