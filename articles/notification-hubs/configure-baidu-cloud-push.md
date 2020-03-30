---
title: Накончайте облако Baidu Push в концентрах уведомлений Azure (ru) Документы Майкрософт
description: Узнайте, как настроить настройки Baidu для концентратора уведомлений Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 759e35ba353f470ea3abc5f5d4182fa2b2ea0e73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212514"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Обеззараживаено: Настройте настройки облака Baidu для концентратора уведомлений на портале Azure

В этой статье показано, как настроить настройки облака Baidu для концентратора уведомлений Azure с помощью портала Azure.

> [!IMPORTANT]
> Этот учебник является deprecated. 

## <a name="prerequisites"></a>Предварительные требования
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Настройка push-уведомлений облака Baidu
Следующая процедура дает вам шаги для настройки параметров облака Baidu Для концентратора уведомлений:

1. На портале Azure на странице **Notification Hub** выберите **Baidu (Android China)** в левом меню. 
2. В соответствующее поле введите **ключ API**, полученный из консоли Baidu, в проекте службы push-уведомлений облака Baidu. 
3. Введите **секретный ключ**, полученный из консоли Baidu в проекте службы push-уведомлений облака Baidu. 
4. Нажмите кнопку **Сохранить**. 

    ![Снимок экрана Центров уведомлений, на котором показана настройка push-уведомлений в Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Дальнейшие действия
Для учебника с пошаговыми инструкциями для нажатия уведомлений на Baidu с помощью [Get started with Notification Hubs by using Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)концентраторов уведомлений Azure и Baidu Cloud Push см.
