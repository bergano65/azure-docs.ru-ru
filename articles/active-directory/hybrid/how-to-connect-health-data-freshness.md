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
ms.openlocfilehash: 3ffd783ec41b1b0c4a11ee426648c1e36fbbbf75
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60349953"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Оповещение "Данные службы работоспособности неактуальны"

## <a name="overview"></a>Обзор

Агенты на локальных компьютерах, которые Azure AD Connect Health периодически отслеживает отправьте данные в службу Azure AD Connect Health. Если служба не получает данные от агента, сведения, которые представляет портале устареть. Чтобы выделить проблему, служба будет вызывать **данные службы работоспособности не находится в актуальном состоянии** оповещение. Это предупреждение создается в том случае, когда служба не получил полные данные за последние два часа.  

- **Предупреждение** срабатывает предупреждение о состоянии, если служба работоспособности получила только **частичного** типы данных, отправленных сервером за последние два часа. Предупреждение о состоянии предупреждения не инициирует уведомления по электронной почте получателям настроенных. 
- **Ошибка** срабатывает предупреждение о состоянии, если служба работоспособности не получил любые типы данных с сервера за последние два часа. Оповещение о состоянии ошибки уведомления по электронной почте настроенных получателям.

Служба получает данные от агентов, выполняющихся на локальных компьютерах, в зависимости от типа службы. В следующей таблице перечислены агентов, выполняющихся на компьютере, что они делают и типы данных, создаваемые службой. В некоторых случаях участвуют несколько служб в процессе, поэтому любой из них может быть причиной. 

## <a name="understanding-the-alert"></a>Основные сведения об оповещении

**Сведения о предупреждении** колонке отображаются, когда оповещение произошла и последнего обнаружен. Фоновый процесс, который выполняется каждые два часа приводит к возникновению ошибки и повторно вычисляет оповещение. В следующем примере начальное предупреждения на 03/10 в 09:59. Оповещение по-прежнему находились на 03/12 в в 10:00 по время оповещения попытку вычисления. Колонки также подробно описывается время служба работоспособности в последний раз получен определенный тип данных. 
 
 ![Azure AD Connect Health сведениях о предупреждении](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
В таблице ниже приведены типы служб в соответствующие типы необходимых данных:

| Service type (Тип службы) | Агент (имя службы Windows) | Назначение | Тип данных, созданный  |
| --- | --- | --- | --- |  
| Azure AD Connect (Sync) | Служба Sync Insights Azure AD Connect Health | Собирать сведения, относящиеся с AAD Connect (соединителей, правила синхронизации, и т.д.) | AadSyncService-SynchronizationRules <br />  AadSyncService соединителей <br /> AadSyncService-GlobalConfigurations  <br />  AadSyncService-RunProfileResults <br /> AadSyncService-ServiceConfigurations <br /> AadSyncService-ServiceStatus   |
|  | Служба Sync Monitoring Azure AD Connect Health | Сбор, относящиеся к AAD Connect счетчики производительности, трассировки событий Windows, файлы | Счетчик производительности |
| AD DS | служба AD DS для Azure AD Connect Health; | Выполните тесты — искусственные, сбора сведений о топологии, метаданные репликации |  — Добавляет TopologyInfo-Json <br /> -Common-TestData-Json (создает результаты тестов)   | 
|  | служба наблюдения AD DS Azure AD Connect Health. | Собирать счетчики производительности, относящиеся к ADDS, трассировки событий Windows, файлы | -Счетчик производительности  <br /> -Common-TestData-Json (Отправка результатов теста)  |
| AD FS | Служба диагностики AD FS Azure AD Connect Health | Выполните тесты — искусственные | TestResult (создает результаты тестов) | 
| | Служба AD FS получения ценной информации из данных о работоспособности Azure AD Connect  | Собирать метрики использования служб федерации Active Directory | Adfs-UsageMetrics |
| | Служба наблюдения AD FS Azure AD Connect Health | Собирать счетчики производительности, относящиеся к ADFS, трассировки событий Windows, файлы | TestResult (Отправка результатов теста) |

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок 

Шаги, необходимые для диагностики проблемы приведены ниже. Первый — это набор Основные проверки, которые являются общими для всех типов служб. Таблица ниже, перечислены конкретные действия для каждого типа службы и типа данных. 

> [!IMPORTANT] 
> Для этого оповещения соблюдается [политика хранения данных](reference-connect-health-user-privacy.md#data-retention-policy) Connect Health

* Убедитесь, что установлены последние версии агентов. Представление [История выпусков](reference-connect-health-version-history.md). 
* Убедитесь, что службы агентов Azure AD Connect Health, **под управлением** на компьютере. Например, Connect Health для AD FS должен состоять из трех служб.
  ![Проверка Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Откройте [раздел требований](how-to-connect-health-agent-install.md#requirements) и убедитесь, что все они соблюдены.
* Используйте [средство тестирования подключения](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) для обнаружения возможных проблем с подключением.
* Если вы используете прокси-сервер HTTP, следуйте этим [этапам конфигурации](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Дальнейшие действия
Если какие-либо выше шаги выявления проблемы, устранить ее и дождитесь оповещение, чтобы разрешить. Оповещения фоновый процесс выполняется каждые 2 часа, поэтому может занять до двух часов, чтобы устранить это предупреждение. 

* [Политика хранения данных Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [Часто задаваемые вопросы об Azure AD Connect Health](reference-connect-health-faq.md)
