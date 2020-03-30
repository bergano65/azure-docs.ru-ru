---
title: Создание Windows VM с помощью Aure Image Builder (предварительный просмотр)
description: Создайте Windows VM с помощью a Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: e82d82dac833f7455e3d83d7e11c0c57c4eea816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238805"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Предварительный просмотр: Создание Windows VM с помощью Aure Image Builder

Эта статья должна показать вам, как можно создать индивидуальное изображение Windows с помощью Azure VM Image Builder. Пример в этой статье использует [настройки](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) для настройки изображения:
- PowerShell (ScriptUri) - скачать и запустить [сценарий PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Перезагрузка Windows - перезапускает VM.
- PowerShell (включив) - запустите определенную команду. В этом примере он создает каталог на `mkdir c:\\buildActions`VM с помощью .
- Файл - копия файла из GitHub на VM. Этот пример [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) копирует `c:\buildArtifacts\index.html` index.md на VM.

Вы также можете `buildTimeoutInMinutes`указать . По умолчанию 240 минут, и вы можете увеличить время сборки, чтобы позволить более длительные сборки.

Мы будем использовать образец шаблона .json для настройки изображения. Файл .json, который мы используем, здесь: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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

## <a name="set-variables"></a>Задание переменных

Мы будем использовать некоторые части информации неоднократно, поэтому мы создадим некоторые переменные для хранения этой информации.


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

Создайте переменную для идентификатора подписки. Вы можете получить `az account show | grep id`это с помощью .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Создание группы ресурсов
Эта группа ресурсов используется для хранения артефакта шаблона конфигурации изображения и изображения.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Установка разрешений в группе ресурсов

Дайте «вкладчику» изображения разрешение на создание изображения в группе ресурсов. Без этого сборка изображения не будет построена. 

Значение `--assignee` — идентификатор регистрации приложения для службы Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Скачать пример шаблона конфигурации изображения

Для того, чтобы попробовать, создан параметризированный шаблон конфигурации изображения. Загрузите пример файл .json и настроили его с параметрами, которые вы установили ранее.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

Вы можете изменить этот пример, в терминале с помощью текстового редактора, как `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Для исходного изображения необходимо всегда [указывать версию,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)несную. `latest`
> При добавлении или изменении группы ресурсов, на которой распространяется изображение, необходимо [настроить разрешения](#set-permissions-on-the-resource-group) на группу ресурсов.
 
## <a name="create-the-image"></a>Создание образа

Отправить конфигурацию изображения в службу VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

После завершения, это вернет сообщение об успехе `Image Builder Configuration Template` обратно `$imageResourceGroup`на консоль, и создать в . Этот ресурс можно увидеть в группе ресурсов на портале Azure, если включить "Показать скрытые типы".

В фоновом режиме Image Builder также создаст группу промежуточных ресурсов в вашей подписке. Эта группа ресурсов используется для создания изображения. Это будет в таком формате:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Вы не должны удалять группу промежуточных ресурсов напрямую. Сначала удалите артефакт шаблона изображения, это приведет к удалению группы промежуточных ресурсов.

Если служба сообщает о сбое во время представления шаблона конфигурации изображения:
-  Просмотрите эти шаги [устранения неполадок.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) 
- Вам нужно будет удалить шаблон, используя следующий фрагмент, прежде чем повторно представить.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Начало сборки изображения
Начните процесс создания изображения с помощью [ресурса az invoke-action.](/cli/azure/resource#az-resource-invoke-action)

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Подождите, пока сборка будет завершена. Это может занять около 15 минут.

Если вы столкнулись с ошибками, пожалуйста, просмотрите эти шаги [устранения неполадок.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте VM с помощью созданного изображения. Замените * \<пароль>* собственным `aibuser` паролем для VM.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Проверка настройки

Создайте соединение удаленного рабочего стола с VM, используя имя пользователя и пароль, который вы установили при создании VM. Внутри VM, откройте cmd подсказку и введите:

```console
dir c:\
```

Вы должны увидеть эти два каталога, созданные во время настройки изображения:
- сборкаДействия
- сборкаартефактов

## <a name="clean-up"></a>Очистка

Когда вы закончите, удалите ресурсы.

### <a name="delete-the-image-builder-template"></a>Удалить шаблон шаблона создание изображений

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>Удалить группу ресурсов изображений

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о компонентах файла .json, используемого в этой статье, [см.](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
