---
title: Настройка службы push-уведомлений Windows в центрах уведомлений Azure | Документация Майкрософт
description: Узнайте, как настроить параметры службы push-уведомлений Windows для центра уведомлений Azure.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: a7f7734d97cd67c133ff0cedc3ef2376967bcdf4
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212411"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка параметров службы push-уведомлений Windows (WNS) для центра уведомлений в портал Azure
В этой статье показано, как настроить параметры службы уведомлений Windows (WNS) для центра уведомлений Azure с помощью портал Azure.  

## <a name="prerequisites"></a>Предварительные требования
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Настройка службы push-уведомлений Windows (WNS)

Следующая процедура позволяет настроить параметры службы push-уведомлений Windows (WNS) для центра уведомлений. 

1. В портал Azure на странице **Центр уведомлений** выберите **Windows (WNS)** в меню слева.
2. Введите значения для **идентификатора безопасности пакета** и **ключа безопасности**.
3. Щелкните **Сохранить**.

   ![Снимок экрана, на котором показаны поля "ИД безопасности пакета" и "Ключ безопасности"](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Следующие шаги
Пошаговые инструкции по отправке уведомлений в универсальная платформа Windows приложения с помощью центров уведомлений Azure и службы push-уведомлений Windows (WNS) см. в статье [Отправка уведомлений в приложения UWP с помощью уведомлений Azure. Концентраторы](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


