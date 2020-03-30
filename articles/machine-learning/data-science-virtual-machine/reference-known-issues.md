---
title: 'Справка: Известные проблемы & устранению неполадок'
titleSuffix: Azure Data Science Virtual  Machine
description: Получите список известных проблем, обходных пути и устранения неполадок для виртуальной машины Azure Data Science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206526"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Известные проблемы и устранение неполадок в виртуальной машине Azure Data Science

Эта статья поможет вам найти и исправить ошибки или сбои, с которые вы можете встретить при использовании виртуальной машины Azure Data Science.

## <a name="python-package-installation-issues"></a>Проблемы с установкой пакета Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Установка пакетов с пипсом нарушает зависимость от Linux

Используйте `sudo pip install` `pip install` вместо установки пакетов.

## <a name="disk-encryption-issues"></a>Проблемы с шифрованием дисков

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Шифрование диска выходит из строя на Ubuntu DSVM

Шифрование azure Disk (ADE) в настоящее время не поддерживается на Ubuntu DSVM. В качестве обходного пути рассмотрите возможность настройки [шифрования серверной стороны управляемых дисков Azure.](../../virtual-machines/windows/disk-encryption.md)

## <a name="tool-appears-disabled"></a>Инструмент отображается отключенным

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V не работает на Windows DSVM

То, что Hyper-V изначально не работает на Windows, как ожидается, поведение. Для загрузки производительности, мы отключили некоторые услуги. Для включения Hyper-V:

1. Откройте панель поиска на Windows DSVM
1. Введите в "Услуги",
1. Установите все услуги Hyper-V на "Руководство"
1. Набор "Гипер-V Виртуальное управление машиной" на "Автоматическая"

Последний экран должен выглядеть следующим образом:

   ![Включение Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

