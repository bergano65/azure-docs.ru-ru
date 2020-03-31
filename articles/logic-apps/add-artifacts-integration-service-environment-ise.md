---
title: Добавление ресурсов в среды служб интеграции
description: Добавление логических приложений, учетных записей интеграции, пользовательских разъемов и управляемых разъемов в среду служб интеграции (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 58d2efd0c61045739930ce36ba317b1aa6a40ce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164882"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Добавление ресурсов в среду интеграционных служб (ISE) в приложенияazlogic Logic Apps

После создания [среды служб интеграции (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)добавьте ресурсы, такие как логические приложения, учетные записи интеграции и разъемы, чтобы они могли получить доступ к ресурсам в виртуальной сети Azure. Например, управляемые разъемы ISE, которые становятся доступными после создания ISE, автоматически не отображаются в Logic App Designer. Прежде чем вы сможете использовать эти разъемы ISE, вы должны вручную [добавить и развернуть эти разъемы для вашего ISE](#add-ise-connectors-environment) так, чтобы они появляются в Logic App Designer.

> [!IMPORTANT]
> Для того чтобы логические приложения и учетные записи интеграции работали вместе в ISE, оба должны использовать тот *же ISE,* что и их местоположение.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* ISE, созданный для запуска логических приложений. Если у вас нет ISE, [создайте ISE в первую очередь](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

* Для создания, добавления или обновления ресурсов, развернутых в ISE, необходимо назначить роль владельца или автора на этом ISE или получить разрешения, унаследованные через подписку Azure или группу ресурсов Azure, связанную с ISE. Для людей, у которых нет разрешений на владельца, участника или унаследованных разрешений, им может быть присвоена роль участника службы интеграции или роль разработчика среды службы интеграции. Для получения дополнительной информации о элементах [What is role-based access control (RBAC) for Azure resources](../role-based-access-control/overview.md)управления доступом на основе ролей (RBAC) см.

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Создание логических приложений

Для создания логических приложений, запускаемых в среде интеграционных служб (ISE), выполните следующие действия:

1. Найдите и откройте ISE, если они еще не открыты. Из меню ISE, в **настройках,** выберите **Логические приложения** > **Добавить**.

   ![Добавление нового приложения логики в ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. Предоставьте информацию о логическом приложении, которое вы хотите создать, например:

   ![Выбор среды службы интеграции](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | Свойство | Обязательно | Описание |
   |----------|----------|-------------|
   | **Название** | Да | Имя для создания приложения логики |
   | **Подписка** | Да | Имя подписки Azure, которую нужно использовать. |
   | **Группа ресурсов** | Да | Имя группы ресурсов Azure (новая или существующая) для использования |
   | **Расположение** | Да | В **средах службы интеграции**выберите ISE для использования, если он еще не выбран. <p><p> **Важно:** Для использования логических приложений с учетной записью интеграции оба должны использовать один и тот же ISE. |
   ||||

1. Когда все будет готово, выберите **Создать**.

1. Продолжить [создание приложения логики обычным способом.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

   Для различия в том, как работают триггеры и действия и как они помечены при использовании ISE по сравнению с мультитенантным сервисом Logic Apps, см. [Изолированные и мультиарендаторы в обзоре ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

1. Чтобы управлять логическими приложениями и подключением API в ISE, [см.](../logic-apps/ise-manage-integration-service-environment.md)

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Создание интеграционных учетных записей

На основе [ISE SKU,](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) выбранного при создании, ваш ISE включает в себя конкретное использование интеграционного счета без каких-либо дополнительных затрат. Логические приложения, существующие в среде служб интеграции (ISE), могут ссылаться только на учетные записи интеграции, которые существуют в том же ISE. Таким образом, для работы учетной записи интеграции с логическими приложениями в ISE, как интеграционная учетная запись, так и логические приложения должны использовать ту *же среду,* что и их местоположение. Для получения дополнительной информации об [Integration accounts with ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)интеграционных счетах и ISE см.

Чтобы создать учетную запись интеграции, используюую ISE, выполните следующие действия:

1. Найдите и откройте ISE, если они еще не открыты. Из меню ISE, в **настройках,** выберите **Интеграционные учетные записи** > **Добавить**.

   ![Добавление новой учетной записи интеграции в ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. Предоставьте информацию о логическом приложении, которое вы хотите создать, например:

   ![Выбор среды службы интеграции](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | Свойство | Обязательно | Описание |
   |----------|----------|-------------|
   | **Название** | Да | Название интеграционной учетной записи, которую вы хотите создать |
   | **Подписка** | Да | Имя для подписки Azure, которую вы хотите использовать |
   | **Группа ресурсов** | Да | Имя группы ресурсов Azure (новая или существующая) для использования |
   | **Ценовая категория** | Да | Ценовой уровень для использования для интеграционного счета |
   | **Расположение** | Да | В **средах служб интеграции**выберите тот же ISE, который используют ваши логические приложения, если они еще не выбраны. <p><p> **Важно:** чтобы использовать учетную запись интеграции с логическими приложениями, оба должны использовать один и тот же ISE. |
   ||||

1. Когда все будет готово, выберите **Создать**.

1. [Свяжите свое логическое приложение с учетной записью интеграции обычным способом.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)

1. Продолжить, добавляя ресурсы на свой интеграционный счет, такие как [торговые партнеры](../logic-apps/logic-apps-enterprise-integration-partners.md) и [соглашения.](../logic-apps/logic-apps-enterprise-integration-agreements.md)

1. Чтобы управлять учетными записями интеграции в ISE, [см.](../logic-apps/ise-manage-integration-service-environment.md)

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Добавить разъемы ISE

Разъемы, управляемые корпорацией Майкрософт, которые становятся доступными после создания ISE, автоматически не отображаются в сборщике разъемов на Logic App Designer. Прежде чем вы сможете использовать эти разъемы ISE, вы должны вручную добавить и развернуть эти разъемы для вашего ISE так, чтобы они появляются в Logic App Designer.

1. В меню ISE, под **настройками,** выберите **управляемые разъемы.** На панели инструментов нажмите кнопку **Добавить**.

   ![Просмотр управляемых разъемов](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. На **добавлении нового управляемого разъема,** откройте список **разъемов Поиска.** Выберите разъем ISE, который вы хотите использовать, но еще не развернуты в вашем ISE. Выберите **Создать**.

   ![Выберите разъем ISE, который вы хотите развернуть в ISE](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   Только разъемы ISE, которые имеют право, но еще не развернуты на вашем ISE, доступны для выбора. Соединения, уже развернутые в ISE, недоступны для выбора.

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Создание пользовательских разъемов

Чтобы использовать пользовательские разъемы в вашем ISE, создайте эти пользовательские разъемы прямо внутри вашего ISE.

1. Найдите и откройте ISE, если они еще не открыты. Из меню ISE, под **настройками,** выберите **пользовательские разъемы** > **Добавить.**

   ![Создание пользовательского соединителя](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Предоставьте имя, подписку Azure и группу ресурсов Azure (новую или существующую) для использования для пользовательского разъема.

1. Из списка **местоположений** в разделе **«Среды интеграции»** выберите тот же ISE, который используют ваши логические приложения, и выберите **Создать,** например:

   ![Выбор среды службы интеграции](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. Выберите новый пользовательский разъем, а затем выберите **Edit,** например:

   ![Выберите и отображете пользовательский разъем](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Продолжить, создавая разъем обычным способом от [определения OpenAPI](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) или [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Чтобы управлять пользовательскими разъемами в ISE, [см.](../logic-apps/ise-manage-integration-service-environment.md)

## <a name="next-steps"></a>Дальнейшие действия

* [Управление средами службы интеграции](../logic-apps/ise-manage-integration-service-environment.md)
