---
title: включение файла
description: включение файла
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 06/28/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 324f41055cf333081f308a3ff533ff7df6b33038
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479240"
---
>[!NOTE]
>В этом разделе приводятся инструкции для [новой регистрации приложения Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Если у вас все еще есть прежняя регистрация собственного приложения, вы можете использовать ее, пока она поддерживается. Кроме того, если по какой-то причине новый способ регистрации приложений не работает в установке, вы можете попытаться создать прежнее собственное приложение AAD. Дополнительные инструкции см. в статье [Register your Azure Digital Twins app with Azure Active Directory legacy](../articles/digital-twins/how-to-use-legacy-aad.md) (Регистрация приложения Azure Digital Twins с помощью прежней версии Azure Active Directory). 

1. На [портале Azure](https://portal.azure.com) выберите **Azure Active Directory** на панели слева, а затем откройте область **Регистрация приложений**. Нажмите кнопку **Новая регистрация**.

    ![Зарегистрированное приложение](./media/digital-twins-permissions/aad-app-register.png)

1. Задайте понятное имя для этой регистрации приложения в поле **Имя**. В разделе **URI перенаправления (необязательно)** выберите параметр **Public client (mobile & desktop)** (Общедоступный клиент (мобильный и классический)) в раскрывающемся списке слева и введите `https://microsoft.com` в текстовое поле справа. Выберите **Зарегистрировать**.

    ![Область создания](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Чтобы убедится, что [приложение зарегистрировано как *собственное приложение*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), откройте область **Аутентификация** для регистрации приложения и прокрутите вниз в этой области. В разделе **Тип клиента по умолчанию** выберите **Да** для параметра **Treat application as a public client** (Рассматривать приложение как общедоступный клиент). 

    ![Собственное по умолчанию](./media/digital-twins-permissions/aad-app-default-native.png)

1.  Откройте область **Обзор** зарегистрированного приложения и скопируйте значения следующих сущностей во временный файл. Эти значения будут использоваться для настройки примера приложения в следующих разделах.

    - **Application (client) ID** (Идентификатор приложения (клиент))
    - **Идентификатор каталога (клиент)**

    ![Идентификатор приложения Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Откройте область **Разрешения API** для регистрации приложения. Нажмите кнопку **Add a permission** (Добавить разрешение). В области **Запрос разрешений API** щелкните вкладку **Интерфейсы API, используемые моей организацией**, а затем выполните поиск по запросу **Azure Smart Spaces**. Выберите API **Azure Smart Spaces Service**.

    ![API поиска](./media/digital-twins-permissions/aad-app-search-api.png)

1. Выбранный API появится как **Azure Digital Twins** в той же области **Запрос разрешений API**. Выберите раскрывающийся список **Read (1)** (Чтение (1)), а затем установите флажок **Read.Write**. Нажмите кнопку **Add permissions** (Добавить разрешения).

    ![Добавление разрешений API](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. В зависимости от параметров вашей организации вам может потребоваться предпринять дополнительные шаги для предоставления доступа администратора к этому API. Для получения дополнительной информации обратитесь к администратору. Как только доступ администратора будет утвержден, столбец **ADMIN CONSENT REQUIRED** (Требуется согласие администратора) в области **Разрешения API** будет отображать примерно следующее для интерфейсов API:

    ![Добавление разрешений API](./media/digital-twins-permissions/aad-app-admin-consent.png)

