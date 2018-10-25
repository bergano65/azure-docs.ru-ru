---
title: Управление доступностью виртуальных машин Linux в Azure | Документация Майкрософт
description: Описывается, как использовать несколько виртуальных машин для обеспечения высокой доступности приложения Linux в Azure.
services: virtual-machines-linux
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: zarhoads
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca339b7e4b4eb6d42f3955013499150b1534ea2a
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49464703"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Управление доступностью виртуальных машин Linux

Изучите способы настройки нескольких виртуальных машин и управления ими для обеспечения высокой доступности приложения Linux в Azure. Вы также можете [управлять доступностью виртуальных машин Windows](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Инструкции по созданию группы доступности с использованием интерфейса командной строки в модели развертывания с помощью Resource Manager см. в разделе [Azure Availset. Команды для управления группами доступности](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Дополнительная информация
Чтобы больше узнать о балансировке нагрузки виртуальных машин, ознакомьтесь с разделом [Балансировка нагрузки виртуальных машин](../virtual-machines-linux-load-balance.md).

