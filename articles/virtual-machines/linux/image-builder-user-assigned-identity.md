---
title: Создать образ виртуальной машины и использовать назначаемое пользователем управляемое удостоверение для доступа к файлам в службе хранилища Azure (Предварительная версия)
description: Создание образа виртуальной машины, с помощью Azure Image Builder, который можно получить доступ к файлам, хранящимся в хранилище Azure с помощью назначаемого пользователем управляемого удостоверения.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: b6347765f8d2e21c352834dc8d28b65c28f99758
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671449"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Создание образа и использовать назначаемое пользователем управляемое удостоверение для доступа к файлам в службе хранилища Azure 

Azure поддерживает Image Builder с помощью скриптов или скопировать файлы из нескольких расположений, таких как GitHub и служба хранилища Azure и т.д. Чтобы использовать их, необходимо их доступном для Azure Image Builder, но может защитить большие двоичные объекты службы хранилища Azure с помощью маркеров SAS.

В этой статье показано, как создать настраиваемый образ с помощью построителя образ виртуальной Машины Azure, где будет использоваться службой [назначаемого пользователем управляемого удостоверения](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) для доступа к файлам в службе хранилища Azure для настройки образа, без необходимости вносить файлы общедоступной или Настройка маркеров SAS.

В приведенном ниже примере вы создадите две группы ресурсов, один будет использоваться для пользовательского образа и другой будет размещаться учетную запись хранения Azure, который содержит файл скрипта. Эта процедура имитирует реальном сценарии, в которых используется артефакты сборки или файлов изображений в разных учетных записях хранения, за пределами Image Builder. Вы создадите назначаемого пользователем удостоверения, то grant, разрешения на запись файла скрипта, но не будет задать любой общий доступ к этому файлу. Затем используем настройщика оболочки для загрузки и запуска этого скрипта из учетной записи хранения.


> [!IMPORTANT]
> Azure Image Builder в настоящее время находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Регистрация функций
Чтобы использовать Azure Image Builder на этапе предварительной версии, необходимо зарегистрировать новый компонент.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Проверьте состояние регистрации компонента.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Проверки регистрации.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Если не говорят зарегистрированных, используйте следующую команду:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Создание группы ресурсов

Мы будем использовать некоторые части информации, поэтому мы создадим некоторые переменные для хранения этих сведений.


```azurecli-interactive
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Создайте переменную для идентификатора вашей подписки. Можно получить при помощи этого `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Создание группы ресурсов для образа и хранилище скриптов.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Создайте хранилище и скопируйте сценарий в него из GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Предоставьте разрешение Image Builder для создания ресурсов в группе ресурсов изображение. `--assignee` Значение — идентификатор регистрации приложения для службы Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Создание назначаемого пользователем управляемого удостоверения

Создайте удостоверение и назначьте разрешения для учетной записи хранения сценария. Дополнительные сведения см. в разделе [назначаемого пользователем управляемого удостоверения](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Измените пример

Загрузить пример JSON-файле и настройте его с помощью переменных, которые вы создали.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Создание образа

Отправьте конфигурацию образа в службе Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Начать сборку образа.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Дождитесь завершения сборки. Это может занять около 15 минут.

## <a name="create-a-vm"></a>Создание виртуальной машины

Создайте виртуальную Машину из образа. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

После создания виртуальной Машины, запустите сеанс SSH с виртуальной Машиной.

```azurecli-interactive
ssh aibuser@<publicIp>
```

Вы увидите, что образ был настроена с сообщением, дня, как только SSH-подключение устанавливается!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Очистка

Когда вы закончите, можно удалить ресурсы, если они больше не требуются.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Следующие шаги

При наличии каких-либо осложнений, работа с построителем образ Azure, см. в разделе [Устранение неполадок](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).