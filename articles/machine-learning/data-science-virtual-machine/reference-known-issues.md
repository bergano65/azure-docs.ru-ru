---
title: 'Ссылка: известные проблемы & устранении неполадок'
titleSuffix: Azure Data Science Virtual  Machine
description: Получите список известных проблем, обходных путей и устранение неполадок для виртуальной машины Azure для обработки и анализа данных.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206526"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Известные проблемы и устранение неполадок виртуальной машины Azure для обработки и анализа данных

Эта статья поможет вам найти и исправить ошибки или сбои, которые могут возникнуть при использовании виртуальной машины Azure для обработки и анализа данных.

## <a name="python-package-installation-issues"></a>Проблемы при установке пакета Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Установка пакетов с разрываются зависимостями в Linux

При установке пакетов используйте вместо `pip install` `sudo pip install`.

## <a name="disk-encryption-issues"></a>Проблемы с шифрованием дисков

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Сбой шифрования диска в Ubuntu DSVM

Шифрование дисков Azure (ADE) сейчас не поддерживается в Ubuntu DSVM. В качестве обходного решения рекомендуется настроить [Шифрование на стороне сервера для управляемых дисков Azure](../../virtual-machines/windows/disk-encryption.md).

## <a name="tool-appears-disabled"></a>Средство отображается как отключено

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V не работает в DSVM Windows

Изначально Hyper-V не работает в Windows — ожидаемое поведение. Для производительности загрузки мы отключили некоторые службы. Чтобы включить Hyper-V, сделайте следующее:

1. Открытие панели поиска в DSVM Windows
1. Введите "службы",
1. Задать для всех служб Hyper-V значение "вручную"
1. Задайте для "Управление виртуальными машинами Hyper-V" значение "автоматически".

Окончательный экран должен выглядеть следующим образом:

   ![Включение Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

