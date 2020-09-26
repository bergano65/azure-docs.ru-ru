---
title: Устранение проблем с пулом мощностей для Azure NetApp Files | Документация Майкрософт
description: Описывает потенциальные проблемы, которые могут возникнуть при управлении пулами ресурсов, и предоставляет решения для этих проблем.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 0b5558501042dd7816202ea05b3a332b23400ff4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91345503"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Устранение проблем с пулом ресурсов

В этой статье описываются способы устранения проблем, которые могут возникнуть при управлении пулами ресурсов. 

## <a name="issues-creating-a-capacity-pool"></a>Проблемы при создании пула ресурсов

Убедитесь, что число пулов емкости не превышает предельное значение. См. раздел [ограничения ресурсов для Azure NetApp Files](azure-netapp-files-resource-limits.md).  Если число меньше ограничения, но возникают проблемы, отправьте запрос в службу поддержки и укажите имя пула емкости.

## <a name="issues-deleting-a-capacity-pool"></a>Проблемы при удалении пула ресурсов

Убедитесь, что удалены все Azure NetApp Files тома и моментальные снимки в подписке, в которую вы пытаетесь удалить пул емкости.   

Если вы уже удалили все тома и моментальные снимки, и вы по-прежнему не можете удалить пул ресурсов, ссылки на ресурсы могут по-прежнему существовать без отображения на портале. В этом случае отправьте запрос в службу поддержки и укажите, что вы выполнили рекомендованные действия. 

## <a name="volume-creation-or-modification-fails-with-requested-throughput-not-available-error"></a>Сбой создания или изменения тома с ошибкой "запрошенная пропускная способность недоступна"

Доступная пропускная способность для тома определяется размером пула ресурсов и уровнем обслуживания. Если пропускная способность не достаточна, следует увеличить размер пула или изменить пропускную способность существующего тома.


## <a name="next-steps"></a>Дальнейшие действия  

* [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
* [Управление пулом емкости QoS вручную](manage-manual-qos-capacity-pool.md)
