---
title: Azure AD Connect Health. Оповещение "Данные службы работоспособности неактуальны" | Документация Майкрософт
description: В этом документе описана причина отправки оповещения "Данные службы работоспособности неактуальны" и действия по устранению этой неполадки.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: maheshu
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: zhiweiw
ms.openlocfilehash: 93f0d31f5daa13d0598367e2a4ddab67ff3dbc66
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51279731"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Оповещение "Данные службы работоспособности неактуальны"

## <a name="overview"></a>Обзор
<li>Расширение Azure AD Connect Health создает оповещение об актуальности данных, если не получает все точки данных от сервера в течение двух часов. Заголовок оповещения — **Данные службы работоспособности неактуальны**. </li>
<li>Оповещение с состоянием **Предупреждение** срабатывает, если служба Connect Health не получает отправленные с сервера частичные элементы данных в течение двух часов. Оповещение с состоянием "Предупреждение" не инициирует отправку уведомлений по электронной почте для администратора клиента. </li>
<li>Оповещение с состоянием **Ошибка** срабатывает, если служба Connect Health не получает никаких отправленных с сервера элементов данных в течение двух часов. Оповещение с состоянием "Ошибка" инициирует отправку уведомлений по электронной почте для администратора клиента. </li>

>[!IMPORTANT] 
> Для этого оповещения соблюдается [политика хранения данных](reference-connect-health-user-privacy.md#data-retention-policy) Connect Health

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок 
* Откройте [раздел требований](how-to-connect-health-agent-install.md#requirements) и убедитесь, что все они соблюдены.
* Используйте [средство тестирования подключения](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) для обнаружения возможных проблем с подключением.
* Если у вас есть прокси-сервер HTTP, настройте его, как описано [здесь](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

### <a name="connect-health-for-sync"></a>Connect Health для синхронизации

| Элементы данных | Действия по устранению неполадок |
| --- | --- | 
| PerfCounter | - [Исходящие подключения к конечным точкам службы Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections). <br />- [Проверка SSL для исходящего трафика отфильтрована или отключена](https://technet.microsoft.com/library/ee796230.aspx). <br /> - [Порты брандмауэра на сервере с агентом](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx). <br /> - [Разрешение назначенных веб-сайтов при включенной политике усиленной безопасности IE](https://technet.microsoft.com/windows/ms537180(v=vs.60)). |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - Исходящие подключения на основе IP-адресов. Cм. [диапазоны IP-адресов Azure](https://www.microsoft.com/download/details.aspx?id=41653). <br /> - [Исходящие подключения к конечным точкам службы Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections). <br /> -  [Порты брандмауэра на сервере с агентом](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx). | 

### <a name="connect-health-for-adfs"></a>Connect Health для ADFS

Проведите дополнительные проверки AD FS и выполните инструкции из [справки по AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

| Элементы данных | Действия по устранению неполадок |
| --- | --- | 
| PerfCounter, TestResult | - [Исходящие подключения к конечным точкам службы Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections). <br />- [Проверка SSL для исходящего трафика отфильтрована или отключена](https://technet.microsoft.com/library/ee796230.aspx). <br />-  [Порты брандмауэра на сервере с агентом](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx). <br /> - [Разрешение назначенных веб-сайтов при включенной политике усиленной безопасности IE](https://technet.microsoft.com/windows/ms537180(v=vs.60)). |
|  Adfs-UsageMetrics | Исходящие подключения на основе IP-адресов. Cм. [диапазоны IP-адресов Azure](https://www.microsoft.com/download/details.aspx?id=41653). | 

### <a name="connect-health-for-adds"></a>Connect Health для доменных служб Active Directory

| Элементы данных | Действия по устранению неполадок |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Исходящие подключения к конечным точкам службы Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections). <br /> - [Проверка SSL для исходящего трафика отфильтрована или отключена](https://technet.microsoft.com/library/ee796230.aspx). <br />-  [Порты брандмауэра на сервере с агентом](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx). <br /> - [Разрешение назначенных веб-сайтов при включенной политике усиленной безопасности IE](https://technet.microsoft.com/windows/ms537180(v=vs.60)). <br />  - Исходящие подключения на основе IP-адресов. Cм. [диапазоны IP-адресов Azure](https://www.microsoft.com/download/details.aspx?id=41653).  |


## <a name="next-steps"></a>Дополнительная информация
* [Часто задаваемые вопросы об Azure AD Connect Health](reference-connect-health-faq.md)
