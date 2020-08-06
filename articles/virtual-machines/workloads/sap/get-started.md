---
title: Приступая к работе с SAP на виртуальных машинах Azure | Документация Майкрософт
description: Сведения о работе решений SAP на виртуальных машинах в Microsoft Azure.
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
ms.date: 08/04/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a882e3a21d5e1e99f6f9154fd2162071752b4499
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800354"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Использование Azure для размещения и выполнения сценариев рабочей нагрузки SAP

Microsoft Azure позволяет с уверенностью выполнять критически важные рабочие нагрузки и сценарии SAP на масштабируемой и совместимой платформе, проверенной многими компаниями. Воспользуйтесь масштабируемостью и гибкостью Azure, а также сэкономьте денежные средства. Благодаря расширенному партнерству Майкрософт и SAP на платформе Azure в полной мере поддерживается трехуровневый сценарий для приложений SAP (среда разработки, среда тестирования и производственная среда). Мы осуществляем поддержку SAP NetWeaver и SAP S/4HANA, перенос SAP BI из Linux в Windows, SAP HANA и SQL.

Помимо размещения сценариев SAP NetWeaver с различными СУБД в Azure, можно также размещать другие сценарии рабочей нагрузки SAP, например SAP BI в Azure. 

Azure для SAP HANA — это предложение, выделяющее Azure среди конкурентов. Чтобы включить размещение сценариев SAP, которые требуют больше памяти и ресурсов процессора и включают SAP HANA, Azure предлагает использовать чистое оборудование без операционной системы. Используйте это решение для выполнения развертываний SAP HANA, требующих до 24 ТБ (масштабирование до 120 ТБ) памяти для S/4HANA или другой рабочей нагрузки SAP HANA. 

Размещение сценариев рабочей нагрузки SAP в Azure также может требовать интеграции удостоверений и применения единого входа. Такая ситуация может возникнуть, если вы используете Azure Active Directory (Azure AD) для подключения различных компонентов SAPЕ, программного обеспечения SAP как услуги (SaaS) или платформы как услуги (PaaS). Список таких сценариев интеграции и единого входа с помощью сущностей Azure AD и SAP представлен в разделе "Интеграция AAD с приложением для управления удостоверениями SAP и единый вход".

## <a name="changes-to-the-sap-workload-section"></a>Изменения в разделе рабочей нагрузки SAP
Изменения в документах в разделе рабочей нагрузки SAP в Azure перечислены в конце этой статьи. Записи в журнале изменений хранятся около 180 дней.

## <a name="you-want-to-know"></a>Что вам нужно знать
Если у вас есть конкретные вопросы, мы укажем вам на определенные документы или последовательности в этом разделе начальной страницы. Что вам нужно знать

- Какие виртуальные машины Azure и единицы крупных экземпляров HANA поддерживаются, для каких выпусков программного обеспечения SAP и для каких версий операционных систем. Ответы на эти вопросы и порядок работы см. в разделе [Какое программное обеспечение SAP поддерживается в развертывании Azure](./sap-supported-product-on-azure.md).
- Какие сценарии развертывания SAP поддерживают на виртуальных машинах Azure и в крупных экземплярах HANA. Дополнительные сведения о поддерживаемых сценариях см. в следующих документах:
    - [Поддерживаемые сценарии использования рабочей нагрузки SAP в виртуальных машинах Azure](./sap-planning-supported-configurations.md)
    - [Поддерживаемые сценарии для крупных экземпляров HANA](./hana-supported-scenario.md)
- Информацию о том, что такое службы Azure, какие типы виртуальных машин и службы хранилища Azure доступны в разных регионах Azure, см. на сайте [Доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/). 
- Работают ли кадры высокой доступности сторонних производителей, помимо Windows и Pacemaker? Ознакомьтесь с [примечаниями о поддержке SAP](https://launchpad.support.sap.com/#/notes/1928533) в нижней части #1928533

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA в Azure (крупные экземпляры)

Серия документов описывает SAP HANA в Azure (крупные экземпляры) или, сокращенно, крупные экземпляры HANA. Изучите информацию о крупных экземплярах HANA в документе [Обзор и описание архитектуры SAP HANA в Azure (крупные экземпляры)](./hana-overview-architecture.md) и просмотрите соответствующие документы в разделе "Крупные экземпляры HANA".



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA на виртуальных машинах Azure
Этот раздел документации охватывает различные аспекты SAP HANA. Перед началом работы необходимо ознакомиться с понятием субъекта-службы в Azure, которая предоставляет элементарные службы Azure IaaS, то есть вы должны знать о вычислениях, хранении и сетях в Azure. Многие из этих тем рассматриваются в [руководстве по планированию Azure](./planning-guide.md) для SAP NetWeaver. 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Развертывание SAP NetWeaver на виртуальных машинах Azure
В этом разделе перечислены документы по планированию и развертыванию SAP NetWeaver и Business One в Azure. В этих документах основное внимание уделяется обзору и использованию баз данных, помимо HANA, с рабочей нагрузкой SAP в Azure. Кроме того, информацию о высокой доступности HANA в Azure содержат следующие документы и статьи.

- [Руководство по планированию Azure](./planning-guide.md). 
- [Использование SAP Business One на виртуальных машинах Azure](./business-one-azure.md)
- [Защита многоуровневого развертывания приложения SAP NetWeaver с помощью Site Recovery](../../../site-recovery/site-recovery-sap.md)
- [Соединитель SAP LaMa для Azure](./lama-installation.md)

Информацию о базах данных, кроме HANA, с рабочей нагрузкой SAP в Azure см. в следующих статьях.

- [Вопросы развертывания СУБД для рабочей нагрузки SAP на виртуальных машинах Azure](./dbms_guide_general.md)
- [SAP NetWeaver на виртуальных машинах Azure. Руководство по развертыванию СУБД SQL Server](./dbms_guide_sqlserver.md)
- [Рабочие нагрузки SAP на Виртуальных машинах Azure. Руководство по развертыванию СУБД Oracle](./dbms_guide_oracle.md)
- [Рабочие нагрузки SAP на Виртуальных машинах Azure. Руководство по развертыванию СУБД IBM DB2](./dbms_guide_ibm.md)
- [Рабочие нагрузки SAP на Виртуальных машинах Azure. Руководство по развертыванию СУБД SAP ASE](./dbms_guide_sapase.md)
- [Развертывание SAP MaxDB, liveCache и сервера содержимого на виртуальных машинах Azure](./dbms_guide_maxdb.md)

Информацию о базах данных SAP HANA в Azure см. в разделе "SAP HANA на виртуальных машинах Azure".

Информацию о высокой доступности рабочей нагрузки SAP в Azure см. в следующем документе:

- [Руководство по обеспечению высокого уровня доступности SAP NetWeaver на виртуальных машинах Azure](./sap-high-availability-guide-start.md)

В этом документе упоминаются документы для других архитектур и сценариев. В указанных ниже документах со сценариями предоставляются ссылки на подробные технические документы, объясняющие развертывание и настройку различных методов обеспечения высокого уровня доступности. Различные документы по установке и настройке высокого уровня доступности для рабочей нагрузки SAP NetWeaver относятся к операционным системам Linux и Windows.


Информацию об интеграции Azure Active Directory (Azure AD) со службами SAP и единым входом см. в следующих документах:

- [Руководство. по интеграции Azure Active Directory с SAP Cloud for Customer](../../../active-directory/saas-apps/sap-customer-cloud-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. по интеграции Azure Active Directory с приложением SAP Cloud Platform Identity Authentication](../../../active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. по интеграции Azure Active Directory с SAP Cloud Platform](../../../active-directory/saas-apps/sap-hana-cloud-platform-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. Интеграция Azure Active Directory с SAP NetWeaver](../../../active-directory/saas-apps/sap-netweaver-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. по интеграции Azure Active Directory с SAP Business ByDesign](../../../active-directory/saas-apps/sapbusinessbydesign-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Руководство. интеграции Azure Active Directory с SAP HANA](../../../active-directory/saas-apps/saphana-tutorial.md?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Ваша среда S/4HANA: единый вход SAML с панели запуска Fiori при помощи Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Информацию об интеграции служб Azure в компоненты SAP см. в следующих документах:

- [Использование SAP HANA в Power BI Desktop](/power-bi/desktop-sap-hana)
- [DirectQuery и SAP HANA](/power-bi/desktop-directquery-sap-hana)
- [Использование соединителя SAP BW в Power BI Desktop](/power-bi/desktop-sap-bw-connector) 
- [Возможность интеграции данных SAP HANA и Business Warehouse в Фабрике данных Azure](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Журнал изменений

- 08/04/2020: измените [настройку Pacemaker в SLES в Azure](./high-availability-guide-suse-pacemaker.md) и [настроив Pacemaker на RHEL в Azure](./high-availability-guide-rhel-pacemaker.md) , чтобы подчеркнуть важность надежного разрешения имен для кластеров Pacemaker.
- 08/04/2020: изменение в [SAP NW ha в вфкс с общей папкой](./sap-high-availability-installation-wsfc-file-share.md), [SAP NW HA на вфкс с общим диском](./sap-high-availability-installation-wsfc-shared-disk.md), [HA для SAP NW на виртуальных машинах Azure](./high-availability-guide.md), [HA для SAP NW на виртуальных машинах Azure в SLES](./high-availability-guide-suse.md), высокая доступность для SAP NW [на виртуальных машинах Azure в SLES с использовании](./high-availability-guide-suse-netapp-files.md), [HA для SAP NW на виртуальных машинах Azure по SLES Multi-SID](./high-availability-guide-suse-multi-sid.md), [высокий уровень доступности для SAP NetWeaver на](./high-availability-guide-rhel.md)виртуальных машинах Azure в RHEL, HA для SAP NW на виртуальных машинах Azure в [RHEL с использовании](./high-availability-guide-rhel-netapp-files.md) и [HA для SAP NW на виртуальных машинах Azure в RHEL Multi-ИД безопасности](./high-availability-guide-rhel-multi-sid.md) для уточнения использования параметра`enque/encni/set_so_keepalive`
- 07/23/2020: добавлена статья " [Сохранение в SAP HANA (крупные экземпляры) с резервированием Azure](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md) ", объясняющая, что необходимо знать перед покупкой SAP HANA (крупные экземпляры) резервирования и о том, как выполнить покупку.
- 07/16/2020: Описание использования Azure PowerShell для установки нового расширения виртуальной машины для SAP в руководство по [развертыванию](deployment-guide.md)
- 7/04/2020: выпуск [Azure Monitor для решений SAP (Предварительная версия)](./azure-monitor-overview.md)
- 07/01/2020: рекомендуется настроить менее дорогостоящую конфигурацию хранилища на основе функции ускорения хранилища Azure класса Premium в документе [SAP HANA конфигурациях хранилища виртуальных машин Azure](./hana-vm-operations-storage.md) . 
- 06/24/2020: измените [настройку Pacemaker на SLES в Azure](./high-availability-guide-suse-pacemaker.md) , чтобы освободить новый улучшенный агент ограждения Azure и более устойчивую конфигурацию STONITH для устройств на основе агента ограждения Azure. 
- 06/24/2020: измените [настройку Pacemaker для RHEL в Azure](./high-availability-guide-rhel-pacemaker.md) , чтобы освободить более устойчивую конфигурацию STONITH
- 06/23/2020. изменения в руководство по [планированию и реализации SAP NetWeaver на виртуальных машинах Azure](./planning-guide.md) и общие сведения о [типах хранилищ Azure для рабочих нагрузок SAP](./planning-guide-storage.md)
- 06/22/2020. Добавление шагов установки для нового расширения виртуальной машины для SAP в [руководство по развертыванию](deployment-guide.md)
- 06/16/2020: измените [Подключение к общедоступной конечной точке для виртуальных машин с помощью Azure Standard ilB в сценариях SAP Ha](./high-availability-guide-standard-load-balancer-outbound-connections.md) , чтобы добавить ссылку на документацию по инфраструктуре общедоступного облака SUSE 101 
- 06/10/2020: Добавление новых номеров SKU ХЛИ в [Доступные номера SKU для](./hana-available-skus.md) [архитектуры хранилища Хли и SAP HANA (крупные экземпляры)](./hana-storage-architecture.md)
- 21.05.2020: в статьи [Настройка Pacemaker в SLES в Azure](./high-availability-guide-suse-pacemaker.md) и [Настройка Pacemaker в RHEL в Azure](./high-availability-guide-rhel-pacemaker.md) добавлена ссылка на документ [Подключение к общедоступной конечной точке для виртуальных машин с помощью стандартного внутреннего балансировщика нагрузки Azure в сценариях обеспечения высокого уровня доступности SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  
- 19.05.2020: в статью [Конфигурации хранилища виртуальных машин SAP HANA в Azure](./hana-vm-operations-storage.md) добавлено важное указание не использовать корневую группу томов при использовании LVM для связанных томов HANA.
- 05/19/2020: добавьте новую поддерживаемую ОС для типа крупных экземпляров HANA II в [совместимые операционные системы для крупных экземпляров Hana](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) .
- 12.05.2020: в статье [Подключение к общедоступной конечной точке для виртуальных машин с помощью стандартного балансировщика нагрузки Azure в сценариях обеспечения высокого уровня доступности SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) обновлены ссылки и добавлена информация о настройке стороннего брандмауэра.
- 11.05.2020: в статье [Высокий уровень доступности SAP HANA на виртуальных машинах Azure в SLES](./sap-hana-high-availability.md) закрепление ресурса netcat изменили на 0, поскольку это обеспечивает более оптимизированную отработку отказа. 
- 05.05.2020: в статью [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению](./planning-guide.md) добавлена информация о том, что развертывания 2-го поколения теперь доступны для семейства виртуальных машин Mv1.
- 24.04.2020: в статьи [Горизонтальное масштабирование SAP HANA с использованием резервного узла на виртуальных машинах с ANF в SLES](./sap-hana-scale-out-standby-netapp-files-suse.md), [Горизонтальное масштабирование SAP HANA с использованием резервного узла на виртуальных машинах с ANF в RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md), [Обеспечение высокой доступности SAP NetWeaver на виртуальных машинах Azure в SLES с ANF](./high-availability-guide-suse-netapp-files.md) и [Обеспечение высокой доступности SAP NetWeaver на виртуальных машинах Azure в RHEL с ANF](./high-availability-guide-rhel-netapp-files.md) добавлено пояснение о том, что IP-адреса томов ANF назначаются автоматически.
- 22.04.2020: в статье [Обеспечение высокой доступности SAP HANA на виртуальных машинах Azure в SLES](./sap-hana-high-availability.md) из инструкций удален атрибут метаданных `is-managed`, поскольку он конфликтует с размещением кластера с включенным или выключенным режимом обслуживания.
- 21.04.2020: в статьях [Программное обеспечение SAP, поддерживаемое для развертываний Azure](./sap-supported-product-on-azure.md) и [Сертификаты и конфигурации SAP на платформе Microsoft Azure](./sap-certifications.md) в список СУБД, поддерживаемых SAP (Hybris) Commerce Platform 1811 и более поздних версий, добавлена база данных SQL Azure.
- 16.04.2020: в статьях [Программное обеспечение SAP, поддерживаемое для развертываний Azure](./sap-supported-product-on-azure.md) и [Сертификаты и конфигурации SAP на платформе Microsoft Azure](./sap-certifications.md) в список СУБД, поддерживаемых SAP (Hybris) Commerce Platform, добавили SAP HANA.
- 13.04.2020: в статье [Рабочие нагрузки SAP на виртуальных машинах Azure. Руководство по развертыванию СУБД SAP ASE](./dbms_guide_sapase.md) исправлены номера выпусков SAP ASE.
- 07.04.2020: в статью [Настройка Pacemaker в SLES в Azure](./high-availability-guide-suse-pacemaker.md) добавлены пояснения к инструкциям cloud-netconfig-azure.
- 06.04.2020: в статьях [Горизонтальное масштабирование SAP HANA с использованием резервного узла на виртуальных машинах с Azure NetApp Files в SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) и [Горизонтальное масштабирование SAP HANA с использованием резервного узла на виртуальных машинах с Azure NetApp Files в RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md) удалены ссылки на NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) (заменены на [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf)).
- 31.03.2020: в статьи [Высокий уровень доступности SAP HANA на виртуальных машинах Azure в SLES](./sap-hana-high-availability.md) и [Высокий уровень доступности SAP HANA на виртуальных машинах Azure в RHEL](./sap-hana-high-availability-rhel.md) добавлены инструкции по указанию размера полосы при создании чередующихся томов.
- 27.03.2020: в статье [Высокий уровень доступности SAP NW на виртуальных машинах Azure в SLES с ANF для приложений SAP](./high-availability-guide-suse-netapp-files.md) параметры файловой системы приведены в соответствие с NetApp TR-4746 (удален параметр синхронизации подключения).
- 26.03.2020: в статью [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в SLES с несколькими ИД безопасности](./high-availability-guide-suse-multi-sid.md) добавлено указание на NetApp TR-4746.
- 03/26/2020: изменение [высокого уровня доступности SAP NetWeaver на виртуальных машинах Azure в SLES для приложений SAP](./high-availability-guide-suse.md), [Высокая доступность для SAP NetWeaver на виртуальных машинах Azure в SLES с Azure NETAPP Files для приложений SAP](./high-availability-guide-suse-netapp-files.md) [высокий уровень доступности NFS на ВИРТУАЛЬНЫХ машинах Azure в SLES](./high-availability-guide-suse-nfs.md), [высокий уровень доступности SAP NETWEAVER на виртуальных МАШИНАХ Azure в RHEL с несколькими ИД безопасности](./high-availability-guide-suse-multi-sid.md), [высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в RHEL для приложений SAP](./high-availability-guide-rhel.md) и [высокий уровень доступности для SAP NetWeaver на виртуальных машинах Azure в RHEL с Azure NetApp Files для приложений SAP](./high-availability-guide-rhel-netapp-files.md) для обновления диаграмм и уточнения инструкций для Azure Load Balancer создания серверного пула
- 19.03.2020: документ [Краткое руководство. Установка одного экземпляра SAP HANA вручную на виртуальных машинах Azure](./hana-get-started.md) заменили на статью [Установка SAP HANA на виртуальных машинах Azure](./hana-get-started.md).
- 17.03.2020: в статье [Настройка Pacemaker в SUSE Linux Enterprise Server в Azure](./high-availability-guide-suse-pacemaker.md) удален параметр настройки SBD, который больше не требуется.
- 16.03.2020: в статью [Программное обеспечение SAP, поддерживаемое для развертываний Azure](./sap-supported-product-on-azure.md) добавлено разъяснение сценария сертификации столбцов на сертифицированной платформе SAP HANA IaaS.
- 11.03.2020: в статью [Поддерживаемые сценарии использования рабочей нагрузки SAP в виртуальных машинах Azure](./sap-planning-supported-configurations.md) добавлено пояснение о поддержке нескольких баз данных на экземпляр СУБД.
- 11.03.2020: в статью [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению](./planning-guide.md) добавлена информация о виртуальных машинах 1-го и 2-го поколения.
- 10.03.2020: в статью [Конфигурации хранилища виртуальных машин SAP HANA в Azure](./hana-vm-operations-storage.md) добавление пояснение о фактических ограничениях на пропускную способность ANF.
- 09.03.2020: в статьях [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в SUSE Linux Enterprise Server для приложений SAP](./high-availability-guide-suse.md), [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в SUSE Linux Enterprise Server с Azure NetApp Files для приложений SAP](./high-availability-guide-suse-netapp-files.md), [Высокий уровень доступности NFS на виртуальных машинах Azure в SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md), [Настройка Pacemaker в SUSE Linux Enterprise Server в Azure](./high-availability-guide-suse-pacemaker.md), [Высокий уровень доступности IBM Db2 LUW на виртуальных машинах Azure в SUSE Linux Enterprise Server с Pacemaker](./dbms-guide-ha-ibm.md), [Высокий уровень доступности SAP HANA на виртуальных машинах Azure в SUSE Linux Enterprise Server](./sap-hana-high-availability.md) и [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в SLES с несколькими ИД безопасности](./high-availability-guide-suse-multi-sid.md) обновили кластерные ресурсы, включив агент ресурсов azure-lb. 
- 05.03.2020: в статье [SAP NetWeaver на виртуальных машинах Windows. Руководство по планированию и внедрению](./planning-guide.md) изменена структура, а также информация о регионах и виртуальных машинах Azure.
- 03.03.2020: в статью [Высокий уровень доступности SAP NW на виртуальных машинах Azure в SLES с ANF для приложений SAP](./high-availability-guide-suse-netapp-files.md) добавлена более эффективная структура тома ANF.
- 01.03.2020: в статью [Руководство по резервному копированию SAP HANA на виртуальных машинах Azure](./sap-hana-backup-guide.md) добавили службу резервного копирования Azure. Сокращено и сжато содержание статьи [Резервное копирование SAP HANA на уровне файлов](./sap-hana-backup-file-level.md), удален третий документ, в котором говорится о резервном копировании с помощью моментального снимка диска. Содержание этого документа перенесли в руководство по резервному копированию SAP HANA на виртуальных машинах Azure. 
- 27 февраля 2020: изменение [высокого уровня доступности SAP NW на виртуальных машинах Azure в SLES для приложений SAP](./high-availability-guide-suse.md), [Высокая доступность для SAP NW на виртуальных машинах Azure в SLES с использовании для приложений SAP](./high-availability-guide-suse-netapp-files.md) и [высокий уровень доступности для SAP NetWeaver на ВИРТУАЛЬНЫХ машинах Azure в SLES с несколькими ИД безопасности](./high-availability-guide-suse-multi-sid.md) для настройки параметра кластера "при сбое"
- 26 февраля 2020 г.: изменение [SAP HANA конфигураций хранилища виртуальных машин Azure](./hana-vm-operations-storage.md) для уточнения выбора файловой системы для Hana в Azure
- 26 февраля 2020. изменение [архитектуры высокого уровня доступности и сценариев для SAP](./sap-high-availability-architecture-scenarios.md) включает ссылку на службу поддержки SAP NetWeaver на виртуальных машинах Azure по RHEL Multi-SID.
- 26 февраля 2020: изменение высокого уровня [доступности SAP NW на виртуальных машинах Azure в SLES для приложений](./high-availability-guide-suse.md)SAP, [высокий уровень доступности для SAP NW на виртуальных машинах Azure в SLES с использовании для приложений SAP](./high-availability-guide-suse-netapp-files.md), [Высокая доступность виртуальных машин Azure для SAP NetWeaver на RHEL](./high-availability-guide-rhel.md) и [Высокая доступность ВИРТУАЛЬНЫХ машин azure для SAP NetWeaver на RHEL с Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) для удаления инструкции, которая не поддерживается несколькими идентификаторами ASCS/ERS.
- 26 февраля 2020: выпуск высокого уровня [доступности SAP NetWeaver на виртуальных машинах Azure в RHEL с несколькими ИД безопасности](./high-availability-guide-rhel-multi-sid.md) для добавления ссылки на руководство по использованию кластера SUSE Multi-SID
- 25.02.2020: в статью [Архитектура и сценарии обеспечения высокого уровня доступности для SAP](./sap-high-availability-architecture-scenarios.md) добавлены ссылки на более новые статьи о высоком уровне доступности.
- 25 февраля 2020 г.: изменение [высокого уровня доступности IBM DB2 LUW на виртуальных машинах Azure на SUSE Linux Enterprise Server с Pacemaker](./dbms-guide-ha-ibm.md) для указания документа, описывающего доступ к общедоступной конечной точке с помощью стандартного балансировщика нагрузки Azure
- 21 февраля 2020 г. — Полная версия статьи [SAP ASE Azure Virtual Machines Deployment for SAP](./dbms_guide_sapase.md)
- 21 февраля 2020 г. изменение [конфигурации хранилища виртуальных машин Azure SAP HANA](./hana-vm-operations-storage.md) для представления новой рекомендации в размере чередования для/Hana/Data и добавление параметров планировщика операций ввода-вывода
- 21 февраля 2020: изменения в документах крупных экземпляров HANA для представления новых сертифицированных номеров SKU S224 и S224m
- 21 февраля 2020: изменение [высокой доступности виртуальных машин Azure для SAP NetWeaver на RHEL](./high-availability-guide-rhel.md) и [виртуальных машинах Azure для SAP NetWeaver на RHEL с Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) для настройки ограничений кластера для архитектуры репликации сервера постановки в очередь 2 (ENSA2)
- 20 февраля 2020. изменение [высокого уровня доступности SAP NetWeaver на виртуальных машинах Azure в SLES с несколькими ИД безопасности](./high-availability-guide-suse-multi-sid.md) для добавления ссылки на руководство по использованию кластера SUSE Multi-SID
- 13 февраля 2020 г. изменения [виртуальных машин Azure планирование и реализация SAP NetWeaver](./planning-guide.md) для реализации ссылок на новые документы
- 13 февраля 2020 г. Добавлен новый документ [Рабочая нагрузка SAP в сценарии, поддерживаемом виртуальной машиной Azure](./sap-planning-supported-configurations.md)
- 13 февраля 2020: добавлен новый документ [, в котором программное обеспечение SAP поддерживается для развертывания Azure](./sap-supported-product-on-azure.md) .
- 13 февраля 2020: изменение [высокого уровня доступности IBM DB2 LUW на виртуальных машинах Azure на сервере Red Hat Enterprise Linux](./high-availability-guide-rhel-ibm-db2-luw.md) для указания документа, который описывает доступ к общедоступной конечной точке с помощью стандартного балансировщика нагрузки Azure
- 13 февраля 2020 г.: Добавление новых типов виртуальных машин в [Сертификаты и конфигурации SAP, выполняемые на Microsoft Azure](./sap-certifications.md)
- 13 февраля 2020 г.: Добавление новых заметок о поддержке SAP [рабочие нагрузки SAP в Azure: контрольный список для планирования и развертывания](./sap-deployment-checklist.md)
- 13 февраля 2020: изменение [высокой доступности виртуальных машин Azure для SAP NetWeaver на RHEL](./high-availability-guide-rhel.md) и [виртуальных машинах Azure для SAP NetWeaver на RHEL с Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) для согласования времени ожидания ресурсов кластера с рекомендациями по времени ожидания Red Hat
- 11 февраля 2020 г.: выпуск [SAP HANA с миграцией крупных экземпляров Azure на виртуальные машины Azure](./hana-large-instance-virtual-machine-migration.md)
- 07 февраля 2020: изменение [подключения к общедоступной конечной точке для виртуальных машин с помощью Azure Standard ilB в сценариях с высоким уровнем доступности SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md) для обновления демонстрационного снимка экрана NSG
- 03 февраля 2020: изменение [высокого уровня доступности SAP NW на виртуальных машинах Azure в SLES для приложений SAP](./high-availability-guide-suse.md) и [высокий уровень доступности для SAP NW на виртуальных машинах Azure в SLES с использовании для приложений SAP](./high-availability-guide-suse-netapp-files.md) для устранения предупреждения об использовании тире в именах узлов кластеров в SLES
- 28 января 2020: изменение [высокого уровня доступности SAP HANA на виртуальных машинах Azure в RHEL](./sap-hana-high-availability-rhel.md) для согласования времени ожидания ресурсов кластера SAP HANA с рекомендациями по времени ожидания Red Hat
- 17 января 2020 г. изменение в [группах размещения Azure для обеспечения оптимальной задержки в сети с помощью приложений SAP](./sap-proximity-placement-scenarios.md) для изменения раздела перемещения существующих виртуальных машин в группу размещения с учетом расположения
- 17 января 2020: изменение [конфигураций рабочих нагрузок SAP с зоны доступности Azure](./sap-ha-availability-zones.md) для указания процедуры, которая автоматизирует измерения задержки между зоны доступности
- 16 января 2020 г. изменение [способа установки и настройки SAP HANA (крупные экземпляры) в Azure](./hana-installation.md) для адаптации выпусков ОС в каталог оборудования IaaS для Hana
- 16 января 2020 г. изменения в [высоком уровне доступности SAP NetWeaver на виртуальных машинах Azure в SLES с несколькими ИД безопасности](./high-availability-guide-suse-multi-sid.md) для добавления инструкций для систем SAP с использованием архитектуры Server 2 для постановки в очередь (ENSA2)
- 10 января 2020. изменения [SAP HANA горизонтальном масштабировании с резервным узлом на виртуальных машинах Azure с Azure NetApp Files на SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) и в [SAP HANA масштабирования с резервным узлом на виртуальных машинах Azure с Azure NetApp Files на RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md) , чтобы добавить инструкции о том, как сделать `nfs4_disable_idmapping` изменения постоянными.
- 10 января 2020. изменения в [высоком уровне доступности SAP NetWeaver на виртуальных машинах Azure в SLES с Azure NetApp Files для приложений SAP](./high-availability-guide-suse-netapp-files.md) и на [виртуальных машинах Azure высокий уровень доступности для SAP NetWeaver на RHEL с Azure NETAPP Files для приложений SAP](./high-availability-guide-rhel-netapp-files.md) добавьте инструкции по подключению томов Azure NetApp Files NFSv4.
- 23 декабря 2019 года: выпуск статьи [Высокий уровень доступности SAP NetWeaver на виртуальных машинах в SLES с несколькими ИД безопасности](./high-availability-guide-suse-multi-sid.md).
- 18 декабря 2019 года: выпуск статьи [Горизонтальное масштабирование SAP HANA с использованием резервного узла на виртуальных машинах с Azure NetApp Files в RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md).
- 21 ноября 2019 года: в статью [Горизонтальное масштабирование SAP HANA с использованием резервного узла на виртуальных машинах с Azure NetApp Files на SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md) внесли изменения, чтобы упростить настройку сопоставления удостоверений NFS, и изменили рекомендуемый основной сетевой интерфейс для упрощения маршрутизации.
- 15 ноября 2019 года: в статьи [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в SUSE Linux Enterprise Server с Azure NetApp Files для приложений SAP](high-availability-guide-suse-netapp-files.md) и [Высокий уровень доступности SAP NetWeaver в Red Hat Enterprise Linux с Azure NetApp Files для приложений SAP](high-availability-guide-rhel-netapp-files.md) добавлены пояснения об ограничениях на размер пула емкости и удалено утверждение о том, что поддерживается только версия NFSv3.
- 12 ноября 2019 года: выпуск статьи [Высокий уровень доступности SAP NetWeaver в Windows с Azure NetApp Files (SMB)](high-availability-guide-windows-netapp-files-smb.md).
- 8 ноября 2019 года: в статье [Высокий уровень доступности SAP HANA на виртуальных машинах Azure в SUSE Linux Enterprise Server](sap-hana-high-availability.md), [Настройка репликации системы SAP HANA на виртуальных машинах Azure](sap-hana-high-availability-rhel.md), [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в SUSE Linux Enterprise Server для приложений SAP](high-availability-guide-suse.md), [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в SUSE Linux Enterprise Server с Azure NetApp Files](high-availability-guide-suse-netapp-files.md), [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в Red Hat Enterprise Linux](high-availability-guide-rhel.md), [Высокий уровень доступности SAP NetWeaver на виртуальных машинах Azure в Red Hat Enterprise Linux с Azure NetApp Files](high-availability-guide-rhel-netapp-files.md), [Высокий уровень доступности NFS на виртуальных машинах в SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS на виртуальных машинах в Red Hat Enterprise Linux для SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) добавлена рекомендация использовать стандартный балансировщик нагрузки Azure.  
- 8 ноября 2019 года: в статью [Контрольный список для планирования и развертывания рабочей нагрузки SAP](sap-deployment-checklist.md) добавлено разъяснение рекомендации по шифрованию.  
- 4 ноября 2019 года: в статью [Настройка Pacemaker в SUSE Linux Enterprise Server в Azure](high-availability-guide-suse-pacemaker.md) добавлено указание о том, что кластеры должны создаваться с одноадресной конфигурацией.  
