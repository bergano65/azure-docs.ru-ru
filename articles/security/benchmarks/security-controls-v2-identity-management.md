---
title: Тестирование безопасности Azure v2 — управление удостоверениями
description: Управление удостоверениями Azure Security, версия 2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e7447d03fc231d8d96b71c7d944a380c10add0d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757942"
---
# <a name="security-control-v2-identity-management"></a>Управление безопасностью v2: Управление удостоверениями

Управление удостоверениями охватывает элементы управления для обеспечения безопасного управления удостоверениями и доступом с помощью Azure Active Directory. Сюда входит использование единого входа, строгие проверки подлинности, управляемые удостоверения (и принципы службы) для мониторинга приложений, условного доступа и аномалий учетных записей.

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: стандартизация Azure Active Directory в качестве централизованной системы идентификации и проверки подлинности

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| IM-1 | 16,1, 16,2, 16,4, 16,5 | IA-2, IA-8, AC-2, AC-3 |

Azure Active Directory (Azure AD) — это служба управления удостоверениями и доступом Azure по умолчанию. Для управления удостоверениями и доступом в вашей организации следует стандартизировать Azure AD.
- Облачные ресурсы Майкрософт, такие как портал Azure, служба хранилища Azure, виртуальные машины Azure (Linux и Windows), Azure Key Vault, PaaS и приложения SaaS.

- Ресурсы Организации, такие как приложения в Azure или ресурсы корпоративной сети.

Безопасность Azure AD должна быть высокой приоритетной в целях безопасности в облаке вашей организации. Azure AD предоставляет удостоверение безопасного удостоверения, помогающее оценить безопасность идентификации по сравнению с рекомендациями Майкрософт. Используйте оценку, чтобы оценить, насколько близко ваша конфигурация соответствует рекомендациям, и улучшить безопасность.

Примечание. Azure AD поддерживает внешние поставщики удостоверений, которые позволяют пользователям без учетная запись Майкрософт входить в свои приложения и ресурсы с внешними удостоверениями.

- [Аренда в Azure AD](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Создание и настройка экземпляра Azure AD](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Определение клиентов Azure AD](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Использование внешних поставщиков удостоверений для приложения](/azure/active-directory/b2b/identity-providers)

- [Оценка безопасного удостоверения в Azure AD](../../active-directory/fundamentals/identity-secure-score.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Управление удостоверениями и ключами](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [Архитектура безопасности](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2. безопасное и автоматическое управление удостоверениями приложений

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| IM-2 | Недоступно | AC-2, AC-3, IA-2, IA-4, IA-9 |

Для учетных записей, не связанных с персоналом, таких как службы или Автоматизация, используйте управляемые удостоверения Azure вместо создания более мощной учетной записи для доступа к ресурсам или выполнения кода. Управляемые удостоверения Azure могут проходить проверку подлинности в службах и ресурсах Azure, поддерживающих проверку подлинности Azure AD. Проверка подлинности включается с помощью предварительно определенных правил предоставления доступа, что позволяет избежать жестко заданной учетной записи в исходном коде или файлах конфигурации. 

Для служб, которые не поддерживают управляемые удостоверения, используйте Azure AD для создания субъекта-службы с ограниченными разрешениями на уровне ресурса.  Рекомендуется настроить субъекты-службы с учетными данными сертификата и вернуться к секретам клиента. В обоих случаях Azure Key Vault можно использовать в сочетании с управляемыми удостоверениями Azure, чтобы среда выполнения (например, функция Azure) могла извлекать учетные данные из хранилища ключей.

- [Управляемые удостоверения Azure](../../active-directory/managed-identities-azure-resources/overview.md)

- [Службы с поддержкой управляемых удостоверений для ресурсов Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Субъект-служба Azure](/powershell/azure/create-azure-service-principal-azureps)

- [Создание субъекта-службы с сертификатами](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

Используйте Azure Key Vault для регистрации субъекта безопасности: Authentication # авторизовать-a-Security-Principal-to-Access-Key-Vault.

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Управление удостоверениями и ключами](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3. Использование единого входа Azure AD (SSO) для доступа к приложениям

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| IM-3 | 4.4. | IA-2, IA-4 |

Azure AD предоставляет управление удостоверениями и доступом к ресурсам Azure, облачным приложениям и локальным приложениям. Управление удостоверениями и доступом применяется к корпоративным удостоверениям, таким как сотрудники, а также к внешним удостоверениям, таким как партнеры, поставщики и поставщики.

Используйте единый вход Azure AD (SSO) для управления доступом к данным и ресурсам Организации в локальной среде и в облаке, а также для обеспечения безопасного доступа к ним. Подключение всех пользователей, приложений и устройств к Azure AD для обеспечения беспрепятственного и безопасного доступа, а так же улучшенного контроля. 

- [Общие сведения о единый вход приложений в Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Архитектура безопасности](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Управление удостоверениями и ключами](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4. использование строгих средств проверки подлинности для всех Azure Active Directory доступом

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| IM-4 | 4,2, 4,4 4,5, 11,5, 12,11, 16,3 | AC-2, AC-3, IA-2, IA-4 |

Azure AD поддерживает строгое управление проверкой подлинности с помощью многофакторной проверки подлинности (MFA) и надежных методов безпарольного ввода.  
- Многофакторная проверка подлинности. Включите Azure AD MFA и следуйте рекомендациям по управлению удостоверениями и доступом в центре безопасности Azure для настройки MFA. MFA можно применять ко всем пользователям, выбирать пользователей или на уровне отдельных пользователей, учитывая условия входа и факторы риска. 

- Проверка подлинности с паролем: доступны три варианта проверки подлинности с паролем: Windows Hello для бизнеса, Microsoft Authenticator приложение и локальные методы проверки подлинности, такие как смарт-карты. 

Для администраторов и привилегированных пользователей убедитесь, что используется самый высокий уровень надежной проверки подлинности, а затем выполнив соответствующую политику строгой проверки подлинности другим пользователям.

Если для проверки подлинности Azure AD по-прежнему используется устаревшая проверка подлинности на основе паролей, имейте в виду, что учетные записи только для облака (учетные записи пользователей, созданные непосредственно в Azure) имеют стандартную политику паролей. И гибридные учетные записи (учетные записи пользователей, поступающие из локальной Active Directory) следуют локальным политикам паролей. При использовании проверки подлинности на основе пароля Azure AD предоставляет возможность защиты паролем, которая запрещает пользователям задавать пароли, которые легко угадать. Корпорация Майкрософт предоставляет глобальный список запрещенных паролей, которые обновляются на основе данных телеметрии, и клиенты могут дополнять список в соответствии с потребностями (например, фирменная символика, культурные ссылки и т. д.). Эту защиту паролей можно использовать только для облачных и гибридных учетных записей. 

Примечание. Проверка подлинности на основе только учетных данных пароля является уязвимой для популярных методов атак. Для повышения безопасности используйте строгую проверку подлинности, такую как MFA, и политику надежных паролей. Для приложений сторонних производителей и служб Marketplace, которые могут иметь пароли по умолчанию, их следует изменить во время начальной настройки службы. 

- [Включение MFA в Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Общие сведения о параметрах проверки подлинности с паролем для Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Политика паролей Azure AD по умолчанию](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Устранение недопустимых паролей с помощью защиты паролей Azure AD](../../active-directory/authentication/concept-password-ban-bad.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Архитектура безопасности](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Управление удостоверениями и ключами](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: отслеживание и оповещение об аномалиях учетной записи

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| IM-5 | 4,8, 4,9, 16,12, 16,13 | AC-2, AC-3, AC-7, AU-6 |

Azure AD предоставляет следующие источники данных: 
-   Входы. Отчет о входах содержит информацию об использовании управляемых приложений и входах пользователей.

-   Журналы аудита — обеспечивает трассировку с помощью журналов для всех изменений, внесенных с помощью различных функций в Azure AD. В число журналов аудита внесенных в журнал изменений входят добавление или удаление пользователей, приложений, групп, ролей и политик.

-   Входы, представляющие риск. Вход, представляющий риск, означает, что в систему пытался войти пользователь, который не является законным владельцем учетной записи.

-   Пользователи, находящиеся в группе риска. Такая пометка означает, что конфиденциальность учетной записи пользователя, возможно, нарушена.

Эти источники данных можно интегрировать с Azure Monitor, Sentinel Azure или сторонними SIEM системами.

Центр безопасности Azure также может оповещать о некоторых подозрительных действиях, например о чрезмерном числе неудачных попыток проверки подлинности, а также об устаревших учетных записях в подписке. 

Azure Advanced Threat protection (ATP) — это решение для обеспечения безопасности, которое может использовать локальные Active Directoryные сигналы для выявления, обнаружения и исследования сложных угроз, скомпрометированных удостоверений и действий по предварительной проверке.

- [Отчеты о действиях аудита в Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Просмотр рискованных входов в Azure AD](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Как определить пользователей Azure AD, помеченных для события риска](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Мониторинг пользовательских действий идентификации и доступа в Центре безопасности Azure](../../security-center/security-center-identity-access.md)

- [Оповещения в модуле защиты "аналитика угроз" центра безопасности Azure](/azure/security-center/alerts-reference)

- [Как интегрировать журналы действий Azure в Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Подключение данных из защита идентификации Azure AD](../../sentinel/connect-azure-ad-identity-protection.md)

- [Расширенная защита от угроз Azure](/azure-advanced-threat-protection/what-is-atp)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: ограничение доступа к ресурсам Azure на основе условий

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| IM-6 | Недоступно | AC-2, AC-3 |

Используйте условный доступ Azure AD для более детализированного контроля доступа на основе определяемых пользователем условий, например для входа пользователей из определенных диапазонов IP-адресов для использования MFA. Управление сеансом с детализированной проверкой подлинности также можно использовать с помощью политики условного доступа Azure AD для различных вариантов использования. 

- [Общие сведения о условном доступе в Azure](../../active-directory/conditional-access/overview.md)

- [Распространенные политики условного доступа](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Настройка управления сеансом проверки подлинности с условным доступом](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md).

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Управление удостоверениями и ключами](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Анализ угроз](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: устранение непреднамеренной раскрытия учетных данных

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| IM-7 | 18,1, 18,7 | IA-5 |

Реализуйте средство проверки учетных данных Azure DevOps для указания учетных данных в коде. Сканер учетных данных также способствует перемещению обнаруженных учетных данных в более безопасные расположения, такие как Azure Key Vault.

Для GitHub можно использовать функцию просмотра собственных секретов для указания учетных данных или других форм секретов в коде.

- [Как настроить сканер учетных данных](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [Проверка секрета GitHub](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: безопасный доступ пользователей к устаревшим приложениям

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| IM-8 | 14,6 | AC-2, AC-3, SC-11 |

Убедитесь, что у вас есть современные элементы управления доступом и наблюдение за сеансами для устаревших приложений и данных, которые они хранят и обрабатывают. Хотя виртуальные частные сети обычно используются для доступа к устаревшим приложениям, они часто имеют только базовый контроль доступа и ограниченный мониторинг сеансов.

Azure AD Application Proxy позволяет публиковать устаревшие локальные приложения для удаленных пользователей с помощью единого входа (SSO) и явно проверять надежность как удаленных пользователей, так и устройств с помощью условного доступа Azure AD. 

Кроме того, Microsoft Cloud App Security — это служба брокера облачного доступа (КАСБ), которая может предоставлять элементы управления для отслеживания сеансов приложения пользователя и блокировки действий (как для устаревших локальных приложений, так и для приложений SaaS). 

- [AD Application Proxy Azure](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Рекомендации по Microsoft Cloud App Security](/cloud-app-security/best-practices)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов** (дополнительные[сведения](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Архитектура безопасности](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Безопасность инфраструктуры и конечных точек](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
