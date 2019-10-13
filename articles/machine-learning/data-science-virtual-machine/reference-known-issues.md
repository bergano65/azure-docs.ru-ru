---
title: Известные проблемы и устранение неполадок
titleSuffix: Azure Data Science Virtual  Machine
description: Получите список известных проблем, обходных путей и устранение неполадок для виртуальной машины Azure для обработки и анализа данных.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: a0b22326a429edfa2f2b8741453215b42910891c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301923"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Известные проблемы и устранение неполадок виртуальной машины Azure для обработки и анализа данных

Эта статья поможет вам найти и исправить ошибки или сбои, возникшие при использовании виртуальной машины Azure для обработки и анализа данных.

## <a name="python-package-installation-issues"></a>Проблемы при установке пакета Python

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Установка пакетов с разрываются зависимостями в Linux

При установке пакетов используйте `sudo pip install` вместо `pip install`.

## <a name="disk-encryption-issues"></a>Проблемы шифрования диска

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Сбой шифрования диска в Ubuntu DSVM

Шифрование дисков Azure (ADE) в настоящее время не поддерживается в Ubuntu DSVM. В качестве обходного решения рассмотрите возможность настройки [шифрования службы хранилища Azure с помощью управляемых клиентом ключей](../../storage/common/storage-encryption-keys-portal.md).

## <a name="tool-appears-disabled"></a>Средство отображается как отключено

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V не работает в DSVM Windows

Это ожидаемое поведение, так как для производительности загрузки мы отключили некоторые службы. Чтобы снова включить, откройте панель поиска в DSVM Windows, введите "службы", а затем задайте для всех служб Hyper-V значение "вручную" и установите для параметра "Управление виртуальными машинами Hyper-V" значение "автоматически".

Окончательный экран должен выглядеть следующим образом:

   ![Включение Hyper-V](./media/workaround/hyperv-enable-dsvm.png)

