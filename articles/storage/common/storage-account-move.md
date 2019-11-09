---
title: Перемещение учетной записи хранения Azure в другой регион | Документация Майкрософт
description: Показано, как переместить учетную запись хранения Azure в другой регион.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 8ce949ac997ba7ee38cb057752d89f4b4d22388f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838706"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Перемещение учетной записи хранения Azure в другой регион

Чтобы переместить учетную запись хранения, создайте копию учетной записи хранения в другом регионе. Затем переместите данные в эту учетную запись с помощью AzCopy или другого инструмента по своему усмотрению.

Из этой статьи вы узнаете о следующем:

> [!div class="checklist"]
> 
> * Экспорт шаблона.
> * Измените шаблон, добавив целевую область и имя учетной записи хранения.
> * Разверните шаблон, чтобы создать новую учетную запись хранения.
> * Настройте новую учетную запись хранения.
> * Перемещение данных в новую учетную запись хранения.
> * Удалите ресурсы из исходного региона.

## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что службы и функции, используемые вашей учетной записью, поддерживаются в целевом регионе.

- Для функций предварительной версии убедитесь, что ваша подписка список разрешений для целевого региона.

<a id="prepare" />

## <a name="prepare"></a>Подготовка.

Чтобы приступить к работе, экспортируйте и измените шаблон диспетчер ресурсов. 

### <a name="export-a-template"></a>Экспорт шаблона

Этот шаблон содержит параметры, описывающие вашу учетную запись хранения. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Чтобы экспортировать шаблон с помощью портал Azure, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com).

2. Выберите **все ресурсы** , а затем выберите свою учетную запись хранения.

3. Выберите **параметры** > > **Экспорт шаблона**.

4. Выберите **скачать** в колонке **Экспорт шаблона** .

5. Выберите ZIP-файл, скачанный с портала, и распакуйте его в выбранную папку.

   Этот ZIP-файл содержит JSON-файлы, составляющие шаблон и скрипты для развертывания шаблона.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы экспортировать шаблон с помощью PowerShell, выполните следующие действия.

1. Войдите в подписку Azure с помощью команды [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) и следуйте инструкциям на экране.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Если ваше удостоверение связано с более чем одной подпиской, установите активную подписку на подписку учетной записи хранения, которую требуется переместить.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Экспортируйте шаблон исходной учетной записи хранения. Эти команды сохраняют шаблон JSON в текущем каталоге.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>Изменение шаблона 

Измените шаблон, изменив имя и регион учетной записи хранения.

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Чтобы развернуть шаблон с помощью портал Azure, выполните следующие действия.

1. На портале Azure выберите **Создать ресурс**.

2. В строке **Поиск в Marketplace** введите **развертывание шаблона** и нажмите клавишу **ВВОД**.

3. Выберите **Развертывание шаблона**.

    ![Библиотека шаблонов Azure Resource Manager](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Нажмите кнопку **Создать**.

5. Выберите **Создать собственный шаблон в редакторе**.

6. Выберите **загрузить файл**и следуйте инструкциям по загрузке файла **template. JSON** , скачанного в последнем разделе.

7. В файле **template. JSON** назовите целевую учетную запись хранения, указав значение по умолчанию для имени учетной записи хранения. В этом примере в качестве значения по умолчанию для имени учетной записи хранения задается значение `mytargetaccount`.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Чтобы получить коды расположения регионов, см. раздел [расположения Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Код для региона — это имя региона без пробелов, **Центральная американская** = **centralus**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы развернуть шаблон с помощью PowerShell, выполните следующие действия.

1. В файле **template. JSON** назовите целевую учетную запись хранения, указав значение по умолчанию для имени учетной записи хранения. В этом примере в качестве значения по умолчанию для имени учетной записи хранения задается значение `mytargetaccount`.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Измените свойство **Location** в файле **template. JSON** на целевой регион. В этом примере целевому региону присваивается значение `eastus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Коды регионов можно получить, выполнив команду [Get-азлокатион](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) .

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Move (Переместить)

Разверните шаблон, чтобы создать новую учетную запись хранения в целевом регионе. 

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

1. Сохраните файл **template. JSON** .

2. Введите или выберите значения свойств:

- **Подписка**— выберите подписку Azure.

- **Группа ресурсов**: щелкните **Создать** и укажите имя группы ресурсов.

- **Расположение**. Выберите расположение Azure.

3. Установите флажок **я принимаю указанные выше условия** и нажмите кнопку **выбрать покупку** .

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Получите идентификатор подписки, в которой вы хотите развернуть целевой общедоступный IP-адрес с помощью [Get-азсубскриптион](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Используйте следующие команды для развертывания шаблона:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Настройка новой учетной записи хранения

Некоторые компоненты не экспортируются в шаблон, поэтому их необходимо добавить в новую учетную запись хранения. 

В следующей таблице перечислены эти функции и приведены рекомендации по их добавлению в новую учетную запись хранения.

| Функция    | Руководство    |
|--------|-----------|
| **Политики управления жизненным циклом** | [Управление жизненным циклом хранилища BLOB-объектов Azure (предварительная версия)](../blobs/storage-lifecycle-management-concepts.md) |
| **Статические веб-сайты** | [Размещение статического веб-сайта в службе хранилища Azure](../blobs/storage-blob-static-website-how-to.md) |
| **Подписки на события** | [Reacting to Blob storage events (preview)](../blobs/storage-blob-event-overview.md) (Реагирование на события хранилища BLOB-объектов) |
| **Оповещения** | [Создание, просмотр и Управление оповещениями журнала действий с помощью Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Сеть доставки содержимого (CDN)** | [Использование Azure CDN для доступа к BLOB-объектам с пользовательскими доменами по протоколу HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Если вы настроили CDN для исходной учетной записи хранения, просто измените источник существующей сети CDN на конечную точку основной конечной службы BLOB-объектов (или основную конечную точку веб-сайта) новой учетной записи. 

### <a name="move-data-to-the-new-storage-account"></a>Перемещение данных в новую учетную запись хранения

Вот несколько способов перемещения данных.

: heavy_check_mark: **Обозреватель службы хранилища Azure**

  Он удобен в использовании и подходит для небольших наборов данных. Вы можете скопировать контейнеры и общие файловые ресурсы, а затем вставить их в целевую учетную запись.

  См. [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/);

: heavy_check_mark: **AzCopy**

  Это предпочтительный подход. Она оптимизирована для повышения производительности.  В этом случае данные копируются непосредственно между серверами хранилища, поэтому AzCopy не использует пропускную способность сети компьютера. Используйте AzCopy в командной строке или в составе пользовательского скрипта.

  См. статью Начало [работы с AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

: heavy_check_mark: **фабрика данных Azure** 

  Используйте это средство только в том случае, если вам нужны функциональные возможности, которые не поддерживаются в текущем выпуске AzCopy. Например, в текущем выпуске AzCopy нельзя копировать большие двоичные объекты между учетными записями, имеющими иерархическое пространство имен. Кроме того, AzCopy не сохраняет списки управления доступом к файлам или метки времени файлов (например, метки времени создания и изменения). 

  См. следующие ссылки:
  - [Копирование данных в хранилище BLOB-объектов Azure и обратно с помощью фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Копирование данных в Azure Data Lake Storage 2-го поколения с помощью фабрики данных Azure или из нее](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Копирование данных из хранилища файлов Azure или в него с помощью фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Копирование данных в хранилище таблиц Azure и из него с помощью фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Удалить или очистить

Если после развертывания вы хотите начать заново, можно удалить целевую учетную запись хранения и повторить действия, описанные в разделах [Подготовка](#prepare) и [Перемещение](#move) этой статьи.

Чтобы сохранить изменения и завершить перемещение учетной записи хранения, удалите исходную учетную запись хранения.

# <a name="portaltabazure-portal"></a>[Портал](#tab/azure-portal)

Чтобы удалить учетную запись хранения с помощью портал Azure, выполните следующие действия.

1. В портал Azure разверните меню в левой части окна, чтобы открыть меню служб, и выберите **учетные записи хранения** , чтобы отобразить список учетных записей хранения.

2. Выберите целевую учетную запись хранения для удаления и щелкните правой кнопкой мыши кнопку **Дополнительно** ( **...** ) в правой части списка.

3. Выберите **Удалить**и подтвердите.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте команду [Remove-азсторажеаккаунт](/powershell/module/az.resources/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы переместили учетную запись хранения Azure из одного региона в другой и очистили исходные ресурсы.  Дополнительные сведения о перемещении ресурсов между регионами и аварийным восстановлением в Azure см. по следующим ссылке:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
