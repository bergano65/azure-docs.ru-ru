---
title: Включить имя файла
description: включить файл
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80628426"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Доступность по регионам
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Ограничения

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Портал


1. Войдите в [портал Azure](https://portal.azure.com/) и перейдите к диску, который вы хотите создать моментальный снимок.
1. На диске выберите **создать моментальный снимок** .

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Снимок экрана. Колонка диска с выделенным * * + созданием моментального снимка * *, как это необходимо выбрать.":::

1. Выберите группу ресурсов, которую вы хотите использовать, и введите имя.
1. Выберите **добавочный** и выберите **Проверка + создать** .

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Снимок экрана. Создайте колонку моментальный снимок, введите имя и выберите добавочный, а затем создайте моментальный снимок.":::

1. Нажмите кнопку **Создать**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Снимок экрана. Страница проверки для моментального снимка, Подтвердите выборы и Создайте моментальный снимок.":::

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите просмотреть пример кода, демонстрирующий возможность создания добавочных моментальных снимков с помощью .NET, см. статью [копирование резервных копий управляемых дисков Azure в другой регион с помощью разностных моментальных снимков](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
