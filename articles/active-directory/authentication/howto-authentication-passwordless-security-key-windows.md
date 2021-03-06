---
title: Вход с защитой паролем в Windows — Azure Active Directory
description: Включение входа с использованием ключа безопасности без пароля в Azure AD с помощью ключей безопасности FIDO2 (Предварительная версия)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce2b420c2124c86610058ce2f31cd6d7bf620a97
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848465"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Включение входа с помощью ключа безопасности без пароля на устройствах Windows 10 (Предварительная версия)

В этом документе рассматривается включение проверки подлинности без пароля на основе ключа безопасности FIDO2 на устройствах Windows 10. В конце этой статьи вы сможете войти в веб-приложения и присоединенные к Azure AD устройства Windows 10 с помощью учетной записи Azure AD, используя ключ безопасности FIDO2.

|     |
| --- |
| Ключи безопасности FIDO2 — это общедоступная Предварительная версия функции Azure Active Directory. См. дополнительные сведения о [дополнительных условиях использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="requirements"></a>Требования

- [Многофакторная идентификация Azure](howto-mfa-getstarted.md)
- [Общая Предварительная версия регистрации сведений о безопасности](concept-registration-mfa-sspr-combined.md)
- Совместимые [ключи безопасности FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- Для WebAuthN требуется Windows 10 версии 1809 или более поздней.
- Для [устройств, присоединенных к Azure AD](../devices/concept-azure-ad-join.md) , требуется Windows 10 версии 1809 или более поздней.
- [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (необязательно)
- Пакет подготовки (необязательно)

### <a name="unsupported-scenarios"></a>Неподдерживаемые сценарии

- Развертывание Windows Server домен Active Directory Services (AD DS), присоединенном к домену (только локальные устройства), **не поддерживается**.
- Сценарии RDP, VDI и Citrix **не поддерживаются** с помощью ключа безопасности.
- S/MIME **не поддерживается** с помощью ключа безопасности.
- "Запуск от имени" **не поддерживается** с помощью ключа безопасности.
- Вход на сервер с помощью ключа безопасности **не поддерживается**.
- Если вы не использовали ключ безопасности для входа на устройство в режиме "в сети", вы не сможете использовать его для входа или разблокировки в автономном режиме.
- Вход или разблокировка устройства Windows 10 с ключом безопасности, содержащим несколько учетных записей Azure AD. В этом сценарии будет использоваться последняя учетная запись, добавленная к ключу безопасности. Процесс WebAuthN позволит пользователям выбрать учетную запись, которую хотите использовать.

## <a name="prepare-devices-for-preview"></a>Подготовка устройств для предварительной версии

Устройства, присоединенные к Azure AD, для которых выполняется пилотное развертывание, должны работать под управлением Windows 10 версии 1809 или более поздней. В Windows 10 версии 1903 или более поздней.

## <a name="enable-security-keys-for-windows-sign-in"></a>Включение ключей безопасности для входа в Windows

Организации могут использовать один или несколько из следующих методов, чтобы включить использование ключей безопасности для входа в Windows в соответствии с требованиями Организации.

- [Включение с помощью Intune](#enable-with-intune)
- [Целевое развертывание Intune](#targeted-intune-deployment)
- [Включить с помощью пакета подготовки](#enable-with-a-provisioning-package)

### <a name="enable-with-intune"></a>Включение с помощью Intune

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите к **Microsoft Intune** > **регистрации устройств** > **регистрации Windows** > **Свойства** **Windows Hello для бизнеса** > .
1. В разделе **Параметры** установите для параметра **Вход использовать ключи безопасности** значение **включено**.

Настройка ключей безопасности для входа в систему не зависит от настройки Windows Hello для бизнеса.

### <a name="targeted-intune-deployment"></a>Целевое развертывание Intune

Чтобы включить поставщик учетных данных для конкретных групп устройств, используйте следующие пользовательские параметры через Intune.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите в **Microsoft Intune** > **Конфигурация устройства** > **Профили** > **создать профиль**.
1. Настройте новый профиль со следующими параметрами.
   1. Имя: ключи безопасности для входа в Windows
   1. Описание: Включение использования ключей безопасности FIDO во время входа Windows
   1. Платформа: Windows 10 и более поздние версии
   1. Тип профиля: настраиваемый
   1. Настраиваемые параметры OMA-URI:
      1. Имя: включить ключи безопасности FIDO для входа в Windows
      1. OMA-URI:./девице/вендор/мсфт/пасспортфорворк/секуритикэй/усесекуритикэйфорсигнин
      1. Тип данных: целое число
      1. Значение: 1
1. Эту политику можно назначить конкретным пользователям, устройствам или группам. Дополнительные сведения можно найти в статье [Назначение профилей пользователей и устройств в Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Создание настраиваемой политики конфигурации устройств Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Включить с помощью пакета подготовки

Для устройств, не управляемых Intune, можно установить пакет подготовки, чтобы включить функциональные возможности. Приложение "конструктор конфигураций Windows" можно установить из [Microsoft Store](https://www.microsoft.com/en-us/p/windows-configuration-designer/9nblggh4tx22).

1. Запустите конструктор конфигураций Windows.
1. Выберите **файл** > **Новый проект**.
1. Присвойте проекту имя и запишите путь, по которому создан проект.
1. Щелкните **Далее**.
1. Оставьте выбранный в качестве **рабочего процесса проекта** **пакет подготовки** и нажмите кнопку **Далее**.
1. Выберите **все выпуски Windows Desktop** в разделе **Выбор параметров для просмотра и настройки** и нажмите кнопку **Далее**.
1. Выберите **Готово**.
1. В созданном проекте перейдите к **параметрам среды выполнения** > **виндовшеллофорбусинесс** > **секуритикэйс** > **усесекуритикэйфорсигнин**.
1. Установите для параметра **усесекуритикэйфорсигнин** значение **Enabled**.
1. Выберите **экспорт** > **подготовки пакета**
1. Оставьте значения по умолчанию в окне **Сборка** в разделе **Описание пакета подготовки** и нажмите кнопку **Далее**.
1. Оставьте значения по умолчанию в окне **Сборка** в разделе **выберите сведения о безопасности для пакета подготовки** и нажмите кнопку **Далее**.
1. Запишите или измените путь в окнах **Сборка** в разделе **Выбор места сохранения пакета подготовки** и нажмите кнопку **Далее**.
1. Выберите **Сборка** на странице **Построение пакета подготовки** .
1. Сохраните два созданных файла (ppkg и CAT) в расположении, где их можно будет применить к компьютерам позже.
1. Следуйте указаниям в статье [Применение пакета подготовки](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)для применения созданного пакета подготовки.

> [!NOTE]
> Устройства под управлением Windows 10 версии 1809 также должны включать режим общего компьютера (Енаблешаредпкмоде). Сведения о включении этой функции можно найти в статье [Настройка общего или ГОСТЕВОГО компьютера с Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

## <a name="sign-in-to-windows-with-a-fido2-security-key"></a>Вход в Windows с помощью ключа безопасности FIDO2

В приведенном ниже примере пользователь Bala Сандху уже предоставил свой ключ безопасности FIDO2, выполнив действия, описанные в предыдущей статье, и [Включите вход с помощью ключа безопасности без пароля](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Bala может выбрать поставщик учетных данных ключа безопасности на экране блокировки Windows 10 и вставить ключ безопасности для входа в Windows.

![Вход с помощью ключа безопасности на экране блокировки Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Управление биометрической метрикой, ПИН-кодом или сбросом ключа безопасности

* Windows 10 версии 1903 или более поздней
   * Пользователи могут открывать **Параметры Windows** на своих **учетных записях** > устройств > **ключе безопасности** .
   * Пользователи могут изменить свой ПИН-код, обновить биометрию или сбросить ключ безопасности

## <a name="troubleshooting-and-feedback"></a>Устранение неполадок и обратная связь

Если вы хотите поделиться отзывами или столкнуться с проблемами при предварительном просмотре этой функции, предоставьте общий доступ через приложение центра отзывов Windows.

1. Запустите **центр отзывов** и убедитесь, что вы вошли в него.
1. Отправьте отзыв по следующей категории:
   1. Категория: безопасность и конфиденциальность
   1. Подкатегория: FIDO
1. Чтобы записать журналы, используйте параметр: **повторно создать мою проблему**

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

### <a name="does-this-work-in-my-on-premises-environment"></a>Работает ли это в локальной среде?

Эта функция не работает в чистой локальной среде домен Active Directory служб (AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Моей организации требуется двухфакторная проверка подлинности для доступа к ресурсам, что можно сделать для поддержки этого требования?

Ключи безопасности бывают разными конструктивными факторами. Обратитесь к изготовителю устройства, чтобы обсудить, как можно включить устройства с помощью ПИН-кода или биометрической метрики в качестве второго фактора.

### <a name="can-admins-set-up-security-keys"></a>Могут ли администраторы настраивать ключи безопасности?

Мы работаем над этой возможностью в общедоступной версии этой функции.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Где можно найти соответствующие ключи безопасности?

[Ключи безопасности FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Что делать, если я потеряли ключ безопасности?

Вы можете удалить ключи из портал Azure, перейдя на страницу сведений о безопасности и удалив ключ безопасности.

## <a name="next-steps"></a>Дальнейшие действия

[Дополнительные сведения о регистрации устройств](../devices/overview.md)

[Дополнительные сведения о службе многофакторной идентификации Azure](../authentication/howto-mfa-getstarted.md)
