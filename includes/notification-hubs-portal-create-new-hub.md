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
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509087"
---
1. Войдите на [портале Azure](https://portal.azure.com).

1. Выберите **Все службы** в меню слева и щелкните **Центры уведомлений** в разделе **Мобильный**. Щелкните значок звездочки рядом с именем службы, чтобы добавить ее в раздел **Избранное** в меню слева. После добавления **Центров уведомлений** в **Избранное** выберите их в меню слева.

      ![Портал Azure. Выбор Центров уведомлений](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. На странице **Центры уведомлений** выберите **Добавить** на панели инструментов.

      ![Центры уведомлений. Добавление кнопки панели инструментов](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. На странице **Концентратор уведомлений** выполните следующие действия:

    1. Введите имя в поле **Центр уведомлений**.  

    1. Введите имя в поле **Создать пространство имен**. Пространство имен содержит один или несколько центров.

    1. Выберите значение в раскрывающемся списке **Расположение**. Это значение определяет расположение, в котором создается центр.

    1. В поле **Группа ресурсов** выберите существующую группу ресурсов или укажите имя для новой группы ресурсов.

    1. Нажмите кнопку **Создать**.

        ![Портал Azure: настройка свойств концентратора уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Последовательно выберите **Уведомления** (значок колокольчика) и **Перейти к ресурсу**. Вы также можете обновить список на странице **Центры уведомлений** и выбрать свой центр.

      ![Портал Azure — "Уведомления" -> Go to resource (Перейти к ресурсу)](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Выберите **Политики доступа** в списке. Обратите внимание, что станут доступны две строки подключения. Они потребуются позже для обработки push-уведомлений.

      >[!IMPORTANT]
      >*Не* используйте в приложении политику **DefaultFullSharedAccessSignature**. Этот параметр можно использовать только в серверной части.
      >

      ![Портал Azure: строки подключения к концентратору уведомлений](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
