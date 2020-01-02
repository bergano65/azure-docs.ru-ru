---
title: Использование Azure Image Builder с коллекцией образов для виртуальных машин Linux (Предварительная версия)
description: Создание образов виртуальных машин Linux с помощью Azure Image Builder и коллекции общих образов.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 09dceb84a20ef49b3e9d5264b94bb5e74180cd2b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976134"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Предварительная версия: создание образа Linux и его распространение в общую коллекцию образов 

В этой статье показано, как с помощью построителя образов Azure создать версию образа в [общей коллекции образов](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries), а затем распространить образ глобально.


Мы будем использовать шаблон Sample. JSON для настройки образа. JSON-файл, который мы используем: [хеллоимажетемплатефорсиг. JSON](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Чтобы распространить образ в общую коллекцию образов, шаблон использует [шаредимаже](image-builder-json.md#distribute-sharedimage) в качестве значения для раздела `distribute` шаблона.

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

## <a name="set-variables-and-permissions"></a>Задание переменных и разрешений 

Мы будем использовать несколько фрагментов информации повторно, поэтому мы создадим некоторые переменные для хранения этих данных.

Для предварительной версии построитель изображений поддерживает создание пользовательских образов в той же группе ресурсов, что и исходный управляемый образ. Обновите имя группы ресурсов в этом примере, чтобы оно совпадало с группой ресурсов исходного управляемого образа.

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

Создайте переменную для идентификатора подписки. Его можно получить с помощью `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Создайте группу ресурсов.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Предоставьте разрешение Azure Image Builder для создания ресурсов в этой группе ресурсов. Значение `--assignee` — это идентификатор регистрации приложения для службы "Построитель образов". 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Создание определения образа и коллекции

Чтобы использовать построитель изображений с общей коллекцией изображений, необходимо иметь существующую коллекцию образов и определение образа. Построитель образов не будет создавать коллекцию изображений и определение изображения.

Если у вас еще нет определения коллекции и образа, начните с их создания. Сначала создайте коллекцию образов.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Затем создайте определение образа.

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


## <a name="download-and-configure-the-json"></a>Скачивание и настройка JSON

Скачайте шаблон JSON и настройте его с помощью переменных.

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

## <a name="create-the-image-version"></a>Создание версии образа

В следующей части будет создана версия образа в коллекции. 

Отправьте конфигурацию образа в службу Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Запустите сборку образа.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Создание образа и его репликация в оба региона могут занять некоторое время. Дождитесь завершения этой части, прежде чем переходить к созданию виртуальной машины.


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную машину на основе версии образа, созданной с помощью Azure Image Builder.

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

Вы должны увидеть, что образ был настроен с *сообщением дня* , как только подключение SSH установлено.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите выполнить повторную настройку версии образа, чтобы создать новую версию того же образа, пропустите дальнейшие действия и перейдите к разделу [Использование Azure Image Builder для создания другой версии образа](image-builder-gallery-update-image-version.md).


Это приведет к удалению созданного образа вместе со всеми остальными файлами ресурсов. Убедитесь, что вы завершили работу с этим развертыванием, прежде чем удалять ресурсы.

При удалении ресурсов коллекции образов необходимо удалить все версии образа, прежде чем можно будет удалить определение образа, использованное для их создания. Чтобы удалить галерею, сначала необходимо удалить все определения образов в коллекции.

Удалите шаблон построителя образов.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Получить версию образа, созданную построителем образов, это всегда начинается с `0.`, а затем удаляется версия образа.

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


Удаление определения образа.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Удалите коллекцию.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Удалите ее.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [галереях общих образов Azure](shared-image-galleries.md).