---
title: Шифрование дисков на виртуальной машине Linux в Azure | Документация Майкрософт
description: Как шифровать диски в виртуальной машине Linux для улучшения уровня безопасности с помощью Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2a23b6fa-6941-4998-9804-8efe93b647b3
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 15bd3cf2ab6ea5285662610c2c0a850bb180e2f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731623"
---
# <a name="how-to-encrypt-a-linux-virtual-machine-in-azure"></a>Как зашифровать виртуальную машину Linux в Azure

Для повышения уровня безопасности и соответствия требованиям виртуальной машины виртуальные диски и саму машину можно зашифровать. Виртуальные машины можно зашифровать с использованием криптографических ключей, защищенных в Azure Key Vault. Вы будете управлять этими криптографическими ключами и проводить аудит их использования. В этой статье описывается шифрование дисков в виртуальной машине Linux с помощью Azure CLI. 

## <a name="launch-azure-cloud-shell"></a>Запуск Azure Cloud Shell

Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. 

Чтобы открыть Cloud Shell, просто выберите **Попробовать** в правом верхнем углу блока кода. Cloud Shell можно также запустить в отдельной вкладке браузера, перейдя на страницу [https://shell.azure.com/bash](https://shell.azure.com/bash). Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу "ВВОД", чтобы выполнить его.

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="overview-of-disk-encryption"></a>Общие сведения о шифровании дисков
Виртуальные диски на виртуальных машинах Linux шифруются с помощью [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). В Azure за шифрование виртуальных дисков плата не взимается. Криптографические ключи хранятся в хранилище ключей Azure с применением защиты программного обеспечения. В качестве альтернативы можно импортировать или создать ключи аппаратных модулей безопасности (HSM), сертифицированных по стандартам уровня 2 FIPS 140-2. Вы сохраняете контроль над этими криптографическими ключами и можете проводить аудит их использования. Эти криптографические ключи используются для шифрования и расшифровки виртуальных дисков, подключенных к виртуальной машине. 

Процесс шифрования виртуальной машины выполняется следующим образом.

1. Создайте криптографический ключ в хранилище ключей Azure.
1. Настройте криптографический ключ таким образом, чтобы его можно было использовать для шифрования дисков.
1. Включите шифрование для виртуальных дисков.
1. Необходимые ключи шифрования запрашиваются из Azure Key Vault.
1. Виртуальные диски зашифровываются с использованием предоставленного криптографического ключа.


## <a name="requirements-and-limitations"></a>Требования и ограничения
Поддерживаемые сценарии и требования для шифрования дисков:

* SKU серверов Linux — Ubuntu, CentOS, SUSE, SUSE Linux Enterprise Server (SLES) и Red Hat Enterprise Linux;
* все ресурсы (такие как хранилище ключей, учетная запись хранения и виртуальная машина) должны относиться к одному региону и одной подписке Azure;
* стандартные серии виртуальных машин A, D, DS, G и GS и т. д.;
* обновление криптографических ключей на уже зашифрованных виртуальных машинах Linux.

Шифрование дисков сейчас не поддерживается в следующих сценариях:

* виртуальные машины уровня "Базовый";
* виртуальные машины, созданные с использованием классической модели развертывания;
* отключение шифрования дисков ОС на виртуальных машинах Linux;
* использование пользовательских образов Linux.

Дополнительные сведения о поддерживаемых сценариях и ограничениях см. в статье о [шифровании дисков Azure для виртуальных машин IaaS](../../security/azure-security-disk-encryption.md).


## <a name="create-an-azure-key-vault-and-keys"></a>Создание Azure Key Vault и ключей
В следующих примерах замените имена параметров собственными значениями. Примеры имен параметров: *myResourceGroup*, *myKey* и *myVM*.

Первым делом создайте хранилище ключей Azure для хранения криптографических ключей. В хранилище ключей Azure можно хранить ключи, секреты или пароли, что позволяет безопасно реализовать их в приложениях и службах. Для шифрования виртуальных дисков используйте хранилище ключей, чтобы хранить криптографический ключ, используемый для шифрования или расшифровки виртуальных дисков.

Включите поставщик Azure Key Vault в подписке Azure, выполнив команду [az provider register](/cli/azure/provider#az-provider-register), и создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az-group-create). В следующем примере создается имя группы ресурсов *myResourceGroup* в расположении `eastus`:

```azurecli-interactive
az provider register -n Microsoft.KeyVault
resourcegroup="myResourceGroup"
az group create --name $resourcegroup --location eastus
```

Хранилище ключей Azure, содержащее криптографические ключи и связанные вычислительные ресурсы, такие как хранилище и виртуальная машина, должны находиться в одном и том же регионе. Создайте Azure Key Vault, выполнив команду [az keyvault create](/cli/azure/keyvault#az-keyvault-create), и включите Key Vault для использования при шифровании дисков. Укажите уникальное имя Key Vault для параметра *keyvault_name*, выполнив следующую команду:

```azurecli-interactive
keyvault_name=myvaultname$RANDOM
az keyvault create \
    --name $keyvault_name \
    --resource-group $resourcegroup \
    --location eastus \
    --enabled-for-disk-encryption True
```

Хранить криптографические ключи можно с помощью программного обеспечения или защиты HSM. Для использования HSM требуется хранилище ключей уровня "Премиум". Создание хранилища ключей уровня "Премиум" осуществляется за дополнительную плату в отличие от хранилища ключей уровня "Стандартный", в котором хранятся ключи, защищенные программным обеспечением. Чтобы создать хранилище ключей уровня "Премиум", на предыдущем шаге добавьте `--sku Premium` к команде. В следующем примере используются ключи, защищенные программным обеспечением, так как вы создали хранилище ключей уровня "Стандартный".

Для обеих моделей защиты платформе Azure необходимо предоставить доступ на запрос криптографических ключей при загрузке виртуальной машины для расшифровки виртуальных дисков. Создайте криптографический ключ в своем Key Vault, выполнив команду [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create). В следующем примере создается ключ с именем *myKey*:

```azurecli-interactive
az keyvault key create \
    --vault-name $keyvault_name \
    --name myKey \
    --protection software
```


## <a name="create-a-virtual-machine"></a>Создание виртуальной машины
Создайте виртуальною машину, выполнив команду [az vm create](/cli/azure/vm#az-vm-create), и подключите диск данных емкостью 5 ГБ. Только некоторые образы Marketplace поддерживают шифрование диска. В следующем примере из образа *Ubuntu 16.04 LTS* создается виртуальная машина с именем *myVM*.

```azurecli-interactive
az vm create \
    --resource-group $resourcegroup \
    --name myVM \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 5
```

Установите SSH-подключение к виртуальной машине с помощью *publicIpAddress* из выходных данных предыдущей команды. Создайте раздел и файловую систему, а затем подключите диск данных. Дополнительные сведения см. в разделе [Подключение к виртуальной машине Linux для подключения нового диска](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#connect-to-the-linux-vm-to-mount-the-new-disk). Закройте сеанс SSH.


## <a name="encrypt-the-virtual-machine"></a>Шифрование виртуальной машины


Зашифруйте виртуальную машину, выполнив команду [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable).

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type all
```

Для завершения шифрования диска потребуется некоторое время. Состояние процесса можно контролировать с помощью команды [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show):

```azurecli-interactive
az vm encryption show --resource-group $resourcegroup --name myVM --query 'status'
```

По завершении выходные данные будут выглядеть примерно так:

```json
[
  {
    "code": "ProvisioningState/succeeded",
    "displayStatus": "Provisioning succeeded",
    "level": "Info",
    "message": "Encryption succeeded for all volumes",
    "time": null
  }
]
```


## <a name="add-additional-data-disks"></a>Добавление дополнительных дисков данных
Когда вы зашифруете диски данных, вы сможете добавить виртуальные диски в виртуальную машину и зашифровать их. 

После добавления дисков данных в виртуальную машину повторно выполните команду, чтобы зашифровать виртуальные диски, следующим образом:

```azurecli-interactive
az vm encryption enable \
    --resource-group $resourcegroup \
    --name myVM \
    --disk-encryption-keyvault $keyvault_name \
    --key-encryption-key myKey \
    --volume-type data
```


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об управлении хранилищем ключей Azure, а также об удалении криптографических ключей и хранилищ см. в статье [Управление хранилищем ключей с помощью интерфейса командной строки](../../key-vault/key-vault-manage-with-cli2.md).
* Дополнительные сведения о шифровании дисков, а именно о подготовке зашифрованной настраиваемой виртуальной машины к передаче в Azure, см. в статье [Дисковое шифрование Azure для виртуальных машин IaaS под управлением Windows и Linux](../../security/azure-security-disk-encryption.md).
