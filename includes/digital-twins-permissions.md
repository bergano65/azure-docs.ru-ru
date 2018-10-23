---
title: включение файла
description: включение файла
services: azure-digital-twins
author: alinamstanciu
ms.service: azure-digital-twins
ms.topic: include
ms.date: 09/19/2018
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: d08b7a1256a26d1d7d39481ba15a8637339063ea
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322974"
---
1. На [портале Azure](https://portal.azure.com) выберите **Azure Active Directory** на панели навигации слева, а затем откройте область **Свойства**. Скопируйте **идентификатор каталога** во временный файл. Это значение будет использоваться для настройки примера приложения в следующем разделе.

    ![Идентификатор каталога Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. Откройте область **Регистрация приложений** и щелкните **Регистрация нового приложения**.
    
    ![Регистрация нового приложения в Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-start.png)

1. Задайте понятное имя для этой регистрации приложения в поле **Имя**. В поле **Тип приложения** укажите **_Собственное_**, а в поле **URL-адрес перенаправления** — **_https://microsoft.com_**. Нажмите кнопку **Создать**.

    ![Создание регистрации приложения в Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Откройте зарегистрированное приложение и скопируйте значение в поле **Идентификатор приложения** во временный файл. Это значение является идентификатором приложения Azure Active Directory. Этот идентификатор будет использоваться для настройки примера приложения в следующих разделах.

    ![Идентификатор приложения в Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Откройте панель регистрации приложений и щелкните **Настройки** > **Необходимые разрешения**:
    - Слева вверху выберите **Добавить**, чтобы открыть область **Добавить доступ через API**.
    - Нажмите кнопку **Выбрать API** и выполните поиск по запросу **Azure Digital Twins**. Если найти API не удается, выполните поиск по запросу **Azure Smart Spaces**.
    - Выберите вариант **Azure Digital Twins (Azure Smart Spaces Service)** и нажмите кнопку **Выбрать**.
    - Щелкните **Выбор разрешений**. Установите флажок **Доступ на чтение/запись** в разделе "Делегированные разрешения" и нажмите кнопку **Выбрать**.
    - В области **Добавить доступ через API** щелкните **Готово**.
    - В области **Необходимые разрешения** щелкните значок **Предоставить разрешения** и примите отобразившееся подтверждение.

       ![Добавление API при регистрации приложения в Azure Active Directory](./media/digital-twins-permissions/aad-app-req-permissions.png)
