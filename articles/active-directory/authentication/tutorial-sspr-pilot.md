---
title: Включение пилотной программы Azure AD SSPR
description: В этом руководстве описано, как включить самостоятельный сброс пароля Azure AD для пилотной группы пользователей
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecdde4ef12c6991fad53f2286ee462fec31606ae
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846289"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>Руководство по Выполнение пилотного развертывания самостоятельного сброса пароля Azure AD

В этом руководстве описано включение пилотного развертывания самостоятельного сброса пароля (SSPR) Azure AD в организации и тестирование его с помощью учетной записи без прав администратора.

Важно, чтобы любое тестирование самостоятельного сброса пароля выполнялось с помощью учетных записей без прав администратора. Корпорация Майкрософт управляет политикой сброса пароля для учетных записей администратора и требует применения более сильных способов проверки подлинности. Эта политика не поддерживает использование контрольных вопросов и ответов, а требует использования двух методов для сброса.

> [!div class="checklist"]
> * Включение самостоятельного сброса пароля
> * Тестирование SSPR в качестве пользователя

## <a name="prerequisites"></a>Предварительные требования

* Учетная запись глобального администратора

## <a name="enable-self-service-password-reset"></a>Включение самостоятельного сброса пароля

1. Войдите на [портал Azure](https://portal.azure.com), используя учетную запись глобального администратора.
1. Перейдите к **Azure Active Directory** и выберите **Сброс пароля**.
1. Начните с пилотной группы, включив самостоятельный пароль для подмножества пользователей в организации.
   * На странице **Свойства** для параметра **Разрешен самостоятельный сброс пароля** выберите **Выбрано** и назначьте пилотную группу пользователей.
      * Только члены выбранной группы Azure AD могут использовать функцию SSPR. Рекомендуем определить группу пользователей и использовать этот параметр при развертывании этой функции для подтверждения концепции. Вложение групп безопасности поддерживается.
      * Убедитесь, что пользователи в выбранной группе лицензированы соответствующим образом.
   * Нажмите кнопку **Сохранить**
1. Действия на странице **Способы проверки подлинности**
   * Задайте для параметра **Число способов, необходимых для сброса** значение **1**.
   * Выберите **методы, доступные пользователям**  организации, которые необходимо разрешить. В целях этого руководства установите флажки, чтобы включить параметры **Электронная почта**, **Мобильный телефон**, **Рабочий телефон**, **Уведомление мобильного приложения** и **Код мобильного приложения**.
   * Нажмите кнопку **Сохранить**
1. Действия на странице **Регистрация**
   * Выберите **Да** для параметра **Требовать регистрацию пользователя при входе**.
   * Для параметра **Количество дней, по истечении которых пользователям будет предложено повторно подтвердить данные проверки подлинности** выберите **180**.
   * Нажмите кнопку **Сохранить**
1. Действия на странице **Уведомления**
   * Для параметра **Уведомлять пользователей о сбросе пароля** выберите **Да**.
   * Для параметра **Уведомлять всех администраторов, если другие администраторы сбрасывают свои пароли** установите **Да**.
1. Действия на странице **Настройка**
   * Корпорация Майкрософт рекомендует присваивать параметру **Настроить ссылку на службу технической поддержки** значение **Да** и указывать в поле **Адрес электронной почты или URL-адрес нестандартной службы технической поддержки** адрес электронной почты или URL-адрес веб-страницы, где пользователи могут получить дополнительную помощь от вашей организации.
   * В целях этого руководства параметру **Настроить ссылку на службу технической поддержки** задано значение **Нет**.

Самостоятельный сброс пароля уже настроен для облачных пользователей пилотной группы.

## <a name="test-sspr-as-a-user"></a>Тестирование SSPR в качестве пользователя

Тестирование самостоятельного сбрасывания пароля с помощью учетной записи тестового пользователя, не имеющего прав администратора, который является членом пилотной группы. **При использовании учетной записи, которая имеет какие-либо назначенные ей роли администратора, методы проверки подлинности и номер могут отличаться от того, который был выбран, поскольку корпорация Майкрософт управляет политикой администратора.**

1. Откройте новое окно браузера в режиме InPrivate или инкогнито.
1. Используйте регистрацию тестового пользователя для самостоятельного сброса пароля с использованием портала регистрации, расположенного по адресу [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup).
1. С помощью того же тестового пользователя перейдите на портал самостоятельного сброса пароля [https://aka.ms/sspr](https://aka.ms/sspr) и попытайтесь сбросить пароль, используя сведения, предоставленные на предыдущем шаге.
1. Необходимо иметь возможность успешно сбросить пароль.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если больше нет необходимости использовать функциональные возможности, которые были настроены в рамках этого руководства, внесите следующие изменения.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Перейдите к **Azure Active Directory** и выберите **Сброс пароля**.
1. На странице **Свойства** для параметра **Разрешен самостоятельный сброс пароля** выберите **Нет**.
1. Нажмите кнопку **Сохранить**

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве было произведено включение самостоятельного сброса пароля Azure AD. Перейдите к следующему руководству по интеграции локальной инфраструктуры доменных служб Active Directory в интерфейс самостоятельного сброса пароля.

> [!div class="nextstepaction"]
> [Tutorial: Enabling password writeback](tutorial-enable-writeback.md) (Руководство. Включение обратной записи паролей)
