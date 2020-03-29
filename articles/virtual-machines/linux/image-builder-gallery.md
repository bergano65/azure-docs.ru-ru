---
title: Используйте Azure Image Builder с галереей изображений для Linux VMs (предварительный просмотр)
description: Создавайте изображения Linux VM с помощью Azure Image Builder и общей галереи изображений.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945022"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Предварительный просмотр: Создайте изображение Linux и распределите его в общей галерее изображений 

В этой статье показано, как можно использовать a Azure Image Builder и ClI Azure для создания версии изображений в [общей галерее изображений,](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)а затем распространять изображение по всему миру. Вы также можете сделать это с помощью [Azure PowerShell.](../windows/image-builder-gallery.md)


Мы будем использовать образец шаблона .json для настройки изображения. Файл .json, который мы используем, здесь: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Для распространения изображения в общей галерее изображений шаблон использует `distribute` [sharedImage](image-builder-json.md#distribute-sharedimage) в качестве значения для раздела шаблона.

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

## <a name="set-variables-and-permissions"></a>Установка переменных и разрешений 

Мы будем использовать некоторые части информации неоднократно, поэтому мы создадим некоторые переменные для хранения этой информации.

Для Preview строитель изображений будет поддерживать только создание пользовательских изображений в той же группе ресурсов, что и управляемое исходным изображением. Обновление имени группы ресурсов в этом примере, чтобы быть той же группой ресурсов, что и управляемое исходным изображением.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Создайте переменную для идентификатора подписки. Вы можете получить `az account show | grep id`это с помощью .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Создайте группу ресурсов.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Дайте Ресурсу Для создания ресурсов в этой группе ресурсов, выдать Ресурсы. Значение `--assignee` — идентификатор регистрации приложения для службы Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Создание определения изображения и галереи

Чтобы использовать Image Builder с общей галереей изображений, необходимо иметь существующую галерею изображений и определение изображения. Image Builder не будет создавать галерею изображений и определение изображения для вас.

Если у вас еще нет галереи и определения изображения, начните с их создания. Во-первых, создать галерею изображений.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Затем создайте определение изображения.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Скачать и настроить .json

Загрузите шаблон .json и наверсните его с помощью переменных.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Создание версии изображения

Следующая часть создаст версию изображения в галерее. 

Отправьте конфигурацию изображения в службу Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Начало сборки изображения.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Создание изображения и его воспроизведение в обоих регионах может занять некоторое время. Подождите, пока эта часть будет закончена, прежде чем перейти к созданию VM.


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте VM из версии изображений, созданной Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Подключитесь к виртуальной машине по протоколу SSH.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Вы должны увидеть, что изображение было настроено с *Посланием Дня,* как только ваше соединение SSH установлено!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите сейчас попробовать перенастроить версию изображения для создания новой версии того же изображения, пропустите следующие шаги и перейдите к [использованию Azure Image Builder для создания другой версии изображения.](image-builder-gallery-update-image-version.md)


Это позволит удалить созданное изображение, а также все другие файлы ресурсов. Убедитесь, что вы закончили с этим развертыванием, прежде чем удалять ресурсы.

При удалении ресурсов галереи изображений необходимо удалить все версии изображений, прежде чем можно удалить определение изображения, используемое для их создания. Чтобы удалить галерею, сначала необходимо удалить все определения изображения в галерее.

Удалите шаблон шаблона шаблона шаблона шаблона шаблона шаблона шаблона шаблона

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Получить изображение версии, созданной изображением `0.`builder, это всегда начинается с , а затем удалить версию изображения

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Удалите определение изображения.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Удалите галерею.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Удалите ее.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о [галереях общих изображений Azure](shared-image-galleries.md).