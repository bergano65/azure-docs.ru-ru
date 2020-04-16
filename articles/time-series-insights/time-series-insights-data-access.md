---
title: Настройка безопасности для предоставления доступа к данным - Azure Time Series Обзоры Предварительный просмотр (ru) Документы Майкрософт
description: Узнайте, как настроить систему безопасности, разрешения и управлять политиками доступа к данным в среде просмотра Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: 1735daf66483df496141a642ac6633973aa0abf0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407548"
---
# <a name="grant-data-access-to-an-environment"></a>Предоставление доступа к данным в среде

В этой статье рассматриваются два типа политик доступа службы "Аналитика временных рядов Azure" (предварительная версия).

> [!TIP]
> Прочитайте [проверку подлинности и авторизации](time-series-insights-authentication-and-authorization.md) для шагов регистрации приложений Azure Active Directory.

## <a name="sign-in-to-time-series-insights"></a>Войти на Time Series Исследования

1. Войдите на [портал Azure](https://portal.azure.com/).
1. Найдите среду "Аналитика временных рядов". Введите `Time Series` в поле **Поиск**. Выберите **среды временных рядов** в результатах поиска.
1. Выберите среду Time Series Insights в списке.

## <a name="grant-data-access"></a>Предоставление доступа к данным

Сделайте следующее, чтобы предоставить доступ к данным для субъекта-пользователя.

1. Выберите **Политики доступа к данным**, а затем нажмите кнопку **+ Добавить**.

    [![Выберите и добавите политику доступа к данным](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Выберите **Выбор пользователя**. Выполните поиск по имени пользователя или адресу электронной почты, чтобы найти пользователя, которого следует добавить. **Выберите для** подтверждения выбора.

    [![Выберите пользователя для добавления](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Выберите **Выбор ролей**. Выберите соответствующую роль доступа для пользователя.

    * Выберите **Участник**, если вы хотите разрешить пользователю изменять ссылочные данные и предоставить другим пользователям среды доступ к сохраненным запросам и перспективам.

    * В противном случае выберите **Читатель**, чтобы разрешить пользователю обращаться к данным в среде и сохранять в ней личные (не общие) запросы.

   Нажмите кнопку **ОК**, чтобы подтвердить выбор роли.

    [![Подтверждение выбранной роли](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. На странице **Выбор роли пользователя** нажмите кнопку **ОК**.

    [![Выберите OK на странице роли пользователя Select](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Убедитесь, что на странице **Политики доступа к данным** перечислены пользователи и соответствующие роли.

    [![Проверка правильных пользователей и ролей](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Предоставить гостевой доступ от другого арендатора Azure AD

Роль `Guest` не является управленческой ролью. Это термин, используемый для учетной записи, приглашенной из одного клиента в другой. После того как гостевая учетная запись приглашена в каталог клиента, к ней могут быть применены те же права управления доступом, что и в любой другой учетной записи. Предоставить доступ для управления к среде "Аналитика временных рядов Azure" можно с помощью колонки "Управление доступом (IAM)". Кроме того, предоставить доступ к данным в среде можно в колонке "Политики доступа к данным". Дополнительные сведения о гостевом доступе к клиенту Azure Active Directory см. в статье [Добавление пользователей службы совместной работы B2B на портале Azure](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Выполните эти действия, чтобы предоставить гостевой доступ к среде службы "Аналитика временных рядов" пользователю Azure Active Directory из другого клиента.

1. Выберите **Политики доступа к данным**, а затем нажмите кнопку **+ Пригласить**.

    [![Выберите полицию доступа к данным, а затем пригласите](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Введите адрес электронной почты пользователя, которого вы хотите пригласить. Этот адрес электронной почты должен быть связан с Azure AD. Вы также можете добавить к приглашению личное сообщение.

    [![Введите адрес электронной почты, чтобы найти выбранного пользователя](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Найдите всплывающее окно с подтверждением, которое отображается на экране.

    [![Ищите пузырь подтверждения, чтобы появиться](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Выберите **Выбор пользователя**. Найдите адрес электронной почты приглашенного гостевого пользователя, чтобы найти пользователя, которого необходимо добавить. Затем **выберите** для подтверждения выделения.

    [![Выберите пользователя и подтвердите выбор](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Выберите **Выбор ролей**. Выберите соответствующую роль гостевого пользователя.

    * Выберите **Участник**, если вы хотите разрешить пользователю изменять ссылочные данные и предоставить другим пользователям среды доступ к сохраненным запросам и перспективам.

    * В противном случае выберите **Читатель**, чтобы разрешить пользователю обращаться к данным в среде и сохранять в ней личные (не общие) запросы.

   Нажмите кнопку **ОК**, чтобы подтвердить выбор роли.

    [![Подтвердите выбор роли](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. На странице **Выбор роли пользователя** нажмите кнопку **ОК**.

1. Убедитесь, что на странице **Политики доступа к данным** перечислены гостевые пользователи и соответствующие роли.

    [![Проверить правильное присвоение пользователям и ролям](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Теперь гость получит приглашение по электронной почте на указанный выше адрес электронной почты. Пользователь-гость выберет **Get Started,** чтобы подтвердить свое принятие и подключиться к облаку Azure.

    [![Гость выбирает Получить Начало принимать](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. После выбора **Get Started**гостевому пользователю будет представлена коробка разрешений, связанная с организацией администратора. После предоставления разрешения, выбрав **Accept,** они будут подписаны.

    [![Гость рассматривает разрешения и принимает](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. Администратор [делится URL-адресом среды](time-series-insights-parameterized-urls.md) со своим гостем.

1. После того, как гость пользователя впишется на адрес электронной почты, который вы использовали, чтобы пригласить его, и они примут приглашение, они будут направлены на портал Azure. 

1. Теперь гость может получить доступ к общей среде, используя URL-адрес среды, предоставленный администратором. Они могут ввести этот URL в свой веб-браузер для немедленного доступа.

1. Арендатор администратора будет отображаться гостевому пользователю после выбора значка профиля в правом верхнем углу explorer Time Series.

    [![Выбор Аватара на insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)


    После того, как гость пользователя выберет клиента администратора, он сможет выбрать общую среду Time Series Insights. 
    
    Теперь они имеют все возможности, связанные с ролью, которую вы предоставили им в **шаге 5**.

    [![Гостевой пользователь выбирает арендатора Azure из выпадения](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте о том, [как добавить источник событий концентратора событий Azure](./time-series-insights-how-to-add-an-event-source-eventhub.md) в среду "Аналитика временных рядов".

* Отправка [событий в источник события.](./time-series-insights-send-events.md)

* Просмотрите [свою среду в обозревателе службы "Аналитика временных рядов" (предварительная версия)](./time-series-insights-update-explorer.md).
