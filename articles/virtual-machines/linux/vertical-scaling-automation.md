---
title: Вертикальное масштабирование виртуальной машины Azure c помощью службы автоматизации Azure | Документация Майкрософт
description: Как вертикально масштабировать виртуальную машину Linux в ответ на предупреждения мониторинга c помощью службы автоматизации Azure
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e08d0e2084f2310623391a3b20f0f09df4c419e8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103520"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Вертикальное масштабирование виртуальной машины Linux в Azure c помощью службы автоматизации Azure
Вертикальное масштабирование — это процесс увеличения или уменьшения объема ресурсов виртуальной машины в зависимости от рабочей нагрузки. В Azure это можно сделать, изменив размер виртуальной машины. Вертикальное масштабирование можно использовать в следующих сценариях:

* если виртуальная машина используется редко, вы можете уменьшить ее размер, чтобы сократить ежемесячные затраты;
* если виртуальная машина испытывает пиковые нагрузки, ее размер можно увеличить, чтобы расширить емкость.

Ниже представлены шаги для реализации вертикального масштабирования.

1. Настройка службы автоматизации Azure для доступа к виртуальным машинам.
2. Импорт модулей Runbook службы автоматизации Azure для вертикального масштабирования в подписку
3. Добавление веб-перехватчика в модуль Runbook.
4. Добавление правила оповещения для виртуальной машины


## <a name="scale-limitations"></a>Ограничения масштабирования

Размеры, до которых можно масштабировать виртуальную машину, могут быть ограничены из-за размера первой виртуальной машины ввиду поддерживаемых размеров кластера, в котором развернута текущая виртуальная машина. В этой статье мы учли эту особенность и в опубликованных модулях Runbook службы автоматизации использовали для масштабирования только приведенные ниже примеры размеров виртуальных машин. Это означает, что нельзя внезапно увеличить виртуальную машину размером Standard_D1v2 до размера Standard_G5 или уменьшить до Basic_A0. Кроме того, размеры виртуальных машин с ограниченным масштабированием не поддерживаются. 

Вы можете выбрать для масштабирования следующие пары размеров:

* [Серия A](#a-series)
* [Серия B](#b-series)
* [Серия D](#d-series)
* [Серия E](#e-series)
* [Серия F](#f-series)
* [Серия G](#g-series)
* [Серия H](#h-series)
* [Серия L](#l-series)
* [Серия M](#m-series)
* [Серия N](#n-series)

### <a name="a-series"></a>Серия A

| Начальный размер | Увеличить масштаб | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standard_A0 | Standard_A1 |
| Standard_A1 | Standard_A2 |
| Standard_A2 | Standard_A3 |
| Standard_A3 | Standard_A4 |
| Standard_A5 | Standard_A6 |
| Standard_A6 | Standard_A7 |
| Standard_A8 | Standard_A9 |
| Standard_A10 | Standard_A11 |
| Standard_A1_v2 | Standard_A2_v2 |
| Standard_A2_v2 | Standard_A4_v2 |
| Standard_A4_v2 | Standard_A8_v2 |
| Standard_A2m_v2 | Standard_A4m_v2 |
| Standard_A4m_v2 | Standard_A8m_v2 |

### <a name="b-series"></a>Серия B

| Начальный размер | Увеличить масштаб | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>Серия D

| Начальный размер | Увеличить масштаб | 
| --- | --- |
| Standard_D1 | Standard_D2 |
| Standard_D2 | Standard_D3 |
| Standard_D3 | Standard_D4 |
| Standard_D11 | Standard_D12 |
| Standard_D12 | Standard_D13 |
| Standard_D13 | Standard_D14 |
| Standard_DS1 | Standard_DS2 |
| Standard_DS2 | Standard_DS3 |
| Standard_DS3 | Standard_DS4 |
| Standard_DS11 | Standard_DS12 |
| Standard_DS12 | Standard_DS13 |
| Standard_DS13 | Standard_DS14 |
| Standard_D1_v2 | Standard_D2_v2 |
| Standard_D2_v2 | Standard_D3_v2 |
| Standard_D3_v2 | Standard_D4_v2 |
| Standard_D4_v2 | Standard_D5_v2 |
| Standard_D11_v2 | Standard_D12_v2 |
| Standard_D12_v2 | Standard_D13_v2 |
| Standard_D13_v2 | Standard_D14_v2 |
| Standard_DS1_v2 | Standard_DS2_v2 |
| Standard_DS2_v2 | Standard_DS3_v2 |
| Standard_DS3_v2 | Standard_DS4_v2 |
| Standard_DS4_v2 | Standard_DS5_v2 |
| Standard_DS11_v2 | Standard_DS12_v2 |
| Standard_DS12_v2 | Standard_DS13_v2 |
| Standard_DS13_v2 | Standard_DS14_v2 |
| Standard_D2_v3 | Standard_D4_v3 |
| Standard_D4_v3 | Standard_D8_v3 |
| Standard_D8_v3 | Standard_D16_v3 |
| Standard_D16_v3 | Standard_D32_v3 |
| Standard_D32_v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standard_D8s_v3 |
| Standard_D8s_v3 | Standard_D16s_v3 |
| Standard_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>Серия E

| Начальный размер | Увеличить масштаб | 
| --- | --- |
| Standard_E2_v3 | Standard_E4_v3 |
| Standard_E4_v3 | Standard_E8_v3 |
| Standard_E8_v3 | Standard_E16_v3 |
| Standard_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standard_E32_v3 |
| Standard_E32_v3 | Standard_E64_v3 |
| Standard_E2s_v3 | Standard_E4s_v3 |
| Standard_E4s_v3 | Standard_E8s_v3 |
| Standard_E8s_v3 | Standard_E16s_v3 |
| Standard_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standard_E32s_v3 |
| Standard_E32s_v3 | Standard_E64s_v3 |

### <a name="f-series"></a>Серия F

| Начальный размер | Увеличить масштаб | 
| --- | --- |
| Standard_F1 | Standard_F2 |
| Standard_F2 | Standard_F4 |
| Standard_F4 | Standard_F8 |
| Standard_F8 | Standard_F16 |
| Standard_F1s | Standard_F2s |
| Standard_F2s | Standard_F4s |
| Standard_F4s | Standard_F8s |
| Standard_F8s | Standard_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>Серия G

| Начальный размер | Увеличить масштаб | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard_G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Standard_GS5 |

### <a name="h-series"></a>Серия H

| Начальный размер | Увеличить масштаб | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>Серия L

| Начальный размер | Увеличить масштаб | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>Серия M

| Начальный размер | Увеличить масштаб | 
| --- | --- |
| Standard_M8ms | Standard_M16ms |
| Standard_M16ms | Standard_M32ms |
| Standard_M32ms | Standard_M64ms |
| Standard_M64ms | Standard_M128ms |
| Standard_M32ls | Standard_M64ls |
| Standard_M64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>Серия N

| Начальный размер | Увеличить масштаб | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |
| Standard_NV12s_v3 |Standard_NV48s_v3 |


## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Настройка службы автоматизации Azure для доступа к виртуальным машинам.
Сначала необходимо создать учетную запись службы автоматизации Azure, в которой будут размещаться модули Runbook, используемые для масштабирования набора виртуальных машин. Недавно в службе автоматизации появилась функция "Запуск от имени...", которая упрощает настройку субъекта-службы для автоматического запуска модулей Runbook от имени пользователя. Дополнительные сведения об этом см. в следующей статье:

* [Проверка подлинности модулей Runbook в Azure с помощью учетной записи запуска от имени](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Импорт модулей Runbook службы автоматизации Azure для вертикального масштабирования в подписку
Модули Runbook, необходимые для вертикального масштабирования виртуальной машины, уже опубликованы в коллекции Runbook службы автоматизации Azure. Вам потребуется импортировать их в подписку. Дополнительные сведения об импорте модулей Runbook см. в статье:

* [Runbook и коллекции модулей для службы автоматизации Azure](../../automation/automation-runbook-gallery.md)

На приведенном ниже рисунке показаны модули Runbook, которые необходимо импортировать.

![Импорт модулей Runbook](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Добавление веб-перехватчика в модуль Runbook.
После импорта модулей Runbook в них необходимо добавить веб-перехватчик. Этого может потребовать система оповещений виртуальной машины. Дополнительные сведения о создании веб-перехватчика для вашего модуля Runbook см. в статье:

* [Объекты Webhook в службе автоматизации Azure](../../automation/automation-webhooks.md)

Прежде чем закрывать диалоговое окно, убедитесь, что вы скопировали веб-перехватчик. Он понадобится в следующем разделе.

## <a name="add-an-alert-to-your-virtual-machine"></a>Добавление правила оповещения для виртуальной машины
1. Перейдите в раздел параметров виртуальной машины.
2. Щелкните "Правила оповещения".
3. Щелкните "Добавить оповещение".
4. Выберите метрику, на основе значения которой будут срабатывать оповещения.
5. Выберите условие, при выполнении которого будет срабатывать оповещение.
6. Установите пороговое значение для условия, упомянутого выше.
7. Выберите период для проверки условий и порогового значения, упомянутых выше.
8. Вставьте скопированный веб-перехватчик, описанный в предыдущем разделе.

![Добавить правило оповещения для виртуальной машины 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Добавить правило оповещения для виртуальной машины 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

