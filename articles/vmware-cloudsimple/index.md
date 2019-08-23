---
title: Решение VMware в Azure от CloudSimple
description: Портал документации по решению VMware в Azure от CloudSimple.
author: sharaths-cs
ms.author: b-mashar
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 936a9eb4d9b33b45800fab213fc72a306a04146e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972738"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>Решение VMware в Azure от CloudSimple

Вас приветствует объединенный портал поддержки по решению VMware в Azure от CloudSimple.
На этом сайте документации вы можете изучить следующие темы.

## <a name="overview"></a>Обзор

Дополнительные сведения о решении VMware в Azure от CloudSimple.

* Дополнительные сведения о функциях, преимуществах и сценариях использования см. в статье [What is Azure VMware Solution by CloudSimple](cloudsimple-vmware-solutions-overview.md) (Что такое решение VMware в Azure от CloudSimple).
* Изучите [основные концепции администрирования](key-concepts.md).

## <a name="quickstart"></a>Краткое руководство

Узнайте, как начать работу с этим решением.

* Узнайте, как [инициализировать службу и приобрести емкость](quickstart-create-cloudsimple-service.md).
* Узнайте, как создать среду VMware, изучив статью [Configure a Private Cloud Environment](quickstart-create-private-cloud.md) (Настройка среды частного облака).
* Узнайте, как унифицировать управление между VMware и Azure, изучив статью [Consume VMware VMs on Azure](quickstart-create-vmware-virtual-machine.md) (Использование виртуальных машин VMware в Azure).

## <a name="concepts"></a>Основные понятия

Подробнее изучите перечисленные ниже концепции.

* [Служба CloudSimple](cloudsimple-service.md), также известная как "Решение VMware в Azure от CloudSimple — служба". Этот ресурс следует создавать отдельно для каждого региона.
* Приобретите емкость для среды, создав один или несколько ресурсов [Узел CloudSimple](cloudsimple-node.md). Эти ресурсы указываются как "Решение VMware в Azure от CloudSimple — узел".
* Инициализируйте и настройте среду VMware с помощью [частных облаков.](cloudsimple-private-cloud.md)
* Унифицируйте управление с помощью [виртуальных машин CloudSimple](cloudsimple-virtual-machines.md), которые указываются как "Решение VMware в Azure от CloudSimple — виртуальная машина".
* Создайте базовую сеть на основе [виртуальных сетей и подсетей](cloudsimple-vlans-subnets.md).
* Разделите и защитите базовую сеть с помощью ресурса [Таблица брандмауэра](cloudsimple-firewall-tables.md).
* Получите безопасный доступ к средам VMware через глобальную сеть с помощью [VPN-шлюзов](cloudsimple-vpn-gateways.md).
* Включите общий доступ для рабочих нагрузок с помощью [общедоступного IP-адреса.](cloudsimple-public-ip-address.md)
* Установите подключение к виртуальным сетям Azure и (или) локальным сетям с помощью [сетевого подключения Azure](cloudsimple-azure-network-connection.md).
* Настройте цели для оповещений по электронной почте с помощью [управления учетными записями](cloudsimple-account.md).
* Просмотрите журналы действий пользователя и системы с помощью экранов [управления действиями](cloudsimple-activity.md).
* Изучите разные [компоненты VMware](vmware-components.md).

## <a name="tutorials"></a>Учебники

Узнайте, как выполнять стандартные задачи:

* [Создайте службу CloudSimple](create-cloudsimple-service.md), по одной для каждого региона, в котором вы будете развертывать среды VMware.
* Настройте базовые функции службы на [портале CloudSimple](access-cloudsimple-portal.md).
* Добавьте емкость и оптимизируйте расходы на инфраструктуру, [приобретая узлы CloudSimple](create-nodes.md).
* Управляйте конфигурациями среды VMware с помощью частных облаков. Вы можете [создавать](create-private-cloud.md), [администрировать](manage-private-cloud.md), [расширять](expand-private-cloud.md) и [сжимать](shrink-private-cloud.md) частные облака.
* Организуйте унифицированное управление, настроив [сопоставление подписок Azure](azure-subscription-mapping.md).
* Наблюдение за активностью пользователей и системы с помощью [страниц действий](monitor-activity.md).
* Настройте сетевое окружение для сред, [создавая подсети и управляя ими](create-vlan-subnet.md).
* Разделите и защитите среды с помощью [таблиц и правил брандмауэра](firewall.md).
* Настройте входящий доступ из Интернета для рабочих нагрузок, [выделив общедоступные IP-адреса](public-ips.md).
* Настройте подключение из внутренних сетей или с клиентских рабочих станций через [VPN-подключение](vpn-gateway.md).
* Включите обмен данными между [локальными средами](on-premises-connection.md) и [виртуальными сетями Azure](virtual-network-connection.md).
* Настройте целевые объекты оповещений и контролируйте общую приобретенную емкость через [сводку по учетной записи](account.md).
* Проверьте, какие [пользователи](users.md) обращались к порталу CloudSimple.
* Управляйте виртуальными машинами VMware на портале Azure.
    * [Создайте виртуальные машины](azure-create-vm.md) на портале Azure.
    * [Управляйте виртуальными машинами](azure-manage-vm.md), которые вы только что создали.

## <a name="how-to-guides"></a>Руководства

В следующих руководствах рассматриваются решения для выполнения конкретных задач:

* [Защитите свою среду](private-cloud-secure.md).
* Установите сторонние средства, настройте дополнительных пользователей и внешний источник проверки подлинности для vSphere с помощью [укрупнения привилегий](escalate-privileges.md).
* Настройте доступ к разным службам VMware через [локальную службу DNS](on-premises-dns-setup.md).
* Включите распределение имен и адресов для рабочих нагрузок, [настроив DNS и DHCP для рабочих нагрузок](dns-dhcp-setup.md).
* Узнайте, как служба поддерживает безопасность и функциональность для вашей платформы с помощью [обновлений службы](vmware-components.md#updates-and-upgrades).
* Снижайте совокупную стоимость владения для резервных копий, создав архитектуру резервного копирования на основе [стороннего программного обеспечения для резервного копирования, например Veeam](backup-workloads-veeam.md).
* Создайте безопасную среду, включив шифрование неактивных данных с помощью [стороннего программного обеспечения KMS для шифрования](vsan-encryption.md).
* Расширьте возможности управления Azure Active Directory (AAD) в VMware, настроив [источник удостоверений Azure AD](azure-ad.md).
