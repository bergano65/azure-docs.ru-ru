---
title: Соединитель управления ИТ-услугами — безопасный экспорт в Azure Monitor-Configuration с помощью ServiceNow
description: В этой статье показано, как подключить продукты и службы ITSM с ServiceNow по безопасному экспорту в Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 8ca77c1fa1232f7aed88b0d6942d8a0085caf0d5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625746"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Подключение ServiceNow к Azure Monitor

В следующих разделах приводятся сведения о подключении продукта ServiceNow и безопасном экспорте в Azure.

## <a name="prerequisites"></a>Предварительные требования

Убедитесь, что выполнены следующие предварительные требования:

* Служба Azure AD зарегистрирована.
* У вас установлена поддерживаемая версия управления событиями ServiceNow — ИТОМ (версия Нью-Йорк или более поздняя).
* [Приложение](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.2.0?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3Devent%2520management%2520connectors&sl=sh) , установленное в экземпляре ServiceNow.

## <a name="configure-the-servicenow-connection"></a>Настройка подключения ServiceNow

1. Используйте ссылку HTTPS://(имя экземпляра). Service-Now. com/API/sn_em_connector/ем/inbound_event? Source = азуремонитор URI для определения безопасного экспорта.

2. Следуйте инструкциям в соответствии с версией:
   * [Париж](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Орландо](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Нью-Йорк](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
