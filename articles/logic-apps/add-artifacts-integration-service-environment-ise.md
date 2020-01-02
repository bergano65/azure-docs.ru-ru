---
title: Добавление артефактов в среду службы интеграции
description: Добавление приложений логики, учетных записей интеграции и пользовательских соединителей в среду службы интеграции (ISE) для доступа к виртуальным сетям Azure (виртуальных сетей)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7221619c8d9388a9f6d46ec1dfbb11e467db861a
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793304"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Добавление артефактов в среду службы интеграции (ISE) в Azure Logic Apps

После создания [среды службы интеграции (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)добавьте артефакты, такие как приложения логики, учетные записи интеграции и соединители, чтобы они могли получать доступ к ресурсам в виртуальной сети Azure.

## <a name="prerequisites"></a>Технические условия

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* Интегрированная среда сценариев, созданная для запуска приложений логики. Если у вас нет интегрированной среды сценариев, [сначала создайте интегрированную среду сценариев](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Создание приложений логики

Чтобы создать приложения логики, которые выполняются в среде службы интеграции (ISE), выполните следующие действия.

1. Найдите и откройте интегрированную среду сценариев, если она еще не открыта. В меню интегрированной среды сценариев в разделе **Параметры**выберите **приложения логики** > **добавить**.

   ![Добавление нового приложения логики в ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   -или-

   В главном меню на портале Azure последовательно выберите **Создать ресурс** > **Интеграция** > **Приложение логики**.

1. Укажите имя, подписку Azure и группу ресурсов Azure (новую или существующую) для использования в приложении логики.

1. В списке **Расположение** в разделе **среды службы Integration** Services выберите интегрированную среду сценариев, например:

   ![Выбор среды службы интеграции](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > Если вы хотите использовать приложения логики с учетной записью интеграции, эти приложения логики и учетная запись интеграции должны использовать одну и ту же интегрированную среду сценариев.

1. Продолжайте [создавать приложения логики обычным образом](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Сведения о том, как работают триггеры и действия и как они помечаются при использовании интегрированной среды сценариев по сравнению с глобальной службой Logic Apps, см. [в разделе «изолированные и глобальные» в обзоре интегрированной среды сценариев](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Сведения об управлении приложениями логики и подключениями API в интегрированной среде сценариев см. в статье [Управление средой службы интеграции](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Создание учетных записей интеграции

В зависимости от [номера SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) , выбранного при создании, интегрированная среда разработки включает в себя конкретные сведения об использовании учетной записи интеграции без дополнительной оплаты. Приложения логики, которые существуют в среде служб Integration Services (ISE), могут ссылаться только на учетные записи интеграции, которые существуют в одной ISE. Таким образом, чтобы учетная запись интеграции работала с приложениями логики в интегрированной среде сценариев, как учетная запись интеграции, так и приложения логики должны использовать ту *же среду* , что и их расположение. Дополнительные сведения об учетных записях интеграции и Исес см. [в статье интеграция учетных записей с интегрированной](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)средой сценариев.

Чтобы создать учетную запись интеграции, использующую интегрированную среду сценариев, выполните следующие действия.

1. Найдите и откройте интегрированную среду сценариев, если она еще не открыта. В меню интегрированной среды сценариев в разделе **Параметры**выберите **учетные записи интеграции** > **добавить**.

   ![Добавление новой учетной записи интеграции в ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   -или-

   В главном меню Azure выберите **создать ресурс** > **Интеграция** > **учетная запись интеграции**.

1. Укажите имя, подписку Azure, группу ресурсов Azure (новую или существующую), а также ценовую категорию для использования в учетной записи интеграции.

1. В списке **Расположение** в разделе **среды службы Integration** Services выберите ту же интегрированную среду сценариев, которую используют приложения логики, например:

   ![Выбор среды службы интеграции](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [Свяжите приложение логики с учетной записью интеграции обычным способом](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Продолжайте добавление артефактов в учетную запись интеграции, например [торговые партнеры](../logic-apps/logic-apps-enterprise-integration-partners.md) и [соглашения](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Сведения об управлении учетными записями интеграции в интегрированной среде сценариев см. в статье [Управление средой службы интеграции](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Добавление соединителей ISE

Вы можете добавить соединители, управляемые Майкрософт, которые доступны для использования в интегрированной среде сценариев, но не развернуты в интегрированной среде сценариев.

1. В меню интегрированной среды сценариев в разделе **Параметры**выберите **управляемые соединители**. На панели инструментов нажмите кнопку **Добавить**.

   ![Просмотр управляемых соединителей](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. В области **Добавить новый управляемый соединитель** откройте список **Найти соединитель** . Если нужного соединителя доступен, выберите этот соединитель и нажмите кнопку **создать**.

   В списке отображаются только соединители, которые подходят, но не развернуты в интегрированной среде сценариев. Соединители, уже развернутые в интегрированной среде сценариев, доступны для выбора.

   ![Выбор подходящего соединителя](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Создание настраиваемых соединителей

Чтобы использовать пользовательские соединители в интегрированной среде сценариев, создайте эти пользовательские соединители непосредственно внутри интегрированной среды сценариев.

1. Найдите и откройте интегрированную среду сценариев, если она еще не открыта. В меню интегрированной среды сценариев в разделе **Параметры**выберите **настраиваемые соединители** > **добавить**.

   ![Создание пользовательского соединителя](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Укажите имя, подписку Azure и группу ресурсов Azure (новую или существующую) для использования с настраиваемым соединителем.

1. В списке **Расположение** в разделе **среды службы Integration** Services выберите ту же интегрированную среду сценариев, которую используют приложения логики, и выберите **создать**, например:

   ![Выбор среды службы интеграции](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. Выберите новый настраиваемый соединитель, а затем щелкните **изменить**, например:

   ![Выбор и изменение настраиваемого соединителя](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Продолжайте создание соединителя обычным способом из [определения OpenAPI](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) или [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Сведения об управлении пользовательскими соединителями в интегрированной среде сценариев см. в статье [Управление средой службы интеграции](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Управление средами службы интеграции](../logic-apps/ise-manage-integration-service-environment.md)
