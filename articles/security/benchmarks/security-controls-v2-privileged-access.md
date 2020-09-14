---
title: Тестирование безопасности Azure v2 — привилегированный доступ
description: Привилегированный доступ к системе безопасности Azure версии 2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059434"
---
# <a name="security-control-privileged-access"></a>Управление безопасностью: привилегированный доступ

Привилегированный доступ охватывает элементы управления для защиты привилегированного доступа к вашему клиенту и ресурсам Azure. Сюда входит ряд элементов управления для защиты административной модели, административных учетных записей и рабочих станций с привилегированным доступом к намеренным и случайным рискам.

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1: защита и ограничение глобальных администраторов

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| PA-1 | 4,3, 4,8 | AC-2 |

Пользователи, которым назначена роль глобального администратора, могут считывать и изменять все административные параметры в организации Azure AD. Ограничьте число учетных записей глобального администратора Azure до двух для каждой подписки. Наиболее важными встроенными ролями в Azure AD являются глобальные администраторы и администратор привилегированных ролей, так как пользователи, которым назначены эти две роли, могут делегировать роли администратора:
- Глобальный администратор или администратор Организации. пользователи с этой ролью имеют доступ ко всем функциям администрирования в Azure AD, а также к службам, использующим удостоверения Azure AD.

- Администратор привилегированных ролей. пользователи с этой ролью могут управлять назначениями ролей в Azure AD, а также в Azure AD Privileged Identity Management (PIM). Кроме того, эта роль позволяет управлять всеми аспектами PIM и административными единицами.

Примечание. у вас могут быть другие критически важные роли, которыми необходимо управлять при использовании пользовательских ролей с назначенными привилегированными разрешениями. Кроме того, может потребоваться применить аналогичные элементы управления к учетной записи администратора критических бизнес-ресурсов.  

Вы можете включить привилегированный доступ (JIT) для ресурсов Azure и Azure AD с помощью Azure AD Privileged Identity Management (PIM). JIT предоставляет временные разрешения на выполнение привилегированных задач только в том случае, если это требуется пользователям. PIM также может создавать оповещения системы безопасности при наличии подозрительных или ненадежных действий в Организации Azure AD.

Примечание. Некоторые службы Azure, такие как SQL Azure, поддерживают проверку подлинности локального пользователя в дополнение к проверке подлинности Azure AD. Этот тип проверки подлинности локального пользователя не управляется с помощью Azure AD. Вам потребуется управлять этими пользователями отдельно.

- [Разрешения роли администратора в Azure AD](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Настраиваемые роли Azure](../../role-based-access-control/custom-roles.md)

- [Использование оповещений системы безопасности Azure управление привилегированными пользователями](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Как получить роль каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Использование центра безопасности Azure для наблюдения за удостоверениями и доступом](../../security-center/security-center-identity-access.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов**:

- [Удостоверения и ключи](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2: регулярное изучение и согласование доступа пользователей

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| PA-2 | 4,1, 16,9, 16,10 | AC-2 |

Регулярно просматривайте учетные записи пользователей и права доступа, чтобы убедиться, что учетные записи пользователей и их доступ действительны. 

Используйте проверки подлинности и доступа Azure AD для управления членством в группах, доступом к корпоративным приложениям и назначениями ролей. Служба отчетов Azure AD может предоставлять журналы для облегчения поиска устаревших учетных записей. Можно также использовать Azure AD Privileged Identity Management для создания рабочего процесса отчета по проверке доступа, чтобы упростить процесс проверки.

Для пользователей с правами администратора на уровне служб и рабочих нагрузок Azure следует выполнить более частую проверку пользователя и доступа. Вы также можете использовать оповещения системы безопасности Azure управление привилегированными пользователями, чтобы определить, когда создаются учетные записи администратора, а также найти устаревшие или неправильно настроенные учетные записи администратора. 

Примечание. Некоторые службы Azure, такие как SQL Azure, поддерживают локальных пользователей, которые не управляются с помощью Azure AD. Вам потребуется управлять этими пользователями отдельно.

- [Как получить роль каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Как получить членов роли каталога в Azure AD с помощью PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [Использование центра безопасности Azure для наблюдения за удостоверениями и доступом](../../security-center/security-center-identity-access.md)

- [Использование оповещений системы безопасности Azure управление привилегированными пользователями](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Выровняйте административные обязанности между командами](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [Общие сведения об отчетах Azure AD](/azure/active-directory/reports-monitoring/)

- [Как использовать проверки подлинности и доступа Azure AD](../../active-directory/governance/access-reviews-overview.md)

- [Управление привилегированными пользователями. Проверка доступа к ролям Azure AD](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Центр безопасности Azure — мониторинг удостоверений и доступа](../../security-center/security-center-identity-access.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов**:

- [Удостоверения и ключи](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3: Настройка учетной записи для аварийного доступа в Azure AD

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| PA-3 | 12,3 | AC-2 |

Чтобы предотвратить случайную блокировку вашей организации Azure AD, настройте учетную запись аварийного доступа для доступа, если обычные административные учетные записи использовать нельзя. Учетные записи аварийного доступа обычно имеют высокий уровень привилегий и не должны назначаться конкретным пользователям. Учетные записи для аварийного доступа используются только в непредвиденных ситуациях, в которых невозможно использовать обычные учетные записи администратора.

Следует убедиться, что учетные данные (например, пароль, сертификат или смарт-карта) для учетных записей аварийного доступа защищены и известны только тем лицам, которые имеют право использовать их только в экстренной ситуации.

- [Управление учетными записями для аварийного доступа в Azure AD](../../active-directory/users-groups-roles/directory-emergency-access.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов**:

- [Удостоверения и ключи](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Центр обеспечения безопасности (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4: Автоматизация рабочего процесса удостоверения и запроса доступа Azure

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| PA-4 | Недоступно | AC-2, AC-5, PM-10 |

Используйте функции управления обслуживанием Azure AD для автоматизации рабочих процессов запросов Azure Access, включая назначения доступа, проверки и истечение срока действия. Поддерживается также два или несколько промежуточных утверждений.  

- [Что такое управление назначениями Azure AD](../../active-directory/governance/entitlement-management-overview.md)

- [Как настроить процесс запроса и утверждения доступа](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов**:

- [Удостоверения и ключи](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5: использование высокозащищенных компьютеров для административных задач

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| PA-5 | 4,6, 11,6, 12,12 | AC-2, SC-7 |

Защищенные изолированные рабочие станции критически важны для защиты конфиденциальных ролей, таких как администраторы, разработчики и критические операторы обслуживания. Используйте высокозащищенные рабочие станции пользователей и (или) Azure бастиона для административных задач:
- Чтобы развернуть защищенную и управляемую рабочую станцию для административных задач, используйте Azure Active Directory, расширенный уровень защиты от угроз Microsoft Defender (ATP) и (или) Microsoft Intune. Защищенные рабочие станции можно централизованно управлять, чтобы обеспечить безопасную настройку, включая строгую проверку подлинности, базовые показатели программного обеспечения и оборудования, ограниченный логический и сетевой доступ. 

- Используйте функцию Azure бастиона, чтобы получить безопасный путь для доступа к виртуальным машинам через RDP или SSH. Azure бастиона — это полностью управляемая служба PaaS, которая может быть подготовлена для каждой виртуальной сети без создания новой виртуальной сети. 

- [Общие сведения о защищенных рабочих станциях под управлением Azure](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Развертывание защищенной рабочей станции, управляемой Azure](../../active-directory/devices/howto-azure-managed-workstation.md)

- [Использование узла бастиона Azure](../../bastion/bastion-create-host-portal.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов**:

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Центр обеспечения безопасности (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Удостоверения и ключи](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6: назначение привилегий для ресурсов с помощью Azure RBAC

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| PA-6 | 14,6 | AC-2, AC-3 |

Управление доступом на основе ролей (RBAC) в Azure позволяет управлять правами доступа к ресурсам Azure с помощью назначений ролей. Эти роли можно назначить пользователям, группам субъектов-служб и управляемым удостоверениям. Существуют предварительно определенные встроенные роли для определенных ресурсов, и эти роли могут быть учтены или запрошены с помощью таких средств, как Azure CLI, Azure PowerShell или портал Azure. 

- [Что такое управление доступом на основе ролей в Azure (Azure RBAC)](../../role-based-access-control/overview.md)

- [Настройка RBAC в Azure](../../role-based-access-control/role-assignments-portal.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов**:

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Управление состоянием](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Удостоверения и ключи](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7: выбор процесса утверждения для службы поддержки Майкрософт

| Идентификатор Azure | ИДЕНТИФИКАТОРы элементов управления CIS v 7.1 | ИДЕНТИФИКАТОРы для директивы NIST SP800-53 |
|--|--|--|--|
| PA-7 | 16 | AC-2 (AC-3), AC-4 |

В сценариях поддержки, в которых корпорация Майкрософт должна получить доступ к данным клиентов, защищенное хранилище предоставляет возможность явно просматривать и утверждать или отклонять запросы на доступ к данным клиентов.

- [Общие сведения о защищенное хранилище](../fundamentals/customer-lockbox-overview.md)

**Ответственность**: Customer

**Заинтересованные лица по безопасности клиентов**:

- [Безопасность приложений и DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Управление соответствием требованиям безопасности](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Удостоверения и ключи](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

