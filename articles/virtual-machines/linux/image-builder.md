---
title: Создание Linux VM с помощью системы создания изображений Azure (предварительный просмотр)
description: Создайте Linux VM с помощью системы azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066687"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Предварительный просмотр: Создайте Linux VM с помощью системы создания изображений Azure

В этой статье показано, как можно создать настраиваемый образ Linux с помощью системы «Разжитель изображений» и Azure CLI. Пример в этой статье использует три различных [настройки](image-builder-json.md#properties-customize) для настройки изображения:

- Shell (ScriptUri) - загружает и запускает [сценарий оболочки](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (в строке) - выполняет определенные команды. В этом примере внеочередные команды включают создание каталога и обновление ОС.
- Файл - копии [файла с GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) в каталог на VM.

Вы также можете `buildTimeoutInMinutes`указать . По умолчанию 240 минут, и вы можете увеличить время сборки, чтобы позволить более длительные сборки.

Мы будем использовать образец шаблона .json для настройки изображения. Файл .json, который мы используем, здесь: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Настройка примера переменных

Мы будем использовать некоторые части информации неоднократно, поэтому мы создадим некоторые переменные для хранения этой информации.


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Создайте переменную для идентификатора подписки. Вы можете получить `az account show | grep id`это с помощью .

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Создайте группу ресурсов.
Это используется для хранения артефакта шаблона конфигурации изображения и изображения.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Установка разрешений в группе ресурсов
Дайте «вкладчику» изображения разрешение на создание изображения в группе ресурсов. Без соответствующих разрешений сборка изображения завершится неудачей. 

Значение `--assignee` — идентификатор регистрации приложения для службы Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Скачать пример шаблона

Для использования создан параметризированный шаблон конфигурации изображения образца. Загрузите образец файла .json и настроили его с переменными, которые вы установили ранее.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Вы можете изменить этот пример .json по мере необходимости. Например, можно увеличить значение `buildTimeoutInMinutes` для более длительных запусков. Вы можете отредакивать файл в `vi`облачной оболочке с помощью текстового редактора, как .

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Для исходного изображения необходимо всегда [указывать версию,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)несную. `latest`
>
> При добавлении или изменении группы ресурсов, в которой распространяется изображение, необходимо убедиться, что [разрешения настроены для группы ресурсов.](#set-permissions-on-the-resource-group)


## <a name="submit-the-image-configuration"></a>Отправить конфигурацию изображения
Отправить конфигурацию изображения в службу VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Если он успешно завершается, он вернет сообщение об успехе и создаст артефакт шаблона шаблона шаблона шаблона шаблона создания изображений в $imageResourceGroup. Вы можете увидеть группу ресурсов на портале, если включить "Показать скрытые типы".

Кроме того, в фоновом режиме Image Builder создает группу промежуточных ресурсов в вашей подписке. Image Builder использует группу промежуточных ресурсов для создания изображения. Название ресурсной группы будет в `IT_<DestinationResourceGroup>_<TemplateName>`этом формате: .

> [!IMPORTANT]
> Не удаляйте группу промежуточных ресурсов напрямую. Если вы удалите артефакт шаблона изображения, он автоматически удалит группу промежуточных ресурсов. Для получения дополнительной информации смотрите раздел [«Очистка»](#clean-up) в конце этой статьи.

Если служба сообщает о сбое во время представления шаблона конфигурации изображения, обратитесь к шагам [устранения неполадок.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) Вам также нужно будет удалить шаблон перед повторной отправкой сборки. Чтобы удалить шаблон:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Начало сборки изображения

Начало сборки изображения.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Подождите, пока сборка будет завершена, например, это может занять 10-15 минут.

Если вы столкнулись с ошибками, пожалуйста, просмотрите эти шаги [устранения неполадок.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting)


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте VM с помощью созданного изображения.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Получите IP-адрес от вывода создания VM и использовать его для SSH для VM.

```bash
ssh azureuser@<pubIp>
```

Вы должны увидеть, что изображение было настроено с Посланием Дня, как только ваше соединение SSH установлено!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Введите, `exit` когда вы сделали, чтобы закрыть соединение SSH.

## <a name="check-the-source"></a>Проверьте источник

В шаблоне Image Builder template в «Свойствах» вы увидите исходное изображение, сценарий настройки, который он запускает, и место его распространения.

```bash
cat helloImageTemplateLinux.json
```

Для получения более подробной информации [Image builder template reference](image-builder-json.md) об этом файле .json см.

## <a name="clean-up"></a>Очистка

Когда вы закончите, вы можете удалить ресурсы.

Удалите шаблон шаблона шаблона шаблона шаблона шаблона шаблона шаблона шаблона

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Удалить группу ресурсов изображений.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о компонентах файла .json, используемого в этой статье, [см.](image-builder-json.md)
