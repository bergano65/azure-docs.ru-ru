---
title: Разработка приложений для Azure Stack | Документация Майкрософт
description: Рекомендации по разработке при создании прототипов приложений в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 3be22e7f8e69ded8ccc8956cc7fd7c6d71fe5fa1
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2018
ms.locfileid: "46497742"
---
# <a name="develop-for-azure-stack"></a>Разработка для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Вы можете сразу приступить к разработке приложений даже при отсутствии доступа к среде Azure Stack. Так как Azure Stack предоставляет службы Microsoft Azure, выполняемые в вашем центре обработки данных, при разработке в среде Azure Stack вы можете использовать те же средства и процессы, что и для Azure. 

## <a name="development-considerations"></a>Вопросы разработки

Подготовившись и ознакомившись с приведенными ниже разделами, вы сможете использовать Azure для эмуляции среды Azure Stack.

* В Azure можно создавать шаблоны Azure Resource Manager, которые можно развертывать в Azure Stack. Ознакомьтесь с [руководством по разработке шаблонов для обеспечения переносимости](azure-stack-develop-templates.md).
* Среды Azure и Azure Stack отличаются уровнем доступности служб и возможностями управления версиями. Вы можете использовать [модуль политики Azure Stack](azure-stack-policy-module.md), чтобы ограничить доступность служб и типы ресурсов Azure для компонентов, доступных в Azure Stack. Ограничив доступность служб, вы обеспечите использование приложениями служб, доступных в Azure Stack.
* [Шаблоны быстрого запуска для Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) — это общие примеры того, как создавать шаблоны, которые можно развертывать как в Azure, так и в Azure Stack.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о разработке в Stack см. в следующих статьях:

- [Рекомендации по работе с шаблонами Azure Resource Manager](azure-stack-develop-templates.md)
- [Шаблоны быстрого запуска Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates)