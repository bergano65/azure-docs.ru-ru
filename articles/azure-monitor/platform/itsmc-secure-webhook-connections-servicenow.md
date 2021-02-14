---
title: Соединитель управления ИТ-услугами — безопасный экспорт в Azure Monitor-Configuration с помощью ServiceNow
description: В этой статье показано, как подключить продукты и службы ITSM с ServiceNow по безопасному экспорту в Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7801145ba1bcaa2ffd543becfe190f05d232e8c8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104966"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Подключение ServiceNow к Azure Monitor

В следующих разделах приводятся сведения о подключении продукта ServiceNow и безопасном экспорте в Azure.

## <a name="prerequisites"></a>Предварительные требования

Убедитесь, что выполнены следующие предварительные требования:

* Служба Azure AD зарегистрирована.
* У вас установлена поддерживаемая версия управления событиями ServiceNow — ИТОМ (версия Нью-Йорк или более поздняя).

## <a name="configure-the-servicenow-connection"></a>Настройка подключения ServiceNow

1. Используйте ссылку HTTPS://(имя экземпляра). Service-Now. com/API/sn_em_connector/ем/inbound_event? Source = азуремонитор URI для определения безопасного экспорта.

2. Следуйте инструкциям в соответствии с версией:
   * [Париж](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Орландо](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Нью-Йорк](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
