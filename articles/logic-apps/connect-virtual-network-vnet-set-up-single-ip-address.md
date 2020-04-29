---
title: Настройка общедоступного исходящего IP-адреса для Исес
description: Узнайте, как настроить единый общедоступный исходящий IP-адрес для сред службы интеграции (Исес) в Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77191488"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Настройте один IP-адрес для одной или нескольких сред службы интеграции в Azure Logic Apps

При работе с Azure Logic Apps можно настроить [ *среду службы интеграции* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) для размещения приложений логики, которым требуется доступ к ресурсам в [виртуальной сети Azure](../virtual-network/virtual-networks-overview.md). При наличии нескольких экземпляров ISE, которым требуется доступ к другим конечным точкам с ограничениями IP-адресов, разверните [брандмауэр Azure](../firewall/overview.md) или [сетевой виртуальный](../virtual-network/virtual-networks-overview.md#filter-network-traffic) модуль в виртуальной сети и направьте исходящий трафик через этот брандмауэр или сетевой виртуальный модуль. После этого все экземпляры ISE в виртуальной сети используют один, Открытый, статический и прогнозируемый IP-адрес для связи с конечными системами. Таким образом, вам не придется настраивать дополнительные пути брандмауэра в конечных системах для каждой интегрированной среды сценариев.

В этом разделе показано, как маршрутизировать исходящий трафик через брандмауэр Azure, но вы можете применить аналогичные концепции к сетевому виртуальному устройству, такому как сторонний брандмауэр из Azure Marketplace. Хотя в этом разделе основное внимание уделяется установке нескольких экземпляров ISE, этот подход можно также использовать для одной интегрированной среды сценариев, если в сценарии требуется ограничить количество IP-адресов, которым требуется доступ. Определите, имеет ли смысл дополнительные затраты на брандмауэр или устройство виртуальной сети для вашего сценария. Дополнительные сведения о [ценах на брандмауэр Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Предварительные условия

* Брандмауэр Azure, работающий в той же виртуальной сети, что и интегрированная среда сценариев. Если у вас нет брандмауэра, сначала [добавьте подсеть](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) с именем `AzureFirewallSubnet` в виртуальную сеть. Затем можно [создать и развернуть брандмауэр](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) в виртуальной сети.

* [Таблица маршрутизации](../virtual-network/manage-route-table.md)Azure. Если у вас ее нет, сначала [Создайте таблицу маршрутов](../virtual-network/manage-route-table.md#create-a-route-table). Дополнительные сведения о маршрутизации см. в разделе [Маршрутизация трафика виртуальной сети](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Настройка таблицы маршрутов

1. В [портал Azure](https://portal.azure.com)выберите таблицу маршрутов, например:

   ![Выбор таблицы маршрутов с правилом для направления исходящего трафика](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Чтобы [Добавить новый маршрут](../virtual-network/manage-route-table.md#create-a-route), в меню Таблица маршрутов выберите **маршруты** > **Добавить**.

   ![Добавление маршрута для прямого исходящего трафика](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. На панели **Добавление маршрута** [Настройте новый маршрут](../virtual-network/manage-route-table.md#create-a-route) с помощью правила, которое указывает, что весь исходящий трафик к целевой системе следует этому поведению:

   * Использует [**виртуальное устройство**](../virtual-network/virtual-networks-udr-overview.md#user-defined) в качестве типа следующего прыжка.

   * Переходит к частному IP-адресу экземпляра брандмауэра в качестве адреса следующего прыжка.

     Чтобы найти этот IP-адрес, в меню брандмауэра выберите **Обзор**, найдите адрес в разделе **частный IP-адрес**, например:

     ![Поиск частного IP-адреса брандмауэра](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Ниже приведен пример того, как может выглядеть такое правило:

   ![Настройка правила для направления исходящего трафика](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Свойство | Значение | Описание |
   |----------|-------|-------------|
   | **Имя маршрута** | <*уникальный-Route-Name*> | Уникальное имя маршрута в таблице маршрутов |
   | **Префикс адреса** | <*адрес назначения*> | Адрес целевой системы, куда должен быть отправлен трафик. Убедитесь, что для этого адреса используется [нотация между междоменной маршрутизацией (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . |
   | **Тип следующего прыжка** | **Виртуальный модуль** | [Тип прыжка](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) , используемый исходящим трафиком |
   | **Адрес следующего прыжка** | <*Firewall-частный-IP-адрес*> | Частный IP-адрес для брандмауэра |
   |||

## <a name="set-up-network-rule"></a>Настройка правила сети

1. В портал Azure найдите и выберите свой брандмауэр. В меню "брандмауэр" в разделе " **Параметры**" выберите " **правила**". На панели правила выберите >  **коллекция сетевых правил****Добавить коллекцию сетевых правил**.

   ![Добавление коллекции правил сети в брандмауэр](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. В коллекции добавьте правило, разрешающее трафик в целевую систему.

   Например, предположим, что у вас есть приложение логики, которое работает в интегрированной среде сценариев и должно взаимодействовать с системой SFTP. Вы создаете коллекцию сетевых правил с именем `LogicApp_ISE_SFTP_Outbound`, которая содержит сетевое правило с именем `ISE_SFTP_Outbound`. Это правило разрешает трафик с IP-адреса любой подсети, в которой работает интегрированная среда сценариев, в вашей виртуальной сети с использованием частного IP-адреса брандмауэра.

   ![Настройка сетевого правила для брандмауэра](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Свойства коллекции сетевых правил**

   | Свойство | Значение | Описание |
   |----------|-------|-------------|
   | **имя**; | <*сеть-Rule-Collection-Name*> | Имя коллекции правил сети |
   | **Priority** | <*уровень приоритета*> | Порядок приоритета, используемый для запуска коллекции правил. Дополнительные сведения см. в статье [что такое основные понятия брандмауэра Azure](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)? |
   | **Действие** | **Разрешить** | Тип действия, выполняемого для этого правила |
   |||

   **Свойства правила сети**

   | Свойство | Значение | Описание |
   |----------|-------|-------------|
   | **имя**; | <*сеть-имя правила*> | Имя правила сети |
   | **протокол**; | <*протоколы подключения*> | Используемые протоколы соединения. Например, если вы используете правила NSG, выберите **TCP** и **UDP**, а не только **TCP**. |
   | **Исходные адреса** | <*ISE — подсеть — адреса*> | IP-адреса подсети, в которых выполняется интегрированная среда сценариев, и место происхождения трафика от приложения логики. |
   | **Адреса назначения** | <*Destination-IP-адрес*> | IP-адрес целевой системы, куда должен быть отправлен трафик |
   | **Конечные порты** | <*Назначение — порты*> | Все порты, используемые конечной системой для входящего трафика |
   |||

   Дополнительные сведения о сетевых правилах см. в следующих статьях:

   * [Настройка правила сети](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Логика обработки правил Брандмауэра Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Часто задаваемые вопросы о службе "Брандмауэр Azure"](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-Азфиреваллнетворкруле](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az Network Firewall Network — Rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Дальнейшие шаги

* [Подключение к виртуальным сетям Azure из Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)