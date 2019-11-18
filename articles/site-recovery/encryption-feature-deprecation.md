---
title: Нерекомендуемая функция шифрования данных Azure Site Recovery | Документация Майкрософт
description: Подробные сведения регариг Azure Site Recovery функции шифрования данных
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135000"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Нерекомендуемая функция шифрования данных Site Recovery

В этом документе описаны сведения об устаревании и действиях по исправлению, которые необходимо выполнить при использовании функции шифрования данных Site Recovery при настройке аварийного восстановления виртуальных машин Hyper-V в Azure. 

## <a name="deprecation-information"></a>Сведения об устаревании


Функция шифрования данных Site Recovery была доступна клиентам, которые защищают виртуальные машины Hyper-V, чтобы гарантировать, что реплицированные данные были защищены от угроз безопасности. Эта функция будет признана устаревшей **до 30 декабря 2019**. Он заменяется функцией более расширенного [шифрования неактивных](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) компонентов, которая использует [Шифрование службы хранилища](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE). При использовании SSE данные шифруются до сохранения в хранилище и расшифровки при извлечении, а при отработке отказа в Azure виртуальные машины будут запускаться из зашифрованных учетных записей хранения, что позволит улучшить целевое время восстановления (RTO).

Обратите внимание, что если вы являетесь клиентом, использующим эту функцию, вы получите связь со сведениями об устаревании и действиями по исправлению. 


## <a name="what-are-the-implications"></a>Каковы последствия?

После **30 декабря 2019**виртуальные машины, которые по-прежнему используют отмененное шифрование, не смогут выполнять отработку отказа. 

## <a name="required-action"></a>Требуемое действие
Чтобы продолжить выполнение операций отработки отказа, а репликации выполните следующие действия:

Выполните следующие действия для каждой виртуальной машины. 
1.  [Отключите репликацию](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Создайте новую политику репликации](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Включите репликацию](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) и выберите учетную запись хранения с включенным SSE.

После завершения начальной репликации в учетные записи хранения с включенным SSE виртуальные машины будут использовать шифрование неактивных данных с Azure Site Recovery.


## <a name="next-steps"></a>Дополнительная информация
Спланируйте выполнение действий по исправлению и выполните их в самом раннем случае. Если у вас есть запросы, касающиеся этой нерекомендуемости, обратитесь к служба поддержки Майкрософт. Дополнительные сведения о сценарии Hyper-V в Azure см. [здесь](hyper-v-vmm-architecture.md).

