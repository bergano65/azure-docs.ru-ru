---
title: включение файла
description: включение файла
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: b68fa345d4772134c30ce8b8b559f98113a0496f
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453110"
---
1. Войдите на [портале Azure](https://portal.azure.com).

2. Выберите **Создать ресурс** > **Мобильные устройства** > **Концентратор уведомлений**.

      ![Портал Azure: создание центра уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)

3. В поле **Центр уведомлений** введите уникальное имя. Выберите **регион**, **подписку** и **группу ресурсов** (если она уже создана).

      Если у вас нет пространства имен служебной шины, можно использовать имя по умолчанию. Оно создается на основе имени концентратора (если имя пространства имен доступно).

      Если у вас уже есть пространство имен служебной шины, в котором требуется создать концентратор, сделайте следующее:

    a. В области **Пространство имен** выберите ссылку **Выбрать существующее**.

    b. Нажмите кнопку **Создать**.

    ![Портал Azure: настройка свойств концентратора уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Последовательно выберите **Уведомления** (значок колокольчика) и **Go to resource** (Перейти к ресурсу).

      ![Портал Azure — "Уведомления" -> Go to resource (Перейти к ресурсу)](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)
5. Выберите **Политики доступа** в списке. Запишите две строки подключения, которые отобразятся. Они требуются для дальнейшей обработки push-уведомлений.

      >[!IMPORTANT]
      >**НЕ** используйте в приложении DefaultFullSharedAccessSignature. Этот параметр можно использовать только в серверной части.
      >

      ![Портал Azure: строки подключения к концентратору уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
