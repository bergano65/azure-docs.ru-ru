---
title: Настройка Push-облако Baidu в центрах уведомлений Azure | Документация Майкрософт
description: Сведения о настройке параметров Baidu для концентратора уведомлений Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9942b1d4d8b5d538f5150a36e596753282039be7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488383"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка Push-уведомлений облака Baidu параметры для концентратора уведомлений на портале Azure
В этой статье показано, как Настройка параметров Push-уведомлений облака Baidu для концентратора уведомлений Azure с помощью портала Azure. 

## <a name="prerequisites"></a>Технические условия
Если вы уже создавали концентратора уведомлений, создайте ее. Дополнительные сведения см. в разделе [Создание концентратора уведомлений Azure на портале Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Настройка Push-уведомлений облака Baidu
В следующей процедуре представлены шаги по настройке параметров Push-уведомлений облака Baidu для концентратора уведомлений:

1. На портале Azure на **концентратора уведомлений** выберите **Baidu (Android China)** в меню слева. 
2. Введите **ключ Api** , полученный из консоли Baidu в проекте Push-уведомлений облака Baidu. 
3. Введите **секретный ключ** , полученный из консоли Baidu в проекте Push-уведомлений облака Baidu. 
4. Щелкните **Сохранить**. 

    ![Снимок экрана из центров уведомлений, показана конфигурация для Push-уведомлений Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Дальнейшие действия
Учебник с пошаговыми инструкциями по отправку уведомлений Baidu с помощью центров уведомлений Azure и Push-уведомлений облака Baidu, см. в разделе [начало работы с центрами уведомлений с помощью Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
