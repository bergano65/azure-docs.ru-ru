---
title: Создание пользовательского образа виртуальной машины для Azure Marketplace | Документация Майкрософт
description: Перечень шагов и указаний, необходимых для создания пользовательского образа виртуальной машины.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: bf87856dc28e83fb1308f20613338b9bbfd8f896
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196461"
---
# <a name="create-a-user-vm-image"></a>Создание пользовательского образа виртуальной машины

В этой статье объясняются два основных этапа, необходимые для создания неуправляемого образа из универсального виртуального жесткого диска.  Предоставленные ссылки помогают выполнить каждый из шагов: создание образа и его подключение.


## <a name="capture-the-vm-image"></a>Запись образа виртуальной машины

Используйте инструкции в статье о записи виртуальной машины, которая соответствует вашему подходу к получению доступа:

-  PowerShell: [Создание неуправляемого образа виртуальной машины на основе виртуальной машины Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [Создание образа виртуальной машины или виртуального жесткого диска](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtual Machines — Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture) (Виртуальные машины. Запись)


## <a name="generalize-the-vm-image"></a>Обобщение образа виртуальной машины

Так как вы создали пользовательский образ на основе обобщенного ранее диска VHD, он также должен быть обобщен.  Опять выберите статью, которая соответствует вашему механизму доступа.  (Возможно, вы уже обобщили диск во время записи.)

-  PowerShell: [Подготовка виртуальной машины к использованию](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [Шаг 2. Создайте образ виртуальной машины](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtual Machines — Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize) (Виртуальные машины. Подготовка)


## <a name="next-steps"></a>Дополнительная информация

После этого следует [создать сертификат](cpp-create-key-vault-cert.md) и сохранить его в новом хранилище Azure Key Vault.  Этот сертификат является обязательным для создания безопасного подключения WinRM к виртуальной машине.
