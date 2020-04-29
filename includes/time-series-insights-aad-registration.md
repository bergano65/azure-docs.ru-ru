---
title: Включить имя файла
description: включить файл
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 5be6e7937a6e1f710b8e2576a9058963413fb6c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76984595"
---
1. На [портале Azure](https://ms.portal.azure.com/) последовательно выберите **Azure Active Directory** > **Регистрация приложений** > **Новая регистрация**.

   [![Регистрация нового приложения в Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Приложение будет указано здесь после его регистрации.

1. Присвойте приложению имя и выберите пункт **Accounts in this organizational directory only** (Учетные записи только в этом каталоге организации),чтобы указать **поддерживаемые типы учетных записей**, с помощью которых можно получить доступ к API. Выберите допустимый URI для перенаправления пользователей после аутентификации, а затем нажмите кнопку **Зарегистрировать**.

   [![Создание приложения в Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Важная информация о приложении Azure Active Directory отображается в колонке **Обзор** указанного приложения. Выберите приложение в разделе **Собственные приложения** и щелкните **Обзор**.

   [![Копирование идентификатора приложения](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Скопируйте значение параметра **Идентификатор приложения (клиента)**, чтобы использовать его в клиентском приложении.

1. В колонке **Аутентификация** указаны важные параметры конфигурации аутентификации. 

    1. Добавьте **URI перенаправления** и настройте **Маркеры доступа**, выбрав **+ Add a platform** (+ Добавить платформу).

    1. Определите, является ли приложение **общедоступным клиентом** или нет, выбрав **Да** или **нет**.

    1. Проверьте, какие учетные записи и клиенты поддерживаются.

    [![Настройка неявного предоставления](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Выбрав нужную платформу, настройте **URI перенаправления** и **Маркеры доступа** на боковой панели справа от пользовательского интерфейса.

    1. **URI перенаправления** должны соответствовать адресу, указанному в запросе аутентификации.

        * Для приложений, размещенных в локальной среде разработки, выберите **Public client (mobile & desktop)** (Общедоступный клиент (мобильный и классический)). Не забудьте задать для **общедоступного клиента** значение **Да**.
        * Для одностраничных приложений, размещенных в Службе приложений Azure, выберите **Интернет**.

    1. Определите, подходит ли **URL-адрес выхода**.

    1. Включите поток неявного предоставления разрешения, проверив **маркеры доступа** или **токены идентификатора**.

    [![Создание URI перенаправления](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Щелкните **Настроить**, а затем **Сохранить**.

1. Выберите **сертификаты & секреты** , а затем **новый секрет клиента** , чтобы создать пароль приложения, который ваше клиентское приложение может использовать для подтверждения его подлинности.

   [![Создать новый секрет клиента](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   После этого будет отображен секретный пароль клиента. Скопируйте ключ в предпочитаемый текстовый редактор.

   > [!NOTE]
   > Вместо этого вы можете импортировать сертификат. Для повышения безопасности мы рекомендуем использовать сертификат. Для этого щелкните **Отправить сертификат**.

1. Свяжите приложение Azure Active Directory с Аналитикой временных рядов Azure. Выберите **разрешения** > **Добавить API разрешений** > , которые**использует Моя организация**. 

    [![Связывание API с приложением Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Введите `Azure Time Series Insights` в строке поиска, а затем выберите `Azure Time Series Insights`.

1. Затем укажите тип разрешения API, необходимый для приложения. По умолчанию будет выделен тип **Делегированные разрешения**. Выберите тип разрешения и щелкните **Добавить разрешения**.

    [![Укажите тип разрешения API, требуемого для приложения](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
