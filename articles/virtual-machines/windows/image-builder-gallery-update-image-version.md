---
title: Создайте новую версию изображения из существующей версии изображений с помощью Azure Image Builder (предварительный просмотр)
description: Создайте новую версию изображения VM из существующей версии изображения с помощью Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.openlocfilehash: 766e7d5c4151000a582bcf07d80b89af3b7d8a65
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869539"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Предварительный просмотр: Создайте новую версию изображения VM из существующей версии изображений с помощью Azure Image Builder

В этой статье показано, как взять существующую версию изображения в [общей галерее изображений,](shared-image-galleries.md)обновить ее и опубликовать в качестве новой версии изображения в галерее.

Мы будем использовать образец шаблона .json для настройки изображения. Файл .json, который мы используем, здесь: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
az provider show -n Microsoft.Compute | grep registrationState
```

Если они не говорят зарегистрированы, запустить следующее:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Установка переменных и разрешений

Если вы использовали Создание изображения и распространение в [общей галерее изображений](image-builder-gallery.md) для создания общей галереи изображений, вы уже создали необходимые переменные. Если нет, пожалуйста, установите некоторые переменные, которые будут использоваться для этого примера.

Для Preview строитель изображений будет поддерживать только создание пользовательских изображений в той же группе ресурсов, что и управляемое исходным изображением. Обновление имени группы ресурсов в этом примере, чтобы быть той же группой ресурсов, что и управляемое исходным изображением.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Создайте переменную для идентификатора подписки. Вы можете получить `az account show | grep id`это с помощью .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Получите версию изображения, которую вы хотите обновить.

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Если у вас уже есть общая галерея изображений, и вы не последовали предыдущему примеру, вам нужно будет назначить разрешения для Image Builder для доступа к группе ресурсов, чтобы она могла получить доступ к галерее.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Изменить пример helloImage
Вы можете просмотреть пример, который мы собираемся использовать, открыв файл .json здесь: [helloImageTemplateforSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) вместе со [ссылкой на шаблон Image Builder.](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 


Загрузите пример .json и навядите его с переменными. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Создание образа

Отправить конфигурацию изображения в службу vM Image Builder Service.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Начало сборки изображения.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Подождите, пока изображение будет построено и реплицировано, прежде чем перейти к следующему шагу.


## <a name="create-the-vm"></a>Создание виртуальной машины

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Проверка настройки
Создайте соединение удаленного рабочего стола с VM, используя имя пользователя и пароль, который вы установили при создании VM. Внутри VM, откройте cmd подсказку и введите:

```console
dir c:\
```

Теперь вы должны увидеть два каталога:
- `buildActions`который был создан в первой версии изображения.
- `buildActions2`который был создан как часть обновления первой версии изображения для создания второй версии изображения.


## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о компонентах файла .json, используемого в этой статье, [см.](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)