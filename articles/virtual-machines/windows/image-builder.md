---
title: Создание виртуальной Машины Windows с Azure Image Builder (Предварительная версия)
description: Создание виртуальной Машины Windows с помощью Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: jeconnoc
ms.openlocfilehash: 01109aa83c12bda9b1d21ec25784d663f8abf700
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159725"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Предварительный просмотр: Создание виртуальной Машины Windows с помощью Azure Image Builder

В этой статье — Показать, как можно создать пользовательский образ Windows с помощью построителя образ виртуальной Машины Azure. Пример в этой статье используются три разных [настройщики](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) для настройки образа:
- PowerShell (ScriptUri) - скачайте и запустите [сценарий PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Перезапуск Windows - перезапускает виртуальную Машину.
- PowerShell (встроенные) — запустить определенную команду. В этом примере он создает каталог на виртуальной Машине с помощью `mkdir c:\\buildActions`.
- Файл — скопировать файл из GitHub на виртуальную Машину. Этот пример копирует [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) для `c:\buildArtifacts\index.html` на виртуальной Машине.

Мы используем пример шаблона .json для настройки образа. JSON-файл, мы используем находится здесь: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Создайте переменную для идентификатора вашей подписки. Можно получить при помощи этого `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Создайте группу ресурсов.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

Предоставьте разрешение Image Builder для создания ресурсов в этой группе ресурсов. `--assignee` Значение — идентификатор регистрации приложения для службы Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-json-example"></a>Загрузить пример .json

Загрузить пример JSON-файле и настройте его с помощью переменных, которые вы создали.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

## <a name="create-the-image"></a>Создание образа

Отправить конфигурацию образа в службе VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Начать сборку образа.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Дождитесь завершения сборки. Это может занять около 15 минут.

## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную Машину, использующую образ, который вы создали. Замените *<password>* своим паролем для `aibuser` на виртуальной Машине.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Проверьте настройки

Создайте подключение удаленного рабочего стола к виртуальной Машине, используя имя пользователя и пароль, заданные при создании виртуальной Машины. На виртуальной Машине откройте окно командной строки и введите:

```console
dir c:\
```

Вы должны увидеть эти два каталоги, созданные во время настройки изображения:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Очистка

Когда вы закончите, удалите все ресурсы.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
az group delete -n $imageResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о компонентах JSON-файла, используемые в этой статье, см. в разделе [изображения Справочник по шаблонам построитель](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

