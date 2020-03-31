---
title: Authenticate blob выход с управляемой аналитикой потока идентификации Azure
description: В этой статье описывается, как использовать управляемые идентификаторы для проверки подлинности задания Azure Stream Analytics на выходе из хранилища Azure Blob.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 13f48a9e0bc3ed8f8c4d5f1b7da4b6c03f54cdf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79129974"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output"></a>Используйте управляемый итог для аутентификации задания Azure Stream Analytics на выходе из хранилища Azure Blob

[Управляемая аутентификация идентификации](../active-directory/managed-identities-azure-resources/overview.md) для вывода в хранилище Azure Blob обеспечивает заданиям Stream Analytics прямой доступ к учетной записи хранилища вместо использования строки подключения. В дополнение к улучшенной безопасности эта функция также позволяет записывать данные на учетную запись хранения в виртуальной сети (VNET) в Azure.

В этой статье показано, как включить управляемую идентификацию для вывода Blob (ы) задания Stream Analytics через портал Azure и через развертывание диспетчера ресурсов Azure.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Создание задания Stream Analytics с помощью портала Azure

1. Создайте новое задание Stream Analytics или откройте существующее задание на портале Azure. Из панели меню, расположенной на левой стороне экрана, выберите **Управляемую идентификацию,** расположенную под **настройкой.** Убедитесь, что "Использование системы назначенных управляемой идентичности" выбран, а затем нажмите кнопку **Сохранить** в нижней части экрана.

   ![Настройка управляемой итог Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. В окне выходных свойств выходной раковины хранилища Azure Blob выберите режим проверки подлинности и выберите **Управляемую идентификацию.** Для получения информации о других свойствах вывода см. [Выводы «Понимание» из аналитики потоков Azure](./stream-analytics-define-outputs.md). Закончив, нажмите кнопку **Сохранить**.

   ![Настройка объема хранения Azure Blob](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. Теперь, когда задание создано, [см. доступ к заданиям Stream Analytics к разделу учетной записи хранилища](#give-the-stream-analytics-job-access-to-your-storage-account) этой статьи.

## <a name="azure-resource-manager-deployment"></a>Развертывание Azure Resource Manager

Использование менеджера ресурсов Azure позволяет полностью автоматизировать развертывание задания Stream Analytics. Шаблоны диспетчера ресурсов можно развернуть с помощью Azure PowerShell или [Azure CLI.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) В приведенных ниже примерах используется Azure CLI.


1. Вы можете создать ресурс **Microsoft.StreamAnalytics/streamingjobs** с управляемой идентичностью, включив следующее свойство в раздел ресурса шаблона «Менеджер ресурсов»:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Это свойство советует менеджеру ресурсов Azure создавать и управлять идентификацией для вашей работы Stream Analytics. Ниже приведен пример шаблона resource Manager, который развертывает работу Stream Analytics с включенным управляемым удостоверением и поглотителем вывода Blob, используюющим управляемую идентификацию:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
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
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
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

    Вышеупомянутое задание может быть развернуто в группе ресурсов **ExampleGroup** с использованием нижеупомянутой команды Azure CLI:

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. После создания задания можно использовать диспетчер ресурсов Azure для получения полного определения задания.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    Вышеупомянутая команда вернет ответ, как ниже:

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
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
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   Обратите внимание на **mainId** из определения задания, которое определяет управляемую идентификацию вашей работы в Active Directory Azure и будет использоваться на следующем этапе, чтобы предоставить доступ к учетной записи службы для хранения данных Stream Analytics.

3. Теперь, когда задание создано, [см. доступ к заданиям Stream Analytics к разделу учетной записи хранилища](#give-the-stream-analytics-job-access-to-your-storage-account) этой статьи.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Предоставьте вакансии Stream Analytics учетной записи учетной записи

Есть два уровня доступа, которые вы можете выбрать, чтобы дать работу Stream Analytics:

1. **Доступ к уровню контейнера:** эта опция дает задания доступ к определенному существующему контейнеру.
2. **Доступ к уровню учетной записи:** эта опция дает задания общий доступ к учетной записи хранилища, включая возможность создания новых контейнеров.

Если вам не нужна работа по созданию контейнеров от вашего имени, вы должны выбрать **доступ к уровню контейнера,** так как эта опция предоставит задания минимальный уровень необходимого доступа. Оба варианта приведены ниже для портала Azure и командной строки.

### <a name="grant-access-via-the-azure-portal"></a>Грант доступ через портал Azure

#### <a name="container-level-access"></a>Доступ на уровне контейнеров

1. Перейдите к панели конфигурации контейнера в учетной записи хранилища.

2. Выберите **элемент управления доступом (IAM)** на левой стороне.

3. В разделе "Добавить назначение роли" нажмите **Кнопка Добавить**.

4. В панели назначения роли:

    1. Установите **роль** "Хранение Blob данных вкладчика"
    2. Убедитесь, что **доступ Присваивать** сярприза установлен на "пользователь Azure AD, группа или основной службы".
    3. Введите имя задания Stream Analytics в поле поиска.
    4. Выберите задание Stream Analytics и нажмите **Кнопка Сохранить**.

   ![Доступ к контейнерам гранта](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>Доступ к уровню учетной записи

1. Войдите в свою учетную запись хранения.

2. Выберите **элемент управления доступом (IAM)** на левой стороне.

3. В разделе "Добавить назначение роли" нажмите **Кнопка Добавить**.

4. В панели назначения роли:

    1. Установите **роль** "Хранение Blob данных вкладчика"
    2. Убедитесь, что **доступ Присваивать** сярприза установлен на "пользователь Azure AD, группа или основной службы".
    3. Введите имя задания Stream Analytics в поле поиска.
    4. Выберите задание Stream Analytics и нажмите **Кнопка Сохранить**.

   ![Доступ к учетной записи гранта](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>Грант доступ через командную строку

#### <a name="container-level-access"></a>Доступ на уровне контейнеров

Чтобы предоставить доступ к определенному контейнеру, запустите следующую команду с помощью Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>Доступ к уровню учетной записи

Чтобы предоставить доступ ко всей учетной записи, запустите следующую команду с помощью Azure CLI:

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>Включить доступ к VNET

При настройке **брандмауэров и виртуальных сетей**учетной записи хранилища можно дополнительно разрешить сетевой трафик из других надежных служб Майкрософт. Когда Stream Analytics проверяет подлинность с помощью Управляемой идентификации, она предоставляет доказательства того, что запрос исходит от надежной службы. Ниже приведены инструкции, чтобы включить это исключение доступа VNET.

1.  Перейдите к панели конфигурации "Firewalls and virtual networks" в панели конфигурации учетной записи.
2.  Убедитесь, что опция "Разрешить доверенным службам Майкрософт доступ к этой учетной записи хранилища" включена.
3.  Если вы включили его, нажмите **Сохранить**.

   ![Включить доступ к VNET](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>Ограничения
Ниже приведены текущие ограничения этой функции:

1. Классические учетные записи хранения Azure.

2. Учетные записи Azure без активного каталога Azure.

3. Мультитенантный доступ не поддерживается. Основной сервис, созданный для данного задания Stream Analytics, должен находиться в том же арендаторе Azure Active Directory, в котором было создано задание, и не может использоваться с ресурсом, который находится в другом арендаторе Active Directory Azure.

4. [Назначенный пользователем идентификация](../active-directory/managed-identities-azure-resources/overview.md) не поддерживается. Это означает, что пользователь не может ввести свой собственный принцип обслуживания, который будет использоваться их работой Stream Analytics. Основной сервис должен быть создан аналитикой Azure Stream Analytics.

## <a name="next-steps"></a>Дальнейшие действия

* [Описание выходных данных из Azure Stream Analytics](./stream-analytics-define-outputs.md)
* [Секционирование выходных данных пользовательского большого двоичного объекта Azure Stream Analytics](./stream-analytics-custom-path-patterns-blob-storage-output.md)
