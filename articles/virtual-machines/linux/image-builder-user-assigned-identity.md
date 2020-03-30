---
title: Создайте изображение виртуальной машины и используйте управляемое удостоверение, назначенное пользователем, для доступа к файлам в Хранилище Azure (предварительный просмотр)
description: Создавайте виртуальное изображение машины с помощью Azure Image Builder, которое может получать доступ к файлам, хранящимся в Хранилище Azure, используя управляемую изнача для пользователей.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 27f4073efc8647d331faa14afbda0e15f92b8d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060752"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Создание изображения и использование управляемого идентификатора, назначенного пользователем, для доступа к файлам в хранилище Azure 

Azure Image Builder поддерживает использование скриптов или копирование файлов из нескольких мест, таких как Хранение GitHub и Azure и т.д. Чтобы использовать их, они должны быть внешне доступны для системы изображения Azure, но можно защитить капли хранения Azure с помощью токенов SAS.

В этой статье показано, как создать настраиваемый образ с помощью Azure VM Image Builder, где служба будет использовать [управляемую идентификацию пользователя](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) для доступа к файлам в хранилище Azure для настройки изображения, без необходимости делать файлы общедоступными или настраивать токены SAS.

В приведенном ниже примере вы создадите две группы ресурсов, одна из которой будет использоваться для пользовательского изображения, а другая будет размещать учетную запись хранения Azure, содержащую файл скрипта. Это имитирует реальный сценарий, где могут быть артефакты или файлы изображений в различных учетных записях хранения, за пределами Image Builder. Вы создадите удостоверение, назначенное пользователем, а затем предоставите разрешения на чтение файла скрипта, но не установите общедоступный доступ к этому файлу. Затем вы будете использовать настройку Shell для загрузки и запуска этого скрипта из учетной записи хранилища.


> [!IMPORTANT]
> В настоящее время Azure Image Builder находится в открытом доступе.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Регистрация функций
Для использования Azure Image Builder во время предварительного просмотра необходимо зарегистрировать новую функцию.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Проверьте состояние регистрации функций.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Проверьте свою регистрацию.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Если они не говорят зарегистрированы, запустить следующее:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Создание группы ресурсов

Мы будем использовать некоторые части информации неоднократно, поэтому мы создадим некоторые переменные для хранения этой информации.


```console
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

Создайте переменную для идентификатора подписки. Вы можете получить `az account show | grep id`это с помощью .

```console
subscriptionID=<Your subscription ID>
```

Создавайте группы ресурсов как для изображения, так и для хранения скриптов.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Создайте хранилище и скопируйте образец сценария в него с GitHub.

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



Дайте Image Builder разрешение на создание ресурсов в группе ресурсов изображений. Значение `--assignee` — идентификатор регистрации приложения для службы Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначенного пользователем

Создайте идентификацию и присвоите разрешения для учетной записи хранения скриптов. Для получения дополнительной информации смотрите [Управляемый итог пользователя.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)

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


## <a name="modify-the-example"></a>Изменение примера

Загрузите файл .json и навядите его с созданными переменными.

```console
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

Отправьте конфигурацию изображения в службу Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Начало сборки изображения.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Дождитесь завершения сборки. Это может занять около 15 минут.

## <a name="create-a-vm"></a>Создание виртуальной машины

Создайте VM из изображения. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

После создания VM начните сеанс SSH с VM.

```console
ssh aibuser@<publicIp>
```

Вы должны увидеть, что изображение было настроено с Посланием Дня, как только ваше соединение SSH установлено!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Очистка

Когда вы закончите, вы можете удалить ресурсы, если они больше не нужны.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Если у вас возникли проблемы с работой с Azure Image Builder, [см.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)