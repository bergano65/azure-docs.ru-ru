---
title: Начало работы с SAP на виртуальных машинах Azure | Документация Майкрософт
description: Дополнительные сведения о решениях SAP, запущенными на виртуальных машинах (ВМ) в Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b8eebd7a7a8933d082741a3086ffcf03a58066f
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357706"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Используйте Azure для размещения и выполнения сценариев рабочей нагрузки SAP

При использовании Microsoft Azure, можно уверенностью выполнять критически важные рабочие нагрузки SAP и сценариев на платформе масштабируемой, совместимых и проверенные многими компаниями. Вы получите масштабируемость, гибкость и гибкостью Azure. С помощью расширенному партнерству Майкрософт и SAP могут выполняться приложения SAP в сценариях разработки и тестирования и эксплуатации в Azure и полностью поддерживаются. SAP NetWeaver, SAP S/4HANA, SAP BI в Linux для Windows и SAP HANA и SQL, мы поможем вам.

Помимо размещения сценариев SAP NetWeaver с различными СУБД в Azure, вы можете размещать другие сценарии рабочей нагрузки SAP, например SAP BI в Azure. 

Уникальность Azure для SAP HANA — это предложение, которое задает Azure друг от друга. Возможность размещения больше памяти и ЦП с ресурсоемкими SAP сценариев, использующих SAP HANA, Azure предлагает использование специализированного клиента физического оборудования. Используйте это решение для запуска развертывания SAP HANA, которые требуют до 24 ТБ (120 ТБ горизонтального масштабирования) памяти для S/4HANA или других рабочих нагрузок SAP HANA. 

Размещение сценариев рабочей нагрузки SAP в Azure также можно создать требования к интеграции удостоверений и единого входа. Это может произойти при использовании Azure Active Directory (Azure AD) для подключения различных компонентов SAP и SAP software как услуга (SaaS) или платформа как служба (PaaS) предложения. Список таких интеграции и сценариев единого входа в Azure AD и сущности SAP описанный и описано в разделе «Интеграция удостоверений AAD SAP и единый вход.»

## <a name="latest-changes"></a>Последние изменения

- Общие сведения о пути, быстро ExpressRoute и глобальным доступом для крупных экземпляров HANA в [сетевой архитектуры SAP HANA (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) и связанных документов
- Выпуск [управление крупных экземпляров HANA в Azure с помощью портала Azure](hana-li-portal.md)
- Выпуск [высокий уровень доступности для SAP NetWeaver на виртуальных машинах Azure в SUSE Linux Enterprise Server с файлами NetApp Azure для приложений SAP](high-availability-guide-suse-netapp-files.md)
- Разъяснение к **net.ipv4.tcp_timestamps параметр ОС Linux** подсистемы балансировки нагрузки параметры вместе с Azure






## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA в Azure (большие экземпляры)

Серии документов проведет вас через SAP HANA в Azure (крупные экземпляры), или сокращенно крупных экземпляров HANA. Сведения о следующих областях крупных экземпляров HANA см. в разделе:

- [Основные сведения о SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Архитектура SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Инфраструктура и подключение к SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Установка SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Обеспечение высокого уровня доступности и аварийное восстановление SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Устранение неполадок и мониторинг SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Дальнейшие действия:

- Чтение [Обзор и описание архитектуры SAP hana в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA на виртуальных машинах Azure
Этот раздел документации охватывает различные аспекты SAP HANA. Перед началом необходимо ознакомиться с помощью субъекта-службы Azure, которые предоставляют простой службы Azure IaaS. Таким образом необходимо знание вычислений Azure, хранилища и сети. Многие из этих вопросов обрабатываются в SAP NetWeaver связанные [Azure руководство по планированию](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Сведения о HANA в Azure см. следующие статьи и их subarticles:

- [Краткое руководство установке одного экземпляра SAP HANA вручную на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Развертывание SAP S/4HANA или BW/4HANA в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Конфигурации и операции инфраструктуры SAP HANA в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Обеспечение высокого уровня доступности для SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Доступность SAP HANA в пределах одного региона Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Доступность SAP HANA в разных регионах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Высокий уровень доступности SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Руководство по резервному копированию SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Резервное копирование SAP HANA в Azure на уровне файлов](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Резервное копирование SAP HANA на основе моментальных снимков хранилища](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver, развернутых на виртуальных машинах Azure
В этом разделе перечислены в документации по планированию и развертыванию для SAP NetWeaver и Business One в Azure. Основное внимание уделяется основы и использование баз данных не относящееся к HANA с рабочей нагрузкой SAP в Azure. Документы и статьи, для обеспечения высокой доступности также являются основой для высокого уровня доступности HANA в Azure, такие как:

- [SAP Business One на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Развертывание SAP IDES EHP7 SP3 для SAP ERP 6.0 в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Запустите SAP NetWeaver на виртуальных машинах SUSE Linux Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Развертывание виртуальных машин Azure для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Защита многоуровневого развертывания приложения SAP NetWeaver с помощью Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Соединитель SAP LaMa для Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Сведения о базах данных не относящееся к HANA в группе рабочей нагрузки SAP в Azure см. в разделе:

- [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver на виртуальных машинах Azure. Руководство по развертыванию СУБД SQL Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Рабочие нагрузки SAP на виртуальных машинах Azure. Руководство по развертыванию СУБД Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Рабочие нагрузки SAP на виртуальных машинах Azure. Руководство по развертыванию СУБД IBM DB2](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Рабочие нагрузки SAP на виртуальных машинах Azure. Руководство по развертыванию СУБД SAP ASE](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB, Live кэш и сервер содержимого развертывания на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Сведения о базах данных SAP HANA в Azure см. в разделе «SAP HANA на виртуальных машинах Azure».

Дополнительные сведения о высоком уровне доступности рабочих нагрузок SAP в Azure см. в разделе:

- [Руководство по обеспечению высокого уровня доступности SAP NetWeaver на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

В этом документе будет указывать на различные другие документы архитектура и сценарии. В более поздних версиях сценарий предоставляются ссылки на подробные технические документы, в которых объясняется, развертывание и настройку для различных методов высокого уровня доступности. Различные документы, которые показывают, как установить и настроить высокий уровень доступности при использовании рабочей нагрузки SAP NetWeaver охватывают операционных систем Linux и Windows.


Дополнительные сведения об интеграции между Azure Active Directory (Azure AD) и службы SAP и единый вход см. в разделе:

- [Учебник. по интеграции Azure Active Directory с SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с приложением SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. Интеграция Azure Active Directory с SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство по интеграции Azure Active Directory с SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство интеграции Azure Active Directory с SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Среды S/4HANA: Fiori панель запуска SAML единого входа в Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Дополнительные сведения об интеграции служб Azure в компонентов SAP см. в разделе:

- [Использование SAP HANA в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery и SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Использование соединителя SAP BW в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Возможность интеграции данных SAP HANA и Business Warehouse в Фабрике данных Azure](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




