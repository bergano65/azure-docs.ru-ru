---
title: SMS-интерфейс пользователя для номера телефона (предварительный просмотр) - Azure AD
description: Подробнее о пользовательском интерфейсе SMS для новых или существующих телефонных номеров
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378838"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>Используйте свой номер телефона в качестве имени пользователя (предварительный просмотр)

Регистрация устройства дает вашему телефону доступ к услугам организации и не позволяет вашей организации доступ к вашему телефону. Если вы администратор, вы можете найти более подробную информацию в [Настройка и включить пользователей для SMS-проверки подлинности.](../authentication/howto-authentication-sms-signin.md)

Если ваша организация не сделала SMS-вхинг, вы не увидите опцию для него при регистрации телефона с вашей учетной записью.  

## <a name="when-you-have-a-new-phone-number"></a>Если у вас есть новый номер телефона

Если вы получаете новый телефон или новый номер и регистрируете его в организации, для которой доступна SMS-регистрация, вы испытываете обычный процесс регистрации телефона:

1. Выберите **метод добавления**.
1. Выберите **телефон**.
1. Введите номер телефона и выберите **Текст мне код**.
1. После ввода кода выберите **Next**.
1. Вы увидите запрос, который говорит: "SMS проверено. Ваш телефон был успешно зарегистрирован».

> [!Important]
> Из-за известной проблемы в предварительном просмотре, в течение короткого времени добавление номера телефона не будет регистрировать номер для SMS-входе. Вам придется войти в систему с добавленным номером, а затем следовать запросам, чтобы зарегистрировать номер для SMS-входе.

### <a name="when-the-phone-number-is-in-use"></a>Когда номер телефона используется

Если вы попытаетесь использовать номер телефона, который использует кто-то другой в вашей организации, вы увидите следующее сообщение:

![Сообщение об ошибке при ужеиспользовании номера телефона](media/sms-sign-in-explainer/sms-sign-in-error.png)

Обратитесь к своему адумину, чтобы усмотреть проблему.

## <a name="when-you-have-an-existing-number"></a>Если у вас есть существующий номер

Если вы уже используете номер телефона с организацией, и с помощью вашего номера телефона, как имя пользователя становится доступным, следующие шаги могут помочь вам войти в систему.

1. При доступе sMS-входе в систему отображается баннер с просьбой включить номер телефона для входиного SMS:

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. Кроме того, при выборе кнопки "Включить" в плитке метода телефона появляется кнопка **Enable:**

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. Для включения метода выберите **Enable**. Вам предлагается подтвердить действие:

    ![Диалог подтверждения для включения SMS-входа для номера телефона](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. Нажмите кнопку **Включить**.

## <a name="when-you-remove-your-phone-number"></a>Когда вы удалите номер телефона

1. Чтобы удалить номер телефона, выберите кнопку удаления на плитке sMS-доступа для телефона.

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. При подтверждении действия выберите **OK**.

Вы не можете удалить номер телефона, который используется в качестве метода вхластвого по умолчанию. Чтобы удалить номер, необходимо изменить метод вхламыва по умолчанию, а затем снова удалить номер телефона.
