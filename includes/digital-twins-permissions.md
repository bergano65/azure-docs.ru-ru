---
title: включение файла
description: включение файла
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 09/24/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 1fc30ea5aa843329b6227bfa564b3d10e2273cd7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310498"
---
>[!NOTE]
>В этом разделе приводятся инструкции для [новой регистрации приложения Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Если у вас все еще есть прежняя регистрация собственного приложения, вы можете использовать ее, пока она поддерживается. Кроме того, если по какой-то причине новый способ регистрации приложений не работает в установке, вы можете попытаться создать прежнее собственное приложение AAD. Дополнительные инструкции см. в статье [Register your Azure Digital Twins app with Azure Active Directory legacy](../articles/digital-twins/how-to-use-legacy-aad.md) (Регистрация приложения Azure Digital Twins с помощью прежней версии Azure Active Directory). 

1. На [портале Azure](https://portal.azure.com) выберите **Azure Active Directory** на панели слева, а затем откройте область **Регистрация приложений**. Нажмите кнопку **Новая регистрация**.

    [![Зарегистрированное приложение](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Задайте понятное имя для этой регистрации приложения в поле **Имя**. В разделе **URI перенаправления (необязательно)** выберите параметр **Public client (mobile & desktop)** (Общедоступный клиент (мобильный и классический)) в раскрывающемся списке слева и введите `https://microsoft.com` в текстовое поле справа. Выберите **Зарегистрировать**.

    [![Область создания](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Чтобы убедится, что [приложение зарегистрировано как *собственное приложение*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), откройте область **Аутентификация** для регистрации приложения и прокрутите вниз в этой области. В разделе **Тип клиента по умолчанию** выберите **Да** для параметра **Treat application as a public client** (Рассматривать приложение как общедоступный клиент). 

    [![Собственное по умолчанию](./media/digital-twins-permissions/aad-app-default-native.png)](./media/digital-twins-permissions/aad-app-default-native.png#lightbox)

1.  Откройте область **Обзор** зарегистрированного приложения и скопируйте значения следующих сущностей во временный файл. Эти значения будут использоваться для настройки примера приложения в следующих разделах.

    - **Application (client) ID** (Идентификатор приложения (клиент))
    - **Идентификатор каталога (клиент)**

    [![Идентификатор приложения Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Откройте область **Разрешения API** для регистрации приложения. Нажмите кнопку **Add a permission** (Добавить разрешение). В области **Запрос разрешений API** щелкните вкладку **Интерфейсы API, используемые моей организацией**, а затем выполните поиск по запросу
    
    1. **Azure Digital Twins**. Выберите API **Azure Digital Twins**.

        [![Поиск API или Azure Digital Twins](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Либо выполните поиск по запросу **Azure Smart Spaces**. Выберите API **Azure Smart Spaces Service**.

        [![Поиск API для Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!NOTE]
    > Точное имя, которое будет отображаться при поиске, может отличаться в зависимости от того, к какому клиенту Azure вы принадлежите.

1. Выбранный API появится как **Azure Digital Twins** в той же области **Запрос разрешений API**. Выберите раскрывающийся список **Read (1)** (Чтение (1)), а затем установите флажок **Read.Write**. Нажмите кнопку **Add permissions** (Добавить разрешения).

    [![Добавление разрешений API](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. В зависимости от параметров вашей организации вам может потребоваться предпринять дополнительные шаги для предоставления доступа администратора к этому API. Для получения дополнительной информации обратитесь к администратору. Как только доступ администратора будет утвержден, столбец **ADMIN CONSENT REQUIRED** (Требуется согласие администратора) в области **Разрешения API** будет отображать примерно следующее для интерфейсов API:

    [![Добавление разрешений API](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

