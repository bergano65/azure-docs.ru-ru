---
title: включение файла
description: включение файла
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 08/16/2019
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: fcb4b3d54e1e62a7f197f2f499e2b176bb707fd8
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71843826"
---
1. На [портале Azure](https://portal.azure.com) выберите **Azure Active Directory** на панели слева, а затем откройте область **Свойства**. Скопируйте **идентификатор каталога** во временный файл. Это значение будет использоваться для настройки примера приложения в следующем разделе.

    [Идентификатор каталога ![Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. В [портал Azure](https://portal.azure.com)откройте **Azure Active Directory** в левой области, а затем откройте панель **Регистрация приложений (устаревшие)** . Нажмите кнопку **Регистрация нового приложения** .

1. Задайте понятное имя для этой регистрации приложения в поле **Имя**. В поле **Тип приложения** укажите **Собственное**, а в поле **URL-адрес перенаправления** — `https://microsoft.com`. Нажмите кнопку **Создать**.

    [![Область создания](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Откройте зарегистрированное приложение и скопируйте значение в поле **Идентификатор приложения** во временный файл. Это значение является идентификатором приложения Azure Active Directory. Этот идентификатор будет использоваться для настройки примера приложения в следующих разделах.

    [![Идентификатор приложения Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Откройте панель регистрации своего приложения. Выберите **Параметры** > **Необходимые разрешения**, а затем:

   1\. Слева вверху выберите **Добавить**, чтобы открыть область **Добавить доступ через API**.

   2\. Нажмите кнопку **Выбрать API** и выполните поиск по запросу **Azure Digital Twins**. Если найти API не удается, выполните поиск по запросу **Azure Smart Spaces**.

   В. Выберите вариант **Azure Digital Twins (Azure Smart Spaces Service)** и нажмите кнопку **Выбрать**.

   Г. Щелкните **Выбор разрешений**. Установите флажок **Доступ на чтение/запись** в разделе "Делегированные разрешения" и нажмите кнопку **Выбрать**.

   Д. В области **Добавить доступ через API** щелкните **Готово**.

   f. В области **необходимые разрешения** нажмите кнопку **предоставить разрешения** и примите подтверждение, которое появляется. Если разрешение не предоставлено для этого API, обратитесь к администратору.

      [панель разрешений ![Required](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
