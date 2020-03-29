---
title: Как удалить и экспортировать персональные данные из Лаборатории Azure DevTest
description: Сведения об удалении и экспорте персональных данных из службы Azure DevLast Labs для выполнения всех обязанностей, установленных Общим регламентом по защите данных (GDPR).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: c87e2fb534480bbf9bbe625d67782e5a11eda18c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169699"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Экспорт или удаление персональных данных из Azure DevTest Labs
В этой статье описываются действия по удалению и экспорту персональных данных из службы Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Какие личные данные собирает DevTest Labs?
DevTest Labs собирает два основных фрагмента персональных данных пользователя. К ним относится: адрес электронной почты пользователя и идентификатор объекта пользователя. Эта информация является критически важной для предоставления возможностей службы администраторам и пользователям лаборатории.

### <a name="user-email-address"></a>Адрес электронной почты пользователя
DevTest Labs использует адрес электронной почты пользователя для отправки пользователям лаборатории уведомлений по электронной почте об автоматическом завершении работы. На электронную почту пользователей приходит уведомление о завершении работы их компьютера. Пользователи могут при желании отложить или пропустить завершение работы. Адрес электронной почты настраивается на уровне лаборатории или на уровне виртуальной машины.

**Задание электронной почты в лаборатории:**

![Задание электронной почты на уровне лаборатории](./media/personal-data-delete-export/lab-user-email.png)

**Задание электронной почты на виртуальной машине:**

![Задание электронной почты на уровне виртуальной машины](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Идентификатор объекта пользователя
С помощью идентификатора объекта пользователя DevTest Labs отображает тенденции затрат по месяцам и затрат по ресурсам для администраторов лаборатории. Это позволяет отслеживать затраты и управлять пороговыми значениями для их лаборатории. 

**Тенденция сметной стоимости для текущего календарного месяца:**
![Тенденция сметной стоимости для текущего календарного месяца](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Ориентировочная стоимость за месяц по ресурсам:**
![ориентировочные месячные затраты по ресурсам](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Зачем нужны персональные данные?
Служба DevTest Labs использует персональные данные в оперативных целях. Эти данные являются критически важными для того, чтобы служба предоставляла основные возможности. Если для адреса электронной почты пользователя задать политику хранения, пользователи лаборатории не будут своевременно получать уведомления об автоматическом завершении работы по электронной почте после того, как их адрес электронной почты будет удален из нашей системы. Аналогичным образом администратор лаборатории не может просматривать тенденции затрат по месяцам и затрат по ресурсам для компьютеров в их лабораториях, если идентификаторы объектов пользователей удалены в соответствии с политикой хранения. Таким образом, эти данные необходимо хранить до тех пор, пока ресурс пользователя активен в лаборатории.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Что необходимо сделать, чтобы система не сохраняла мои персональные данные?
Если вы, как пользователь лаборатории, хотите удалить эти персональные данные, это можно сделать, удалив соответствующий ресурс в лаборатории. Служба DevTest Labs анонимизирует удаленные персональные данные через 30 дней после удаления пользователем.

Например, если вы удалите виртуальную машину или адрес своей электронной почты, служба DevTest Labs анонимизирует эти данные через 30 дней после удаления ресурса. 30-дневная политика хранения после удаления необходима для предоставления администратору лаборатории точных данных о прогнозируемой стоимости по месяцам.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Как запросить экспорт персональных данных?
Вы можете экспортировать личные данные и данные об использовании лаборатории с помощью портала Azure или PowerShell. Данные экспортируются в виде двух различных файлов CSV:

- **disks.csv** - содержит информацию о дисках, используемых различными VMs
- **virtualmachines.csv** - содержит информацию о виртуальных машинах в лаборатории.

### <a name="azure-portal"></a>Портал Azure
Как пользователь лаборатории, вы можете запросить экспорт персональных данных, которые хранятся в службе DevTest Labs. Чтобы запросить экспорт, перейдите к параметру **Персональные данные** на странице **Обзор** лаборатории. Нажмите кнопку **Запрос экспорта**, в результате чего в учетной записи хранения администратора лаборатории будет создан скачиваемый файл Excel. Затем обратитесь к администратору лаборатории, чтобы просмотреть эти данные.

1. Выберите **Персональные данные** в меню слева. 

    ![Страница "Персональные данные"](./media/personal-data-delete-export/personal-data-page.png)
2. Выберите **группу ресурсов**, в которой содержится лаборатория.

    ![Выбор группы ресурсов](./media/personal-data-delete-export/select-resource-group.png)
3. В группе ресурсов выберите **учетную запись хранения**.
4. На странице **Учетная запись хранения** выберите **Большие двоичные объекты**.

    ![Выбор плитки "Большие двоичные объекты"](./media/personal-data-delete-export/select-blobs-tile.png)
5. Выберите контейнер с именем **labresourceusage** в списке контейнеров.

    ![Выбор контейнера больших двоичных объектов](./media/personal-data-delete-export/select-blob-container.png)
6. Выберите **папку** с именем лаборатории. В этой папке вы найдете файлы **CSV** для **дисков** и **виртуальных машин** в лаборатории. Вы можете скачать эти CSV-файлы, отфильтровать содержимое для пользователя лаборатории, запрашивающего доступ, и предоставить его ему.

    ![Скачивание CSV-файла](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

Ключевыми компонентами в вышеуказанном образце являются:

- Команда Invoke-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Два параметра действия
    - **blobStorageAbsoluteSasuri** - учетная запись хранения URI с токеном общей подписи доступа (SAS). В скрипте PowerShell это значение может быть передано вместо ключа хранения.
    - **useStartDate** - Дата начала для вытягивания данных, с датой окончания текущей даты, на которой выполняется действие. Детализация находится на дневном уровне, так что даже если вы добавите информацию о времени, она будет проигнорирована.

### <a name="exported-data---a-closer-look"></a>Экспортированные данные - пристальный взгляд
Теперь давайте подробнее рассмотрим экспортированные данные. Как упоминалось ранее, как только данные будут успешно экспортированы, будет два файла CSV. 

**Virtualmachines.csv** содержит следующие столбцы данных:

| Имя столбца | Описание |
| ----------- | ----------- | 
| SubscriptionId | Идентификатор подписки, в существовании которого существует лаборатория. |
| Лабид | Уникальный идентификатор GUID для лаборатории. |
| LabName | Имя лаборатории. |
| LabResourceId | Полностью квалифицированный идентификатор ресурсов лаборатории. |
| ResourceGroupName | Название группы ресурсов, содержащей VM | 
| ResourceId | Полностью квалифицированный идентификатор ресурсов для VM. |
| РесурсУИД | GUID для VM |
| name | Имя виртуальной машины. |
| CreatedTime | Дата-время, в которое был создан VM. |
| DeletedDate | Дата-время, в которое VM был удален. Если он пуст, удаление еще не произошло. |
| Владелец ресурсов | Владелец ВМ. Если значение пусто, то это либо претензионный VM или созданный директором службы. |
| Ценообразование | Ценовой уровень VM |
| РесурсСтатус | Состояние доступности ВМ. Активный, если все еще существует или неактивен, если VM был удален. |
| ComputeResourceId | Полностью квалифицированный идентификатор ресурсов виртуального машинного вычисления. |
| Претензионные | Установить на истину, если VM является претензионным VM | 
| Окружающая средаId | Идентификатор ресурсов среды, в котором была создана виртуальная машина. Он пуст, когда VM не был создан как часть ресурса среды. |
| ExpirationDate | Срок годности для ВМ. Он установлен на пустой, если срок годности не был установлен.
| GalleryImageReferenceVersion |  Версия базового изображения VM. |
| GalleryImageСправкаПредложение | Предложение базового изображения VM. |
| GalleryImageReferencePublisher | Издатель базового изображения VM. |
| GalleryImageReferenceSku | Sku базового изображения VM |
| GalleryImageReferenceOsType | Тип базы ОС изображения базы VM |
| CustomImageId | Полностью квалифицированный идентификатор пользовательского изображения базы VM. |

Ниже перечислены столбцы данных, содержащиеся в **дисках.csv:**

| Имя столбца | Описание | 
| ----------- | ----------- | 
| SubscriptionId | Идентификатор подписки, содержащей лабораторию |
| Лабид | GUID для лаборатории |
| LabName | Название лаборатории | 
| LabResourceId | Полностью квалифицированный идентификатор ресурсов для лаборатории | 
| ResourceGroupName | Название группы ресурсов, содержащей лабораторию | 
| ResourceId | Полностью квалифицированный идентификатор ресурсов для VM. |
| РесурсУИД | GUID для VM |
 |name | Название прилагаемого диска |
| CreatedTime |Дата и время создания диска данных. |
| DeletedDate | Дата и время, в течение которого диск данных был удален. |
| РесурсСтатус | Состояние ресурса. Активный, если ресурс существует. Неактивный при удалении. |
| DiskBlobName | Blob имя диска данных. |
| DiskSizeGB | Размер диска данных. |
| DiskType | Тип диска данных. 0 для стандарта, 1 для Премиум. |
| ЛизингByVmId | Идентификатор ресурса VM, к которому был прикреплен диск данных. |


> [!NOTE]
> Если вы имеете дело с несколькими лабораториями и хотите получить общую информацию, два ключевых столбца **являются LabUID** и **ResourceUId**, которые являются уникальными идентификаторами по подписке.

Экспортируемые данные можно манипулировать и визуализировать с помощью таких инструментов, как S'L Server, Power BI и т.д. Эта функция особенно полезна, если вы хотите сообщить об использовании лаборатории вашей управленческой команде, которая может использовать не ту же подписку Azure, что и вы.

## <a name="next-steps"></a>Дальнейшие действия
См. следующие статьи: 

- [Управление базовыми политиками лаборатории в Azure DevTest Labs](devtest-lab-get-started-with-lab-policies.md)
- [Часто задаваемые вопросы](devtest-lab-faq.md)
