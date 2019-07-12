---
title: Создание виртуальной Машины Linux с помощью Azure Image Builder (Предварительная версия)
description: Создайте виртуальную Машину Linux с помощью построителя образов Azure.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2966a1803d0664312d71ba992a5ba65f73b27370
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667527"
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

## <a name="setup-example-variables"></a>Переменные пример установки

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

## <a name="create-the-resource-group"></a>Создайте группу ресурсов.
Используется для хранения артефакта шаблона конфигурации образ и образ.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Установка разрешений для группы ресурсов
Предоставьте разрешение «участник» Image Builder для создания образа в группе ресурсов. Без необходимых разрешений не удастся сборки образа. 

`--assignee` Значение — идентификатор регистрации приложения для службы Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Скачать пример шаблона

Изображение конфигурации параметризованные пример шаблона будет создана для использования. Загрузите образец JSON-файла и настроить его, используя переменные, заданные ранее.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

При необходимости можно изменить этот пример .json. Например, можно увеличить значение `buildTimeoutInMinutes` для обеспечения более выполняющиеся сборки. Можно изменить файл в Cloud Shell с помощью `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Для исходного изображения, необходимо всегда [указать версию](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), нельзя использовать `latest`.
>
> Если добавить или изменить группу ресурсов, где распространяется образа, необходимо убедитесь, что [разрешения для группы ресурсов](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Отправить конфигурацию образа
Отправить конфигурацию образа в службе VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Если он завершается успешно, он будет возвращать сообщение об успешном выполнении и создать артефакт образа построитель конфигурации шаблона в $imageResourceGroup. При включении «Показать скрытые типы», вы увидите группу ресурсов на портале.

Кроме того в фоновом режиме, Image Builder создает промежуточную группу ресурсов в вашей подписке. Image Builder использует промежуточную группу ресурсов для сборки образа. Группы ресурсов будет называться в следующем формате: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Не удаляйте непосредственно промежуточную группу ресурсов. При удалении артефакта шаблона образа, оно автоматически удалит промежуточную группу ресурсов. Дополнительные сведения см. в разделе [очистки](#clean-up) в конце этой статьи.

Если служба сообщает о сбое во время отправки образа шаблона конфигурации, см. в разделе [Устранение неполадок](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) действия. Также необходимо будет удалить шаблон, прежде чем повторить попытку отправки сборки. Чтобы удалить шаблон:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Начать сборку образа

Начать сборку образа.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Подождите, пока сборка не завершится, в этом примере, может потребоваться 10 – 15 минут.

Если возникают ошибки, просмотрите эти [Устранение неполадок](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) действия.


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

Когда все будет готово, вы можете удалить ресурсы.

Удалите шаблон построитель образа.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Удалите группу ресурсов для образа.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о компонентах JSON-файла, используемые в этой статье, см. в разделе [Справочник по шаблонам Image Builder](image-builder-json.md).
