---
title: включение файла
description: включение файла
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 0f528117b19a3f26b964dee7ae453fc184c8dddb
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123079"
---
Служба вычислений Azure предлагает размеры виртуальных машин, которые используют оборудование определенного типа и выделяются отдельному клиенту.  Эти размеры лучше всего подходят для рабочих нагрузок, требующих высокого уровня изоляции от других клиентов. Это могут быть рабочие нагрузки, включающие в себя такие элементы, как соблюдение соответствия и нормативных требований.  Клиенты также могут разделить ресурсы этих изолированных виртуальных машин благодаря [поддержке вложенных виртуальных машин в Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).

Использование размера изолированной виртуальной машины гарантирует, что на конкретном экземпляре сервера будет запущена только ваша виртуальная машина.  Ниже перечислены текущие предложения изолированных виртуальных машин:
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

Дополнительные сведения о каждом доступном изолированном размере см. [здесь](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory).