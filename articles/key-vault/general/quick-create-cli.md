---
title: Краткое руководство. Создание Azure Key Vault с помощью Azure CLI
description: Краткое руководство по созданию Azure Key Vault с помощью Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e29a692e3fdad1bea7132b3bed50444c7398ba46
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936317"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Краткое руководство. Создание хранилища ключей с помощью Azure CLI

Azure Key Vault — это облачная служба, которая предоставляет защищенное хранилище для [ключей](../keys/index.yml), [секретов](../secrets/index.yml) и [сертификатов](../certificates/index.yml). Дополнительные сведения о Key Vault см. в [этой статье](overview.md), а сведения о том, что можно хранить в хранилище ключей, см. [здесь](about-keys-secrets-certificates.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Для работы с этим кратким руководством требуется Azure CLI версии 2.0.4 или более поздней. Если вы используете Azure Cloud Shell, последняя версия уже установлена.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *ContosoResourceGroup* в расположении *eastus*.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Создайте Key Vault в группе ресурсов, созданной на предыдущем шаге. Необходимо будет указать следующие сведения:

- Имя хранилища ключей: строка длиной от 3 до 24 знаков, которая может содержать только цифры (0–9), буквы (a-z, A-Z) и дефисы (-).

  > [!Important]
  > Каждое хранилище ключей должно иметь уникальное имя. В следующих примерах замените <your-unique-keyvault-name> именем своего хранилища ключей.

- Имя группы ресурсов: **myResourceGroup**.
- Расположение: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

В выходных данных командлета будут показаны свойства созданного Key Vault. Запишите значения двух указанных ниже свойств.

- **Имя хранилища.** Имя, которое вы указали в параметре --name выше.
- **URI хранилища**. В нашем примере это https://&lt;your-unique-keyvault-name&gt;.vault.vault.azure.net. Необходимо, чтобы приложения, использующие ваше хранилище через REST API, использовали этот URI.

На этом этапе любые операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="clean-up-resources"></a>Очистка ресурсов

Другие руководства в этой серии созданы на основе этого документа. Если вы планируете продолжить работу с последующими краткими руководствами и статьями, эти ресурсы можно не удалять.

Вы можете удалить ставшую ненужной группу ресурсов и все связанные с ней ресурсы с помощью Azure CLI, выполнив команду [az group delete](/cli/azure/group):

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

С помощью этого краткого руководства вы создали Key Vault и удалили его. Дополнительные сведения о Key Vault и его интеграции в приложения см. в следующих статьях.

- [Обзор Azure Key Vault](overview.md)
- Статья [Обзор системы безопасности Azure Key Vault](security-overview.md)
- Справочник по [командам az keyvault для Azure CLI](/cli/azure/keyvault)

