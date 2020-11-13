---
title: Подключение защитника Майкрософт для данных удостоверения (ранее — Azure ATP) к Azure Sentinel | Документация Майкрософт
description: Узнайте, как выполнять потоковую передачу журналов из защитника Майкрософт для удостоверения (ранее — Azure Advanced Threat protection) (ATP) в Azure Sentinel одним щелчком мыши.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 319e0d29ef297f2a675128d498820a2d5b8ac42e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579527"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Подключение данных из защитника Майкрософт к удостоверениям (ранее — Azure Advanced Threat protection)

> [!IMPORTANT]
> Microsoft Defender для соединителя удостоверений данных в Azure Sentinel в настоящее время находится в общедоступной предварительной версии.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Вы можете выполнить потоковую передачу журналов из [защитника Майкрософт для идентификации](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) в Azure Sentinel одним щелчком мыши.

## <a name="prerequisites"></a>Предварительные требования

- Пользователь с разрешениями глобального администратора или администратора безопасности
- Вы должны быть предварительным клиентом защитника Майкрософт для идентификации и включения интеграции между защитником Майкрософт для идентификации и Microsoft Cloud App Security. Дополнительные сведения см. в [статье защитник Майкрософт для интеграции удостоверений](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection).

## <a name="connect-to-microsoft-defender-for-identity"></a>Подключение к защитнику Майкрософт для идентификации

Убедитесь, что в [сети включен](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Microsoft Defender для предварительной версии удостоверения.
Если защитник Майкрософт для идентификации развернут и принимает данные, подозрительные оповещения можно легко передать в Azure Sentinel. Запуск потоковой передачи в Azure Sentinel может занять до 24 часов.


1. Чтобы подключить Microsoft Defender для удостоверения к Azure Sentinel, сначала необходимо включить интеграцию между защитником Майкрософт для идентификации и Microsoft Cloud App Security. Сведения о том, как это сделать, см. в разделе [защитник Майкрософт для интеграции удостоверений](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection).

1. В поле Sentinel Azure выберите **соединители данных** , а затем щелкните плитку **защитник Майкрософт для идентификации (Предварительная версия)** .

1. Вы можете выбрать, следует ли автоматически автоматически создавать инциденты в Azure Sentinel с помощью оповещений от защитника Майкрософт. В разделе **Создать инциденты** выберите **Разрешить** , чтобы включить правило аналитики по умолчанию, которое автоматически создает инциденты на основе оповещений, созданных в подключенной службе безопасности. Затем это правило можно изменить в разделе **Аналитика** , а затем **Активные правила**.

1. Нажмите кнопку **Соединить**.

1. Чтобы использовать соответствующую схему в Log Analytics защитника Майкрософт для оповещений об удостоверениях, выполните поиск по запросу **секуритялерт**.

> [!NOTE]
> Если размер оповещений превышает 30 КБ, то Azure Sentinel прекращает отображать поле сущности в предупреждениях.

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как подключить защитник Майкрософт для идентификации в Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats-built-in.md).

