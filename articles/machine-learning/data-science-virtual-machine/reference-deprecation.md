---
title: 'Ссылка: прекращение использования изображения DSVM'
description: Сведения об устаревании, затрагивающих виртуальную машину Azure для обработки и анализа данных (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 252f9c66034dbadaf7a2e9e6f78665c26d414deb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456284"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Справочник: устаревшие образы DSVM

Ниже обсуждаются предложения по устранению нерекомендуемых версий на виртуальной машине Azure для обработки и анализа данных.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: перенос дисков данных

Мы будем прекращать поддержку образа Windows 2012 DSVM 31 декабря 2019. Чтобы перенести диск данных из существующего Windows 2012 DSVM в DSVM Windows 2016, выполните следующие действия.

1. Создайте новый DSVM Windows 2016, следуя приведенным [здесь](./provision-vm.md#create-your-dsvm)инструкциям.
1. Используйте [эти инструкции](../../virtual-machines/windows/detach-disk.md)для отключения существующих дисков данных из образа Windows 2012.
1. Присоедините диск из предыдущего шага к образу Windows 2016 с помощью [этих инструкций](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
