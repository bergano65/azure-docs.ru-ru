---
title: 'Ссылка: прекращение использования изображения DSVM'
description: Сведения об устаревании, затрагивающих виртуальную машину Azure для обработки и анализа данных (DSVM)
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 4968ac08b75141ed84994ca287215a34728232c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333343"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Справочник: устаревшие образы DSVM

Ниже обсуждаются предложения по устранению нерекомендуемых версий на виртуальной машине Azure для обработки и анализа данных.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: перенос дисков данных

Поддержка образа DSVM для Windows 2012 будет прервана 5 ноября 2019. Чтобы перенести диск данных из существующего Windows 2012 DSVM в DSVM Windows 2016, выполните следующие действия.

1. Создайте новый DSVM Windows 2016, следуя приведенным [здесь](./provision-vm.md#create-your-dsvm)инструкциям.
1. Используйте [эти инструкции](../../virtual-machines/windows/detach-disk.md)для отключения существующих дисков данных из образа Windows 2012.
1. Присоедините диск из предыдущего шага к образу Windows 2016 с помощью [этих инструкций](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
