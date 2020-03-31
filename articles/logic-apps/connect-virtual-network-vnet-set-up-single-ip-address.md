---
title: Настройка общедоступного исходящего IP-адреса для ИСЭ
description: Узнайте, как настроить единый общедоступный исходящий IP-адрес для сред интеграционных служб (ISEs) в приложениях логики Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191488"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Настройка единого IP-адреса для одной или нескольких сред интеграционных служб в приложениях логики Azure

При работе с приложениями Azure Logic Apps можно настроить [ *среду служб ы интеграции* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) для размещения логических приложений, которым необходим доступ к ресурсам в [виртуальной сети Azure.](../virtual-network/virtual-networks-overview.md) Если у вас есть несколько экземпляров ISE, которые нуждаются в доступе к другим конечным точкам, которые имеют ограничения IP, развернуть [брандмауэр Azure](../firewall/overview.md) или [виртуальный прибор сети](../virtual-network/virtual-networks-overview.md#filter-network-traffic) в виртуальной сети и маршрут исходящего трафика через этот брандмауэр или виртуальный прибор сети. Затем все экземпляры ISE могут использовать один, общедоступный, статический и предсказуемый IP-адрес для связи с системами назначения. Таким образом, вам не нужно устанавливать дополнительные брандмауэры в этих системах назначения для каждого ISE.

В этой теме показано, как маршрутизационировать исходящий трафик через брандмауэр Azure, но можно применить аналогичные концепции к сетевому виртуальному прибору, например к стороннему брандмауэру из Azure Marketplace. Хотя эта тема фокусируется на настройке для нескольких экземпляров ISE, этот подход можно также использовать для одного ISE, когда сценарий требует ограничения количества IP-адресов, которым необходим доступ. Подумайте, имеют ли смысл дополнительные затраты на брандмауэр или виртуальный сетевой прибор для вашего сценария. Узнайте больше о [ценах на брандмауэр Azure Firewall.](https://azure.microsoft.com/pricing/details/azure-firewall/)

## <a name="prerequisites"></a>Предварительные требования

* Брандмауэр Azure, который работает в той же виртуальной сети, что и ваш ISE. Если у вас нет брандмауэра, сначала [добавьте подсеть, названную](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) `AzureFirewallSubnet` в вашей виртуальной сети. Затем можно [создать и развернуть брандмауэр](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) в виртуальной сети.

* [Таблица маршрутов](../virtual-network/manage-route-table.md)Azure . Если у вас его нет, сначала [создайте таблицу маршрутов.](../virtual-network/manage-route-table.md#create-a-route-table) Для получения дополнительной информации о разгроме, [см.](../virtual-network/virtual-networks-udr-overview.md)

## <a name="set-up-route-table"></a>Настройка таблицы маршрутов

1. На [портале Azure](https://portal.azure.com)выберите таблицу маршрутов, например:

   ![Выберите таблицу маршрутов с правилом для направления исходящего трафика](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Чтобы [добавить новый маршрут,](../virtual-network/manage-route-table.md#create-a-route)в меню таблицы маршрута, выберите **Маршруты** > **Добавить**.

   ![Добавление маршрута для направления исходящего трафика](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. На панели **маршрута Добавить** [настройте новый маршрут](../virtual-network/manage-route-table.md#create-a-route) с правилом, которое определяет, что весь исходящий трафик в систему назначения следует этому поведению:

   * Использует [**виртуальный прибор**](../virtual-network/virtual-networks-udr-overview.md#user-defined) в качестве следующего типа хмеля.

   * Переходит на частный IP-адрес для экземпляра брандмауэра в качестве следующего адреса перехода.

     Чтобы найти этот IP-адрес, в меню брандмауэра, выберите **Обзор**, найти адрес под **private IP-адрес,** например:

     ![Найти частный IP-адрес брандмауэра](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Вот пример, который показывает, как может выглядеть такое правило:

   ![Настройка правила для направления исходящего трафика](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Свойство | Значение | Описание |
   |----------|-------|-------------|
   | **Имя маршрута** | <*уникальное название маршрута*> | Уникальное название маршрута в таблице маршрутов |
   | **Адресная префикс** | <*адрес назначения*> | Адрес системы назначения, по которому вы хотите, чтобы трафик пошел. Убедитесь, что вы используете [обозначение Classless Inter-Domain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) для этого адреса. |
   | **Следующий тип хмеля** | **Виртуальный модуль** | [Тип перехода,](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) используемый исходящим трафиком |
   | **Адрес следующего прыжка** | <*брандмауэр-частный IP-адрес*> | Частный IP-адрес для брандмауэра |
   |||

## <a name="set-up-network-rule"></a>Настройка сетевого правила

1. На портале Azure найдите и выберите брандмауэр. В меню брандмауэра, в **настройках,** выберите **Правила.** На панели правил выберите **сбор правил** > сети**Добавить сетевое правило.**

   ![Добавление сбора сетевых правил в брандмауэр](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. В сборе добавьте правило, которое разрешает трафик в систему назначения.

   Например, предположим, что у вас есть логическое приложение, которое работает в ISE и должно общаться с системой SFTP. Вы создаете набор сетевых `LogicApp_ISE_SFTP_Outbound`правил, который называется , который содержит сетевое правило под названием `ISE_SFTP_Outbound`. Это правило позволяет трафик с IP-адреса любой подсети, где ваш ISE работает в вашей виртуальной сети в системе назначения SFTP, используя личный IP-адрес вашего брандмауэра.

   ![Настройка сетевого правила для брандмауэра](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Свойства сбора сетевых правил**

   | Свойство | Значение | Описание |
   |----------|-------|-------------|
   | **Название** | <*сетевое правило-коллекционное имя*> | Название коллекции сетевых правил |
   | **Priority** | <*приоритетного уровня*> | Порядок приоритета для использования для выполнения сбора правил. Для получения дополнительной [информации](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts)см. |
   | **Действие** | **Позволяют** | Тип действия для выполнения этого правила |
   |||

   **Свойства сетевых правил**

   | Свойство | Значение | Описание |
   |----------|-------|-------------|
   | **Название** | <*сетевое правило-имя*> | Название сетевого правила |
   | **Протокол** | <*протоколы подключения*> | Протоколы соединения для использования. Например, если вы используете правила NSG, выберите **tCP** и **UDP,** а не только **TCP.** |
   | **Адреса исходных источников** | <*АДРЕСА ISE-субнет*> | IP-адреса подсети, где работает ISE и откуда происходит трафик из приложения логики |
   | **Адреса назначения** | <*пункт назначения-IP-адрес*> | IP-адрес для системы назначения, где вы хотите, чтобы трафик идти |
   | **Конечные порты** | <*назначения-порты*> | Любые порты, которые система назначения использует для входящих сообщений |
   |||

   Для получения дополнительной информации о правилах сети см.

   * [Настройка правила сети](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Логика обработки правил Брандмауэра Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Часто задаваемые вопросы о службе "Брандмауэр Azure"](../firewall/firewall-faq.md)
   * [Azure PowerShell: Новый-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: правило сети брандмауэров аз](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Дальнейшие действия

* [Подключение к виртуальным сетям Azure из Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)