---
title: Создание виртуальной машины Windows с помощью Azure Image Builder (Предварительная версия)
description: Создайте виртуальную машину Windows с помощью построителя образов Azure.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 9dc4909db5560be6eb082dbad85d4b2d42113bdd
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828685"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Предварительный просмотр: Создание виртуальной машины Windows с помощью Azure Image Builder

В этой статье показано, как создать настраиваемый образ Windows с помощью построителя образов виртуальных машин Azure. В примере, приведенном в этой статье, используются [Настраиваемые](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) для настройки изображения:
- PowerShell (Скриптури) — Скачайте и запустите [сценарий PowerShell](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Перезагрузка Windows — перезапускает виртуальную машину.
- PowerShell (встроенная) — выполнение определенной команды. В этом примере он создает каталог на виртуальной машине с помощью `mkdir c:\\buildActions`.
- Файл — копирование файла из GitHub на виртуальную машину. В`c:\buildArtifacts\index.html` этом примере [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) копируется на виртуальную машину.

Можно также указать `buildTimeoutInMinutes`. Значение по умолчанию — 240 минут. Вы можете увеличить время сборки, чтобы обеспечить более длительное выполнение сборок.

Мы будем использовать шаблон Sample. JSON для настройки образа. JSON-файл, который мы используем: [хеллоимажетемплатевин. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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

## <a name="set-variables"></a>Задание переменных

Мы будем использовать несколько фрагментов информации повторно, поэтому мы создадим некоторые переменные для хранения этих данных.


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

Создайте переменную для идентификатора подписки. Это можно сделать с помощью `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Создать группу ресурсов
Эта группа ресурсов используется для хранения артефакта шаблона конфигурации образа и образа.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Задание разрешений для группы ресурсов

Предоставьте разрешение "участник" построителя образов для создания образа в группе ресурсов. Без этого сборка образа завершится ошибкой. 

`--assignee` Значение представляет собой идентификатор регистрации приложения для службы "Построитель образов". 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Скачать пример шаблона конфигурации образа

Был создан шаблон конфигурации параметризованного образа, который вы хотите использовать. Скачайте файл example. JSON и настройте его с помощью ранее заданных переменных.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

Этот пример можно изменить в терминале, используя текстовый редактор, например `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Для исходного образа необходимо всегда [указывать версию](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), но нельзя использовать `latest`.
> При добавлении или изменении группы ресурсов, в которую распространяется изображение, необходимо [установить разрешения](#set-permissions-on-the-resource-group) для группы ресурсов.
 
## <a name="create-the-image"></a>Создание образа

Отправка конфигурации образа в службу "Построитель образов виртуальных машин"

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

По завершении это приведет к возврату сообщения об успешном выполнении в консоль и созданию `Image Builder Configuration Template` `$imageResourceGroup`в. Этот ресурс можно увидеть в группе ресурсов в портал Azure, если включить параметр "Показать скрытые типы".

В фоновом режиме построитель образов также создаст группу промежуточных ресурсов в подписке. Эта группа ресурсов используется для сборки образа. Он будет иметь следующий формат:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Не следует удалять промежуточную группу ресурсов напрямую. Сначала удалите артефакт шаблона образа. это приведет к удалению промежуточной группы ресурсов.

Если служба сообщает о сбое во время отправки шаблона конфигурации образа:
-  Ознакомьтесь с этими действиями по [устранению неполадок](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) . 
- Перед повторной попыткой отправки необходимо удалить шаблон с помощью следующего фрагмента кода.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Запуск сборки образа
Запустите процесс создания образа с помощью команды [AZ Resource Invoke-Action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Дождитесь завершения сборки. Это может занять около 15 минут.

При возникновении ошибок ознакомьтесь с этими действиями по [устранению неполадок](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) .


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью созданного образа. *Замените\<password >* `aibuser` собственным паролем на виртуальной машине.

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

Создайте удаленный рабочий стол подключение к виртуальной машине, используя имя пользователя и пароль, заданные при создании виртуальной машины. В виртуальной машине откройте командную строку и введите следующую команду:

```console
dir c:\
```

Вы должны увидеть эти два каталога, созданные во время настройки образа:
- буилдактионс
- буилдартифактс

## <a name="clean-up"></a>Очистка

По завершении удалите ресурсы.

### <a name="delete-the-image-builder-template"></a>Удаление шаблона построителя образов
```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>Удаление группы ресурсов образа
```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о компонентах JSON, использованных в этой статье, см. в разделе [Справочник по шаблонам для Image Builder](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
