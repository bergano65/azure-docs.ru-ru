---
title: включить файл
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628426"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Доступность по регионам
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Ограничения

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Портал


1. Войдите на [портал Azure](https://portal.azure.com/) и перейдите на диск, который вы хотите сделать.
1. На диске выберите **Создать снимок**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Скриншот. Лезвие диска с выделенным моментальным снимком , так как это то, что вы должны выбрать.":::

1. Выберите группу ресурсов, которая вы хотите использовать, и введите имя.
1. Выберите **инкрементный** и выберите **Обзор и Создайте**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Скриншот. Создайте лезвие моментального снимка, заполните имя и выберите дополнительные, а затем создайте снимок.":::

1. Выберите **Создать**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Скриншот. Страница проверки для вашего снимка, подтвердите свой выбор, а затем создайте снимок.":::

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите увидеть пример кода, демонстрирующий дифференциальную возможность дополнительных снимков, с помощью .NET, см. [Copy Azure Managed Disks в другой регион с дифференциальной возможностью дополнительных снимков.](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)
