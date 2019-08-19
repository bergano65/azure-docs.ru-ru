---
title: Создание виртуальной машины Linux с помощью Azure Image Builder (Предварительная версия)
description: Создание виртуальной машины Linux с помощью построителя образов Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 1bac04bbb67c7472de92c6da322121bafc20a560
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695435"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Предварительный просмотр: Создание виртуальной машины Linux с помощью Azure Image Builder

В этой статье показано, как создать настраиваемый образ Linux с помощью построителя образов Azure и Azure CLI. В примере, приведенном в этой статье, для настройки изображения используются три различных [настраиваемых способа](image-builder-json.md#properties-customize).

- Shell (Скриптури) — загружает и запускает [сценарий оболочки](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (inline) — выполняет определенные команды. В этом примере встроенные команды включают создание каталога и обновление операционной системы.
- Файл — копирует [файл из GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) в каталог на виртуальной машине.

Можно также указать `buildTimeoutInMinutes`. Значение по умолчанию — 240 минут. Вы можете увеличить время сборки, чтобы обеспечить более длительное выполнение сборок.

Мы будем использовать шаблон Sample. JSON для настройки образа. JSON-файл, который мы используем: [хеллоимажетемплателинукс. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Примеры переменных программы установки

Мы будем использовать несколько фрагментов информации повторно, поэтому мы создадим некоторые переменные для хранения этих данных.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Создайте переменную для идентификатора подписки. Это можно сделать с помощью `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Создайте группу ресурсов.
Используется для хранения артефакта шаблона конфигурации образа и образа.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Задание разрешений для группы ресурсов
Предоставьте разрешение "участник" построителя образов для создания образа в группе ресурсов. Без соответствующих разрешений сборка образа завершится ошибкой. 

`--assignee` Значение представляет собой идентификатор регистрации приложения для службы "Построитель образов". 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Скачать пример шаблона

Для использования создан шаблон конфигурации с параметризованным примером образа. Скачайте файл Sample. JSON и настройте его с помощью переменных, заданных ранее.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

При необходимости можно изменить этот пример. JSON. Например, можно увеличить значение `buildTimeoutInMinutes` , чтобы обеспечить более длительное выполнение сборок. Файл можно изменить в Cloud Shell с помощью текстового редактора, например `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Для исходного образа необходимо всегда [указывать версию](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), но нельзя использовать `latest`.
>
> При добавлении или изменении группы ресурсов, в которой распространяется образ, необходимо убедиться, что [для группы ресурсов заданы разрешения](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Отправка конфигурации образа
Отправка конфигурации образа в службу "Построитель образов виртуальных машин"

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

При успешном завершении он возвратит сообщение об успешном выполнении и создаст артефакт шаблона конфигурации построителя образов в $imageResourceGroup. Группу ресурсов на портале можно увидеть, если включить параметр "Показать скрытые типы".

Кроме того, в фоновом режиме построитель изображений создает промежуточную группу ресурсов в подписке. Построитель образов использует группу ресурсов промежуточного хранения для сборки образа. Имя группы ресурсов будет иметь следующий формат: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Не удаляйте промежуточную группу ресурсов напрямую. Если удалить артефакт шаблона образа, он автоматически удалит промежуточную группу ресурсов. Дополнительные сведения см. в разделе [Очистка](#clean-up) в конце этой статьи.

Если служба сообщает о сбое во время отправки шаблона конфигурации образа, см. инструкции по [устранению неполадок](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) . Кроме того, необходимо удалить шаблон, прежде чем повторять попытку отправки сборки. Чтобы удалить шаблон, выполните следующие действия.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Запуск сборки образа

Запустите сборку образа.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Дождитесь завершения сборки. в этом примере это может занять 10-15 минут.

При возникновении ошибок ознакомьтесь с этими действиями по [устранению неполадок](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) .


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью созданного образа.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Получите IP-адрес из выходных данных по созданию виртуальной машины и используйте его для подключения к виртуальной машине по протоколу SSH.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Вы должны увидеть, что образ был настроен с сообщением дня, как только подключение SSH установлено.

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Введите `exit` , когда завершите подключение SSH.

## <a name="check-the-source"></a>Проверка источника

В шаблоне Image Builder в разделе "Свойства" вы увидите исходное изображение, сценарий настройки, который он запускает, и где он распределен.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Более подробные сведения об этом JSON файле см. в статье [Справочник](image-builder-json.md) по шаблонам в конструкторе образов

## <a name="clean-up"></a>Очистка

По завершении можно удалить ресурсы.

Удалите шаблон построителя образов.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Удалите группу ресурсов образа.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о компонентах JSON, использованных в этой статье, см. в разделе [Справочник по шаблонам для Image Builder](image-builder-json.md).
