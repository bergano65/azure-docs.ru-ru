---
title: Изнурить функцию шифрования данных сайта Azure Recovery Документы Майкрософт
description: Подробная информация о функции шифрования данных сайта regarig Azure
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74135000"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Задатка функции шифрования данных восстановления сайта

В этом документе описаны сведения об амортизации и действия по исправлению положения, которые необходимо предпринять, если вы используете функцию шифрования данных восстановления сайта при настройке аварийного восстановления виртуальных машин Hyper-V в Azure. 

## <a name="deprecation-information"></a>Информация об угновении


Функция шифрования данных Site Recovery была доступна для клиентов, защищающих Hyper-V vms, чтобы гарантировать, что реплицированные данные защищены от угроз безопасности. эта функция будет обесточена до **30 декабря 2019 года.** Он заменяется более продвинутой [функцией шифрования в Rest,](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) которая использует [шифрование служб хранения](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE). С помощью SSE данные шифруются перед хранением и расшифровкой при поиске, и при переходе в Azure ваши ввожденные вывески будут работать с зашифрованных учетных записей хранилища, что позволяет повысить время восстановления (RTO).

Пожалуйста, обратите внимание, что если вы являетесь существующим клиентом, использующим эту функцию, вы получили бы сообщения с сведениями об амортизации и шагами по исправлению положения. 


## <a name="what-are-the-implications"></a>Каковы последствия?

После **30 декабря 2019**года любые вмхоза, которые все еще используют отставную функцию шифрования, не будут допущены к сбою. 

## <a name="required-action"></a>Требуемые действия
Для продолжения успешных операций по отказу от сбоя и репликации следуют тем шагам, которые были упомянуты ниже:

Следуйте этим шагам для каждого VM: 
1.  [Отключить репликацию](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Создайте новую политику репликации.](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy)
3.  [Включить репликацию](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) и выбрать учетную запись хранилища с включенным SSE.

После завершения первоначальной репликации для учетных записей хранения с включенным SSE, ваши вс-технологий будут использовать шифрование в Rest с восстановлением сайта Azure.


## <a name="next-steps"></a>Дальнейшие действия
Планирование выполнения шагов по исправлению исполните их в самое ближайшее время. В случае, если у вас есть какие-либо вопросы относительно этого амортизм, пожалуйста, обратитесь в службу поддержки Майкрософт. Чтобы узнать больше о сценарии Hyper-V к Azure, обратитесь [здесь](hyper-v-vmm-architecture.md).

