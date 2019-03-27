---
title: Настройка службы Apple Push-уведомлений в центрах уведомлений Azure | Документация Майкрософт
description: Узнайте, как настроить концентратор уведомлений Azure с применением конфигурации Apple Push Notification Service (APNS).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9a9db9f05895569b050e56cdeec1ee2ee25af0df
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488386"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Настройка параметров Apple Push Notification Service (APNS) для концентратора уведомлений на портале Azure
В этой статье показано, как настроить параметры Apple Push Notification Service (APNS) для концентратора уведомлений Azure с помощью портала Azure. 

## <a name="prerequisites"></a>Технические условия
Если вы уже создавали концентратора уведомлений, создайте ее. Дополнительные сведения см. в разделе [Создание концентратора уведомлений Azure на портале Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Настройка службы Push-уведомлений Apple

В следующей процедуре представлены шаги по настройке параметров Apple Push Notification Service (APNS) для концентратора уведомлений:

1. На портале Azure на **концентратора уведомлений** выберите **Apple (APNS)** в меню слева.

1. Для **режим проверки подлинности**, выберите пункт **сертификат** или **маркера**.

   a. При выборе **сертификат**:
   * Выберите значок файла, а затем выберите *.p12* файл, который необходимо передать.
   * Введите пароль.
   * Выберите режим **Песочница**. Чтобы отправить Push-уведомлений пользователям, выполнившим покупку приложения в магазине, выберите **рабочей** режим.

     ![Снимок экрана APNS сертификатов конфигурации на портале Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   2. При выборе **маркера**:

   * Введите значения для **ИД ключа**, **идентификатор пакета**, **идентификатор команды**, и **маркера**.
   * Выберите режим **Песочница**. Чтобы отправить Push-уведомлений пользователям, выполнившим покупку приложения в магазине, выберите **рабочей** режим.

     ![Снимок экрана APNS token конфигурации на портале Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Дальнейшие действия
Учебник с пошаговыми инструкциями для Push-уведомления на устройства iOS обратитесь к следующей статье: [Push-уведомления на устройства iOS с помощью центров уведомлений и APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
