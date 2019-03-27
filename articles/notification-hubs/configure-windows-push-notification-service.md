---
title: Настройка службы Windows Push-уведомлений в центрах уведомлений Azure | Документация Майкрософт
description: Узнайте, как настроить параметры Windows Push Notification Service для концентратора уведомлений Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488332"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка параметров Windows Push Notification Service (WNS) для концентратора уведомлений на портале Azure
В этой статье показано, как настроить параметры службы уведомлений Windows (WNS) для концентратора уведомлений Azure с помощью портала Azure.  

## <a name="prerequisites"></a>Технические условия
Если вы уже создавали концентратора уведомлений, создайте ее. Дополнительные сведения см. в разделе [Создание концентратора уведомлений Azure на портале Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Настройка службы Push-уведомлений Windows (WNS)

В следующей процедуре представлены шаги по настройке параметров Windows Push Notification Service (WNS) для концентратора уведомлений: 

1. На портале Azure на **концентратора уведомлений** выберите **Windows (WNS)** в меню слева.
2. Введите значения для **идентификатора безопасности пакета** и **ключа безопасности**.
3. Щелкните **Сохранить**.

   ![Снимок экрана, показывающий поля ИД безопасности пакета и ключ безопасности](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Дальнейшие действия
Учебник с пошаговыми инструкциями по Push-уведомления для приложений универсальной платформы Windows с помощью центров уведомлений Azure и Windows Push Notification Service (WNS), см. в разделе [отправлять уведомления в приложения универсальной платформы Windows с помощью Azure Концентраторы уведомлений](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


