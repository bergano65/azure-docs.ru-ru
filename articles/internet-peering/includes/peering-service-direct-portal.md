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
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687041"
---
1. Выберите вонючее соединение, необходимое для службы пиринга Azure. Затем выберите **...**  >  **Отодвить соединение**.
    > [!div class="mx-imgBorder"]
    > ![Подключение к найму](../media/setup-direct-modify-editconnection.png)
1. В рамках **использования для peering Службы**выберите **Enabled,** а затем выберите **Сохранить.**
    > [!div class="mx-imgBorder"]
    > ![Вглядываевое соединение Включить службу пиринга](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. На экране **Обзора** вы видите детали развертывания. После завершения развертывания выберите **Перейти к ресурсу**.
    > [!div class="mx-imgBorder"]
    > ![Развертывание завершено](../media/setup-direct-modify-overview-deployment-complete.png)

1. На **панели зарегистрированных префикс** овечий, выберите **Добавить зарегистрированную префикс.**
    > [!div class="mx-imgBorder"]
    > ![Добавление зарегистрированного префикса](../media/setup-direct-modify-add-registered-prefix.png)
1. Зарегистрируйте префикс, выбрав **имя** и **приставку** и выбрав **Save.**
    > [!div class="mx-imgBorder"]
    >  ![Регистрация префикса](../media/setup-direct-modify-register-a-prefix.png) 

1. После создания префикса вы видите его в списке **зарегистрированных префиксов.** Выберите **название** префикса, чтобы увидеть более подробную информацию.
    > [!div class="mx-imgBorder"]
    > ![Зарегистрированные префиксы и соединения](../media/setup-direct-modify-registered-prefixes.png)
1. На странице зарегистрированной префикса вы видите полные сведения, которые включают **ключ префикса** для каждой приставки. Этот ключ должен быть предоставлен клиенту, выделяемому эту префиксу от своего провайдера-поставщика. Клиент может зарегистрировать свою префикс в рамках своей подписки, используя этот ключ.
    > [!div class="mx-imgBorder"]
    > ![Префикс с ключом префикса](../media/setup-direct-modify-registered-prefix-detail.png)