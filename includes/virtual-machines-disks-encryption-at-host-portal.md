---
title: Включить имя файла
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f0a706cc51027bc503da9ee3bc637216e9e52853
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171355"
---
## <a name="restrictions"></a>Ограничения

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Поддерживаемые регионы

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Поддерживаемые размеры виртуальных машин

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Предварительные требования

Чтобы иметь возможность использовать шифрование на узле для виртуальных машин или масштабируемых наборов виртуальных машин, необходимо включить эту функцию в подписке. Отправьте сообщение электронной почты по адресу encryptionAtHost@microsoft. com с идентификаторами подписки для получения функции, включенной для ваших подписок.

Войдите в портал Azure с помощью [предоставленной ссылки](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Для доступа к портал Azure необходимо использовать [предоставленную ссылку](https://aka.ms/diskencryptionupdates) . Шифрование на узле в настоящее время не отображается в общедоступной портал Azure без использования ссылки.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Создание Azure Key Vault и набора шифрования дисков

После включения этой функции необходимо настроить Azure Key Vault и набор шифрования дисков, если это еще не сделано.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Развертывание виртуальной машины

Чтобы включить шифрование на узле, необходимо развернуть новую виртуальную машину. она не может быть включена на существующих виртуальных машинах.

1. Выполните поиск **виртуальных машин** и выберите **+ Добавить** , чтобы создать виртуальную машину.
1. Создайте новую виртуальную машину, выберите соответствующий регион и поддерживаемый размер виртуальной машины.
1. Заполните остальные значения в колонке **Основная** , а затем перейдите к колонке **диски** .

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Снимок экрана: колонка &quot;основы создания виртуальной машины&quot;, &quot;регион&quot; и &quot;размер V M&quot; выделены.":::

1. В колонке **диски** выберите **Да** для **шифрования на узле**.
1. Сделайте оставшиеся варианты выбора нужным образом.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Снимок экрана: колонка &quot;основы создания виртуальной машины&quot;, &quot;регион&quot; и &quot;размер V M&quot; выделены.":::

1. Завершите процесс развертывания виртуальной машины, чтобы выбрать параметры, соответствующие вашей среде.

Теперь вы развернули виртуальную машину с включенным шифрованием на узле. все связанные с ним диски будут зашифрованы с помощью шифрования на узле.