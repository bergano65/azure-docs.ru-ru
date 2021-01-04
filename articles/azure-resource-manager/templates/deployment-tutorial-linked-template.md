---
title: Учебник. Развертывание связанного шаблона
description: Узнайте как выполнять развертывание связанного шаблона
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 81a8b6cd5044b31ee80f50448d6610ba6a57281e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585655"
---
# <a name="tutorial-deploy-a-linked-template"></a>Руководство по развертыванию связанного шаблона

В [предыдущих учебниках](./deployment-tutorial-local-template.md) вы узнали, как развернуть шаблон, хранящийся на локальном компьютере. Для развертывания сложных решений вы можете разбить один шаблон на несколько и развернуть их с помощью основного шаблона. В этом учебнике вы узнаете, как развернуть основной шаблон, содержащий ссылку на связанный шаблон. При развертывании главного шаблона запускается развертывание связанного шаблона. Вы также узнаете, как сохранить и защитить связанный шаблон с помощью маркера SAS. Это занимает около **12 минут**.

## <a name="prerequisites"></a>Предварительные требования

Советуем выполнить инструкции из предыдущего учебника, но это необязательно.

## <a name="review-template"></a>Проверка шаблона

В предыдущих учебниках вы развернули шаблон, который создает учетную запись хранения, план Службы приложений и веб-приложение. Использовался шаблон:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Создание связанного шаблона

Ресурс учетной записи хранения можно разделить на связанный шаблон:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Следующий шаблон является основным. Выделенный объект `Microsoft.Resources/deployments` показывает, как вызвать связанный шаблон. Связанный шаблон нельзя сохранять в виде локального файла или файла, который доступен только в вашей локальной сети. Можно предоставить только URI, который включает в себя HTTP или HTTPS. Диспетчер ресурсов должен иметь доступ к шаблону. Один из вариантов — разместить связанный шаблон в учетной записи хранения и использовать универсальный код ресурса (URI) этого элемента. Универсальный код ресурса (URI) передается в шаблон с помощью параметра. См. определение выделенного параметра.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Сохраните копию основного шаблона с расширением _JSON_, например _azuredeploy.json_, на локальном компьютере. Не нужно сохранять копию связанного шаблона. Связанный шаблон будет скопирован в учетную запись хранения из репозитория GitHub.

## <a name="store-the-linked-template"></a>Сохранение связанного шаблона

Следующий сценарий PowerShell создает учетную запись хранения, создает контейнер и копирует связанный шаблон из репозитория GitHub в контейнер. Копия связанного шаблона хранится в [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Выберите **Попробовать** чтобы открыть Cloud Shell, выберите **Копировать**, чтобы скопировать скрипт PowerShell, и щелкните правой кнопкой мыши панель оболочки, чтобы вставить скрипт:

> [!IMPORTANT]
> Имя учетной записи хранения должно содержать от 3 до 24 символов и состоять только из цифр и букв нижнего регистра. Имя должно быть уникальным. В шаблоне имя учетной записи хранения — это имя проекта с добавлением **store**, которое должно содержать от 3 до 11 символов. Поэтому имя проекта должно соответствовать требованиям к имени учетной записи хранения и быть длиной менее 11 символов.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Развертывание шаблона

Чтобы развернуть частный шаблон в учетной записи хранения, создайте маркер SAS и добавьте его в универсальный код ресурса (URI) для шаблона. Задайте срок действия, достаточный для выполнения развертывания. Большой двоичный объект, содержащий шаблон, будет доступен только владельцу учетной записи. Тем не менее, если создать маркер SAS для этого большого двоичного объекта, то он будет доступен любому пользователю, обладающему этим универсальным кодом ресурса (URI). Если другой пользователь перехватит этот универсальный код ресурса (URI), то сможет получить доступ к шаблону. Маркер SAS — хороший способ ограничить доступ к своим шаблонам, но не следует указывать конфиденциальные данные, например пароли, непосредственно в шаблоне.

Если вы еще не создали группу ресурсов, см. [этот раздел](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> В приведенном ниже коде Azure CLI параметр `date` (`-d`) будет недопустимым аргументом в macOS. Поэтому чтобы добавить два часа к текущему времени в терминале macOS, пользователям macOS следует использовать `-v+2H`.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>Очистка ресурсов

Очистите развернутые ресурсы, удалив группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Фильтровать по имени** введите имя группы ресурсов.
3. Выберите имя группы ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

## <a name="next-steps"></a>Дальнейшие действия

Вы узнали как выполнять развертывание связанного шаблона. Из следующего руководства вы узнаете, как создать конвейер DevOps для развертывания шаблона.

> [!div class="nextstepaction"]
> [Создать конвейер](./deployment-tutorial-pipeline.md)
