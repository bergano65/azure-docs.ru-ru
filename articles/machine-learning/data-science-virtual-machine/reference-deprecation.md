---
title: 'Справка: Наука данных Виртуальная машина Изображение Амортизацию'
titleSuffix: Azure Data Science Virtual Machine
description: Подробная информация об амортизирующих устройствах, влияющих на виртуальную машину Azure Data Science
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754764"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Справка: Уничтожение Изображений DSVM

Ниже мы обсудим предложения по борьбе с предстоящими обесценениями на виртуальной машине Azure Data Science.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Миграционные диски данных

Мы прекратим поддерживать изображение Windows 2012 DSVM 31 декабря 2019 года. Для того, чтобы перенести диск данных с существующей Windows 2012 DSVM на Windows 2016 DSVM, предпримите следующие шаги:

1. Создайте новую Windows 2016 DSVM, следуя инструкциям, приведенным [здесь.](./provision-vm.md#create-your-dsvm)
1. Спомощью существующих дисков данных с изображения Windows 2012 с помощью [этих инструкций.](../../virtual-machines/windows/detach-disk.md)
1. Прикрепите диск с предыдущего шага к изображению Windows 2016 с помощью [этих инструкций.](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)

## <a name="centos"></a>CentOS

Новые пользователи должны использовать самые последние изображения Ubuntu или Windows. CentOS будет по-прежнему доступен для использования с существующими шаблонами решений.