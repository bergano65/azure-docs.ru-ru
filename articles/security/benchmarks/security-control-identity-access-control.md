---
title: Управление безопасностью Azure — управление удостоверениями и доступом
description: Управление доступом и удостоверениями управления безопасностью в Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7d8ca0b0fa632321b6aa075ab5ca2bdf6632ab51
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216510"
---
# <a name="security-control-identity-and-access-control"></a>Управление безопасностью: идентификация и управление доступом

Рекомендации по управлению удостоверениями и доступом позволяют сосредоточиться на проблемах, связанных с управлением доступом на основе удостоверений, блокировке административного доступа, предупреждений о событиях, связанных с удостоверениями, ненормальном поведении учетной записи и управлении доступом на основе ролей.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1. Инвентаризация учетных записей администраторов

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.1 | 4.1 | Customer |

В Azure AD есть встроенные роли, которые должны быть явно назначены и доступны для запросов. Используйте модуль Azure AD PowerShell для выполнения нерегламентированных запросов для обнаружения учетных записей, входящих в группы администраторов.

- [Как получить роль каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&preserve-view=true)

- [Как получить членов роли каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&preserve-view=true)

## <a name="32-change-default-passwords-where-applicable"></a>3.2. Изменение паролей по умолчанию, где применимо

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.2 | 4.2 | Customer |

В Azure AD нет концепции паролей по умолчанию. Другие ресурсы Azure, которым требуется пароль, принудительно запрашивают пароль с требованиями к сложности и минимальной длиной пароля, которая зависит от службы. Вы несете ответственность за сторонние приложения и службы Marketplace, которые могут использовать пароли по умолчанию.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3. Применение выделенных административных учетных записей

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.3 | 4.3 | Customer |

Создание стандартных рабочих процедур, связанных с использованием выделенных административных учетных записей. Используйте учетную запись Центра безопасности Azure и управление доступом для мониторинга количества административных учетных записей.

Вы также можете включить JIT-доступ и достаточное время, используя Azure AD Privileged Identity Management привилегированные роли для служб Майкрософт и Azure Resource Manager. 

- [Дополнительные сведения о управление привилегированными пользователями](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4. Использование единого входа с Azure Active Directory

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.4 | 4.4. | Customer |

Везде, где это возможно, следует использовать Azure Active Directory SSO, а не настраивать отдельные автономные учетные данные для каждой службы. Используйте рекомендации по управлению удостоверениями и доступом в центре безопасности Azure.

- [Общие сведения об использовании единого входа в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5. Использование многофакторной проверки подлинности для любого доступа на основе Azure Active Directory

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.5 | 4,5, 11,5, 12,11, 16,3 | Customer |

Включите Azure AD MFA и следуйте рекомендациям по управлению удостоверениями и доступом в центре безопасности Azure.

- [Включение MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Мониторинг идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6. Использование выделенных компьютеров (рабочих станций с привилегированным доступом) для всех административных задач

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3,6 | 4,6, 11,6, 12,12 | Customer |

Используйте лапы (рабочие станции привилегированного доступа) с MFA, настроенным для входа и настройки ресурсов Azure.

- [Использование рабочих станций с привилегированным доступом](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Включение MFA в Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: журналы и оповещения о подозрительных действиях учетных записей администраторов

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3,7 | 4,8, 4,9 | Customer |

Используйте Azure Active Directory отчеты о безопасности для создания журналов и оповещений при возникновении подозрительных или ненадежных действий в среде. Используйте Центр безопасности Azure для мониторинга действий идентификации и доступа.

- [Как определить пользователей Azure AD, помеченных для события риска](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Мониторинг пользовательских действий идентификации и доступа в Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8. Управление ресурсами Azure только из утвержденных расположений

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.8 | 11,7 | Customer |

Используйте условный доступ с именованными расположениями, чтобы разрешить доступ только из конкретных логических групп диапазонов IP-адресов или стран и регионов.

- [Настройка именованных расположений в Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

## <a name="39-use-azure-active-directory"></a>3.9. Использование Azure Active Directory

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.9 | 16,1, 16,2, 16,4, 16,5, 16,6 | Customer |

Используйте Azure Active Directory в качестве централизованной системы проверки подлинности и авторизации. Azure AD защищает данные с помощью надежного шифрования для хранимых и транзитных данных. Кроме того, в Azure AD используются salt-записи, хэши и безопасное хранение учетных данных пользователей.

- [Создание и настройка экземпляра Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10. Регулярная проверка и согласование доступа пользователей

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3.10 | 16,9, 16,10 | Customer |

Azure AD предоставляет журналы для облегчения поиска устаревших учетных записей. Кроме того, используйте проверку доступа удостоверений Azure, чтобы эффективно управлять членством в группах, доступом к корпоративным приложениям и назначениями ролей. Доступ пользователей можно проверять на регулярной основе, чтобы только у авторизованных пользователей был постоянный доступ. 

- [Общие сведения об отчетах Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Использование проверок доступа для идентификации Azure](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: монитор пытается получить доступ к отключенным учетным данным

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3,11 | 16,12 | Customer |

У вас есть доступ к источникам журнала событий входа в Azure AD, аудита и риску, которые позволяют интегрироваться с любым средством SIEM/Monitoring.

Этот процесс можно упростить, создав параметры диагностики для учетных записей пользователей Azure Active Directory и отправив журналы аудита и журналы входа в рабочую область Log Analytics. Вы можете настроить необходимые оповещения в рабочей области Log Analytics.

- [Как интегрировать журналы действий Azure в Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12. Предупреждение при подозрительном входе в учетную запись

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3,12 | 16,13 | Customer |

Используйте функции риска и защиты идентификации Azure AD, чтобы настроить автоматические ответы на обнаруженные подозрительные действия, связанные с удостоверениями пользователей. Вы также можете включить данные в Azure Sentinel для дальнейшего изучения.

- [Просмотр рискованных входов в Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Как настроить и включить политики рисков с помощью защиты идентификации](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Подключение к Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13. Предоставление корпорации Майкрософт доступа к соответствующим данным клиентов в рамках сценариев поддержки

| Идентификатор Azure | Идентификаторы CIS | Несет |
|--|--|--|
| 3,13 | 16 | Customer |

В сценариях поддержки, в которых корпорации Майкрософт требуется доступ к данным клиентов, защищенное хранилище предоставляет интерфейс для просмотра и утверждения или отклонения запросов на доступ к данным клиентов.

- [Общие сведения о защищенное хранилище](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)


## <a name="next-steps"></a>Дальнейшие действия

- См. следующий контроль безопасности: [Защита данных](security-control-data-protection.md)