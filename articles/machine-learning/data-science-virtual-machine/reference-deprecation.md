---
title: 'Справочник: нерекомендуемый образ виртуальной машины для обработки и анализа данных'
titleSuffix: Azure Data Science Virtual Machine
description: Сведения об устаревании, затрагивающих виртуальную машину Azure для обработки и анализа данных
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525793"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Справочник: устаревшие образы DSVM

Ниже обсуждаются предложения по устранению нерекомендуемых версий на виртуальной машине Azure для обработки и анализа данных.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: перенос дисков данных

Мы будем прекращать поддержку образа Windows 2012 DSVM 31 декабря 2019. Чтобы перенести диск данных из существующего Windows 2012 DSVM в DSVM Windows 2016, выполните следующие действия.

1. Создайте новый DSVM Windows 2016, следуя приведенным [здесь](./provision-vm.md#create-your-dsvm)инструкциям.
1. Используйте [эти инструкции](../../virtual-machines/windows/detach-disk.md)для отключения существующих дисков данных из образа Windows 2012.
1. Присоедините диск из предыдущего шага к образу Windows 2016 с помощью [этих инструкций](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
