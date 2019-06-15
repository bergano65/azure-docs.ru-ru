---
title: Использование коллекции образов Azure Image Builder для виртуальных машин Windows (Предварительная версия)
description: Создание образов Windows с помощью Azure Image Builder и общие коллекции образов.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-widows
manager: jeconnoc
ms.openlocfilehash: 2453d37720bcf48b95b428cf78c6186de40b31aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65160115"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Предварительный просмотр: Создание образа Windows и распространить его к коллекции образов Shared 

В этой статье — Показать, как можно использовать для создания версии образа в Azure Image Builder [коллекции образов Shared](shared-image-galleries.md), Глобальное распределение изображения.

Мы используем шаблоном .json для настройки образа. JSON-файл, мы используем находится здесь: [helloImageTemplateforWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

Чтобы распространить образ к коллекции образов Shared, в шаблоне используется [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) как значение для `distribute` раздел шаблона.

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
az provider show -n Microsoft.Compute | grep registrationState
```

Если не говорят зарегистрированных, используйте следующую команду:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Настройка переменных и разрешения 

Мы будем использовать некоторые части информации, поэтому мы создадим некоторые переменные для хранения этих сведений. Замените значения для переменных, например `username` и `vmpassword`, собственными данными.

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

Создайте переменную для идентификатора вашей подписки. Можно получить при помощи этого `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
```

Создайте группу ресурсов.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Предоставление разрешения Azure Image Builder для создания ресурсов в этой группе ресурсов. `--assignee` Значение — идентификатор регистрации приложения для службы Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Создание определения образа и коллекции

Создание галереи изображений. 

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Создает определение изображения.

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


## <a name="download-and-configure-the-json"></a>Скачивание и настройка .json

Загрузите шаблон .json и настроить его, используя переменные.

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

## <a name="create-the-image-version"></a>Создайте версию образа

Эта часть Далее создаст версию образа в коллекции. 

Отправьте конфигурацию образа в службе Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Начать сборку образа.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Создание образа и репликацией его в обоих регионах может занять некоторое время. Дождитесь завершения этого этапа перед переходом к созданию виртуальной Машины.


## <a name="create-the-vm"></a>Создание виртуальной машины

Создание виртуальной Машины из версию образа, который был создан построителем образа Azure.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Проверьте настройки
Создайте подключение удаленного рабочего стола к виртуальной Машине, используя имя пользователя и пароль, заданные при создании виртуальной Машины. На виртуальной Машине откройте окно командной строки и введите:

```console
dir c:\
```

Вы должны увидеть каталог с именем `buildActions` , созданного во время настройки изображения.


## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы хотите теперь попробуйте повторно Настройка версию образа для создания новой версии одного образа: **пропустите этот шаг** и перейдите к пункту [использования Azure Image Builder для создания другой версии образа](image-builder-gallery-update-image-version.md).


Это приведет к удалению образа, который был создан, а также все другие файлы ресурсов. Убедитесь, что вы закончили с этим развертыванием, прежде чем удалить ресурсы.

При удалении коллекции графических ресурсов, необходимо удалить все версии образов перед удалением в определении образа, которые использовались для их создания. Чтобы удалить коллекции, необходимо сначала удалили все определения образа в коллекции.

Удалите шаблон построитель образа.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Получить версию образа, созданный построителем образа, это всегда начинается с `0.`, а затем удалите версию образа

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


Удалите определение образа.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Удаление коллекции.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Удалите ее.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Следующие шаги

Чтобы узнать, как обновить версию образа, который вы создали, см. в разделе [использования Azure Image Builder для создания другой версии образа](image-builder-gallery-update-image-version.md).