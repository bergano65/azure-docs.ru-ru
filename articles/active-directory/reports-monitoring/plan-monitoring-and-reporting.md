---
title: Планирование развертывания Azure Active Directory отчетов и мониторинга
description: Описывает планирование и выполнение имплментатион отчетов и мониторинга.
services: active-directory
documentationcenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: plan
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f68b3a312ff7681fde65154542a66767c5195ff
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406461"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Планирование развертывания Azure Active Directory отчетов и мониторинга

Решение Azure Active Directory (Azure AD) для составления отчетов и мониторинга зависит от ваших юридических требований, безопасности и эксплуатации, а в существующей среде и процессов. В этой статье представлены различные варианты проектирования и руководство по правильной стратегии развертывания.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Преимущества отчетов и мониторинга Azure AD

Служба отчетов Azure AD предоставляет исчерпывающее представление и журналы действий Azure AD в вашей среде, в том числе события входа, события аудита и изменения в каталоге.

Полученные данные позволят вам выполнять следующее:

* Определите, как используются приложения и службы.

* выявление потенциальных рисков, влияющих на работоспособность вашей среды.

* Устранение проблем, мешающих работе пользователей.

* получите ценные сведения, просмотрев события аудита изменений в каталоге Azure AD.

> [!IMPORTANT]
> Мониторинг Azure AD позволяет маршрутизировать журналы, созданные отчетами Azure AD, к разным целевым системам. Эти журналы затем можно сохранить для долговременного использования или интегрировать со сторонними средствами для управления сведениями и событиями безопасности (SIEM), чтобы получать аналитические сведения о своей среде.

С помощью мониторинга Azure AD можно направить журналы в:

* Учетная запись хранения Azure для архивных целей.
* Журналы Azure Monitor, ранее известные как Рабочая область Azure Log Analytics, где можно анализировать данные, создавать панели мониторинга и оповещать о конкретных событиях.
* концентратор событий Azure, который можно интегрировать с существующими средствами SIEM, такими как Splunk, Sumologic или QRadar.

> [!NOTE]
Недавно мы начали использовать термин Azure Monitor журналов вместо Log Analytics. Данные журнала по-прежнему хранятся в рабочей области Log Analytics, собираются и анализируются той же службой Log Analytics. Целью обновления терминологии является лучшее отражение роли [журналов в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection). Дополнительные сведения см. в статье [Изменения фирменной символики Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand).

Дополнительные [сведения о политиках хранения отчетов](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Лицензирование и предварительные требования для отчетов и мониторинга Azure AD

Для доступа к журналам входа в Azure AD потребуется лицензия Azure AD Premium.

Подробные сведения о функции и лицензировании см. в разделе [Azure Active Directory ценовых руководств](https://azure.microsoft.com/pricing/details/active-directory/).

Для развертывания мониторинга Azure AD и создания отчетов вам потребуется пользователь, являющийся глобальным администратором или администратором безопасности для клиента Azure AD.

В зависимости от конечного назначения данных журнала необходимо иметь одно из следующих значений:

* Учетная запись хранения Azure, для которой у вас есть разрешения ListKeys. Рекомендуется использовать общую учетную запись хранения вместо учетной записи хранилища BLOB-объектов. Информацию о ценах на хранение можно узнать на странице [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Пространство имен концентраторов событий Azure для интеграции со сторонними решениями SIEM.

* Рабочая область Azure Log Analytics для отправки данных в журналы Azure Monitor.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Планирование проекта развертывания отчетов и мониторинга Azure

В этом проекте вы определите аудитории, которые будут потреблять и отслеживать отчеты, и определите архитектуру мониторинга Azure AD.

### <a name="engage-the-right-stakeholders"></a>Привлечение нужных заинтересованных лиц

При сбое технологических проектов они обычно делают это из-за несовпадения ожидания, результатов и обязанностей. Чтобы избежать этих ловушек, [Убедитесь, что вы](https://aka.ms/deploymentplans)подрабатываете нужные заинтересованные лица. Также убедитесь, что роли заинтересованных лиц в проекте хорошо понятны, путем документирования заинтересованных лиц, а также их ввода и подотчетности.

### <a name="plan-communications"></a>Планирование информирования

Связь важна для успеха любой новой службы. Заблаговременное взаимодействие с пользователями, как изменится их опыт, когда изменится, и как получить поддержку в случае возникновения проблем.

### <a name="document-your-current-infrastructure-and-policies"></a>Документирование текущей инфраструктуры и политик

Текущая инфраструктура и политики будут обеспечивать проектирование отчетов и мониторинга. Убедитесь, что вы знакомы

* Что, если таковые имеются, SIEM средства, которые вы используете.

* Ваша инфраструктура Azure, включая существующие учетные записи хранения и мониторинг.

* Политики хранения в Организации для журналов, включая все необходимые платформы соответствия требованиям. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Планирование развертывания отчетов и мониторинга Azure AD

Отчеты и мониторинг используются для удовлетворения ваших бизнес-требований, получения ценных сведений о закономерностях использования, а t повышает уровень безопасности в организациях.

### <a name="business-use-cases"></a>Варианты использования для бизнеса

* Требуется для решения в соответствии с бизнес-потребностями
* Приятно, чтобы удовлетворить потребности бизнеса
* Неприменимо

|Область |Описание |
|-|-|
|Удержание| **Срок хранения журнала более 30 дней**. Из-за юридических или бизнес-требований необходимо хранить журналы аудита и журналы входа Azure AD дольше 30 дней. |
|Аналитика| **Журналы должны иметь возможность поиска**. Сохраненные журналы должны поддерживать поиск с помощью аналитических средств. |
| Оперативная аналитика| **Аналитика для различных команд**. Необходимо предоставить доступ различным пользователям для получения оперативной аналитики, такой как использование приложения, ошибки входа, использование самообслуживания, тенденции и т. д. |
| Аналитика безопасности| **Аналитика для различных команд**. Необходимо предоставить доступ различным пользователям для получения оперативной аналитики, такой как использование приложения, ошибки входа, использование самостоятельной службы, тенденции и т. д. |
| Интеграция в SIEM Systems      | **Интеграция SIEM**. Необходимо интегрировать и выполнять потоковую передачу журналов входа и журналов аудита Azure AD в существующие системы SIEM. |

### <a name="choose-a-monitoring-solution-architecture"></a>Выбор архитектуры решения для мониторинга

С помощью мониторинга Azure AD вы можете направить журналы действий Azure AD в систему, которая наилучшим образом соответствует вашим бизнес-потребностям. Затем их можно хранить для долгосрочной отчетности и анализа, чтобы получить ценные сведения о среде и интегрировать ее с SIEM инструментами.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sectionsmediareporting-deployment-plandeploy-reporting-flow-diagrampng"></a>Схема потока принятия решений![Изображение, показывающее, что описано в последующих разделах](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Архивация журналов в учетной записи хранения

При маршрутизации журналов в учетную запись хранения Azure их можно хранить дольше, чем срок хранения по умолчанию, указанный в политиках [хранения](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention). Используйте этот метод, если вам нужно архивировать журналы, но не нужно интегрировать их с SIEM системой и не требовать оперативных запросов и анализа. Вы по-прежнему можете выполнять поиск по запросу.

См. сведения о том, как [направлять данные в учетную запись хранения](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Отправка данных в журналы Azure Monitor

[Журналы Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) объединяют данные мониторинга из разных источников. Он также предоставляет язык запросов и модуль аналитики, позволяющие получить представление о работе приложений и использовании ресурсов. Отправляя журналы действий Azure AD в журналы Azure Monitor, можно быстро получать, отслеживать и оповещать собранные данные. Используйте этот метод, если у вас нет решения SIEM, которое планируется отправить данные напрямую, но требуется выполнить запросы и анализ. После того как данные находятся в журналах Azure Monitor, их можно отправить в концентратор событий и из SIEM, если хотите.

См. дополнительные сведения об [отправке данных в журналы Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Вы также можете установить предварительно созданные представления для журналов действий Azure AD, чтобы отслеживать распространенные сценарии, включающие события входа и аудита.

См. сведения об [установке и использовании предоставлений анализа журналов для журналов действий Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Потоковая передача журналов в концентратор событий Azure

Журналы маршрутизации в концентратор событий Azure обеспечивают интеграцию со сторонними инструментами SIEM. Такая интеграция позволяет объединить данные из журнала действий Azure AD с другими данными, управляемыми средствами SIEM, что позволяет получать более подробные аналитические сведения о среде. 

См. сведения о том, как [осуществлять потоковую передачу журналов в концентратор событий](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Планирование операций и безопасности для отчетов и мониторинга Azure AD

Чтобы получить оперативную аналитику, заинтересованным лицам необходимо получить доступ к журналам Azure AD. Вероятные пользователи включают в себя членов группы безопасности, внутренних или внешних аудиторов, а также группу операций управления удостоверениями и доступом.

Роли Azure AD позволяют делегировать возможность настраивать и просматривать отчеты Azure AD на основе вашей роли. Укажите, кто в вашей организации нуждается в разрешении на чтение отчетов Azure AD и какая роль подходит для них. 

Следующие роли могут читать отчеты Azure AD:

* Глобальный администратор

* Администратор системы безопасности

* Читатель сведений о безопасности

* Средство чтения отчетов

Дополнительные сведения об [административных ролях Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

*Всегда применяйте концепцию наименьших привилегий, чтобы снизить риск компрометации учетной записи*. Рассмотрите возможность реализации [Управление привилегированными пользователями](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) для дальнейшей защиты Организации.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Развертывание отчетов и мониторинга Azure AD

В зависимости от решений, принятых ранее с помощью рекомендаций по проектированию выше, в этом разделе вы найдете документацию по различным вариантам развертывания.

### <a name="consume-and-archive-azure-ad-logs"></a>Использование и архивация журналов Azure AD

[Поиск отчетов о действиях на портале Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Использование пакета содержимого Power BI для Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack)

[Архивация журналов Azure AD в учетную запись хранения Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Реализация мониторинга и аналитики

[Отправка журналов в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Установка и использование представлений Log Analytics для Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Анализ журналов действий Azure AD с помощью журналов Azure Monitor (предварительная версия)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Interpret the Azure AD audit logs schema in Azure Monitor (preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema) (Интерпретация схемы журналов аудита Azure Active Directory в Azure Monitor (предварительная версия))

* [Анализ схемы журналов входа в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Потоковая передача журналов Azure AD в концентратор событий Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Integrate Azure AD logs with Splunk by using Azure Monitor (preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk) (Интеграция журналов Azure AD со Splunk с помощью Azure Monitor (предварительная версия))

* [Интеграция журналов Azure AD с SumoLogic с помощью Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>Следующие шаги

Попробуйте реализовать [Управление привилегированными пользователями](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

Рассмотрите возможность реализации [управления доступом на основе ролей (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) .

 
