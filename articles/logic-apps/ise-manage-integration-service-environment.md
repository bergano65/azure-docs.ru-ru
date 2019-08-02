---
title: Управление средами службы интеграции в Azure Logic Apps
description: Проверьте работоспособность сети и управляйте приложениями логики, подключениями, пользовательскими соединителями и учетными записями интеграции в среде службы интеграции (ISE) для Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 8f10e3d3fd7c67d1e803e8f85c9918c91bb81d59
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517466"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Управление средой службы интеграции (ISE) в Azure Logic Apps

Чтобы проверить работоспособность сети для [среды службы интеграции (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) и управлять приложениями логики, подключениями, учетными записями интеграции и пользовательскими соединителями, которые существуют в интегрированной среде сценариев, выполните действия, описанные в этом разделе.

## <a name="view-your-ise"></a>Просмотр интегрированной среды сценариев

1. Войдите на [портале Azure](https://portal.azure.com).

1. В поле поиска на портале введите "среды службы интеграции", а затем выберите **среды службы интеграции**.

   ![Поиск сред службы интеграции](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. В списке результатов выберите среду службы интеграции.

   ![Выбор среды службы интеграции](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Перейдите к следующим разделам, чтобы найти приложения логики, подключения, соединители или учетные записи интеграции в интегрированной среде сценариев.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Проверка работоспособности сети

В меню интегрированной среды сценариев в разделе **Параметры**выберите **работоспособность сети**. В этой области отображаются сведения о состоянии работоспособности для подсетей и исходящих зависимостей от других служб.

![Проверка работоспособности сети](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Управление приложениями логики

1. В меню интегрированной среды сценариев в разделе **Параметры**выберите пункт **приложения логики**.

   ![Поиск приложений логики](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Чтобы удалить приложения логики из интегрированной среды сценариев, если они больше не нужны, выберите эти приложения логики, а затем нажмите кнопку **Удалить**.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Управление подключениями API

1. Чтобы просмотреть подключения API, созданные приложениями логики, запущенными в интегрированной среде сценариев, в меню ИНТЕГРИРОВАНной среды в разделе **Параметры**выберите **подключения API**.

   ![Поиск подключений API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Чтобы удалить подключения из интегрированной среды сценариев, если они больше не нужны, выберите эти подключения и нажмите кнопку **Удалить**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Управление пользовательскими соединителями

1. Чтобы просмотреть настраиваемые соединители, созданные в интегрированной среде сценариев, в меню интегрированной среды сценариев в разделе **Параметры**выберите **пользовательские соединители**.

   ![Элемент "Настраиваемые соединители"](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Чтобы удалить настраиваемые соединители из интегрированной среды сценариев, если они больше не нужны, выберите эти соединители и нажмите кнопку **Удалить**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Управление учетными записями интеграции

1. В меню интегрированной среды сценариев в разделе **Параметры**выберите **учетные записи интеграции**.

   ![Поиск учетных записей интеграции](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Чтобы удалить учетные записи интеграции из интегрированной среды сценариев, если они больше не нужны, выберите эти учетные записи интеграции и нажмите кнопку **Удалить**.

## <a name="next-steps"></a>Следующие шаги

* Узнайте, как [подключаться к виртуальным сетям Azure из изолированных приложений логики](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).
