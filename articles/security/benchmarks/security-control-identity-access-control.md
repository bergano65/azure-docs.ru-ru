---
title: Контроль безопасности Azure - Identity и контроль доступа
description: Идентификация и контроль безопасности
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 543573610c2ea3ab0bcd89e1b8f4ee5f5a34dbc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934430"
---
# <a name="security-control-identity-and-access-control"></a>Контроль безопасности: identity и контроль доступа

Рекомендации по управлению идентификацией и доступом сосредоточены на решении вопросов, связанных с контролем доступа на основе идентификации, блокировкой административного доступа, оповещения о событиях, связанных с личностью, ненормального поведения учетной записи и управления доступом на основе ролей.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Ведение перечня административных счетов

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3.1 | 4.1 | Customer |

Azure AD имеет встроенные роли, которые должны быть четко назначены и являются запросимыми. Используйте модуль Azure AD PowerShell для выполнения специальных запросов для обнаружения учетных записей, входят в состав административных групп.

Как получить роль каталога в Azure AD с PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Как получить роль участников каталога в Azure AD с PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

## <a name="32-change-default-passwords-where-applicable"></a>3.2: Изменение паролей по умолчанию, где это применимо

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3.2 | 4.2 | Customer |

Azure AD не имеет понятия паролей по умолчанию. Другие ресурсы Azure, требующие пароля, заставляют создавать пароль со сложностью требований и минимальной длиной пароля, которая отличается в зависимости от службы. Вы несете ответственность за сторонние приложения и рыночные сервисы, которые могут использовать пароли по умолчанию.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: Использование выделенных административных учетных записей

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3.3 | 4.3 | Customer |

Создание стандартных операционных процедур использования выделенных административных счетов. Используйте identity identity и access Management Центра безопасности Azure для мониторинга количества административных учетных записей.

Вы также можете включить просто-в-времени / просто-достаточно-доступ с помощью Azure AD Привилегированные функции управления идентификацией для служб Майкрософт, и менеджер ресурсов Azure. 

Подробнее:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Используйте одиночный вписаться (SSO) с активным каталогом Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3.4 | 4.4. | Customer |

Везде, где это возможно, используйте SSO Active Directory, а не настраивайте отдельные автономные учетные данные на службу. Используйте рекомендации Центра идентификации безопасности Azure и управления доступом.

Поймите SSO с Azure AD:

https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Используйте многофакторную аутентификацию для всего доступа на основе активного каталога Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3,5 | 4.5, 11.5, 12.11, 16.3 | Customer |

Включите Azure AD и следуйте рекомендациям Центра идентификации и управления доступом Azure.

Как включить МИД в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Как контролировать личность и доступ в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Используйте выделенные машины (Привилегированные рабочие станции доступа) для всех административных задач

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Customer |

Используйте PAW (привилегированные рабочие станции доступа) с настройками MFA для входа в ресурсы Azure и настройки.

Узнайте о рабочих станциях привилегированном доступе:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Как включить МИД в Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Войти и предупредить о подозрительной активности с административных счетов

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3,7 | 4.8, 4.9 | Customer |

Используйте отчеты безопасности Azure Active Directory для генерации журналов и предупреждений при возникновении подозрительной или небезопасной активности в среде. Используйте Центр безопасности Azure для мониторинга активности идентификации и доступа.

Как определить пользователей Azure AD, помеченных для рискованных действий:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Как отслеживать идентификацию пользователей и доступ к ним в Центре безопасности Azure:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Управление ресурсами Azure только из утвержденных мест

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3.8 | 11.7 | Customer |

Используйте условные места доступа, чтобы обеспечить доступ только из конкретных логических групп диапазонов IP-адресов или стран/регионов.

Как настроить именованные места в Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

## <a name="39-use-azure-active-directory"></a>3.9: Используйте активный каталог Azure

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Customer |

Используйте Active Directory (AAD) в качестве центральной системы аутентификации и авторизации. AAD защищает данные, используя надежное шифрование данных в состоянии покоя и в пути. AAD также соли, хэши, и надежно хранит учетные данные пользователей.

Как создать и настроить экземпляр AAD:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Регулярно проверяйте и согласовывая доступ пользователей

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3,10 | 16.9, 16.10 | Customer |

Azure AD предоставляет журналы, чтобы помочь обнаружить устаревшие учетные записи. Кроме того, используйте Azure Identity Access Reviews для эффективного управления членством в группах, доступом к корпоративным приложениям и ролевым назначениям. Доступ пользователей может быть рассмотрен на регулярной основе, чтобы убедиться, что только право пользователей имеют постоянный доступ. 

Отчетность по AD Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Как использовать обзоры доступа к идентификационным данным Azure:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

## <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Попытка monitor получить доступ к деактивированным учетным записям

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3.11 | 16.12 | Customer |

У вас есть доступ к источникам активности входа в Azure AD, аудита и событий риска, которые позволяют интегрироваться с любым инструментом SIEM/Monitoring.

Этот процесс можно упорядочить, создав диагностические настройки для учетных записей пользователей Azure Active Directory и отправив журналы аудита и журналы входа в рабочее пространство для аналитики журналов. Вы можете настроить желаемые оповещения в рабочей области Log Analytics.

Как интегрировать журналы активности Azure в Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Оповещение об отклонении поведения входа в учетную запись

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3.12 | 16.13 | Customer |

Используйте функции Azure AD Risk and Identity Protection для настройки автоматизированных ответов на обнаруженные подозрительные действия, связанные с идентификаторами пользователей. Вы также можете потрахать данные в Azure Sentinel для дальнейшего изучения.

Как посмотреть рискованные ввоза Azure AD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Как настроить и включить политики защиты от идентификации:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Как на борту Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Предоставить корпорации Майкрософт доступ к соответствующим данным клиентов во время сценариев поддержки

| Идентификатор Azure | Идоты СНГ | Ответственность |
|--|--|--|
| 3.13 | 16 | Customer |

В сценариях поддержки, в которых корпорации Майкрософт необходим доступ к данным клиентов, Customer Lockbox предоставляет вам интерфейс для просмотра, а также для утверждения или отклонения запросов на доступ к данным клиентов.

Понять клиентскую блокировку:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

## <a name="next-steps"></a>Дальнейшие действия

Посмотреть следующий элемент управления безопасностью: [Защита данных](security-control-data-protection.md)
