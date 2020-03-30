---
title: 'Справка: Наука данных Виртуальная машина Изображение Амортизацию'
titleSuffix: Azure Data Science Virtual Machine
description: Подробная информация об амортизирующих устройствах, влияющих на виртуальную машину Azure Data Science
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525793"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Справка: Уничтожение Изображений DSVM

Ниже мы обсудим предложения по борьбе с предстоящими обесценениями на виртуальной машине Azure Data Science.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Миграционные диски данных

Мы прекратим поддерживать изображение Windows 2012 DSVM 31 декабря 2019 года. Для того, чтобы перенести диск данных с существующей Windows 2012 DSVM на Windows 2016 DSVM, предпримите следующие шаги:

1. Создайте новую Windows 2016 DSVM, следуя инструкциям, приведенным [здесь.](./provision-vm.md#create-your-dsvm)
1. Спомощью существующих дисков данных с изображения Windows 2012 с помощью [этих инструкций.](../../virtual-machines/windows/detach-disk.md)
1. Прикрепите диск с предыдущего шага к изображению Windows 2016 с помощью [этих инструкций.](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)
