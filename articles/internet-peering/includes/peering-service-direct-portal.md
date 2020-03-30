---
title: включить файл
titleSuffix: Azure
description: включить файл
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129953"
---
1. Нажмите на вглядывающее соединение, включающее для Peering Service, а затем нажмите на **...**  >  **Кнопка подключения.**
    > [!div class="mx-imgBorder"]
    > ![Вглядывающее подключение Edit](../media/setup-direct-modify-editconnection.png)
1. Под разделом ***Использование для Peering службы***, нажмите на **Enabled** и **сохранить**.
    > [!div class="mx-imgBorder"]
    > ![Peering Connection Включить Peering службы](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. На экране Обзора вы увидите детали развертывания. Как только развертывание завершено, нажмите на **Перейдите к ресурсу.**
    > [!div class="mx-imgBorder"]
    > ![Развертывание завершено](../media/setup-direct-modify-overview-deployment-complete.png)

1. Затем вы увидите под настройками **зарегистрированные префиксы.** Нажмите на **Добавить Зарегистрированная префикса**.
    > [!div class="mx-imgBorder"]
    > ![Зарегистрированные префиксы и соединения](../media/setup-direct-modify-add-registered-prefix.png)
1. Зарегистрируйте префикс, выбрав **имя** и **префикс** и нажмите **Сохранить**
    > [!div class="mx-imgBorder"]
    >  ![Регистрация префикса](../media/setup-direct-modify-register-a-prefix.png) 

1. Как только приставка будет создана, вы увидите его в списке зарегистрированных префиксов. Нажмите на **имя** префикса, чтобы увидеть более подробную информацию.
    > [!div class="mx-imgBorder"]
    > ![Зарегистрированные префиксы и соединения](../media/setup-direct-modify-registered-prefixes.png)
1. На странице зарегистрированной префикса вы увидите полную информацию, чтобы включить **ключ префикса** для каждой приставки. Этот ключ должен быть предоставлен клиенту, выделяемому эту префиксу от своего провайдера-поставщика. Клиент может зарегистрировать свою префикс в рамках своей подписки с помощью этого ключа.
    > [!div class="mx-imgBorder"]
    > ![Префикс с ключом префикса](../media/setup-direct-modify-registered-prefix-detail.png)