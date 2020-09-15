---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482146"
---
1. Убедитесь, что у вас есть [учетная запись Azure с активной подпиской](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Установите [Python 2.7 и выше или 3.5.3 и выше](https://www.python.org/downloads).

1. Установка [Azure CLI](/cli/azure/install-azure-cli).

1. Выполните инструкции из раздела [Настройка проверки подлинности для локального развертывания](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication), чтобы создать локальный субъект-службу и сделать его доступным для клиента Azure Key Vault для Python с помощью переменных среды. 

    При выполнении кода непосредственно в Azure отдельный субъект-служба не требуется, если приложение использует управляемое удостоверение.

1. В терминале или в командной строке создайте подходящую папку проекта, а затем создайте и активируйте виртуальную среду Python, как описано в разделе [Использование виртуальных окружений Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments).

1. Установите библиотеку удостоверений Azure Active Directory:

    ```terminal
    pip install azure.identity
    ```
