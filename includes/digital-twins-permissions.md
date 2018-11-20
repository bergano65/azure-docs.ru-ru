---
title: включение файла
description: включение файла
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 11/13/2018
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 216e2db82d5a07bd8e4cae8b9f357ac7dcee330a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626412"
---
1. На [портале Azure](https://portal.azure.com) выберите **Azure Active Directory** на панели слева, а затем откройте область **Свойства**. Скопируйте **идентификатор каталога** во временный файл. Это значение будет использоваться для настройки примера приложения в следующем разделе.

    ![Идентификатор каталога Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. Откройте область **Регистрация приложений** и нажмите кнопку **Регистрация нового приложения**.

    ![Область "Регистрация приложений"](./media/digital-twins-permissions/aad-app-reg-start.png)

1. Задайте понятное имя для этой регистрации приложения в поле **Имя**. В поле **Тип приложения** укажите **Собственное**, а в поле **URL-адрес перенаправления** — `https://microsoft.com`. Нажмите кнопку **Создать**.

    ![Область создания](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Откройте зарегистрированное приложение и скопируйте значение в поле **Идентификатор приложения** во временный файл. Это значение является идентификатором приложения Azure Active Directory. Этот идентификатор будет использоваться для настройки примера приложения в следующих разделах.

    ![Идентификатор приложения Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Откройте панель регистрации своего приложения. Выберите **Параметры** > **Необходимые разрешения**, а затем:

   a. Слева вверху выберите **Добавить**, чтобы открыть область **Добавить доступ через API**.

   b. Нажмите кнопку **Выбрать API** и выполните поиск по запросу **Azure Digital Twins**. Если найти API не удается, выполните поиск по запросу **Azure Smart Spaces**.

   c. Выберите вариант **Azure Digital Twins (Azure Smart Spaces Service)** и нажмите кнопку **Выбрать**.

   d. Щелкните **Выбор разрешений**. Установите флажок **Доступ на чтение/запись** в разделе "Делегированные разрешения" и нажмите кнопку **Выбрать**.

   д. В области **Добавить доступ через API** щелкните **Готово**.

   Е. В области **Необходимые разрешения** выберите значок **Предоставить разрешения** и примите отобразившееся подтверждение.

      ![Область необходимых разрешений](./media/digital-twins-permissions/aad-app-req-permissions.png)
