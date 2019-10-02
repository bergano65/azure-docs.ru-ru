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
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab6139e1f87938edaa0a8a94453faa213d39e23f
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719012"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Использование Azure для размещения и запуска сценариев рабочей нагрузки SAP

При использовании Microsoft Azure можно надежно выполнять критически важные рабочие нагрузки и сценарии SAP на масштабируемой и совместимой платформе, проверенной на предприятии. Вы получаете масштабируемость, гибкость и экономичность Azure. Благодаря расширенному взаимоотношениям между корпорацией Майкрософт и SAP вы можете запускать приложения SAP в сценариях разработки и тестирования, а также поддерживать все рабочие сценарии в Azure. Из SAP NetWeaver в SAP S/4HANA, SAP BI в Linux для Windows и SAP HANA в SQL, мы работаем над вами.

Помимо размещения сценариев SAP NetWeaver с разными СУБД в Azure, можно размещать другие сценарии рабочей нагрузки SAP, например SAP BI в Azure. 

Уникальность Azure для SAP HANA — это предложение, которое задает разделение Azure. Чтобы включить размещение большего объема памяти и сценариев SAP с ресурсоемкими ресурсами, которые включают SAP HANA, Azure предлагает использование аппаратно-необычного оборудования без операционной системы. Используйте это решение для запуска SAP HANA развертываний, требующих до 24 ТБ памяти (120 ТБ) для S/4HANA или другой SAP HANA рабочей нагрузки. 

Размещение сценариев рабочей нагрузки SAP в Azure также может привести к созданию требований интеграции удостоверений и единого входа. Такая ситуация может возникать, если вы используете Azure Active Directory (Azure AD) для подключения различных компонентов SAP и программного обеспечения SAP или платформы как услуги (PaaS). Список сценариев интеграции и единого входа с сущностями Azure AD и SAP описан в разделе "Интеграция AAD SAP и единый вход".

## <a name="changes-to-the-sap-workload-section"></a>Изменения в разделе рабочей нагрузки SAP
Изменения в документах в разделе "Рабочая нагрузка SAP в Azure" перечислены в конце этой статьи.


## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA в Azure (крупные экземпляры)

Серия документов поможет вам SAP HANA в Azure (крупные экземпляры) или для коротких экземпляров HANA (крупные экземпляры). Сведения о следующих областях крупных экземпляров HANA см. в разделе:

- [Основные сведения о SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Архитектура SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Инфраструктура и подключение к SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Установка SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Высокий уровень доступности и аварийное восстановление SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Устранение неполадок и мониторинг SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Дальнейшие действия:

- Ознакомьтесь [с обзором и архитектурой SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) .



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA на виртуальных машинах Azure
Этот раздел документации охватывает различные аспекты SAP HANA. Предварительные требования должны быть знакомы с основными службами Azure, которые предоставляют простейшие службы Azure IaaS. Итак, вам необходимо знание вычислений, хранилища и сети Azure. Многие из этих субъектов обрабатываются в соответствующем разделе [руководств по планированию Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)для SAP NetWeaver. 

Сведения о HANA в Azure см. в следующих статьях и их подразделах:

- [Краткое руководство установке одного экземпляра SAP HANA вручную на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Развертывание SAP S/4HANA или BW/4HANA в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Конфигурации и операции инфраструктуры SAP HANA в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Обеспечение высокого уровня доступности для SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Доступность SAP HANA в пределах одного региона Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Доступность SAP HANA в разных регионах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Высокий уровень доступности SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Инструкции по резервному копированию для SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Резервное копирование SAP HANA в Azure на уровне файлов](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Резервное копирование SAP HANA на основе моментальных снимков хранилища](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver, развернутые на виртуальных машинах Azure
В этом разделе приведена документация по планированию и развертыванию для SAP NetWeaver и бизнеса One в Azure. В этой документации основное внимание уделяется основам и использованию баз данных, отличных от HANA, с рабочей нагрузкой SAP в Azure. Документы и статьи для обеспечения высокой доступности также являются основой для обеспечения высокого уровня доступности HANA в Azure, например:

- [SAP Business One на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Развертывание SAP IDES EHP7 SP3 для SAP ERP 6.0 в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Запуск SAP NetWeaver на виртуальных машинах SUSE Linux Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Развертывание виртуальных машин Azure для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
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

- [Учебник. по интеграции Azure Active Directory с SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с приложением SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. Интеграция Azure Active Directory с SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство интеграции Azure Active Directory с SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Среда S/4HANA: Fiori панель запуска для единого входа SAML с помощью Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Сведения об интеграции служб Azure в компоненты SAP см. в следующих статьях:

- [Использование SAP HANA в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery и SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Использование соединителя SAP BW в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Возможность интеграции данных SAP HANA и Business Warehouse в Фабрике данных Azure](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Журнал изменений
- 10/01/2019: Изменяйте графики [групп размещения Azure, чтобы обеспечить оптимальную задержку в сети с приложениями SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) для повышения ясности
- 10/01/2019: Изменение [SAP HANA конфигураций инфраструктуры и операций в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) для исправления инструкций по общей папке NFS высокой доступности для/хана/Шаред. 
- 09/28/2019: Изменение [настройки Pacemaker на Red Hat Enterprise Linux в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) для уточнения SBD, так как механизм разграничения не поддерживается в КЛАСТЕРАх RHEL  
- 09/17/2019: Изменение в руководств по планированию и развертыванию NetWeaver для объединения терминов, связанных с расширением VM для SAP  
- 08/22/2019: Изменения в [настройке Pacemaker на SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) для обновления URL-адресов для создания настраиваемой роли  
- 08/16/2019: Изменения в [настройке Pacemaker на Red Hat Enterprise Linux в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) для напоминания клиентам об обновлении действий в пользовательской роли при обновлении до новой версии агента ограждения Azure  
- 08/15/2019: Изменения в [SAP HANA конфигурации хранилища виртуальных машин Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) для отражения общей доступности Ultra Disk (ранее SSD (цен. Категория "ультра"))
- 08/01/2019: Изменения в [настройке Pacemaker на SUSE Linux Enterprise Server в Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) для интеграции изменений, специально предназначенных для SLES 15 
- 07/23/2019: Изменения в [кластере экземпляра SAP ASCS/SCS в отказоустойчивом кластере Windows с помощью файлового ресурса в Azure](sap-high-availability-guide-wsfc-file-share.md) для отражения поддержки прямого дискового пространства службами Azure Site Recovery Services
- 07/14/2019: Выпуск [групп размещения Azure с учетом расположения для оптимальной сетевой задержки с помощью приложений SAP](sap-proximity-placement-scenarios.md)
- 07/11/2019: Изменения в различных документах, охватывающих крупные экземпляры HANA, для покрытия редакции 4 крупных экземпляров HANA
- 07/09/2019: Выпуск нового руководством по [IBM DB2 HADR на сервере Red Hat Enterprise](high-availability-guide-rhel-ibm-db2-luw.md)
- 06/13/2019: Выпуск [высокого уровня доступности SAP NetWeaver на Red Hat Enterprise Linux с Azure NetApp Files для приложений SAP](high-availability-guide-rhel-netapp-files.md)


