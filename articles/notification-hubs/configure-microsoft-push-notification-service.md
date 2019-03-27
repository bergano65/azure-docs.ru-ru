---
title: Настройка службы Microsoft Push-уведомлений в центрах уведомлений Azure | Документация Майкрософт
description: Сведения о настройке параметров Microsoft Push Notification Service для концентратора уведомлений Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488355"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка параметров Microsoft Push Notification Service (MPNS) для концентратора уведомлений на портале Azure
В этой статье показано, как Настройка параметров Microsoft Push Notification Service (MPNS) для концентратора уведомлений Azure с помощью портала Azure. 

## <a name="prerequisites"></a>Технические условия
Если вы уже создавали концентратора уведомлений, создайте ее. Дополнительные сведения см. в разделе [Создание концентратора уведомлений Azure на портале Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Настройка Microsoft Push Notification Service (MPNS)

В следующей процедуре представлены шаги по настройке параметров Microsoft Push Notification Service (MPNS) для концентратора уведомлений: 

1. На портале Azure на **концентратора уведомлений** выберите **Windows Phone (MPNS)** в меню слева.
1. Включение не прошедшие проверку подлинности или проверку подлинности Push-уведомлений:

   a. Чтобы включить без проверки подлинности Push-уведомлений, выберите **включить без проверки подлинности Push-уведомления** > **Сохранить**.

      ![Снимок экрана, показывающий, как не прошедшие проверку подлинности Push-уведомления](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   2. Чтобы включить проверку подлинности Push-уведомлений:
      * На панели инструментов выберите **передать сертификат**.
      * Выберите значок файла и выберите файл сертификата.
      * Введите пароль для сертификата.
      * Нажмите кнопку **ОК**.
      * На **Windows Phone (MPNS)** выберите **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия
Учебник с пошаговыми инструкциями по Push-уведомления на устройства Windows Phone с помощью центров уведомлений Azure и Microsoft Push Notification Service (MPNS), см. в разделе [Push-уведомления в приложения Windows Phone с помощью уведомлений Концентраторы](notification-hubs-windows-mobile-push-notifications-mpns.md).

