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
ms.openlocfilehash: 00b7ffcba876b6abea59cff170331c7413a61d39
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823323"
---
1. Войдите на [портале Azure](https://portal.azure.com).
2. Выберите **Все службы** в меню слева и щелкните **Центры уведомлений** в разделе **Мобильный**. Выберите звездочку (`*`) рядом с именем службы, чтобы добавить ее в раздел **Избранное** в меню слева. После добавления **Центров уведомлений** в **Избранное** выберите их в меню слева. 

      ![Портал Azure. Выбор Центров уведомлений](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. На странице **Центры уведомлений** выберите **Добавить** на панели инструментов. 

      ![Центры уведомлений. Добавление кнопки панели инструментов](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. На странице **Концентратор уведомлений** выполните следующие действия: 
    1. Укажите **имя** **концентратора** уведомлений.  
    2. Укажите **имя** **пространства имен**.
    3. Выберите **расположение**, в котором требуется создать концентратор уведомлений. 
    4. Щелкните имеющуюся группу ресурсов или введите имя для новой **группы ресурсов**.
    5. Нажмите кнопку **Создать**. 

        ![Портал Azure: настройка свойств концентратора уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Выберите **Уведомления** (значок колокольчика) и щелкните **Перейти к ресурсу** или обновите список на странице **Центры уведомлений** и выберите концентратор уведомлений. 

      ![Портал Azure — "Уведомления" -> Go to resource (Перейти к ресурсу)](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. Выберите **Политики доступа** в списке. Запишите две строки подключения, которые отобразятся. Они требуются для дальнейшей обработки push-уведомлений.

      >[!IMPORTANT]
      >**НЕ** используйте в приложении DefaultFullSharedAccessSignature. Этот параметр можно использовать только в серверной части.
      >

      ![Портал Azure: строки подключения к концентратору уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
