---
title: Используйте управляемую идентификацию для аутентификации задания Azure Stream Analytics на выходе Power BI
description: В этой статье описывается, как использовать управляемые идентификаторы для проверки подлинности задания Azure Stream Analytics на выходе Power BI.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/10/2020
ms.openlocfilehash: 31a5195038ef25acadc08e2acbedf8471b25833c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261420"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Используйте управляемую идентификацию для аутентификации задания Analytics потоков Azure в Power BI

[Управляемая аутентификация идентификации](../active-directory/managed-identities-azure-resources/overview.md) для вывода в Power BI обеспечивает работа потоков Analytics прямого доступа к рабочей области в вашей учетной записи Power BI. Эта функция позволяет полностью автоматизированно развертыванию заданий Stream Analytics, так как пользователю больше не требуется интерактивновойтись в Power BI через портал Azure. Кроме того, долго работающие задания, которые пишут в Power BI, теперь лучше поддерживаются, так как вам не нужно будет периодически повторно авторизовать работу.

В этой статье показано, как включить управляемую идентификацию для вывода analytics Power (ы) задания Stream Analytics через портал Azure и через развертывание диспетчера ресурсов Azure.

## <a name="prerequisites"></a>Предварительные требования

Для использования этой функции требуется следующее:

- Учетная запись Power BI с [лицензией Pro.](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)

- Обновленное рабочее пространство в вашей учетной записи Power BI. Более подробную информацию можно узнать [из объявления Power BI](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) об этой функции.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Создание задания Stream Analytics с помощью портала Azure

1. Создайте новое задание Stream Analytics или откройте существующее задание на портале Azure. Из панели меню, расположенной на левой стороне экрана, выберите **Управляемую идентификацию,** расположенную под **настройкой.** Убедитесь, что "Использование системы назначенных управляемой идентичности" выбран, а затем выберите кнопку **Сохранить** в нижней части экрана.

   ![Настройка управляемой итог Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Прежде чем настроить выход, предоставьте рабочей работе Stream Analytics доступ к рабочему пространству Power BI, следуя указаниям в [разделе «Дайте stream Analytics» доступ к рабочему пространству Power BI](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) в этой статье.

3. Перейдите к разделу **выходов** в работе Stream Analytic, выберите **и переключите,** а затем выберите **Power BI.** Затем выберите кнопку **Authorize** и войдите в систему с вашей учетной записью Power BI.

   ![Авторизация с учетной записью Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. После авторизуем список выпадающих мест будет заполнен всеми рабочими областями, к которых у вас есть доступ. Выберите рабочее пространство, утвержденное на предыдущем этапе. Затем выберите **Управляемую идентификацию** в качестве "режима аутентификации". Наконец, выберите кнопку **«Сохранить».**

   ![Настройка выходного выпуска Power BI с управляемой идентичностью](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Развертывание Azure Resource Manager

Менеджер ресурсов Azure позволяет полностью автоматизировать развертывание задания Stream Analytics. Шаблоны диспетчера ресурсов можно развернуть с помощью Azure PowerShell или [Azure CLI.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) В приведенных ниже примерах используется Azure CLI.


1. Вы можете создать ресурс **Microsoft.StreamAnalytics/streamingjobs** с управляемой идентичностью, включив следующее свойство в раздел ресурса шаблона «Менеджер ресурсов»:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Это свойство советует менеджеру ресурсов Azure создавать и управлять идентификацией для вашей работы Stream Analytics. Ниже приведен пример шаблона resource Manager, который развертывает работу Stream Analytics с включенной управляемой идентичностью и выходной раковиной Power BI, используюющей управляемую идентичность:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Развертывание задания выше в группе ресурсов **ExampleGroup** с помощью нижеупомянутой команды Azure CLI:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. После создания задания используйте диспетчер ресурсов Azure для получения полного определения задания.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    Вышеупомянутая команда вернет ответ, как ниже:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Если вы планируете использовать REST API Power BI для добавления задания Stream Analytics в рабочее пространство Power BI, обратите внимание на возвращенный "principalId".

3. Теперь, когда задание создано, продолжайте [давать Stream Analytics доступ к рабочему пространству Power BI](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) в разделе этой статьи.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Предоставьте рабочей работе Stream Analytics доступ к рабочему пространству Power BI

Теперь, когда работа Stream Analytics создана, ему может быть предоставлен доступ к рабочему пространству Power BI.

### <a name="use-the-power-bi-ui"></a>Используйте uI Power BI

   > [!Note]
   > Для добавления задания Stream Analytics в рабочее пространство Power BI с помощью uI также необходимо включить основной доступ к службе в **настройках разработчика** на портале админ-амин.Power BI. Для получения более подробной информации смотрите начало [работы с директором службы.](https://docs.microsoft.com/power-bi/developer/embed-service-principal)

1. Перейдите к настройкам доступа рабочего пространства. Подробнее о работе читайте в этой статье: [Предоставьте доступ к рабочему пространству.](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)

2. Введите имя задания Stream Analytics в текстовом поле и выберите в качестве уровня **доступа.**

3. Выберите **Добавить** и закрыть панель.

   ![Добавление заданий Stream Analytics в рабочее пространство Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Используйте Power BI PowerShell cmdlets

1. Установите power `MicrosoftPowerBIMgmt` BI PowerShell cmdlets.

   > [!Important]
   > Пожалуйста, убедитесь, что вы используете версию 1.0.821 или более позднюю версию cmdlets.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Войти в Power BI.

```powershell
Login-PowerBI
```

3. Добавьте задание Stream Analytics в качестве вкладчика в рабочее пространство.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Используйте Power BI REST API

Задание Stream Analytics также может быть добавлено в качестве вкладчика в рабочее пространство с помощью aPI REST "Add Group User" напрямую. Полную документацию для этого API можно найти здесь: [Группы - Добавить группу пользователя](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser).

**Запрос на выборку**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Текст запроса
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>Ограничения
Ниже приведены ограничения этой функции:

- Классические рабочие области Power BI не поддерживаются.

- Учетные записи Azure без активного каталога Azure.

- Мультитенантный доступ не поддерживается. Основной сервис, созданный для данного задания Stream Analytics, должен находиться в том же арендаторе Azure Active Directory, в котором было создано задание, и не может использоваться с ресурсом, который находится в другом арендаторе Active Directory Azure.

- [Назначенный пользователем идентификация](../active-directory/managed-identities-azure-resources/overview.md) не поддерживается. Это означает, что вы не можете ввести свой собственный принцип обслуживания, который будет использоваться их работой Stream Analytics. Основной сервис должен быть создан аналитикой Azure Stream Analytics.

## <a name="next-steps"></a>Дальнейшие действия

* [Интеграция панели мониторинга Power BI с Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Описание выходных данных из Azure Stream Analytics](./stream-analytics-define-outputs.md)
