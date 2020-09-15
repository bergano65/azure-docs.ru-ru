---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482147"
---
1. Для создания группы ресурсов используйте команду `az group create`:

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    При необходимости вы можете изменить расположение eastus на ближайшее к вам.

1. Для создания хранилища ключей используйте `az keyvault create`:

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    Замените `<your-unique-keyvault-name>` именем, уникальным в пределах Azure. Обычно используется личное имя или название организации, а также числа и идентификаторы. 

1. Создайте переменную среды, которая предоставляет имя Key Vault коду:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
