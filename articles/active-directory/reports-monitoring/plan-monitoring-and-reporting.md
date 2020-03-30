---
title: Отчеты о планировании & мониторинга - Azure AD
description: Описывает, как планировать и осуществлять implmentation отчетности и мониторинга.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232111"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Планирование развертывания и мониторинга активного каталога Azure

Решение для отчетов и мониторинга Azure Active Directory (Azure AD) зависит от ваших юридических, безопасности и эксплуатационных требований, а также от существующей среды и процессов. В этой статье представлены различные варианты проектирования и приводит вас к правильной стратегии развертывания.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Преимущества отчетности и мониторинга Azure AD

Отчетность Azure AD обеспечивает полное представление и журналы активности Azure AD в вашей среде, включая знаквов в событиях, событиях аудита и изменениях в каталоге.

Полученные данные позволят вам выполнять следующее:

* определить, как используются приложения и службы.

* обнаружить потенциальные риски, влияющие на здоровье окружающей среды.

* устранение неполадок, мешающих пользователям получить свою работу.

* получить информацию, увидев события аудита изменений в каталоге Azure AD.

> [!IMPORTANT]
> Мониторинг Azure AD позволяет перемещать журналы, генерируемые Отчетами Azure AD, в различные целевые системы. Эти журналы затем можно сохранить для долговременного использования или интегрировать со сторонними средствами для управления сведениями и событиями безопасности (SIEM), чтобы получать аналитические сведения о своей среде.

С помощью мониторинга Azure AD можно направлять журналы в:

* учетная запись хранения Azure для архивных целей.
* В журналах Azure Monitor, ранее известном как рабочее пространство Azure Log Analytics, где можно анализировать данные, создавать панели мониторинга и предупреждать о конкретных событиях.
* центр событий Azure, где можно интегрироваться с существующими инструментами SIEM, такими как Splunk, Sumologic или «Radar».

> [!NOTE]
Недавно мы начали использовать термин Журналы Azure Monitor вместо аналитики журналов. Данные журнала по-прежнему хранятся в рабочей области Log Analytics, собираются и анализируются той же службой Log Analytics. Целью обновления терминологии является лучшее отражение роли [журналов в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection). Дополнительные сведения см. в статье [Изменения фирменной символики Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand).

[Подробнее о политиках хранения отчетов узнайте](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)больше.

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Лицензирование и предпосылки для отчетности и мониторинга Azure AD

Для доступа к знаку Azure AD в журналах вам понадобится премиум-лицензия Azure AD.

Подробная информация о функциях и лицензировании в [руководстве по ценообразованию Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)доступна.

Для развертывания мониторинга и отчетности Azure AD потребуется пользователь, который является глобальным администратором или администратором безопасности для арендатора Azure AD.

В зависимости от конечного пункта назначения данных журнала вам понадобится один из следующих:

* Учетная запись хранения Azure, для которой имеется разрешение ListKeys. Рекомендуется использовать общую учетную запись хранения вместо учетной записи хранилища BLOB-объектов. Информацию о ценах на хранение можно узнать на странице [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Пространство имен Azure Event Hubs для интеграции со сторонними решениями SIEM.

* Рабочая область Azure Log Analytics для отправки данных в журналы Azure Monitor.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Планирование проекта развертывания отчетов и мониторинга Azure

В этом проекте вы определите аудитории, которые будут потреблять и контролировать отчеты, а также определите архитектуру мониторинга Azure AD.

### <a name="engage-the-right-stakeholders"></a>Привлечение правильных заинтересованных сторон

Когда технологические проекты терпят неудачу, они обычно делают это из-за несовпадающих ожиданий в отношении воздействия, результатов и обязанностей. Чтобы избежать этих ловушек, [убедитесь, что вы привлекаете нужных заинтересованных сторон.](https://aka.ms/deploymentplans) Кроме того, обеспечить, чтобы роль заинтересованных сторон в проекте хорошо понималась путем документирования заинтересованных сторон и их вклада проекта и учетных записей.

### <a name="plan-communications"></a>Планирование информирования

Коммуникация имеет решающее значение для успеха любой новой службы. Проактивно общайтесь с пользователями о том, как изменится их опыт, когда он изменится и как получить поддержку, если у них возникнут проблемы.

### <a name="document-your-current-infrastructure-and-policies"></a>Документирование текущей инфраструктуры и политик

Текущая инфраструктура и политики будут управлять проектированием отчетов и мониторинга. Убедитесь, что вы знаете,

* Какие инструменты SIEM используются, если таковые имеется.

* Инфраструктура Azure, включая существующие учетные записи хранения данных и мониторинг.

* Ваши организационные политики удержания для журналов, включая любые необходимые соответствующие платформы. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Планирование развертывания отчетов и мониторинга Azure AD

Отчетность и мониторинг используются для удовлетворения ваших бизнес-требований, получения информации о моделях использования и т повышения безопасности ваших организаций.

### <a name="business-use-cases"></a>Случаи использования бизнеса

* Требуется для решения для удовлетворения бизнес-потребностей
* Приятно иметь для удовлетворения потребностей бизнеса
* Неприменимо

|Область |Описание |
|-|-|
|Сохранение| **Удержание журнала более 30 дней.** В соответствии с юридическими или бизнес-требованиями необходимо хранить журналы аудита и вносить журналы Azure AD более 30 дней. |
|Analytics| **Бревна должны быть пригодны для поиска.** Сохраненные журналы должны быть доступны для поиска с помощью аналитических инструментов. |
| Operational Insights| **Исследования для различных команд**. Необходимость предоставления доступа для различных пользователей, чтобы получить оперативную информацию, такую как использование приложений, знак в ошибках, самообслуживание, тенденции и т.д. |
| Исследования безопасности| **Исследования для различных команд**. Необходимость предоставить доступ для различных пользователей, чтобы получить оперативную информацию, такую как использование приложений, знак в ошибках, самообслуживание, тенденции и т.д. |
| Интеграция в системы SIEM      | **Интеграция SIEM**. Необходимость интеграции и потоковой передачи знака Azure AD в журналах и журналах аудита в существующие системы SIEM. |

### <a name="choose-a-monitoring-solution-architecture"></a>Выберите архитектуру решений мониторинга

С помощью мониторинга Azure AD можно направить журналы активности Azure AD в систему, которая наилучшим образом отвечает вашим бизнес-потребностям. Затем можно сохранить их для долгосрочной отчетности и анализа, чтобы получить представление о вашей среде, и интегрировать их с инструментами SIEM.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Диаграмма потока решений![Изображение, показывающее то, что описано в последующих разделах](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Архив журналов в учетной записи хранилища

Перекаив журналы в учетную запись хранения Azure, вы можете хранить их дольше, чем период удержания по умолчанию, описанный в наших [политиках удержания.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention) Используйте этот метод, если вам нужно архивировать журналы, но не нужно интегрировать их с системой SIEM, и не нужно постоянные запросы и анализ. Вы все еще можете сделать по требованию поиска.

См. сведения о том, как [направлять данные в учетную запись хранения](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Отправка данных в журналы Azure Monitor

[Журналы Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) консолидируют данные мониторинга из различных источников. Он также предоставляет языковой и аналитический движок запросов, который дает вам представление о работе ваших приложений и использовании ресурсов. Отправив журналы активности Azure AD в журналы Azure Monitor, можно быстро получить, контролировать и предупреждать о собранных данных. Используйте этот метод, когда у вас нет существующего решения SIEM, которое вы хотите отправить ваши данные напрямую, но хотите запросы и анализ. После того, как данные будут в журналах Azure Monitor, вы можете отправить их в концентратор событий, а оттуда в SIEM, если вы хотите.

См. дополнительные сведения об [отправке данных в журналы Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Можно также установить готовые представления для журналов активности Azure AD для мониторинга распространенных сценариев, связанных с событиями входа в систему и аудита.

См. сведения об [установке и использовании предоставлений анализа журналов для журналов действий Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Поток журналов к концентратору событий Azure

Переплет журналов в концентратор событий Azure позволяет интегрироваться с сторонними инструментами SIEM. Такая интеграция позволяет объединить данные из журнала действий Azure AD с другими данными, управляемыми средствами SIEM, что позволяет получать более подробные аналитические сведения о среде. 

См. сведения о том, как [осуществлять потоковую передачу журналов в концентратор событий](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Планирование операций и безопасности для отчетов и мониторинга Azure AD

Для получения оперативной информации заинтересованным сторонам необходимо получить доступ к журналам Azure AD. Вероятно, пользователи включают членов группы безопасности, внутренних или внешних аудиторов, а также группу операций по управлению идентификацией и доступом.

Роли Azure AD позволяют делегировать возможность настройки и просмотра отчетов Azure AD на основе вашей роли. Определите, кому в вашей организации нужно разрешение на чтение отчетов Azure AD и какая роль подходит им. 

Следующие роли можно прочитать отчеты Azure AD:

* глобальный администратор.

* администратор безопасности;

* Читатель сведений о безопасности

* Средство просмотра отчетов

Узнайте больше об [административных ролях Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)

*Всегда применяйте концепцию наименьших привилегий, чтобы уменьшить риск компрометации учетной записи.* Рассмотрите возможность внедрения [привилегированного управления идентификацией](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) для дальнейшей защиты организации.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Развертывание отчетов и мониторинга Azure AD

В зависимости от решений, принятых ранее с использованием руководства по проектированию выше, этот раздел поможет вам в документации по различным вариантам развертывания.

### <a name="consume-and-archive-azure-ad-logs"></a>Потребление и архив журналов Azure AD

[Поиск отчетов о действиях на портале Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Архив журналов Azure AD для учетной записи хранения Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Внедрение мониторинга и аналитики

[Отправка журналов на монитор Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Установка и использование представлений аналитики журналов для active Directory Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Анализ журналов действий Azure AD с помощью журналов Azure Monitor (предварительная версия)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Интерпретация схемы журналов аудита в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Интерпретация знака в схеме журналов в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Поток журналов Azure AD в концентратор событий Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Integrate Azure AD logs with Splunk by using Azure Monitor (preview)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk) (Интеграция журналов Azure AD со Splunk с помощью Azure Monitor (предварительная версия))

* [Интеграция журналов Azure AD с SumoLogic с помощью Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>Дальнейшие действия

Рассмотрите возможность внедрения [управления привилегированными удостоверениями](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

Рассмотрите возможность внедрения [элемента управления доступом на основе ролей (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)

 
