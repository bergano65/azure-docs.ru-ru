---
title: Примеры Azure CLI
description: Таблица, содержащая ссылки на примеры скриптов bash, созданных с помощью Azure CLI, например создание масштабируемого набора и управление им.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 540941bd2d54c5d3c8d26186e700727e8d75f501
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459353"
---
# <a name="azure-cli-samples-for-virtual-machine-scale-sets"></a>Примеры Azure CLI для масштабируемых наборов виртуальных машин

В следующей таблице содержатся ссылки на скрипты Bash, созданные с помощью Azure CLI.

| | |
|---|---|
|**Создание масштабируемого набора и управление им**||
| [Создание масштабируемого набора виртуальных машин](scripts/cli-sample-create-simple-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Создание масштабируемого набора виртуальных машин с минимальной конфигурацией. |
| [Создание масштабируемого набора на основе пользовательского образа виртуальной машины](scripts/cli-sample-create-scale-set-from-custom-image.md?toc=%2fcli%2fmodule%2ftoc.json) | Создание масштабируемого набора виртуальных машин, использующего пользовательский образ виртуальной машины. |
| [Установка приложений в масштабируемом наборе](scripts/cli-sample-install-apps.md?toc=%2fcli%2fmodule%2ftoc.json) | Чтобы установить простое веб-приложение в масштабируемом наборе, используйте расширение пользовательских скриптов Azure. |
|**Управление хранилищем**||
| [Создание дисков и их подключение к масштабируемому набору](scripts/cli-sample-attach-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Создание масштабируемого набора виртуальных машин с подключенными дисками данных. |
|**Управление масштабированием и избыточностью**||
| [Включение автомасштабирования на основе узла](scripts/cli-sample-enable-autoscale.md?toc=%2fcli%2fazure%2ftoc.json) | Создание масштабируемого набора виртуальных машин, настроенного для автоматического масштабирования в зависимости от загрузки ЦП. |
| [Создание однозонного масштабируемого набора](scripts/cli-sample-single-availability-zone-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Создание масштабируемого набора виртуальных машин, который использует одну зону доступности. |
| [Создание масштабируемого набора, избыточного между зонами](scripts/cli-sample-zone-redundant-scale-set.md?toc=%2fcli%2fazure%2ftoc.json) | Создание масштабируемого набора виртуальных машин, который использует несколько зон доступности. |
| | |