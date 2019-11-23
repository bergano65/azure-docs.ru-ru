---
title: Preventing attacks using smart lockout - Azure Active Directory
description: Смарт-блокировка Azure Active Directory помогает защитить вашу организацию от атак методом подбора
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 066c4cb598d9a8c14ab5d6ee893376266e104d15
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381528"
---
# <a name="azure-active-directory-smart-lockout"></a>Смарт-блокировка Azure Active Directory

Смарт-блокировка помогает блокировать злоумышленников, которые пытаются угадать пароли пользователей или использовать методы подбора пароля для входа. Эта функция позволяет распознавать входные сигналы от действительных пользователей и относиться к ним иначе, нежели к злоумышленникам и другим неизвестным источникам. Механизм смарт-блокировки блокирует доступ для злоумышленников, пытающихся войти в систему. При этом обычные пользователи могут спокойно получать доступ к учетным записям и продолжать работу.

По умолчанию после 10 неудачных попыток смарт-блокировка блокирует учетную запись от попыток входа в систему на одну минуту. Учетная запись повторно блокируется после каждой последующей неудачной попытки входа (сначала доступ отсутствует одну минуту, а при последующих попытках — дольше).

Смарт-блокировка отслеживает хэши последних трех попыток неправильного ввода пароля во избежание повторного увеличения счетчика блокировки для того же пароля. Если кто-то введет тот же неверный пароль несколько раз, это не вызовет блокировку учетной записи.

 > [!NOTE]
 > Функция отслеживания хэшей недоступна для клиентов, использующих сквозную аутентификацию, так как аутентификация выполняется локально, а не в облаке.

Federated deployments using AD FS 2016 and AF FS 2019 can enable similar benefits using [AD FS Extranet Lockout and Extranet Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Для всех клиентов Azure AD, у которых установлены эти настройки по умолчанию, обеспечивающие оптимальное сочетание безопасности и удобства в использовании, смарт-блокировка всегда включена. Customization of the smart lockout settings, with values specific to your organization, requires paid Azure AD licenses for your users.

Using smart lockout does not guarantee that a genuine user will never be locked out. When smart lockout locks a user account, we try our best to not lockout the genuine user. Служба блокировки пытается ограничить доступ злоумышленников к учетной записи настоящего пользователя.  

* Каждый центр обработки данных Azure Active Directory отслеживает блокировку независимо друг от друга. У пользователя есть определенное число попыток (threshold_limit * datacenter_count) для каждого центра обработки данных.
* Смарт-блокировка различает злоумышленников и подлинных пользователей по расположению (неизвестное и известное). Для каждого типа расположения будут предусмотрены отдельные счетчики блокировки.

Смарт-блокировку можно интегрировать в гибридные среды, используя синхронизацию хэшей паролей или сквозную проверку подлинности для защиты локальных учетных записей Active Directory от блокировки злоумышленниками. Если правильно установить политики смарт-блокировки в Azure AD, то прежде, чем атаки смогут достичь локальной среды Active Directory, они будут отфильтрованы.

При использовании [сквозной аутентификации](../hybrid/how-to-connect-pta.md) необходимо убедиться, что:

* Пороговое значение блокировки Azure AD должно быть **меньше**, чем пороговое значение блокировки учетных записей Active Directory. Необходимо, чтобы пороговое значение блокировки учетных записей Active Directory как минимум в два-три раза превышало пороговое значение блокировки Azure AD. 
* The Azure AD lockout duration must be set longer than the Active Directory reset account lockout counter after duration. Be aware that the Azure AD duration is set in seconds, while the AD duration is set in minutes. 

For example, if you want your Azure AD counter to be higher than AD, then Azure AD would be 120 seconds (2 minutes) while your on-premises AD is set to 1 minute (60 seconds).

> [!IMPORTANT]
> Currently, an administrator can't unlock the users' cloud accounts if they have been locked out by the Smart Lockout capability. Администраторы должны подождать, пока не завершится срок действия блокировки. However, the user can unlock by using self-service password reset (SSPR) from a trusted device or location.

## <a name="verify-on-premises-account-lockout-policy"></a>Проверка локальных политик блокировки учетных записей

Чтобы проверить локальную политику блокировки учетных записей Active Directory, сделайте следующее:

1. Откройте средства управления групповыми политиками.
2. Измените групповую политику, которая включает в себя политики блокировки учетных записей организации, например **политику домена по умолчанию**.
3. Последовательно выберите **Конфигурация компьютера** > **Политики** > **Параметры Windows** > **Параметры безопасности** > **Политики учетных записей** > **Политика блокировки учетной записи**.
4. Проверьте **пороговое значение блокировки учетных записей** и **время до сброса счетчика блокировки учетных записей**.

![Modify the on-premises Active Directory account lockout policy](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Управление значениями смарт-блокировки Azure AD

Исходя из требований организации, возможно потребуется настроить значение смарт-блокировки. Customization of the smart lockout settings, with values specific to your organization, requires paid Azure AD licenses for your users.

Чтобы проверить или изменить значения смарт-блокировки для организации, выполните следующие действия.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Найдите и выберите *Azure Active Directory*. Select **Authentication methods** > **Password protection**.
1. В зависимости от того, сколько попыток неудачного входа разрешено до первой блокировки учетной записи, установите **пороговое значение блокировки**. По умолчанию задано значение 10.
1. Чтобы указать время для каждой блокировки (в секундах), установите параметр **Длительность блокировки в секундах**. Значение по умолчанию — 60 секунд (одна минута).

> [!NOTE]
> Если первый вход после блокировки завершится ошибкой, учетная запись будет снова заблокирована. Если учетная запись будет заблокирована несколько раз, продолжительность блокировки увеличится.

![Настройка политики смарт-блокировки Azure AD на портале Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>How to determine if the Smart lockout feature is working or not

When the smart lockout threshold is triggered, you will get the following message while the account is locked:

**Your account is temporarily locked to prevent unauthorized use. Try again later, and if you still have trouble, contact your admin.**

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка пользовательского списка заблокированных паролей](howto-password-ban-bad.md)
* [Быстрое развертывание самостоятельного сброса пароля в Azure AD](quickstart-sspr.md)
