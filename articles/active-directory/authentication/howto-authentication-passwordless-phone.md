---
title: Вход без пароля с помощью Microsoft Authenticator приложения Azure Active Directory
description: Включение входа без пароля в Azure AD с помощью Microsoft Authenticator приложения (Предварительная версия)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a7bf9690b4f50c771afc4745bbc2e5377adbba
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848482"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app-preview"></a>Включение входа без пароля с помощью приложения Microsoft Authenticator (Предварительная версия)

Приложение Microsoft Authenticator позволяет войти в любую учетную запись Azure AD, не используя пароль. Как и аналогичная технология [Windows Hello для бизнеса](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator использует аутентификацию на основе ключей, привязывает учетные данные пользователя к определенному устройству и применяет биометрические данные или ПИН-код. Этот метод проверки подлинности можно использовать на любой платформе устройства, включая мобильные приложения, и с любым приложением или веб-сайтом, интегрированным с библиотеками проверки подлинности Майкрософт. 

![Пример входа в браузер с запросом пользователя на утверждение входа](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Вместо вывода запроса на ввод пароля после ввода имени пользователя, пользователь, который включил вход с помощью телефона из приложения Microsoft Authenticator, увидит сообщение о том, что нужно коснуться номера в своем приложении. Пользователь должен передать этот номер в приложение, нажать кнопку подтверждения и предоставить ПИН-код или биометрические данные, чтобы завершить процесс аутентификации.

> [!NOTE]
> Эта возможность наблюдается в Microsoft Authenticator приложении, начиная с марта 2017, поэтому существует вероятность того, что при включении политики для каталога пользователи могут столкнуться с этим потоком немедленно и видеть сообщение об ошибке, если они не были включены политикой. Учитывайте это и не забудьте предупредить пользователей.

## <a name="prerequisites"></a>Технические условия

- Многофакторная идентификация Azure с Push-уведомлениями, разрешенными в качестве метода проверки 
- Последняя версия Microsoft Authenticator на устройствах под управлением iOS версии 8.0 или более поздней версии или Android 6.0 или более поздней версии.

> [!NOTE]
> Если вы включили Microsoft Authenticator предыдущую предварительную версию входа без пароля приложения с помощью Azure AD PowerShell, она была включена для всего каталога. Если вы включили использование этого нового метода, он будет заменен политикой PowerShell. Рекомендуется включить для всех пользователей в клиенте с помощью новых методов проверки подлинности, иначе пользователи, которых нет в новой политике, больше не смогут выполнять вход в пассвордлессли. 

## <a name="enable-passwordless-authentication-methods"></a>Включить методы проверки подлинности, не имеющие пароля

### <a name="enable-the-combined-registration-experience"></a>Включение объединенного процесса регистрации

Функции регистрации для методов проверки подлинности, не защищенных паролем, зависят от предварительной версии регистрации. Выполните действия, описанные в статье [Включение общей регистрации сведений о безопасности (Предварительная версия)](howto-registration-mfa-sspr-combined.md), чтобы включить объединенную предварительную версию регистрации.

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Включить методы проверки подлинности для входа с помощью телефона без пароля

1. Войдите на [портал Azure](https://portal.azure.com)
1. Найдите и выберите *Azure Active Directory*. Выбор **методов проверки Подлинности** > **безопасности** > **политики метода проверки подлинности (Предварительная версия)**
1. В разделе **безпарольный вход с помощью телефона**выберите следующие параметры.
   1. **Включить** -да или нет
   1. **Target** — все пользователи или выбранные пользователи
1. **Сохраните** , чтобы задать новую политику

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Регистрация пользователей и управление Microsoft Authenticator приложением

1. Перейдите на сайт [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
1. Войти, если он еще не создан
1. Добавьте приложение для проверки подлинности, нажав кнопку **Добавить метод**, выбрав **Приложение проверки подлинности**и нажав кнопку **добавить** .
1. Следуйте инструкциям по установке и настройке приложения Microsoft Authenticator на устройстве.
1. Нажмите кнопку **Готово** , чтобы завершить процесс настройки приложения проверки подлинности mfa. 
1. В **Microsoft Authenticator**выберите параметр **включить вход** с помощью телефона в раскрывающемся меню Учетная запись.
1. Следуйте инструкциям в приложении, чтобы завершить регистрацию для входа на телефон без пароля. 

Организации могут указывать своим пользователям на статью [Вход с помощью телефона, а не пароль](../user-help/microsoft-authenticator-app-phone-signin-faq.md) для дальнейшей помощи в настройке Microsoft Authenticator приложения и активации входа с помощью телефона.

## <a name="sign-in-with-passwordless-credential"></a>Вход с использованием учетных данных без пароля

На этапе общедоступной предварительной версии у вас нет возможности обязать пользователей создавать или использовать новый тип учетных данных. Пользователь будет выполнять вход без пароля только после того, как администратор включил свой клиент **, а** пользователь обновил свое приложение Microsoft Authenticator, чтобы включить вход с помощью телефона.

После ввода имени пользователя в Интернете и выбора пункта " **Далее**" пользователям будет предоставлено число, а в Microsoft Authenticator приложении появится запрос на выбор подходящего номера для проверки подлинности вместо использования пароля. 

![Пример входа в браузер с помощью приложения Microsoft Authenticator](./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png)

## <a name="known-issues"></a>Известные проблемы

### <a name="user-is-not-enabled-by-policy-but-still-has-passwordless-phone-sign-in-method-in-microsoft-authenticator"></a>Пользователь не включен с помощью политики, но по-прежнему имеет метод входа без пароля на телефоне в Microsoft Authenticator

Возможно, пользователь в какой-то момент создал учетные данные для входа в систему без пароля в текущем Microsoft Authenticator приложении или на более раннем устройстве. Когда администратор включает политику метода проверки подлинности для входа без пароля, любой пользователь с зарегистрированными учетными данными начнет работать с новым запросом на вход, независимо от того, включены ли они для использования политики. Если пользователю не разрешено использовать учетные данные с помощью политики, они увидят ошибку после завершения потока проверки подлинности. 

Администратор может разрешить пользователю использовать для входа с использованием телефона без пароля или пользователь должен удалить метод. Если пользователь больше не имеет зарегистрированного устройства, он может вернуться в [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) и удалить его. Если для MFA все еще используется средство проверки подлинности, можно выбрать параметр **отключить вход** с помощью телефона из Microsoft Authenticator.  

### <a name="ad-fs-integration"></a>Интеграция AD FS

Когда пользователь включит учетные данные Microsoft Authenticator без пароля, аутентификация этого пользователя по умолчанию всегда будет выполняться через уведомление для подтверждения. Эта логика предотвращает перенаправление пользователя гибридного клиента в ADFS для проверки входа, если этот пользователь не выбрал действие "Использовать свой пароль". Этот процесс также будут обходить любые локальные политики условного доступа и потоки сквозной проверки подлинности. 

Если у пользователя есть ожидающая проверка входа без пароля, а попытки входа еще не выполнены, пользователь может перейти в ADFS для ввода пароля.  

### <a name="azure-mfa-server"></a>Сервер Azure MFA

Конечные пользователи, которые поддерживают MFA через локальный сервер Azure MFA Организации, могут по-прежнему создавать и использовать один и тот же учетные данные для входа в систему без пароля. Если пользователь введет одну учетную запись в несколько (5 и более) установок Microsoft Authenticator, это может привести к ошибке.  

### <a name="device-registration"></a>Регистрация устройства

Одним из предварительных условий для создания новых надежных учетных данных является то, что устройство, на котором установлено Microsoft Authenticator приложение, также должно быть зарегистрировано в клиенте Azure AD для отдельного пользователя. Из-за текущих ограничений на регистрацию устройств устройство может быть зарегистрировано только в одном клиенте. Это означает, что в приложении Microsoft Authenticator можно настроить только одну рабочую или учебную учетную запись для входа с телефона.

> [!NOTE]
> Регистрация устройств не аналогична управлению устройствами или MDM. Он связывает только идентификатор устройства и идентификатор пользователя вместе в каталоге Azure AD.  

## <a name="next-steps"></a>Дальнейшие действия

[Что такое пароль?](concept-authentication-passwordless.md)

[Сведения о регистрации устройств](../devices/overview.md#getting-devices-in-azure-ad)

[Сведения о многофакторной идентификации Azure](../authentication/howto-mfa-getstarted.md)
