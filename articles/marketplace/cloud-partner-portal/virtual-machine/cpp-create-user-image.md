---
title: Создание изображения VM-пользователя для Azure Marketplace
description: Перечень шагов и указаний, необходимых для создания пользовательского образа виртуальной машины.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6bbee7f53cb9a61b72bdbbd941a3a0401f5b913b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273959"
---
# <a name="create-a-user-vm-image"></a>Создание пользовательского образа виртуальной машины

> [!IMPORTANT]
> С 13 апреля 2020 года мы начнем управление вашим предложением Azure Virtual Machine в Партнерский центр. После миграции вы будете создавать и управлять своими предложениями в Partner Center. Следуйте инструкциям в [создании технических активов Azure Virtual Machine](https://aka.ms/AzureVMTechAsset) для управления мигрированными предложениями.

В этой статье объясняются два основных этапа, необходимые для создания неуправляемого образа из универсального виртуального жесткого диска.  Предоставленные ссылки помогают выполнить каждый из шагов: создание образа и его подключение.


## <a name="capture-the-vm-image"></a>Запись образа виртуальной машины

Используйте инструкции в статье о записи виртуальной машины, которая соответствует вашему подходу к получению доступа:

-  PowerShell: [Создание неуправляемого образа виртуальной машины на основе виртуальной машины Azure](../../../virtual-machines/windows/capture-image-resource.md).
-  Azure CLI: [Создание образа виртуальной машины или виртуального жесткого диска](../../../virtual-machines/linux/capture-image.md).
-  API: [Virtual Machines - Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture) (Виртуальные машины. Запись).


## <a name="generalize-the-vm-image"></a>Обобщение образа виртуальной машины

Так как вы создали пользовательский образ на основе обобщенного ранее диска VHD, он также должен быть обобщен.  Опять выберите статью, которая соответствует вашему механизму доступа.  (Возможно, вы уже обобщили диск во время записи.)

-  PowerShell: [Подготовка виртуальной машины к использованию](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm).
-  Azure CLI: [Шаг 2. Создайте образ виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image).
-  API: [Virtual Machines - Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize) (Виртуальные машины. Подготовка).


## <a name="next-steps"></a>Дальнейшие действия

После этого следует [создать сертификат](cpp-create-key-vault-cert.md) и сохранить его в новом хранилище Azure Key Vault.  Этот сертификат является обязательным для создания безопасного подключения WinRM к виртуальной машине.
