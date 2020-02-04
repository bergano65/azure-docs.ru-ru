---
title: включить файл
description: включить файл
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/23/2020
ms.custom: include file
ms.openlocfilehash: a1576e4a97af5de0b936c662de636aae542a19b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76749002"
---
>[!NOTE]
>В этом разделе приводятся инструкции по [регистрации приложения Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. На [портале Azure](https://portal.azure.com) выберите **Azure Active Directory** в развертываемом меню слева, а затем откройте область **Регистрация приложений**. 

    [![Выбор панели Azure Active Directory](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Нажмите кнопку **+ Новая регистрация**.

    [![Нажатие кнопки "Новая регистрация"](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Задайте понятное имя для этой регистрации приложения в поле **Имя**. В разделе **URI перенаправления (необязательно)** выберите параметр **Общедоступный/собственный клиент (мобильный и классический)** в раскрывающемся меню слева и введите `https://microsoft.com` в текстовое поле справа. Выберите **Зарегистрировать**.

    [![Область создания](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Чтобы убедиться, что [приложение зарегистрировано как **общедоступный клиент**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), откройте область **Аутентификация** для регистрации приложения и прокрутите вниз в этой области. В разделе **Тип клиента по умолчанию** выберите **Да** для параметра **Treat application as a public client** (Рассматривать приложение как общедоступный клиент), а затем нажмите кнопку **Сохранить**.

    1. **URI перенаправления** должны соответствовать адресу, указанному в запросе аутентификации.

        * Для приложений, размещенных в локальной среде разработки, выберите **Public client (mobile & desktop)** (Общедоступный клиент (мобильный и классический)). Убедитесь, что для параметра **Тип клиента по умолчанию** установлено значение "Да".
        * Для одностраничных приложений, размещенных в Службе приложений Azure, выберите **Веб**.

        Выберите **Общедоступный клиент (мобильный и классический)** и введите `http://localhost:8080/`.

        [![Настройка URI перенаправления](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    1. Установите флажок **Маркеры доступа**, чтобы настроить параметр **oauth2AllowImplicitFlow** на значение `true` в файле JSON **манифеста** ресурса.

        [![Параметр конфигурации общедоступного клиента](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1.  Откройте область **Обзор** зарегистрированного приложения и скопируйте значения следующих сущностей во временный файл. Эти значения будут использоваться для настройки примера приложения в следующих разделах.

    - **Application (client) ID** (Идентификатор приложения (клиент))
    - **Идентификатор каталога (клиент)**

    [![Идентификатор приложения Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Откройте область **Разрешения API** для регистрации приложения. Нажмите кнопку **+ Add a permission** (+ Добавить разрешение). В области **Запрос разрешений API** щелкните вкладку **Интерфейсы API, используемые моей организацией**, а затем выполните поиск по одному из следующих запросов:
    
    1. `Azure Digital Twins`. Выберите API **Azure Digital Twins**.

        [![Поиск API или Azure Digital Twins](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Кроме того, можно выполнить поиск `Azure Smart Spaces Service`. Выберите API **Azure Smart Spaces Service**.

        [![Поиск API для Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Имя и идентификатор API Azure AD, которые будут отображаться, зависят от вашего клиента:
    > * Для тестовых клиентов и учетных записей клиентов следует искать `Azure Digital Twins`.
    > * Для других учетных записей Майкрософт следует искать `Azure Smart Spaces Service`.

1. Выбранный API появится как **Azure Digital Twins** в той же области **Запрос разрешений API**. Выберите параметр **Чтение** из раскрывающегося списка, а затем установите флажок **Read.Write**. Нажмите кнопку **Add permissions** (Добавить разрешения).

    [![Добавление разрешений API](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. В зависимости от параметров вашей организации вам может потребоваться предпринять дополнительные шаги для предоставления доступа администратора к этому API. Для получения дополнительной информации обратитесь к администратору. Как только доступ администратора будет утвержден, в столбце **Admin Consent Required** (Требуется согласие администратора) в области **Разрешения API** будут отображаться ваши разрешения. 

    [![Утверждение согласия администратора](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Проверьте, отображается ли **Azure Digital Twins**.
