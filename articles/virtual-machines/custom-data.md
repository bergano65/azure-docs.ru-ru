---
title: Пользовательские данные и виртуальные машины Azure
description: Подробная информация об использовании пользовательских данных и Cloud-Init на виртуальных машинах Azure
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: aadac082e90a19d1a185dd7e6181a490adb70a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109632"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Пользовательские данные и облачные технологии на виртуальных машинах Azure

## <a name="what-is-custom-data"></a>Что такое пользовательские данные?

Клиенты часто спрашивают, как они могут вводить сценарий или другие метаданные в виртуальную машину Microsoft Azure во время предоставления.  В других облаках эту концепцию часто называют пользовательскими данными.  В Microsoft Azure у нас есть аналогичная функция, называемая пользовательскими данными. 

Пользовательские данные доступны только для VM во время первой загрузки / начальной установки, мы называем это "подготовка". Подготовка — это процесс, в котором параметры VM Create (например, имя хоста, имя пользователя, пароль, сертификаты, пользовательские данные, ключи и т.д.) доступны для VM, и агент-поставщик обрабатывает их, такие как [Linux Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) и [cloud-init.](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init) 


## <a name="passing-custom-data-to-the-vm"></a>Передача пользовательских данных в VM
Чтобы использовать пользовательские данные, необходимо сначала закодировать содержимое, прежде чем передавать его в API, если только вы не используете инструмент CLI, который делает преобразование для вас, например, q CLI. Размер не может превышать 64 КБ.

В CLI вы можете передавать пользовательские данные в виде файла, и они будут преобразованы в base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

В Azure Resource Manager (ARM) есть [функция base64.](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64)

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customDataBase64": "[variables('customData')]"
        },
```

## <a name="processing-custom-data"></a>Обработка пользовательских данных
Средства подготовки, установленные на ВМ, обрабатывают взаимодействие с платформой и размещение его в файловой системе. 

### <a name="windows"></a>Windows
Пользовательские данные размещаются в *%SYSTEMDRIVE% -AzureData-CustomData.bin* в качестве двоичного файла, но они не обрабатываются. Если вы хотите обработать этот файл, вам нужно будет создать пользовательское изображение и написать код для обработки CustomData.bin.

### <a name="linux"></a>Linux  
На Linux OS пользовательские данные передаются в VM через файл ovf-env.xml, который копируется в *каталог /var/lib/waagent* во время подготовки.  Новые версии Microsoft Azure Linux Agent также будут копировать базовые64 закодированные данные на */var/lib/waagent/CustomData,* а также для удобства.

В настоящее время Azure поддерживает двух агентов по подготовке:
* Linux Agent - По умолчанию агент не будет обрабатывать пользовательские данные, вам нужно будет создать пользовательское изображение с включенным. Соответствующие настройки, в [документации](https://github.com/Azure/WALinuxAgent#configuration) являются:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Когда вы включите пользовательские данные и выполнить скрипт, это приведет к задержке отчета VM, который он готов или что подготовка не увенчалась успехом до завершения скрипта. Если скрипт превышает общую сумму времени в 40 минут, VM Create выйдет из строя. Обратите внимание, что если скрипт не выполняется или ошибки во время выполнения, он не считается фатальным сбоем подготовки, вам нужно будет создать способ уведомления, чтобы предупредить вас о состоянии завершения скрипта.

Для устранения неполадок в выполнении пользовательских данных, просмотрите */var/log/waagent.log*

* облако-инит - По умолчанию будет обрабатывать пользовательские данные по умолчанию, облако-иту принимает [несколько форматов](https://cloudinit.readthedocs.io/en/latest/topics/format.html) пользовательских данных, таких как конфигурация облачного инаит, скрипты и т.д. Как и в Linux Agent, когда облачные процессы обработки пользовательских данных. При возникновении ошибок при выполнении обработки конфигурации или скриптов это не считается фатальным сбоем подготовки, и вам нужно будет создать способ уведомления, чтобы предупредить вас о состоянии завершения скрипта. Тем не менее, в отличие от Linux Agent, cloud-init не ждет завершения пользовательских конфигураций пользовательских данных, прежде чем сообщать на платформу, которую готов VM. Для получения дополнительной информации об облачном ините на лазурном газе просмотрите [документацию.](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)


Чтобы устранить неполадки выполнения пользовательских данных, просмотрите [документацию](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)по устранению неполадок.


## <a name="faq"></a>часто задаваемые вопросы
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Могу ли я обновить пользовательские данные после создания VM?
Для отдельных ВМ пользовательские данные в модели VM не могут быть обновлены, но для VMSS можно обновлять пользовательские данные VMSS через REST API (не применимые для клиентов PS или A CLI). При обновлении пользовательских данных в модели VMSS:
* Существующие экземпляры в VMSS не будут получать обновленные пользовательские данные, только до тех пор, пока они не будут reimaged.
* Существующие экземпляры в обновленной Области VMSS не получат обновленных пользовательских данных.
* Новые экземпляры получат новые пользовательские данные.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Могу ли я разместить конфиденциальные значения в пользовательских данных?
Мы советуем **не** хранить конфиденциальные данные в пользовательских данных. Для получения дополнительной [Azure Security and encryption best practices](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices)информации см.


### <a name="is-custom-data-made-available-in-imds"></a>Доступны ли пользовательские данные в IMDS?
Нет, эта функция в настоящее время недоступна.
