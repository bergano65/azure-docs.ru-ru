---
title: Переместите учетную запись хранилища Azure в другой регион Документы Майкрософт
description: Показывает, как переместить учетную запись хранения Azure в другой регион.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: c8578c518ac45bea147790028c2904c7ce36fffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459038"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Перемещение учетной записи хранения Azure в другой регион

Чтобы переместить учетную запись хранилища, создайте копию учетной записи хранилища в другом регионе. Затем переместите данные в эту учетную запись с помощью AzCopy или другого инструмента по вашему выбору.

В этой статье вы узнаете, как выполнять следующие задачи.

> [!div class="checklist"]
> 
> * Экспорт шаблона.
> * Измените шаблон, добавив область целевого и имя учетной записи хранилища.
> * Развернуть шаблон для создания новой учетной записи хранилища.
> * Нанастройка новой учетной записи хранилища.
> * Перемещение данных в новую учетную запись хранения.
> * Удалить ресурсы в регионе исходного кода.

## <a name="prerequisites"></a>Предварительные требования

- Убедитесь, что службы и функции, которые использует ваша учетная запись, поддерживаются в целевом регионе.

- Для просмотра функций убедитесь, что ваша подписка включена в белый список для целевого региона.

<a id="prepare" />

## <a name="prepare"></a>Подготовка.

Для начала экспортируете, а затем изменяйте шаблон «Менеджер ресурсов». 

### <a name="export-a-template"></a>Экспорт шаблона

Этот шаблон содержит параметры, описывающие учетную запись хранилища. 

# <a name="portal"></a>[Портал](#tab/azure-portal)

Экспорт шаблона с помощью портала Azure:

1. Войдите на [портал Azure](https://portal.azure.com).

2. Выберите **все ресурсы,** а затем выберите учетную запись хранения.

3. Выберите**шаблон экспорта** **> настройки.** > 

4. Выберите **Скачать** в лезвии **шаблона Экспорт.**

5. Найдите файл .zip, который вы загрузили с портала, и распакуйте этот файл в папку по вашему выбору.

   Этот почтовый файл содержит файлы .json, которые составляют шаблон и скрипты для развертывания шаблона.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Экспорт шаблона с помощью PowerShell:

1. Войдите в подписку Azure с командой [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) и следуйте инструкциям на экране:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Если ваша личность связана с более чем одной подпиской, установите активную подписку на подписку на учетную запись хранения, которую вы хотите переместить.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Экспорт шаблона исходной учетной записи хранения. Эти команды сохраняют шаблон json в текущем каталоге.

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

Измените шаблон, изменив имя и область учетной записи хранилища.

# <a name="portal"></a>[Портал](#tab/azure-portal)

Чтобы развернуть шаблон с помощью портала Azure:

1. На портале Azure выберите **Создайте ресурс.**

2. В строке **Поиск в Marketplace** введите **развертывание шаблона** и нажмите клавишу **ВВОД**.

3. Выберите **развертывание шаблонов.**

    ![Библиотека шаблонов Azure Resource Manager](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Нажмите кнопку **создания**.

5. Выберите **Создать собственный шаблон в редакторе**.

6. Выберите **файл Нагрузки,** а затем следуйте инструкциям для загрузки файла **template.json,** который вы загрузили в последнем разделе.

7. В файле **template.json** назовите учетную запись целевого хранилища, установив значение по умолчанию имени учетной записи хранилища. Этот пример устанавливает значение значения учетной `mytargetaccount`записи по умолчанию.
    
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
    Для получения кодов местоположений регионов [см.](https://azure.microsoft.com/global-infrastructure/locations/)  Код для региона является название региона без каких-либо пространств, **Центральный** = **центральный**США .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы развернуть шаблон с помощью PowerShell:

1. В файле **template.json** назовите учетную запись целевого хранилища, установив значение по умолчанию имени учетной записи хранилища. Этот пример устанавливает значение значения учетной `mytargetaccount`записи по умолчанию.
    
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

2. Изназначай свойство **местоположения** в файле **template.json** в целевой регион. Этот пример устанавливает область `eastus`цели для .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    Коды регионов можно получить, запустив команду [Get-AzLocation.](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Переместить

Развертывание шаблона для создания новой учетной записи хранилища в целевом регионе. 

# <a name="portal"></a>[Портал](#tab/azure-portal)

1. Сохранить файл **template.json.**

2. Введите или выберите значения свойств:

- **Подписка**: Выберите подписку Azure.

- **Группа ресурсов**: щелкните **Создать** и укажите имя группы ресурсов.

- **Местоположение**: Выберите местоположение Azure.

3. Нажмите на **кнопку «Я согласен с условиями, указанными выше,** флажок, а затем нажмите кнопку **Select Purchase».**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Получение идентификатора подписки, где вы хотите развернуть целевой общественный IP с [Get-AzSubscription:](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0)

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Используйте эти команды для развертывания шаблона:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Настройка новой учетной записи хранилища

Некоторые функции не будут экспортироваться в шаблон, так что вам придется добавить их в новую учетную запись хранения. 

В следующей таблице перечислены эти функции вместе с рекомендациями по их добавлению в новую учетную запись хранения.

| Компонент    | Руководство    |
|--------|-----------|
| **Политики управления жизненным циклом** | [Управление жизненным циклом хранилища BLOB-объектов Azure](../blobs/storage-lifecycle-management-concepts.md) |
| **Статические веб-сайты** | [Размещение статического веб-сайта в Хранилище Azure](../blobs/storage-blob-static-website-how-to.md) |
| **Подписки на события** | [Реагирование на события хранилища BLOB-объектов](../blobs/storage-blob-event-overview.md) |
| **Предупреждения** | [Создание, представление и управление оповещениями о журналах активности с помощью Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Сеть доставки содержимого (CDN)** | [Получение доступа к большим двоичным объектам с помощью личных доменов по HTTPS с использованием Azure CDN](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Если вы настроили CDN для учетной записи исходного хранилища, просто измените происхождение существующего CDN на основную конечную точку службы капля (или основную статическую конечную точку веб-сайта) новой учетной записи. 

### <a name="move-data-to-the-new-storage-account"></a>Перемещение данных в новую учетную запись хранения

Вот несколько способов перемещения данных.

:heavy_check_mark: **Исследователь хранения azure**

  Он прост в использовании и подходит для небольших наборов данных. Вы можете копировать контейнеры и файл акций, а затем вставить их в целевой счет.

  Смотрите [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/);

:heavy_check_mark: **AzCopy**

  Это предпочтительный подход. Он оптимизирован для производительности.  Один из способов, что это быстрее, является то, что данные копируются непосредственно между серверами хранения, так что AzCopy не использует пропускную способность сети вашего компьютера. Используйте AzCopy в командной строке или как часть пользовательского скрипта.

  Смотрите [Начало работы с AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: **Фабрика данных Azure** 

  Используйте этот инструмент только в том случае, если вам нужна функциональность, которая не поддерживается в текущем выпуске AzCopy. Например, в текущем выпуске AzCopy нельзя копировать капли между учетными записями, которые имеют иерархическое пространство имен. Кроме того, AzCopy не сохраняет списки управления доступом к файлам или временные метки (например: создание и изменение временных штампов). 

  Смотрите следующие ссылки:
  - [Копирование данных в хранилище Azure Blob или из его хранилища с помощью Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Копирование данных в хранилище Azure Data Lake Storage 2-го поколения и из него с помощью Фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Копирование данных из хранилища файлов Azure и обратно с помощью фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Копирование данных в службу "Хранилище таблиц Azure" и обратно с помощью фабрики данных Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Отбросить или очистить

После развертывания, если требуется начать все сначала, можно удалить учетную запись целевого хранилища и повторить шаги, описанные в разделах [«Подготовка](#prepare) и [перемещение»](#move) в этой статье.

Чтобы зафиксировать изменения и завершить перемещение учетной записи хранилища, удалите учетную запись исходного хранилища.

# <a name="portal"></a>[Портал](#tab/azure-portal)

Чтобы удалить учетную запись хранилища с помощью портала Azure:

1. На портале Azure расширьте меню с левой стороны, чтобы открыть меню служб, и выберите **учетные записи хранилища** для отображения списка учетных записей хранилища.

2. Найдите учетную запись целевого хранилища, чтобы удалить, и право йгните кнопку **«Больше»** **(...)** на правой стороне объявления.

3. Выберите **Удалить**, и подтвердить.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Для удаления группы ресурсов и связанных с ней ресурсов, включая новую учетную запись хранения, используйте команду [Удалить-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Следующие шаги

В этом учебнике вы перенесли учетную запись хранения Azure из одного региона в другой и очистили исходные ресурсы.  Чтобы узнать больше о перемещении ресурсов между регионами и аварийном восстановлении в Azure, обратитесь к:


- [Перемещение ресурсов в новую группу ресурсов или подписку](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Перенос виртуальных машин Azure в другой регион](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
