---
title: 'Справочник: нерекомендуемый образ виртуальной машины для обработки и анализа данных'
titleSuffix: Azure Data Science Virtual Machine
description: Сведения об устаревании, затрагивающих виртуальную машину Azure для обработки и анализа данных
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80754764"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Справочник: устаревшие образы DSVM

Ниже обсуждаются предложения по устранению нерекомендуемых версий на виртуальной машине Azure для обработки и анализа данных.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: перенос дисков данных

Мы будем прекращать поддержку образа Windows 2012 DSVM 31 декабря 2019. Чтобы перенести диск данных из существующего Windows 2012 DSVM в DSVM Windows 2016, выполните следующие действия.

1. Создайте новый DSVM Windows 2016, следуя приведенным [здесь](./provision-vm.md#create-your-dsvm)инструкциям.
1. Используйте [эти инструкции](../../virtual-machines/windows/detach-disk.md)для отключения существующих дисков данных из образа Windows 2012.
1. Присоедините диск из предыдущего шага к образу Windows 2016 с помощью [этих инструкций](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Новые пользователи должны использовать самые последние образы Ubuntu или Windows. CentOS будет оставаться доступным для использования с существующими шаблонами решений.