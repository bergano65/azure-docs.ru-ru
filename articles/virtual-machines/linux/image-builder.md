---
title: Создание виртуальной Машины Linux с помощью Azure Image Builder (Предварительная версия)
description: Создайте виртуальную Машину Linux с помощью построителя образов Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 854645af95d780053d94668921e41ac189bbbfb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159515"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Предварительный просмотр: Создание виртуальной Машины Linux с помощью Azure Image Builder

В этой статье показано, как можно создать пользовательский образ Linux с помощью конструктора изображений Azure и Azure CLI. Пример в этой статье используются три разных [настройщики](image-builder-json.md#properties-customize) для настройки образа:

- Оболочка (ScriptUri) - файлы для загрузки и выполнения [скрипт оболочки](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Оболочка (inline) - запуски конкретных команд. В этом примере встроенных команд включают создание каталога и обновление операционной системы.
- Файл — копий [файла из GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) в каталог на виртуальной Машине.

Мы используем пример шаблона .json для настройки образа. JSON-файл, мы используем находится здесь: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
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
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

## <a name="create-the-image"></a>Создание образа
Отправить конфигурацию образа в службе VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Начать сборку образа.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Дождитесь завершения сборки. Это может занять около 15 минут.


## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную Машину, использующую образ, который вы создали.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Получить IP-адрес из выходных данных создания виртуальной Машины и использовать его для подключения по SSH к виртуальной Машине.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Вы увидите, что образ был настроена с сообщением, дня, как только SSH-подключение устанавливается!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Тип `exit` после чтобы закрыть SSH-подключения.

## <a name="check-the-source"></a>Проверка источника

В шаблоне построитель изображения в окне «Свойства», вы увидите исходное изображение, настройки сценарий выполняется, и где он распространяется.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Более подробные сведения об этом JSON-файл, см. в разделе [изображения построитель Справочник по шаблонам](image-builder-json.md)

## <a name="clean-up"></a>Очистка

Когда вы закончите, удалите все ресурсы.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01

az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о компонентах JSON-файла, используемые в этой статье, см. в разделе [Справочник по шаблонам Image Builder](image-builder-json.md).