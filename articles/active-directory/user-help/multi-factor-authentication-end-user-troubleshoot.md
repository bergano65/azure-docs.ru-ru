---
title: Распространенные проблемы & решения с проверкой подлинности учетной записи — Azure AD
description: Узнайте о потенциальных проблемах и решениях для некоторых из наиболее распространенных проблем с двухфакторной проверкой подлинности и вашей рабочей или учебной учетной записи.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: lizross
ms.reviewer: kexia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d998fafbf6b3ba0547991de6c3a8e71b66d91f7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231891"
---
# <a name="common-problems-and-solutions-with-two-factor-verification-and-your-work-or-school-account"></a>Распространенные проблемы и решения с двухфакторной проверкой и рабочей или учебной учетной записью

Ваша организация включила двухфакторную проверку, что означает, что для входа в рабочую или учебную учетную запись требуется сочетание имени пользователя, пароля, мобильного устройства или телефона. Дополнительная проверка обеспечивает более высокий уровень защиты, чем просто пароль, так как опирается на две формы проверки подлинности: то, что вы знаете, и то, что у вас есть. Двухфакторная проверка помогает не допустить выполнение злоумышленником входа от вашего имени, так как даже если у него есть ваш пароль, вероятнее всего, у него нет вашего мобильного устройства.

Существуют некоторые распространенные проблемы двухфакторной проверки, которые могут происходить чаще, чем любой из нас. Эта статья посвящена решению наиболее распространенных проблем и некоторых возможных исправлений.

>[!Important]
>Эти материалы предназначены для пользователей. Администраторы могут найти дополнительные сведения о том, как настроить и контролировать среду Azure Active Directory (Azure AD), в [документации по Azure Active Directory](https://docs.microsoft.com/azure/active-directory).
>
>Это содержимое также предназначено для использования с рабочей или учебной учетной записью, или аккаунтом, предоставленным вашей организацией (например, alain@contoso.com). Если у вас возникли проблемы с двухфакторной проверкой и личной учетной записью Майкрософт (учетная запись, настроенная для себя), (например, danielle@outlook.com), см. раздел [Включение или отключение двухфакторной проверки для учетной записи Майкрософт](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-forgot-my-mobile-device-at-home"></a>Я забыл мое мобильное устройство дома

Такое бывает. Вы оставили мобильное устройство дома, и теперь вы не можете использовать свой телефон для проверки того, что вы вы говорите. Если вы ранее добавили другой метод для входа в учетную запись, например рабочий телефон, вы сможете использовать этот метод сейчас. Если вы никогда не добавили дополнительный метод проверки, вам придется обратиться в службу технической поддержки и получить доступ к вашей учетной записи.

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>Вход в рабочую или учебную учетную запись с помощью другого метода проверки

1. Войдите в учетную запись обычным образом и выберите ссылку Войти по ссылке **другой путь** на странице **двухфакторной проверки** .

    ![Изменение метода проверки при входе](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    >[!Note]
    >Если вы не видите ссылку **войти другим способом**, это значит, что вы не настроили другие методы проверки. Для входа в учетную запись необходимо связаться с администратором.

2. Выберите альтернативный метод проверки и выполните процедуру двухфакторной проверки подлинности.

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>Мое мобильное устройство потеряно или было украдено

Если вы потеряли или заиграли ваше мобильное устройство, вы можете войти с помощью другого метода или попросить службу поддержки очистить параметры. Мы настоятельно рекомендуем предоставить службе технической поддержки возможность определить, был ли ваш телефон потерян или украден, чтобы в вашу учетную запись могли быть внесены соответствующие обновления. После очистки параметров вам будет предложено [зарегистрироваться для двухфакторной проверки](multi-factor-authentication-end-user-first-time.md) при следующем входе в систему.

## <a name="im-not-getting-the-verification-code-sent-to-my-mobile-device"></a>Я не получаю код проверки, отправляемый на мобильное устройство

Не получение кода проверки является распространенной проблемой, которая обычно связана с мобильным устройством и его параметрами. Попробуйте выполнить следующие действия.

- **Перезапустите мобильное устройство.** Иногда устройство просто нуждается в обновлении. Перезапуск устройства завершает работу фоновых процессов или служб, которые в настоящее время выполняются, и может вызвать проблемы, а также обновление основных компонентов устройства, перезапуская их в случае сбоя в какой – то момент.

- **Проверьте правильность сведений о безопасности.** Убедитесь, что сведения о методе проверки безопасности являются точными, особенно ваши номера телефонов. Если вы помещаете неправильный номер телефона, все оповещения будут переходят на неправильный номер. К счастью, этот пользователь не сможет выполнять никаких действий с оповещениями, но он также не поможет войти в учетную запись. Чтобы убедиться, что информация правильная, см. инструкции в статье [Управление параметрами метода двухфакторной проверки подлинности](multi-factor-authentication-end-user-manage-settings.md) .

- **Убедитесь, что уведомления включены.** Убедитесь, что на мобильном устройстве включены уведомления и выбран метод уведомления, позволяющий телефонным звонкам, приложению проверки подлинности и приложению для обмена сообщениями (для текстовых сообщений) отправлять видимые уведомления о предупреждениях на мобильное устройство.

- **Убедитесь, что у вас есть сигнал устройства и подключение к Интернету.** Убедитесь, что телефонные звонки и текстовые сообщения передаются на мобильное устройство. Получив дружественный вызов, вы можете отправить вам текстовое сообщение, чтобы убедиться, что вы получили и то, и другое. Если этого не сделать, сначала убедитесь, что ваше мобильное устройство включено. Если устройство включено, но вы по-прежнему не получаете вызов или текст, скорее всего, проблема связана с сетью, и вам нужно будет обратиться к поставщику. Если часто возникают проблемы, связанные с сигналами, мы рекомендуем установить и использовать [приложение Microsoft Authenticator](user-help-auth-app-download-install.md) на мобильном устройстве. Приложение для проверки подлинности может создавать случайные коды безопасности для входа, не требуя сигнала ячейки или подключения к Интернету.

- **Отключить параметр "не беспокоить".** Убедитесь, что вы не включили функцию "не **беспокоить** " для мобильного устройства. Если эта функция включена, уведомления не могут оповещать вас о вашем мобильном устройстве. Инструкции по отключению этой функции см. в руководстве по мобильному устройству.

- **Разблокировать номера телефонов** В США голосовой звонок от корпорации Майкрософт взят из следующих чисел: + 1 (866) 539 4191, + 1 (855) 330 8653 и + 1 (877) 668 6536.

- **Проверьте параметры, связанные с батареей.** Это выглядит немного странно на поверхности, но если вы настроили оптимизацию аккумулятора, чтобы предотвратить использование менее используемых приложений в фоновом режиме, система уведомлений, скорее всего, повлияла. Чтобы устранить эту проблему, отключите оптимизацию аккумулятора для приложения проверки подлинности и приложения для обмена сообщениями, а затем повторите попытку входа в учетную запись.

## <a name="im-not-getting-prompted-for-my-second-verification-information"></a>Я не получаю запрос на получение второй информации для проверки

Если вы выполнили вход в рабочую или учебную учетную запись, используя имя пользователя и пароль, но не получили запрос на дополнительные сведения о проверке безопасности, возможно, устройство еще не настроено. Мобильное устройство должно быть настроено для работы с дополнительным методом проверки безопасности. Чтобы убедиться, что вы включили мобильное устройство и что оно доступно для использования с методом проверки, см. статью [Управление параметрами метода двухфакторной проверки подлинности](multi-factor-authentication-end-user-manage-settings.md) . Если вы уверены, что вы не настроили устройство или учетную запись, то можете сделать это сейчас, выполнив действия, описанные в статье [Настройка учетной записи для двухфакторной проверки подлинности](multi-factor-authentication-end-user-first-time.md) .

## <a name="i-got-a-new-phone-number-how-do-i-change-it-for-two-factor-verification"></a>Я получил новый номер телефона, как изменить его для двухфакторной проверки подлинности?

Если вы получили новый номер телефона, вам потребуется обновить сведения о методе проверки безопасности, чтобы запросы на подтверждение переходили в нужное расположение. Чтобы обновить метод проверки, выполните действия, описанные в разделе **Добавление или изменение номера телефона** статьи [Управление параметрами метода для двухфакторной проверки](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number) .

## <a name="i-got-a-new-mobile-device-how-do-i-add-it"></a>Я получил новое мобильное устройство, как его добавить?

Если вы получили новое мобильное устройство, необходимо настроить его для работы с двухфакторной проверкой подлинности. Это решение состоит из нескольких этапов:

1. Настройте устройство для работы с рабочей или учебной учетной записью, выполнив действия, описанные в статье [Настройка учетной записи для двухфакторной проверки подлинности](multi-factor-authentication-end-user-first-time.md) .

2. Обновите сведения об учетной записи и устройстве на странице " **Дополнительная проверка безопасности** ", удалив старое устройство и добавив новое. Дополнительные сведения см. в статье [Управление параметрами метода для двухфакторной проверки подлинности](multi-factor-authentication-end-user-manage-settings.md) .

3. необязательный параметр. Скачайте, установите и настройте приложение Microsoft Authenticator на мобильном устройстве, выполнив действия, описанные в статье [Загрузка и установка Microsoft Authenticator приложения](user-help-auth-app-download-install.md) .

4. необязательный параметр. Включите двухфакторную проверку подлинности для доверенных устройств, выполнив действия, описанные в разделе **Включение двухфакторной проверки подлинности на странице доверенное устройство** статьи [Управление параметрами метода для двухфакторной проверки](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) .

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>Возникли проблемы при входе на мобильное устройство в поездках

Возможно, вам больше сложно использовать метод проверки, связанный с мобильными устройствами, например обмен текстовыми сообщениями, когда вы находитесь в международном расположении. Также возможно, что ваше мобильное устройство может повлечь за собой расходы на роуминг. В этой ситуации рекомендуется использовать приложение Microsoft Authenticator с возможностью подключения к хот-споту Wi/Fi. Дополнительные сведения о скачивании, установке и настройке Microsoft Authenticator приложения на мобильном устройстве см. в статье [скачивание и установка Microsoft Authenticator приложения](user-help-auth-app-download-install.md) .

## <a name="i-cant-get-my-app-passwords-to-work"></a>Я не могу заставить мои пароли приложений работать

Пароли приложений заменяют обычные пароли для старых настольных приложений, которые не поддерживают двухфакторную проверку подлинности. Во-первых, убедитесь что пароль был правильно введен. Если это не устраняет проблему, попробуйте создать новый пароль приложения, выполнив действия, описанные в разделе **Создание и удаление паролей приложений с помощью портала "Мои приложения** " статьи [Управление паролями приложений для двухфакторной проверки подлинности](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page) .

## <a name="why-cant-i-turn-two-factor-verification-off"></a>Почему не удается отключить двухфакторную проверку подлинности?

Если вы используете двухфакторную проверку подлинности с рабочей или учебной учетной записью (например, alain@contoso.com), это, скорее всего, означает, что ваша организация решила использовать эту дополнительную функцию безопасности. Если решение об использовании этой функции принято на уровне организации, у вас нет возможности индивидуально отключить ее. Однако если вы используете двухфакторную проверку подлинности с личной учетной записью, например alain@outlook.com, вы можете включить и отключить эту функцию. Инструкции по управлению двухфакторной проверкой для личных учетных записей Майкрософт см. в разделе [Включение или отключение двухфакторной проверки подлинности для учетная запись Майкрософт](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off).

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Здесь нет решения моей проблемы

Если вы выполнили эти действия, но по-прежнему используете проблемы, обратитесь за помощью в службу поддержки.

## <a name="related-articles"></a>Связанные статьи

- [Управление параметрами метода двухфакторной проверки подлинности](multi-factor-authentication-end-user-manage-settings.md)

- [Настройка учетной записи для двухфакторной проверки подлинности](multi-factor-authentication-end-user-first-time.md)

- [Часто задаваемые вопросы о приложении Microsoft Authenticator](user-help-auth-app-faq.md)
