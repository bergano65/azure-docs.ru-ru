---
title: Приступая к работе с SAP на виртуальных машинах Azure | Документация Майкрософт
description: Сведения о работе решений SAP на виртуальных машинах в Microsoft Azure.
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
ms.date: 04/10/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e07824db683b55cf5827962c1030d6cfd6c9f66
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490435"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Размещение и выполнение сценариев рабочей нагрузки SAP с помощью Azure

Выбрав Microsoft Azure, вы сможете с уверенностью выполнять критически важные рабочие нагрузки и сценарии SAP на масштабируемой и совместимой платформе, проверенной многими компаниями.  Воспользуйтесь масштабируемостью и гибкостью Azure, а также сэкономьте денежные средства. Благодаря расширенному партнерству Майкрософт и SAP на платформе Azure в полной мере поддерживается трехуровневый сценарий для приложений SAP (среда разработки, среда тестирования и производственная среда). Мы осуществляем поддержку SAP NetWeaver и SAP S/4HANA, перенос SAP BI из Linux в Windows, SAP HANA и SQL.

Помимо размещения сценариев SAP NetWeaver с различными СУБД в Azure, можно также размещать другие сценарии рабочей нагрузки SAP, например SAP BI в Azure. 

Azure для SAP HANA — это предложение, выделяющее Azure среди конкурентов. Чтобы размещать сценарии SAP с большими требованиями к ресурсам памяти и ЦП, включающие SAP HANA, Azure предлагает использовать специализированное клиентское оборудование без операционной системы для выполнения развертываний SAP HANA, которым требуется до 24 ТБ (масштабирование до 120 ТБ) памяти для рабочих нагрузок S/4HANA или других рабочих нагрузок SAP HANA. Уникальное решение Azure, SAP HANA в Azure (крупные экземпляры), позволяет запускать SAP HANA на выделенном оборудовании без операционной системы на уровне приложения SAP или ПО промежуточного слоя рабочих нагрузок, размещенном в собственных виртуальных машинах Azure. Это решение описано в нескольких документах в разделе "SAP HANA в Azure (крупные экземпляры)".   

Размещение сценариев рабочей нагрузки SAP в Azure также может потребоваться Интеграция удостоверений и Single-Sign-On с помощью Azure Active Directory для других компонентов SAP и SAP SaaS или PaaS предлагает. Список таких сценариев интеграции и единого входа с помощью сущностей Azure Active Directory (AAD) и SAP представлен в разделе "Интеграция AAD с приложением для управления удостоверениями SAP и единый вход".

## <a name="latest-changes"></a>Последние изменения

Выпуск [управление крупных экземпляров HANA в Azure с помощью портала Azure](hana-li-portal.md)

Выпуск [высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в SUSE Linux Enterprise Server с файлами NetApp Azure для приложений SAP](high-availability-guide-suse-netapp-files.md)

Разъяснение к **net.ipv4.tcp_timestamps параметр ОС Linux** подсистемы балансировки нагрузки параметры вместе с Azure

Опубликована статья [Конфигурации рабочих нагрузок SAP с использованием Зон доступности Azure](sap-ha-availability-zones.md).

Опубликована статья [SAP workload planning and deployment checklist](sap-deployment-checklist.md) (Контрольный список для планирования и развертывания рабочей нагрузки SAP).




## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA на сервере SAP HANA в Azure (крупные экземпляры)

Серия документов описывает SAP HANA в Azure (крупные экземпляры) или крупные экземпляры HANA. В этих документах разъясняются следующие аспекты крупных экземпляров HANA:

- [Общие сведения о SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Архитектура SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Инфраструктура и подключение к SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Установка SAP HANA на сервере SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Обеспечение высокого уровня доступности и аварийное восстановление решения "SAP HANA в Azure (крупные экземпляры)"](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Устранение неполадок и мониторинг архитектуры решения "SAP HANA в Azure (крупные экземпляры)".](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Дальнейшие действия:

- Прочтите документ [Обзор и описание архитектуры SAP HANA в Azure (крупные экземпляры)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA на виртуальных машинах Azure
Этот раздел документации охватывает различные аспекты SAP HANA. Перед началом работы необходимо ознакомиться с понятием субъекта-службы в Azure, которая предоставляет элементарные службы Azure IaaS, то есть вы должны знать о вычислениях, хранении и сетях в Azure. Многие из этих тем рассматриваются в [руководстве по планированию Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) для SAP NetWeaver. 

Документация для HANA в Azure состоит из следующих статей и подразделов:

- [Краткое руководство. Установка одного экземпляра SAP HANA вручную на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Развертывание SAP S/4HANA или BW/4HANA в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Конфигурации инфраструктуры SAP HANA и работа с ней в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Обеспечение высокого уровня доступности для SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Доступность SAP HANA в пределах одного региона Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Доступность SAP HANA в разных регионах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Высокий уровень доступности SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Руководство по резервному копированию SAP HANA на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Резервное копирование SAP HANA в Azure на уровне файлов](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Резервное копирование SAP HANA на основе моментальных снимков хранилища](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Развертывание SAP NetWeaver на виртуальных машинах Azure
В этом разделе можно найти документацию по планированию и развертыванию SAP NetWeaver и Business One в Azure. Документация в этой главе сосредоточена, в основном, на основах и использовании баз данных, не относящихся к HANA, с рабочей нагрузкой SAP в Azure. Чтобы понять высокую доступность HANA в Azure, необходимо также ознакомиться с документами и статьями о высокой доступности. Список статей:

- [Использование SAP Business One на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Развертывание SAP IDES EHP7 SP3 для SAP ERP 6.0 в Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Запуск SAP NetWeaver на виртуальных машинах SUSE Linux в Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Развертывание виртуальных машин Azure для SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Защита многоуровневого развертывания приложения SAP NetWeaver с помощью Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Соединитель SAP LaMa для Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Документы о базах данных, не относящихся к HANA, в рабочей нагрузке SAP в Azure:

- [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP NetWeaver на виртуальных машинах Azure. Руководство по развертыванию СУБД SQL Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Развертывание СУБД на виртуальных машинах Azure в Oracle для рабочей нагрузки SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Рабочие нагрузки SAP на виртуальных машинах Azure. Руководство по развертыванию СУБД IBM DB2](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Развертывание СУБД на виртуальных машинах Azure в SAP ASE для рабочей нагрузки SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Развертывание SAP MaxDB, liveCache и сервера содержимого на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Для баз данных SAP HANA в Azure ознакомьтесь с разделом "SAP HANA на виртуальных машинах Azure".

Документ с общими сведениями об обеспечении высокого уровня доступности для рабочих нагрузок SAP в Azure:

- [Руководство по обеспечению высокого уровня доступности SAP NetWeaver на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

В базовом документе упоминаются документы для других архитектур и сценариев. В документах со сценариями предоставляются ссылки на подробные технические документы, объясняющие развертывание и настройку различных методов обеспечения высокого уровня доступности. Различные документы по установке и настройке высокого уровня доступности для рабочей нагрузки SAP NetWeaver относятся к операционным системам Linux и Windows.


Документы об интеграции Azure Active Directory (AAD) со службами SAP и единым входом:

- [Руководство. Интеграция Azure Active Directory с SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. Интеграция Azure Active Directory с приложением SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. Интеграция Azure Active Directory с SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. Интеграция Azure Active Directory с SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. Интеграция Azure Active Directory с SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. Интеграция Azure Active Directory с SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [S/4HANA среды — Fiori панель запуска SAML единого входа в Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Документы об интеграции служб Azure в компоненты SAP:

- [Использование SAP HANA в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery и SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Использование соединителя SAP BW в Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Возможность интеграции данных SAP HANA и Business Warehouse в Фабрике данных Azure](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




