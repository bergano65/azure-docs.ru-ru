---
title: Настройка доступа для нескольких Исес-Azure Logic Apps
description: Для нескольких сред службы интеграции (Исес) можно настроить один общедоступный исходящий IP-адрес для доступа к внешним системам из Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: f35b6836012df7ac3879070f7a85fbfb21b456c0
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74549249"
---
# <a name="set-up-access-for-multiple-integration-service-environments-in-azure-logic-apps"></a>Настройка доступа для нескольких сред службы интеграции в Azure Logic Apps

При работе с Azure Logic Apps можно настроить [ *среду службы интеграции* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) для размещения приложений логики, которым требуется доступ к ресурсам в [виртуальной сети Azure](../virtual-network/virtual-networks-overview.md). Если у вас есть несколько экземпляров ISE, которым требуется доступ к другим конечным точкам с ограничениями IP-адресов, разверните [брандмауэр Azure](../firewall/overview.md) или [сетевой виртуальный](../virtual-network/virtual-networks-overview.md#filter-network-traffic) модуль в виртуальной сети и направьте исходящий трафик через этот брандмауэр или сетевой виртуальный модуль. После этого все экземпляры ISE в виртуальной сети будут использовать один и тот же прогнозируемый и общедоступный IP-адрес для связи с целевыми системами. Таким образом, не нужно настраивать дополнительные пути брандмауэра в конечных системах для каждой интегрированной среды сценариев. В этом разделе показано, как маршрутизировать исходящий трафик через брандмауэр Azure, но вы можете применить аналогичные концепции к виртуальному устройству виртуальной сети, например к брандмауэру стороннего производителя из Azure Marketplace.

## <a name="prerequisites"></a>Технические условия

* Брандмауэр Azure, работающий в той же виртуальной сети, что и интегрированная среда сценариев. Если у вас нет брандмауэра, сначала [добавьте подсеть](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) с именем `AzureFirewallSubnet` в виртуальную сеть. Затем можно [создать и развернуть брандмауэр](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) в виртуальной сети.

* [Таблица маршрутизации](../virtual-network/manage-route-table.md)Azure. Если у вас ее нет, сначала [Создайте таблицу маршрутов](../virtual-network/manage-route-table.md#create-a-route-table). Дополнительные сведения о маршрутизации см. в разделе [Маршрутизация трафика виртуальной сети](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Настройка таблицы маршрутов

1. В [портал Azure](https://portal.azure.com)выберите таблицу маршрутов, например:

   ![Выбор таблицы маршрутов с правилом для направления исходящего трафика](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Чтобы [Добавить новый маршрут](../virtual-network/manage-route-table.md#create-a-route), в меню Таблица маршрутов выберите пункт **маршруты** > **добавить**.

   ![Добавление маршрута для прямого исходящего трафика](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. На панели **Добавление маршрута** [Настройте новый маршрут](../virtual-network/manage-route-table.md#create-a-route) с помощью правила, которое указывает, что весь исходящий трафик к целевой системе следует этому поведению:

   * Использует [**виртуальное устройство**](../virtual-network/virtual-networks-udr-overview.md#user-defined) в качестве типа следующего прыжка.

   * Переходит к частному IP-адресу экземпляра брандмауэра в качестве адреса следующего прыжка.

     Чтобы найти этот IP-адрес, в меню брандмауэра выберите **Обзор**, найдите адрес в разделе **частный IP-адрес**, например:

     ![Поиск частного IP-адреса брандмауэра](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Ниже приведен пример того, как может выглядеть такое правило:

   ![Настройка правила для направления исходящего трафика](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Свойство | Value | Описание |
   |----------|-------|-------------|
   | **Имя маршрута** | <*UNIQUE-Route-name*> | Уникальное имя маршрута в таблице маршрутов |
   | **Префикс адреса** | <*конечный адрес*> | Адрес целевой системы, куда должен быть отправлен трафик. Убедитесь, что для этого адреса используется [нотация между междоменной маршрутизацией (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . |
   | **Тип следующего прыжка** | **Виртуальное устройство** | [Тип прыжка](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) , используемый исходящим трафиком |
   | **Адрес следующего прыжка** | <*Firewall-Private-IP-address*> | Частный IP-адрес для брандмауэра |
   |||

## <a name="set-up-network-rule"></a>Настройка правила сети

1. В портал Azure найдите и выберите свой брандмауэр. В меню "брандмауэр" в разделе " **Параметры**" выберите " **правила**". На панели правила выберите **коллекция сетевых правил** > **Добавить коллекцию сетевых правил**.

   ![Добавление коллекции правил сети в брандмауэр](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. В коллекции добавьте правило, разрешающее трафик в целевую систему.

   Например, предположим, что у вас есть приложение логики, которое работает в интегрированной среде сценариев и должно взаимодействовать с системой SFTP. Вы создаете коллекцию сетевых правил с именем `LogicApp_ISE_SFTP_Outbound`, которая содержит сетевое правило с именем `ISE_SFTP_Outbound`. Это правило разрешает трафик с IP-адреса любой подсети, в которой работает интегрированная среда сценариев, в вашей виртуальной сети с использованием частного IP-адреса брандмауэра.

   ![Настройка сетевого правила для брандмауэра](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Свойства коллекции сетевых правил**

   | Свойство | Value | Описание |
   |----------|-------|-------------|
   | **Имя** | <*сеть-Rule-Collection-name*> | Имя коллекции правил сети |
   | **Приоритет** | <> *уровня приоритета* | Порядок приоритета, используемый для запуска коллекции правил. Дополнительные сведения см. в статье [что такое основные понятия брандмауэра Azure](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)? |
   | **Действие** | **Разрешить** | Тип действия, выполняемого для этого правила |
   |||

   **Свойства правила сети**

   | Свойство | Value | Описание |
   |----------|-------|-------------|
   | **Имя** | <*Сетевое имя-правила*> | Имя правила сети |
   | **Протокол** | <*подключения — протоколы*> | Используемые протоколы соединения. Например, если вы используете правила NSG, выберите **TCP** и **UDP**, а не только **TCP**. |
   | **Исходные адреса** | <*ISE — адреса подсети*> | IP-адреса подсети, в которых выполняется интегрированная среда сценариев, и место происхождения трафика от приложения логики. |
   | **Адреса назначения** | <*назначение IP-адреса*> | IP-адрес целевой системы, куда должен быть отправлен трафик |
   | **Порты назначения** | <*назначения — порты*> | Все порты, используемые конечной системой для входящего трафика |
   |||

   Дополнительные сведения о сетевых правилах см. в следующих статьях:

   * [Настройка правила сети](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Логика обработки правил брандмауэра Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Вопросы и ответы о брандмауэре Azure](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-Азфиреваллнетворкруле](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: az Network Firewall Network — Rule](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Дальнейшие действия

* [Подключение к виртуальным сетям Azure из Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)