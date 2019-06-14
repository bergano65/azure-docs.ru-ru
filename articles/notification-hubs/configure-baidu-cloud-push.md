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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60234262"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка Push-уведомлений облака Baidu параметры для концентратора уведомлений на портале Azure
В этой статье показано, как Настройка параметров Push-уведомлений облака Baidu для концентратора уведомлений Azure с помощью портала Azure. 

## <a name="prerequisites"></a>Технические условия
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Настройка push-уведомлений облака Baidu
В следующей процедуре представлены шаги по настройке параметров Push-уведомлений облака Baidu для концентратора уведомлений:

1. На портале Azure на **концентратора уведомлений** выберите **Baidu (Android China)** в меню слева. 
2. В соответствующее поле введите **ключ API**, полученный из консоли Baidu, в проекте службы push-уведомлений облака Baidu. 
3. Введите **секретный ключ**, полученный из консоли Baidu в проекте службы push-уведомлений облака Baidu. 
4. Щелкните **Сохранить**. 

    ![Снимок экрана Центров уведомлений, на котором показана настройка push-уведомлений в Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Дальнейшие действия
Учебник с пошаговыми инструкциями по отправку уведомлений Baidu с помощью центров уведомлений Azure и Push-уведомлений облака Baidu, см. в разделе [начало работы с центрами уведомлений с помощью Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
