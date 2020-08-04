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
ms.openlocfilehash: 2c71e96dc1c92fc94d403e7c721b2b9f3149b8e9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101153"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Краткое руководство. Создание хранилища ключей с помощью Azure CLI

Azure Key Vault — это облачная служба, которая предоставляет защищенное хранилище для [ключей](../keys/index.yml), [секретов](../secrets/index.yml) и [сертификатов](../certificates/index.yml). Дополнительные сведения о Key Vault см. в [этой статье](overview.md), а сведения о том, что можно хранить в хранилище ключей, см. [здесь](about-keys-secrets-certificates.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Из этого краткого руководства вы узнаете, как создать хранилище ключей с помощью [Azure CLI](/cli/azure/). Azure CLI используется для создания ресурсов Azure и управления ими с помощью скриптов и команд.  Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli).

Чтобы войти в Azure с помощью CLI, введите следующее:

```azurecli
az login
```

Дополнительные сведения о параметрах входа с помощью интерфейса командной строки см. в статье [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *ContosoResourceGroup* в расположении *eastus*.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>Создайте хранилище ключей.

Создайте Key Vault в группе ресурсов, созданной на предыдущем шаге. Необходимо будет указать следующие сведения:

- Имя хранилища ключей: строка длиной от 3 до 24 символов, которая может содержать только цифры (0–9), буквы (a-z, A-Z) и дефисы (-).

  > [!Important]
  > Каждое хранилище ключей должно иметь уникальное имя. В следующих примерах замените <your-unique-keyvault-name> именем своего хранилища ключей.

- Имя группы ресурсов: **myResourceGroup**.
- Расположение: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

В выходных данных командлета будут показаны свойства созданного Key Vault. Запишите значения двух указанных ниже свойств.

- **Имя хранилища.** Имя, которое вы указали в параметре --name выше.
- **URI хранилища**. В нашем примере это https://&lt;your-unique-keyvault-name&gt;.vault.vault.azure.net/. Необходимо, чтобы приложения, использующие ваше хранилище через REST API, использовали этот URI.

На этом этапе любые операции в этом хранилище ключей может выполнять только учетная запись Azure.

## <a name="clean-up-resources"></a>Очистка ресурсов

Другие руководства в этой серии созданы на основе этого документа. Если вы планируете продолжить работу с последующими краткими руководствами и статьями, эти ресурсы можно не удалять.

Вы можете удалить ставшие ненужными группу ресурсов и все связанные с ней ресурсы, выполнив команду [az group delete](/cli/azure/group). Удалите ресурсы следующим образом:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

С помощью этого краткого руководства вы создали Key Vault и удалили его. Дополнительные сведения о Key Vault и его интеграции в приложения см. в следующих статьях.

- [Обзор Azure Key Vault](overview.md)
- Справочник по [командам az keyvault для Azure CLI](/cli/azure/keyvault?view=azure-cli-latest)
- [Рекомендации по Azure Key Vault](best-practices.md)
