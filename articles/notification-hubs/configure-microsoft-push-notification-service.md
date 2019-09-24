---
title: Настройка службы push-уведомлений Майкрософт в центрах уведомлений Azure | Документация Майкрософт
description: Узнайте, как настроить параметры службы push-уведомлений Майкрософт для центра уведомлений Azure.
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
ms.openlocfilehash: 0d7bf5410e05bb74a215fb7a95c704673b764b93
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212479"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка параметров службы push-уведомлений Майкрософт (MPNS) для центра уведомлений в портал Azure
В этой статье показано, как настроить параметры службы push-уведомлений Майкрософт (MPNS) для центра уведомлений Azure с помощью портал Azure. 

## <a name="prerequisites"></a>Предварительные требования
Если вы еще не создали центр уведомлений, сделайте это сейчас. Дополнительные сведения см. в статье [Создание центра уведомлений Azure с помощью портала Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Настройка службы push-уведомлений Майкрософт (MPNS)

Следующая процедура позволяет настроить параметры службы push-уведомлений Майкрософт (MPNS) для центра уведомлений. 

1. В портал Azure на странице **Центр уведомлений** выберите **Windows Phone (MPNS)** в меню слева.
1. Включите push-уведомления с проверкой подлинности или без нее:

   1\. Чтобы включить push-уведомления без проверки подлинности, выберите **Включить push-уведомления без проверки подлинности** > **Сохранить**.

      ![Снимок экрана, на котором показано, как включить push-уведомления без проверки подлинности](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   2\. Чтобы включить push-уведомления с проверкой подлинности, сделайте следующее:
      * На панели инструментов щелкните **Отправка сертификата**.
      * Щелкните значок файла, а затем выберите файл сертификата.
      * Введите пароль для сертификата.
      * Нажмите кнопку **ОК**.
      * На странице **Windows Phone(MPNS)** выберите **Сохранить**.

## <a name="next-steps"></a>Следующие шаги
Пошаговые инструкции по отправке уведомлений на Windows Phone устройства с помощью центров уведомлений Azure и службы push-уведомлений Майкрософт (MPNS) см. в статье Push- [уведомления для Windows Phone приложений с помощью концентраторов уведомлений](notification-hubs-windows-mobile-push-notifications-mpns.md).

