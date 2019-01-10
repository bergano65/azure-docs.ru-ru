---
title: Контрольный список лицензирования компонентов Azure Active Directory Premium P2 | Документация Майкрософт
description: Контрольный список развертывания компонентов Azure Active Directory Premium P2 на 30, 90 и более дней.
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.custom: it-pro, seodec18
ms.openlocfilehash: 4fcb692d7189c84e32f55995538ffc692cb67dd6
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064719"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Контрольный список лицензирования компонентов Azure Active Directory Premium P2

Развертывание и обеспечение безопасности Azure Active Directory (Azure AD) в организации может казаться слишком трудоемким. В этой статье описано несколько распространенных задач, которые могут быть полезны для клиентов. Чтобы обеспечить высокий уровень безопасности, клиенты обычно выполняют такие задачи в течение 30, 90 или более дней. Для получения максимальной отдачи от инвестиций этим контрольным списком могут воспользоваться даже организации, которые уже развернули Azure AD.

Правильно спланированная и реализованная инфраструктура удостоверений является залогом более безопасного доступа к рабочим нагрузкам и данным только со стороны проверенных пользователей и устройств.

## <a name="prerequisites"></a>Предварительные требования

В этом руководстве предполагается, что у вас есть лицензии Azure AD Premium P2, а также Enterprise Mobility + Security E5, Microsoft 365 E5 или подобный пакет лицензий.

[Лицензирование Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)

[Microsoft 365 корпоративный](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise).

[Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security).

## <a name="plan-and-deploy-day-1-30"></a>Планирование и развертывание: дни 1–30

- Назначьте нескольких глобальных администраторов (для экстренных случаев)
   - [Управление учетными записями администратора для аварийного доступа в Azure AD](../users-groups-roles/directory-emergency-access.md)
- Включите Azure AD Privileged Identity Management (PIM) для просмотра отчетов
   - [Начало работы с PIM](../privileged-identity-management/pim-getting-started.md)
- По возможности используйте роли, отличные от глобального администратора.
   - [Назначение ролей администратора в Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
- Authentication
   - [Как развернуть самостоятельный сброс пароля](../authentication/howto-sspr-deployment.md)
   - Разверните защиту паролем Azure AD (предварительная версия)
      - [Исключение неправильных паролей в организации](../authentication/concept-password-ban-bad.md)
      - [Применение защиты паролем Azure AD для Windows Server Active Directory](../authentication/concept-password-ban-bad-on-premises.md)
   - Настройте политики блокировки учетных записей
      - [Смарт-блокировка Azure Active Directory](../authentication/howto-password-smart-lockout.md)
      - [Блокировка и смарт-блокировка экстрасети AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)
   - [Развертывание многофакторной идентификации Azure AD с помощью политик условного доступа](../authentication/howto-mfa-getstarted.md)
   - [Включение конвергентной регистрации методов самостоятельного сброса пароля и многофакторной идентификации Azure AD (предварительная версия)](../authentication/concept-registration-mfa-sspr-converged.md)
   - [Включение Защиты идентификации Azure Active Directory](../identity-protection/enable.md)
      - [Использование событий риска для активации многофакторной идентификации и изменений паролей](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [Руководство по паролям](https://www.microsoft.com/research/publication/password-guidance/)
      - Установите минимальную длину пароля в восемь символов. Большая длина не обязательно будет лучше.
      - Отмените обязательное использование символов различных типов.
      - [Отмените обязательную регулярную смену паролей для учетных записей пользователей.](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- Синхронизируйте пользователей из локальной системы Active Directory
   - [Установка Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md)
   - [Реализация синхронизации хэшей паролей](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [Реализация обратной записи паролей](../authentication/howto-sspr-writeback.md)
   - [Внедрение Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)
- [Назначение лицензий пользователям в соответствии с членством в группах в Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>Планирование и развертывание: дни 31–90

- [Планирование доступа пользователей-гостей](../b2b/what-is-b2b.md)
   - [Add Azure Active Directory B2B collaboration users in the Azure portal](../b2b/add-users-administrator.md) (Добавление пользователей службы совместной работы Azure Active Directory B2B на портале Azure)
   - [Предоставление или отзыв приглашений пользователям B2B из отдельных организаций](../b2b/allow-deny-list.md)
   - [Предоставление пользователям B2B в Azure AD доступа к локальным приложениям](../b2b/hybrid-cloud-to-on-premises.md)
- Примите решения, касающиеся стратегии управления жизненным циклом пользователей
- [Определите стратегию управления устройствами](../devices/overview.md).
   - [Сценарии использования и рекомендации по развертыванию для присоединения к Azure AD](../devices/azureadjoin-plan.md)
- [Управление Windows Hello для бизнеса в организации](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>Планирование и развертывание: день 90 и дальше

- [Управление привилегированными пользователями Azure AD](../privileged-identity-management/pim-configure.md)
   - [Настройка параметров роли каталога Azure AD в PIM](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [Назначение ролей каталога Azure AD в PIM ](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [Завершение проверки доступа ролей каталога Azure AD в PIM](../privileged-identity-management/pim-how-to-start-security-review.md)
- Используйте комплексное управление жизненным циклом пользователей
   - В Azure AD реализован подход к управлению жизненным циклом удостоверений
   - Чтобы предотвратить несанкционированный доступ, устраните выполняемые вручную этапы из жизненного цикла учетных записей сотрудников
      - Синхронизируйте удостоверения из источника достоверных данных (системы управления персоналом) с Azure AD.
      - [Используйте динамические группы для автоматического назначения пользователей группам на основе атрибутов, например отдела, должности, региона и других, из системы управления персоналом (или другого источника достоверных данных).](../users-groups-roles/groups-dynamic-membership.md)
      - [Используйте подготовку для управления доступом на основе групп с целью автоматической подготовки пользователей для приложений SaaS.](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>Дополнительная информация

[Конфигурации доступа для удостоверений и устройств](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Стандартные рекомендуемые политики в отношении удостоверений и доступа к устройствам](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)
