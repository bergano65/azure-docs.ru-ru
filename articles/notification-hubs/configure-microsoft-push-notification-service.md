---
title: Настройка службы уведомлений Майкрософт Push в концентрах уведомлений Azure (ru) Документы Майкрософт
description: Узнайте, как настроить настройки службы уведомлений Майкрософт для концентратора уведомлений Azure.
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
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127326"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Настройка настроек службы уведомлений Microsoft Push (MPNS) на портале Azure

В этой статье показано, как настроить настройки службы уведомлений Microsoft Push (MPNS) для концентратора уведомлений Azure с помощью портала Azure. 

## <a name="prerequisites"></a>Предварительные требования
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Настройка службы уведомлений Microsoft Push (MPNS)

Следующая процедура дает вам шаги для настройки настроек Службы уведомлений Майкрософт Push (MPNS) для концентратора уведомлений: 

1. На портале Azure на странице **концентратора уведомлений** выберите **Windows Phone (MPNS)** в левом меню.
1. Включите push-уведомления с проверкой подлинности или без нее:

   а. Для включения неаутвентивных push-уведомлений выберите **Включить неаутвированный нажатие** > **Сохранить.**

      ![Снимок экрана, на котором показано, как включить push-уведомления без проверки подлинности](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Чтобы включить push-уведомления с проверкой подлинности, сделайте следующее:
      * На панели инструментов щелкните **Отправка сертификата**.
      * Щелкните значок файла, а затем выберите файл сертификата.
      * Введите пароль для сертификата.
      * Нажмите кнопку **ОК**.
      * На странице **Windows Phone(MPNS)** выберите **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия
Для руководства с пошаговыми инструкциями для нажатия уведомлений на устройства Windows Phone с помощью концентраторов уведомлений Azure и службы уведомлений Microsoft Push (MPNS) см. [Push-уведомления для приложений Windows Phone с помощью концентраторов уведомлений.](notification-hubs-windows-mobile-push-notifications-mpns.md)

