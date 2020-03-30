---
title: Создание или управление учетными записями интеграции B2B
description: Создание, связь и управление учетными записями интеграции для интеграции предприятий с приложениями Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 083ed0001adb5524c124295eb3bc31f4afad99cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270333"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Создание и управление учетными записями интеграции для интеграции B2B в приложениях логики Azure

Чтобы создавать [решения для корпоративной интеграции и B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) с помощью [Azure Logic Apps](../logic-apps/logic-apps-overview.md), необходимо создать учетную запись интеграции. По сути, это отдельный ресурс Azure, предоставляющий защищенный, масштабируемый и управляемый контейнер для артефактов интеграции, которые вы определяете и используете в рабочих процессах приложения логики.

Например, можно создавать, хранить и управлять артефактами B2B, такими как торговые партнеры, соглашения, карты, схемы, сертификаты и пакетные конфигурации. Кроме того, прежде чем приложение логики сможет работать с этими артефактами и использовать разъемы Logic Apps B2B, необходимо [связать учетную запись интеграции](#link-account) с приложением логики. Учетная запись интеграции и приложение логики должны существовать в *одном* месте или регионе.

> [!TIP]
> Для создания учетной записи интеграции в [среде службы интеграции](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)см. [Create integration accounts in an ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

Эта тема показывает, как выполнять следующие задачи:

* создание учетной записи интеграции;
* привязка учетной записи интеграции к приложению логики;
* Измените уровень ценообразования для вашей интеграционной учетной записи.
* Отсоедините учетную запись интеграции из приложения логики.
* перемещение учетной записи интеграции в другую подписку или группу ресурсов Azure;
* удаление учетной записи интеграции.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Создание учетной записи интеграции

Для этой задачи можно использовать портал Azure, выследуя следующие действия в этом [разделе, Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)или [Azure CLI.](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create)

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетных данных учетной записи Azure.

1. В главном меню Azure выберите команду **Создать ресурс**. В поле поиска введите "интеграционный счет" в качестве фильтра и выберите **учетную запись интеграции.**

   ![Создание новой учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Под **интеграционным счетом**выберите **Создать**.

   ![Выбор элемента "Добавить" для создания учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Предоставьте эту информацию о вашей учетной записи интеграции:

   ![Предоставление сведений об интеграционных счетах](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Свойство | Обязательно | Значение | Описание |
   |----------|----------|-------|-------------|
   | **Название** | Да | <*интеграция-имя счета*> | Имя вашего интеграционного счета, которое может содержать только`-`буквы,`_`цифры, дефисы (), подчеркивает (, скобки (,`(` `)`, и периоды (`.`). В этом примере используется "Фабрикам-Интеграция". |
   | **Подписка** | Да | <*Имя подписки Azure*> | Имя подписки Azure. |
   | **Группа ресурсов** | Да | <*Azure-ресурс-группа-имя*> | Имя [группы ресурсов Azure](../azure-resource-manager/management/overview.md) для организации связанных ресурсов. Для этого можно привести новую группу ресурсов с названием «ФабрикамИнтеграция-РГ». |
   | **Уровень цен** | Да | <*ценового уровня*> | Ценовой уровень для интеграционного счета, который вы можете изменить позже. Для этого примера выберите **Free**. Дополнительные сведения см. в следующих статьях: <p>- [Модель ценообразования Logic Apps](../logic-apps/logic-apps-pricing.md#integration-accounts) <p>- [Ограничения и конфигурация логических приложений](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>- [Ценообразование логических приложений](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **Расположение** | Да | <*Лазурный регион*> | Регион, где можно хранить метаданные учетной записи интеграции. Выберите то же место, что и приложение логики, либо создайте логические приложения в том же месте, что и учетная запись интеграции. Для этого примера используйте "Запад США". <p>**Примечание:** Для создания учетной записи интеграции в [среде службы интеграции (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)выберите ISE в качестве местоположения. Для получения дополнительной информации [см. Создать интеграционные учетные записи в ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Аналитика журнала** | нет | Выкл, на | Держите настройки **Off** для этого примера. |
   |||||

1. Когда вы закончите, выберите **Создать**.

   После завершения развертывания Azure открывает учетную запись интеграции.

   ![Azure открывает учетную запись интеграции](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Прежде чем приложение логики сможет использовать учетную запись интеграции, выполните следующие шаги, чтобы связать учетную запись интеграции и логическое приложение вместе.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Привязка к приложению логики

Чтобы предоставить вашим логическим приложениям доступ к учетной записи интеграции, содержащей артефакты B2B, необходимо сначала связать учетную запись интеграции с приложением логики. В одном регионе должны существовать как логические приложения, так и учетная запись интеграции. Для выполнения этой задачи можно использовать портал Azure. Если вы используете Visual Studio и ваше приложение логики находится в [проекте Группы ресурсов Azure,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)вы можете [связать свое логическое приложение с интеграционной учетной записью с помощью Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account)

1. На портале Azure найдите и откройте нужное приложение логики.

1. На [портале Azure](https://portal.azure.com)откройте существующее приложение логики или создайте новое приложение логики.

1. В меню приложения логики в разделе **Параметры** выберите **Параметры рабочего процесса**. В соответствии с **учетной записью Integration,** откройте список **учетных записей Select an Integration.** Выберите учетную запись интеграции для ссылки на приложение логики.

   ![Выбор учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Чтобы закончить ссылку, выберите **Сохранить**.

   ![Выбор учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   После успешной связи учетной записи Azure показывает сообщение о подтверждении.

   ![Подтверждение успешной привязки в Azure](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Теперь приложение логики может использовать артефакты в учетной записи интеграции плюс разъемы B2B, такие как проверка XML и кодирование или декодирование плоских файлов.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Смена ценовой категории

Чтобы увеличить [лимиты](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) для интеграционного счета, вы можете [перейти на более высокий уровень ценообразования,](#upgrade-pricing-tier)если это возможно. Например, можно перейти от свободного уровня до базового или стандартного уровня. Вы также можете [понизить до более низкого уровня,](#downgrade-pricing-tier)если это возможно. Для получения дополнительной информации о ценах, см.

* [Ценообразование логических приложений](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Модель ценообразования приложений логики](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Уровень повышения цен

Чтобы внести это изменение, можно использовать портал Azure, выследуя следующие действия в этом разделе или [Azure CLI.](#upgrade-tier-azure-cli)

#### <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетных данных учетной записи Azure.

1. В основном поле поиска Azure введите "интеграционные учетные записи" в качестве фильтра и выберите **учетные записи интеграции.**

   ![Поиск учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure отображает все учетные записи интеграции в подписках Azure.

1. В разделе **Учетные записи интеграции** выберите учетную запись интеграции, которую необходимо переместить. В меню интеграционного счета выберите **Обзор**.

   ![В меню интеграционного счета выберите "Обзор"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. На панели Обзора выберите **уровень ценообразования обновления,** в котором перечислены любые доступные более высокие уровни. При выборе уровня изменение немедленно вступает в силу.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. Если вы еще не сделали этого, [установите предпосылки Azure CLI.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. На портале Azure откройте среду Azure [**Cloud Shell.**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Открытие Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. В запросе команды введите команду `skuName` ресурсов [ **az** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)и установите на более высокий уровень, который вы хотите.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Например, если у вас есть базовый `Standard`уровень, вы можете установить: `skuName`

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Понижение уровня ценообразования

Чтобы внести это изменение, используйте [Azure CLI.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. Если вы еще не сделали этого, [установите предпосылки Azure CLI.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)

1. На портале Azure откройте среду Azure [**Cloud Shell.**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)

   ![Открытие Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. В запросе команды введите команду `skuName` ресурсов [ **az** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) и установите на нижний уровень, который вы хотите.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Например, если у вас есть стандартный `Basic`уровень, вы можете установить: `skuName`

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Удаление привязки к приложению логики

Если вы хотите связать свое логическое приложение с другой учетной записью интеграции или больше не использовать учетную запись интеграции с вашим логическим приложением, удалите ссылку с помощью Azure Resource Explorer.

1. Откройте окно браузера и перейдите на [Azure Resource Explorer (https://resources.azure.com)](https://resources.azure.com). Войти в систему с теми же учетными данными учетной записи Azure.

   ![Обозреватель ресурсов Azure](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. В поле поиска введите имя приложения логики, чтобы можно было найти и выбрать приложение логики.

   ![Поиск и выбор приложения логики](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. На панели заголовка explorer выберите **Read/Write**.

   ![Включение режима "Чтение и запись"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. На вкладке **Данные** выберите **Edit**.

   ![На вкладке "Данные" выберите "Edit"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. В редакторе найдите `integrationAccount` объект и удалите это свойство, которое имеет этот формат:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Пример:

   ![Поиск объекта "интеграциясчета"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. На вкладке **"Данные"** выберите **"Положите"** для сохранения изменений.

   ![Чтобы сохранить изменения, выберите "Put"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. На портале Azure найдите и выберите требуемое приложение логики. В **настройках рабочего процесса**вашего приложения убедитесь, что свойство **учетной записи Интеграции** теперь выглядит пустым.

   ![Проверка на предмет отсутствия привязки у учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Перемещение учетной записи интеграции

Учетную запись интеграции можно переместить в другую группу ресурсов Azure или подписку Azure. При перемещении ресурсов Azure создает новые идентионные данные ресурсов, поэтому убедитесь, что вместо этого вы используете новые идентиматары и обновляете любые скрипты или инструменты, связанные с перемещенными ресурсами. Если вы хотите изменить подписку, необходимо также указать существующую или новую группу ресурсов.

Для этой задачи можно использовать портал Azure, выследуя следующие действия в этом разделе или [Azure CLI.](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move)

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетных данных учетной записи Azure.

1. В основном поле поиска Azure введите "интеграционные учетные записи" в качестве фильтра и выберите **учетные записи интеграции.**

   ![Поиск учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure отображает все учетные записи интеграции в подписках Azure.

1. В разделе **Учетные записи интеграции** выберите учетную запись интеграции, которую необходимо переместить. В меню интеграционного счета выберите **Обзор**.

   ![В меню интеграционного счета выберите "Обзор"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Рядом с **группой ресурсов** или **именем подписки**выберите **изменение.**

   ![Изменение группы ресурсов или подписки](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Выберите любые связанные ресурсы, которые вы также хотите переместить.

1. На основе выбора выполните следующие действия, чтобы изменить группу ресурсов или подписку:

   * Группа ресурсов: Из списка **группы ресурсов** выберите группу ресурсов назначения. Или, чтобы создать другую группу ресурсов, выберите **Создать новую группу ресурсов.**

   * Подписка: Из списка **подписки** выберите подписку на место назначения. Из списка **группы ресурсов** выберите группу ресурсов назначения. Или, чтобы создать другую группу ресурсов, выберите **Создать новую группу ресурсов.**

1. Чтобы подтвердить ваше понимание того, что любые скрипты или инструменты, связанные с перемещенными ресурсами, не будут работать до тех пор, пока вы не обновите их с помощью новых идентимативных идентимативных данных, выберите окно подтверждения, а затем выберите **OK.**

1. После завершения, убедитесь, что вы обновляете все и все скрипты с новым идентизацией ресурсов для ваших перемещенных ресурсов.  

## <a name="delete-integration-account"></a>Удаление учетной записи интеграции

Для этой задачи можно использовать портал Azure, выследуя следующие действия в этом разделе, [Azure CLI](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)или [Azure PowerShell.](/powershell/module/az.logicapp/remove-azintegrationaccount)

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетных данных учетной записи Azure.

1. В основном поле поиска Azure введите "интеграционные учетные записи" в качестве фильтра и выберите **учетные записи интеграции.**

   ![Поиск учетной записи интеграции](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   Azure отображает все учетные записи интеграции в подписках Azure.

1. В разделе **Учетные записи интеграции** выберите учетную запись интеграции, которую необходимо удалить. В меню интеграционного счета выберите **Обзор**.

   ![В меню интеграционного счета выберите "Обзор"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. На панели обзора выберите **Удалить**.

   ![На панели "Обзор" выберите "Удалить"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Чтобы подтвердить, что вы хотите удалить свою учетную запись интеграции, выберите **Да**.

   ![Для подтверждения удаления выберите "Да"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание торговых партнеров на вашем интеграционном счете](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Создание соглашений между партнерами в вашей интеграционной учетной записи](../logic-apps/logic-apps-enterprise-integration-agreements.md)
