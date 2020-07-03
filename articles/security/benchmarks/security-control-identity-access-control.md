---
title: Управление безопасностью Azure — управление удостоверениями и доступом
description: Управление доступом и удостоверениями управления безопасностью в Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 473fe838f53df0a874a2c793792533e01b7c069a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408529"
---
# <a name="security-control-identity-and-access-control"></a>Управление безопасностью: идентификация и управление доступом

Рекомендации по управлению удостоверениями и доступом позволяют сосредоточиться на проблемах, связанных с управлением доступом на основе удостоверений, блокировке административного доступа, предупреждений о событиях, связанных с удостоверениями, ненормальном поведении учетной записи и управлении доступом на основе ролей.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: ведение инвентаризации учетных записей администраторов

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.1 | 4.1 | Customer |

В Azure AD есть встроенные роли, которые должны быть явно назначены и доступны для запросов. Используйте модуль Azure AD PowerShell для выполнения нерегламентированных запросов для обнаружения учетных записей, входящих в группы администраторов.

- [Как получить роль каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Как получить членов роли каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

## <a name="32-change-default-passwords-where-applicable"></a>3,2: Измените пароли по умолчанию, если это применимо

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.2 | 4.2 | Customer |

В Azure AD нет концепции паролей по умолчанию. Другие ресурсы Azure, которым требуется пароль, принудительно запрашивают пароль с требованиями к сложности и минимальной длиной пароля, которая зависит от службы. Вы несете ответственность за сторонние приложения и службы Marketplace, которые могут использовать пароли по умолчанию.

## <a name="33-use-dedicated-administrative-accounts"></a>3,3: используйте выделенные учетные записи администратора

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.3 | 4.3 | Customer |

Создание стандартных рабочих процедур, связанных с использованием выделенных административных учетных записей. Используйте управление удостоверениями и доступом центра безопасности Azure для мониторинга количества административных учетных записей.

Вы также можете включить JIT-доступ и достаточное время, используя Azure AD Privileged Identity Management привилегированные роли для служб Майкрософт и Azure Resource Manager. 

- [Дополнительные сведения о управление привилегированными пользователями](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4. Использование единого входа (SSO) с Azure Active Directory

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.4 | 4.4. | Customer |

Везде, где это возможно, следует использовать Azure Active Directory SSO, а не настраивать отдельные автономные учетные данные для каждой службы. Используйте рекомендации по управлению удостоверениями и доступом в центре безопасности Azure.

- [Общие сведения об использовании единого входа в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: используйте многофакторную проверку подлинности для всех Azure Active Directoryного доступа

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3,5 | 4,5, 11,5, 12,11, 16,3 | Customer |

Включите Azure AD MFA и следуйте рекомендациям по управлению удостоверениями и доступом в центре безопасности Azure.

- [Как включить MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Мониторинг удостоверений и доступа в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: используйте выделенные компьютеры (рабочие станции с привилегированным доступом) для всех административных задач

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.6 | 4,6, 11,6, 12,12 | Customer |

Используйте лапы (рабочие станции привилегированного доступа) с MFA, настроенным для входа и настройки ресурсов Azure.

- [Дополнительные сведения о рабочих станциях с привилегированным доступом](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Как включить MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: журналы и оповещения о подозрительных действиях учетных записей администраторов

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3,7 | 4,8, 4,9 | Customer |

Используйте Azure Active Directory отчеты о безопасности для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде. Используйте центр безопасности Azure, чтобы отслеживать действия по удостоверениям и доступу.

- [Определение пользователей Azure AD, помеченных для рискованных действий](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Мониторинг действий идентификации и доступа пользователей в центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: управление ресурсами Azure из только утвержденных расположений

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.8 | 11,7 | Customer |

Используйте условный доступ с именованными расположениями, чтобы разрешить доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

- [Настройка именованных расположений в Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

## <a name="39-use-azure-active-directory"></a>3,9: используйте Azure Active Directory

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.9 | 16,1, 16,2, 16,4, 16,5, 16,6 | Customer |

Используйте Azure Active Directory (AAD) в качестве централизованной системы проверки подлинности и авторизации. AAD защищает данные с помощью надежного шифрования для неактивных и транзитных данных. AAD также содержит Salt-записи, хэши и безопасно хранит учетные данные пользователя.

- [Создание и настройка экземпляра AAD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: регулярно просматривайте и выверяйте доступ пользователей

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3,10 | 16,9, 16,10 | Customer |

Azure AD предоставляет журналы для облегчения поиска устаревших учетных записей. Кроме того, используйте проверку доступа удостоверений Azure, чтобы эффективно управлять членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователей можно проверить регулярно, чтобы убедиться, что доступ к ним имеют только нужные пользователи. 

- [Общие сведения об отчетах Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Использование проверок доступа для идентификации Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: монитор пытается получить доступ к отключенным учетным данным

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3,11 | 16,12 | Customer |

У вас есть доступ к источникам журнала событий входа в Azure AD, аудита и риску, которые позволяют интегрироваться с любым средством SIEM/Monitoring.

Этот процесс можно упростить, создав параметры диагностики для Azure Active Directory учетных записей пользователей и отправив журналы аудита и журналы входа в рабочую область Log Analytics. Вы можете настроить нужные оповещения в Log Analytics рабочей области.

- [Как интегрировать журналы действий Azure в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: предупреждение об отклонении режима входа учетной записи

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3,12 | 16,13 | Customer |

Используйте функции риска и защиты идентификации Azure AD, чтобы настроить автоматические ответы на обнаруженные подозрительные действия, связанные с удостоверениями пользователей. Вы также можете принимать данные в метку Azure для дальнейшего изучения.

- [Как просмотреть рискованные входы в Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Настройка и включение политик риска для защиты идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Как подключить метку Azure](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: предоставьте корпорации Майкрософт доступ к соответствующим пользовательским данным во время сценариев поддержки

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3,13 | 16 | Customer |

В сценариях поддержки, в которых корпорации Майкрософт требуется доступ к данным клиентов, защищенное хранилище предоставляет интерфейс для просмотра и утверждения или отклонения запросов на доступ к данным клиентов.

- [Общие сведения о защищенное хранилище](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)


## <a name="next-steps"></a>Дальнейшие шаги

- См. следующий контроль безопасности: [Защита данных](security-control-data-protection.md)