---
title: Использование Azure Image Builder с коллекцией образов для виртуальных машин Windows (Предварительная версия)
description: Создание образов Windows с помощью Azure Image Builder и коллекции общих образов.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 33f13c09a06885523298bd7c23744e79f68e5301
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698668"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Предварительный просмотр: Создание образа Windows и его распространение в общую коллекцию образов 

В этой статье показано, как с помощью построителя образов Azure создать версию образа в [общей коллекции образов](shared-image-galleries.md), а затем распространить образ глобально.

Для настройки образа мы будем использовать JSON-шаблон. JSON-файл, который мы используем: [хеллоимажетемплатефорвинсиг. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

Чтобы распространить образ в общую коллекцию образов, шаблон использует [шаредимаже](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) в качестве значения для `distribute` раздела шаблона.

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
az provider show -n Microsoft.Compute | grep registrationState
```

Если они не зарегистрированы, выполните следующую команду:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Задание переменных и разрешений 

Мы будем использовать несколько фрагментов информации повторно, поэтому мы создадим некоторые переменные для хранения этих данных. Замените значения переменных, например `username` и `vmpassword`, собственными сведениями.

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
username="azureuser"
vmpassword="passwordfortheVM"
```

Создайте переменную для идентификатора подписки. Это можно сделать с помощью `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
```

Создайте группу ресурсов.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Предоставьте разрешение Azure Image Builder для создания ресурсов в этой группе ресурсов. `--assignee` Значение представляет собой идентификатор регистрации приложения для службы "Построитель образов". 

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
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>Скачивание и настройка JSON

Скачайте шаблон JSON и настройте его с помощью переменных.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>Создание версии образа

В следующей части будет создана версия образа в коллекции. 

Отправьте конфигурацию образа в службу Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Запустите сборку образа.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Создание образа и его репликация в оба региона могут занять некоторое время. Дождитесь завершения этой части, прежде чем переходить к созданию виртуальной машины.


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную машину на основе версии образа, созданной с помощью Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Проверка настройки
Создайте удаленный рабочий стол подключение к виртуальной машине, используя имя пользователя и пароль, заданные при создании виртуальной машины. В виртуальной машине откройте командную строку и введите следующую команду:

```console
dir c:\
```

Вы должны увидеть каталог с именем `buildActions` , созданный во время настройки образа.


## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы хотите выполнить повторную настройку версии образа, чтобы создать новую версию того же образа, **пропустите этот шаг** и перейдите к разделу [Использование Azure Image Builder для создания другой версии образа](image-builder-gallery-update-image-version.md).


Это приведет к удалению созданного образа вместе со всеми остальными файлами ресурсов. Убедитесь, что вы завершили работу с этим развертыванием, прежде чем удалять ресурсы.

При удалении ресурсов коллекции образов необходимо удалить все версии образа, прежде чем можно будет удалить определение образа, использованное для их создания. Чтобы удалить галерею, сначала необходимо удалить все определения образов в коллекции.

Удалите шаблон построителя образов.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Получить версию образа, созданную построителем образов, это всегда `0.`начинается с, а затем удаляет версию образа.

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

## <a name="next-steps"></a>Следующие шаги

Сведения о том, как обновить созданную версию образа, см. в статье [Использование Azure Image Builder для создания другой версии образа](image-builder-gallery-update-image-version.md).