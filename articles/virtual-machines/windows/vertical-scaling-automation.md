---
title: Вертикальное масштабирование виртуальных машин Windows c помощью службы автоматизации Azure | Документация Майкрософт
description: Вертикальное масштабирование виртуальной машины Windows в ответ на оповещения мониторинга c помощью службы автоматизации Azure
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 44243f97b6c431578185fcdeb1ddcabc548d927f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011747"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Вертикальное масштабирование виртуальных машин Windows с помощью службы автоматизации Azure

Вертикальное масштабирование — это процесс увеличения или уменьшения объема ресурсов виртуальной машины в зависимости от рабочей нагрузки. В Azure это можно сделать, изменив размер виртуальной машины. Вертикальное масштабирование можно использовать в следующих сценариях:

* если виртуальная машина используется редко, вы можете уменьшить ее размер, чтобы сократить ежемесячные затраты;
* если виртуальная машина испытывает пиковые нагрузки, ее размер можно увеличить, чтобы расширить емкость.

Ниже представлены шаги для реализации вертикального масштабирования.

1. Настройка службы автоматизации Azure для доступа к виртуальным машинам.
2. Импорт модулей Runbook службы автоматизации Azure для вертикального масштабирования в подписку
3. Добавление веб-перехватчика в модуль Runbook.
4. Добавление правила оповещения для виртуальной машины

> [!NOTE]
> Размеры, до которых можно масштабировать виртуальную машину, могут быть ограничены из-за размера первой виртуальной машины ввиду поддерживаемых размеров кластера, в котором развернута текущая виртуальная машина. В этой статье мы учли эту особенность и в опубликованных модулях Runbook службы автоматизации использовали для масштабирования только приведенные ниже примеры размеров виртуальных машин. Это означает, что нельзя внезапно увеличить виртуальную машину размером Standard_D1v2 до размера Standard_G5 или уменьшить до Basic_A0. Также не поддерживается ограниченное виртуальной машины, размеры масштабирование вверх или вниз. Вы можете выбрать для масштабирования следующие пары размеров:
> 
> | Пары размеров виртуальных машин, для которых можно применять вертикальное масштабирование |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2v3 |Standard_E64v3 |
> | Standard_E2sv3 |Standard_E64sv3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> 
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Настройка службы автоматизации Azure для доступа к виртуальным машинам.
Первое, что вам нужно сделать, — создать учетную запись службы автоматизации Azure, где будут размещаться модули Runbook, используемые для масштабирования виртуальной машины. Недавно в службе автоматизации появилась функция "Запуск от имени...", которая упрощает настройку субъекта-службы для автоматического запуска модулей Runbook от имени пользователя. Дополнительные сведения об этом см. в следующей статье:

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

