---
title: Создание образа виртуальной машины и использование управляемого удостоверения, назначенного пользователем, для доступа к файлам в службе хранилища Azure (Предварительная версия)
description: Создание образа виртуальной машины с помощью Azure Image Builder, который может получать доступ к файлам, хранящимся в службе хранилища Azure, с помощью управляемого удостоверения, назначенного пользователем.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: f3990037d75f9f77eaedc7ec4049f14814216d9c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944965"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Создание образа и использование управляемого удостоверения, назначенного пользователем, для доступа к файлам в службе хранилища Azure 

Построитель образов Azure поддерживает использование сценариев или копирование файлов из нескольких расположений, таких как GitHub и хранилище Azure, и т. д. Чтобы использовать их, они должны быть доступны для построителя образов Azure извне, но вы можете защитить большие двоичные объекты службы хранилища Azure с помощью маркеров SAS.

В этой статье показано, как создать настраиваемый образ с помощью построителя образов виртуальных машин Azure, где служба будет использовать [назначенное пользователем управляемое удостоверение](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) для доступа к файлам в службе хранилища Azure для настройки образа без необходимости делать эти файлы общедоступными или настроить маркеры SAS.

В приведенном ниже примере вы создадите две группы ресурсов, одна из которых будет использоваться для пользовательского образа, а другая будет размещать учетную запись хранения Azure, содержащую файл скрипта. Это имитирует реальный сценарий, в котором могут находиться артефакты сборки или файлы изображений в разных учетных записях хранения вне построителя образов. Вы создадите пользовательское удостоверение, затем предоставите ему разрешения на чтение файла скрипта, но не будете устанавливать общий доступ к этому файлу. Затем с помощью программы настройки оболочки вы сможете скачать и запустить этот скрипт из учетной записи хранения.


> [!IMPORTANT]
> Azure Image Builder сейчас находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Регистрация компонентов
Чтобы использовать Azure Image Builder во время предварительной версии, необходимо зарегистрировать новую функцию.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Проверьте состояние регистрации компонента.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Проверьте регистрацию.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Если они не зарегистрированы, выполните следующую команду:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Создание группы ресурсов

Мы будем использовать несколько фрагментов информации повторно, поэтому мы создадим некоторые переменные для хранения этих данных.


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

Создайте переменную для идентификатора подписки. Его можно получить с помощью `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Создайте группы ресурсов для образа и хранилища скриптов.

```azurecli-interactive
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Создайте хранилище и скопируйте в него пример скрипта из GitHub.

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



Предоставьте разрешение Image Builder для создания ресурсов в группе ресурсов образа. Значение `--assignee` — это идентификатор регистрации приложения для службы "Построитель образов". 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначенного пользователем

Создайте удостоверение и назначьте разрешения для учетной записи хранения скрипта. Дополнительные сведения см. в разделе [назначенное пользователем управляемое удостоверение](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

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

Скачайте файл example. JSON и настройте его с помощью созданных вами переменных.

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

Отправьте конфигурацию образа в службу Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Запустите сборку образа.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Дождитесь завершения сборки. Это может занять около 15 минут.

## <a name="create-a-vm"></a>Создание виртуальной машины

Создайте виртуальную машину из образа. 

```bash
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

После создания виртуальной машины запустите сеанс SSH с виртуальной машиной.

```azurecli-interactive
ssh aibuser@<publicIp>
```

Вы должны увидеть, что образ был настроен с сообщением дня, как только подключение SSH установлено.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Очистка

По завершении вы можете удалить ресурсы, если они больше не нужны.

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

Если у вас возникли проблемы с построителем образов Azure, см. раздел [Устранение неполадок](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).