---
title: Исправление рекомендаций в центре безопасности Azure | Документация Майкрософт
description: В этом документе объясняется, как исправить рекомендации в центре безопасности Azure, чтобы защитить ресурсы Azure и обеспечить соответствие политикам безопасности.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2019
ms.author: v-mohabe
ms.openlocfilehash: b947796d4af9def1a274f6e04f356c3173d29fff
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778997"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Исправление рекомендаций в центре безопасности Azure

Рекомендации помогут вам лучше защитить свои ресурсы.  Рекомендации реализуются путем выполнения действий по исправлению, приведенных в рекомендации. 

## Действия по исправлению<a name="remediation-steps"></a>

После просмотра всех рекомендаций решите, какие из них следует исправить первыми. Мы рекомендуем использовать [влияние на показатель безопасности](security-center-recommendations.md#monitor-recommendations) , чтобы помочь в определении приоритетов.

1. В списке выберите рекомендацию.
1. Следуйте инструкциям в разделе **действия** по исправлению. Каждая рекомендация имеет собственный набор инструкций. Ниже приведены действия по исправлению для настройки приложений, разрешающие трафик только по протоколу HTTPS.

    ![Сведения о рекомендации](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. После завершения исправления появится уведомление о том, что исправление выполнено успешно.

## <a name="next-steps"></a>Следующие шаги

В этом документе было показано, как исправить рекомендации в центре безопасности. Дополнительные сведения о центре безопасности см. в следующих разделах:

* [Настройка политик безопасности в центре безопасности Azure](tutorial-security-policy.md) — сведения о настройке политик безопасности для подписок и групп ресурсов Azure.
* [Наблюдение за работоспособностью системы безопасности в Центре безопасности Azure](security-center-monitoring.md) — узнайте, как отслеживать работоспособность ресурсов Azure.
