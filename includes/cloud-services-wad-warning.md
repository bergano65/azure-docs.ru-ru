---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66125388"
---
> [!WARNING]
> Когда вы включаете диагностику в существующей роли, при развертывании пакета все настроенные расширения будут отключены. в частности такие:
>
> * диагностика агента мониторинга Microsoft;
> * мониторинг системы безопасности Microsoft Azure;
> * Антивредоносное ПО Майкрософт                 
> * Microsoft Monitoring Agent
> * агент профилировщика службы Microsoft;      
> * расширение домена Microsoft Azure;        
> * расширение Диагностики Azure для Windows;   
> * расширение удаленного рабочего стола Microsoft Azure;
> * сборщик журналов Microsoft Azure.
>
> Развернув обновленную роль, вы сможете сбросить расширения с использованием портала Azure или PowerShell.
>
