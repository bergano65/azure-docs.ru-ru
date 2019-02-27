---
title: Azure AD Connect Health. Оповещение "Данные службы работоспособности неактуальны" | Документация Майкрософт
description: В этом документе описана причина отправки оповещения "Данные службы работоспособности неактуальны" и действия по устранению этой неполадки.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ad829b976d8b712ee8027c89fb618c6c07de1bc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429028"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Оповещение "Данные службы работоспособности неактуальны"

## <a name="overview"></a>Обзор
Агенты в локальных машинах, которые отслеживает Azure AD Connect Health, периодически отправляют данные в службу Azure AD Connect Health. Если служба не получает данные от агента, информация на портале устареет. Чтобы обратить внимание на проблему, служба уведомит вас оповещением **Данные службы работоспособности неактуальны**. Служба использует это оповещение, если не получает данные за последние два часа.  

* Оповещение с состоянием **Предупреждение** срабатывает, если служба Connect Health не получает отправленные с сервера частичные элементы данных в течение двух часов. Оповещение с состоянием "Предупреждение" не инициирует отправку уведомлений по электронной почте для администратора клиента.
* Оповещение с состоянием **Ошибка** срабатывает, если служба Connect Health не получает никаких отправленных с сервера элементов данных в течение двух часов. Оповещение с состоянием "Ошибка" инициирует отправку уведомлений по электронной почте для администратора клиента.


## <a name="troubleshooting-steps"></a>Действия по устранению неполадок 

> [!IMPORTANT] 
> Для этого оповещения соблюдается [политика хранения данных](reference-connect-health-user-privacy.md#data-retention-policy) Connect Health

* Убедитесь, что агенты службы Azure AD Connect Health запущены на компьютере. Например, Connect Health для AD FS должен состоять из трех служб.  
  ![Проверка Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Откройте [раздел требований](how-to-connect-health-agent-install.md#requirements) и убедитесь, что все они соблюдены.
* Используйте [средство тестирования подключения](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) для обнаружения возможных проблем с подключением.
* Если вы используете прокси-сервер HTTP, следуйте этим [этапам конфигурации](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 

В колонке "Сведения об оповещении" содержатся элементы данных, которые отсутствуют на сервере. Указанная ниже таблица поможет сузить проблему. 
### <a name="connect-health-for-sync"></a>Connect Health для синхронизации

| Элементы данных | Действия по устранению неполадок |
| --- | --- | 
| PerfCounter | - [Исходящие подключения к конечным точкам службы Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections). <br />- [Проверка SSL для исходящего трафика отфильтрована или отключена](https://technet.microsoft.com/library/ee796230.aspx). <br /> - [Порты брандмауэра на сервере с агентом](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx). |
| AadSyncService-SynchronizationRules, <br /> AadSyncService-Connectors, <br /> AadSyncService-GlobalConfigurations, <br /> AadSyncService-RunProfileResults, <br /> AadSyncService-ServiceConfigurations, <br /> AadSyncService-ServiceStatus | - [Исходящие подключения к конечным точкам службы Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections). <br /> -  [Порты брандмауэра на сервере с агентом](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx). | 

### <a name="connect-health-for-adfs"></a>Connect Health для ADFS

Проведите дополнительные проверки AD FS и выполните инструкции из [справки по AD FS](https://adfshelp.microsoft.com/TroubleshootingGuides/Workflow/3ef51c1f-499e-4e07-b3c4-60271640e282).

| Элементы данных | Действия по устранению неполадок |
| --- | --- | 
| PerfCounter, TestResult | - [Исходящие подключения к конечным точкам службы Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections). <br />- [Проверка SSL для исходящего трафика отфильтрована или отключена](https://technet.microsoft.com/library/ee796230.aspx). <br />-  [Порты брандмауэра на сервере с агентом](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx). |
|  Adfs-UsageMetrics | Исходящие подключения на основе IP-адресов. Cм. [диапазоны IP-адресов Azure](https://www.microsoft.com/download/details.aspx?id=41653). | 

### <a name="connect-health-for-adds"></a>Connect Health для доменных служб Active Directory

| Элементы данных | Действия по устранению неполадок |
| --- | --- | 
| PerfCounter, Adds-TopologyInfo-Json, Common-TestData-Json | - [Исходящие подключения к конечным точкам службы Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections). <br /> -  [Порты брандмауэра на сервере с агентом](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx). |


## <a name="next-steps"></a>Дополнительная информация
* [Часто задаваемые вопросы об Azure AD Connect Health](reference-connect-health-faq.md)
