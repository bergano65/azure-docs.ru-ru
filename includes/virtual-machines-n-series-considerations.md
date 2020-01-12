---
title: включить файл
description: включить файл
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 202bffb03a73acde67f0d4a03b31aa9a6fbf9824
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901727"
---
## <a name="deployment-considerations"></a>Рекомендации по развертыванию

* Сведения о доступности виртуальных машин серии N по регионам см. на [этой странице](https://azure.microsoft.com/regions/services/).

* Виртуальные машины серии N поддерживают только модель развертывания с помощью Resource Manager.

* Виртуальные машины серии N отличаются типом хранилища Azure, которое поддерживается для их дисков. Виртуальные машины серий NC и NV поддерживают только диски на основе хранилища дисков уровня "Стандартный" (HDD). Виртуальные машины серий NCv2, NCv3, ND, NDv2 и NVv2 поддерживают только диски на основе хранилища дисков ценовой категории "Премиум" (SSD).

* Если вы хотите развернуть большое количество виртуальных машин серии N, мы рекомендуем подписку с оплатой по мере использования или другие варианты покупки. Если вы используете [бесплатную учетную запись Azure](https://azure.microsoft.com/free/), вам доступно ограниченное количество вычислительных ядер Azure.

* Возможно, вам потребуется увеличить квоту на использование ядер (для каждого региона) в подписке Azure и отдельную квоту для ядер серии NC, NCv2, NCv3, ND, NDv2, NV или NVv2. Чтобы увеличить квоту, [отправьте запрос в службу поддержки](../articles/azure-portal/supportability/how-to-create-azure-support-request.md). Это бесплатная услуга. Ограничения по умолчанию отличаются в зависимости от категории подписки.




