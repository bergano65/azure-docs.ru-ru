---
title: Настройка контрольных вопросов в качестве метода проверки — Azure AD
description: Настройка страницы сведений о безопасности (Предварительная версия) для проверки личности с помощью заранее определенных контрольных вопросов в качестве метода проверки.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c32e2e2a7b609f999c7b804494188e2c6820171
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279001"
---
# <a name="set-up-security-questions-as-your-verification-method"></a>Настройка контрольных вопросов в качестве метода проверки

Чтобы добавить свой метод сброса пароля, выполните следующие действия. После первоначальной настройки вы можете вернуться к странице **Сведения для защиты**, чтобы добавить, обновить или удалить сведения для защиты.

После настройки метода сброса пароля необходимо также настроить метод двухфакторной проверки с помощью [приложения проверки подлинности ](security-info-setup-auth-app.md), [текстового сообщения](security-info-setup-text-msg.md) или [телефонного звонка](security-info-setup-phone-number.md).

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

## <a name="set-up-your-security-questions-from-the-security-info-page"></a>Настройка контрольных вопросов на странице сведений для защиты

В зависимости от параметров вашей организации вы сможете выбрать несколько контрольных вопросов и ответить на них для проверки сведений для защиты. Администратор устанавливает количество контрольных вопросов, на которые вы должны ответить.

Мы рекомендуем использовать контрольные вопросы вместе с другим методом. Контрольные вопросы могут оказаться менее безопасными в сравнении с другими методами, так как некоторые пользователи могут знать ответы на вопросы других пользователей.

> [!Note]
> Контрольные вопросы надежно хранятся в объекте пользователя в каталоге. Ответы на них вы можете задать только в процессе регистрации. Администратор не может прочитать или изменить ваши вопросы или ответы.
>
> Если вы не видите параметр для настройки контрольных вопросов, возможно, ваша организация не позволяет использовать их для проверки. Если это так, вам нужно выбрать другой метод или обратиться за помощью к администратору.
>
> Учетным записям администратора запрещено использовать контрольные вопросы в качестве метода сброса пароля. Если вы вошли в систему как учетную запись уровня администратора, эти параметры отображаться не будут.

### <a name="to-set-up-your-security-questions"></a>Настройка контрольных вопросов

1. Войдите в рабочую или учебную учетную запись, а затем перейдите на страницу https://myprofile.microsoft.com/.

    ![Страница "Мой профиль" с выделенными ссылками на сведения для защиты](media/security-info/securityinfo-myprofile-page.png)

2. На панели навигации слева выберите **Сведения для защиты** или щелкните ссылку в блоке **Сведения для защиты**, а затем выберите **Добавить метод** на странице **Сведения для защиты**.

    ![Страница сведений для защиты с выделенным параметром "Добавить метод"](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. На странице **Добавить метод** выберите из раскрывающегося списка **Контрольные вопросы**, а затем щелкните **Добавить**.

    ![Поле "Добавить метод" с выбранным параметром "Контрольные вопросы"](media/security-info/securityinfo-myprofile-addquestions.png)

4. На странице **Контрольные вопросы** выберите и ответьте на контрольные вопросы, а затем щелкните **Сохранить**.

    ![Добавление номера телефона и выбор метода телефонных звонков](media/security-info/securityinfo-myprofile-securityquestions.png)

    Сведения для защиты обновятся, и вы сможете использовать контрольные вопросы, чтобы подтвердить свои данные при сбросе пароля.

## <a name="delete-security-questions-from-your-security-info-methods"></a>Удаление контрольных вопросов из списка методов проверки сведений для защиты

Если вы больше не хотите использовать контрольные вопросы как один из способов проверки, их можно удалить со страницы **Информация для защиты**.

>[!Important]
>Если вы случайно удалили контрольные вопросы, эту операцию невозможно отменить. Вам придется снова добавить этот метод, как описано в разделе о [настройке контрольных вопросов](#set-up-your-security-questions-from-the-security-info-page) в этой статье.

### <a name="to-delete-your-security-questions"></a>Удаление контрольных вопросов

1. На странице **Сведения для защиты** щелкните ссылку **Удалить** рядом с параметром **Контрольные вопросы**.

    ![Ссылка для удаления проверки с помощью телефона со страницы сведений для защиты](media/security-info/securityinfo-myprofile-questionsdelete.png)

2. Выберите **Да** в окне подтверждения, чтобы удалить **контрольные вопросы**. При удалении контрольных вопросов этот метод удаляется из сведений для защиты и не отображается на странице **Сведения для защиты**.

## <a name="additional-security-info-methods"></a>Дополнительные методы проверки сведений для защиты

Чтобы подтвердить вашу личность, ваша организация может связаться с вами различными способами, в зависимости от того, что именно вы пытаетесь выполнить. Эти способы могут быть следующими:

- **Приложение Authenticator.** Скачайте и используйте приложение Authenticator, чтобы получать либо уведомление для утверждения, либо случайно созданный код утверждения для двухфакторной проверки подлинности или сброса пароля. Пошаговые инструкции о том, как настроить и использовать приложение Microsoft Authenticator, см. в статье о [настройке использования приложения проверки подлинности в сведениях для защиты](security-info-setup-auth-app.md).

- **Тестовое сообщение на мобильное устройство.** Введите номер своего мобильного устройства и получите сообщение с кодом, необходимым для двухфакторной проверки подлинности или сброса пароля. Пошаговые инструкции по проверке личности с помощью текстового сообщения (SMS) см. в статье о [настройке использования текстовых сообщений (SMS) в сведениях для защиты](security-info-setup-text-msg.md).

- **Звонок на мобильный или рабочий телефон.** Введите номер своего мобильного устройства, чтобы получить звонок с информацией для двухфакторной проверки подлинности или сброса пароля. Пошаговые инструкции о том, как подтвердить свою личность с помощью номера телефона, см. в статье о [настройке использования телефонных звонков в сведениях для защиты](security-info-setup-phone-number.md).

- **Ключ безопасности.** Зарегистрируйте ключ безопасности, совместимый с Майкрософт, и используйте его вместе с ПИН-кодом для двухфакторной проверки подлинности или сброса пароля. Пошаговые инструкции по проверке удостоверения с помощью ключа безопасности см. [в разделе Настройка сведений о безопасности для использования ключа безопасности](security-info-setup-security-key.md).

- **Адрес электронной почты.** Введите рабочий или учебный адрес электронной почты, чтобы получить электронное письмо для сброса пароля. Этот вариант недоступен для двухфакторной проверки подлинности. Пошаговые инструкции о том, как настроить адрес электронной почты, см. в статье о [настройке использования электронной почты в сведениях для защиты](security-info-setup-email.md).

    >[!Note]
    >Если некоторые из этих параметров отсутствуют, скорее всего, ваша организация не разрешает данные методы. Если это так, вам нужно выбрать доступный метод или обратиться за помощью к администратору.

## <a name="next-steps"></a>Дополнительная информация

- Если вы забыли свой пароль, сбросьте его на [портале сброса пароля](https://passwordreset.microsoftonline.com/) или выполните действия, описанные в статье о [сбросе пароля рабочей или учебной учетной записи](active-directory-passwords-update-your-own-password.md).

- Советы по устранению неполадок и справку по проблемам со входом в систему см. в статье [Не удается войти в учетную запись Майкрософт](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
