---
title: Microsoft Угроза Моделирование релиз 10/16/2019 - Azure
description: Документирование заметок о выпуске средства моделирования угроз
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 10/16/2019
ms.openlocfilehash: 452b44653775a1bcb9456b62e1587b5ff2dff874
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552055"
---
# <a name="threat-modeling-tool-update-release-71610151---10162019"></a>Threat Modeling Tool, выпуск обновления 7.1.61015.1 от 16.10.2019

Версия 7.1.61015.1 инструмента моделирования угроз Microsoft (TMT) была выпущена 16 октября 2019 года и содержит следующие изменения:

- Улучшение специальных возможностей
- Исправления ошибок
- Новые трафареты для приложений логики Azure и исследователя данных Azure

## <a name="notable-bug-fixes"></a>Известные исправления ошибок

### <a name="improved-backward-compatibility-with-files-created-in-threat-modeling-tool-2016"></a>Улучшенная обратная совместимость с файлами, созданными в "Инструмент моделирования угроз 2016"

Исправлено несколько ошибок, связанных с открытием или отображением файлов модели угроз, созданных в "Инструменте моделирования угроз 2016".

## <a name="feature-enhancements"></a>Улучшения функций

### <a name="new-stencils-for-azure-logic-apps-and-azure-data-explorer"></a>Новые трафареты для приложений логики Azure и исследователя данных Azure

Новые трафареты для приложений Azure Logic Apps и Azure Data Explorer были добавлены в Azure Stencil вместе с связанными с ними угрозами и смягчением последствий.

![Приложения для логики Azure и трафареты Explorer данных Azure](./media/threat-modeling-tool-releases-71610151/tmt-logic-apps.png)

## <a name="known-issues"></a>Известные проблемы

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Ошибки, связанные с значениями приоритета за пределами ожидаемых диапазонов

Некоторые клиенты сообщили о получении следующего сообщения об ошибке при открытии файлов, созданных в "Инструментмоделирования угроз 2016" или пользовательских шаблонов:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Этот вопрос находится в стадии расследования

## <a name="system-requirements"></a>Требования к системе

- Поддерживаемые операционные системы
  - [Microsoft Windows 10 Годовщина Обновление](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) или позже
- Требуемая версия .NET
  - [.Net 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) или позже
- Дополнительные требования
  - Для получения обновлений средства и шаблонов требуется подключение к Интернету.

## <a name="documentation-and-feedback"></a>Документация и отзывы

- Документация по Threat Modeling Tool находится на сайте [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) и содержит информацию [об использовании средства](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Дальнейшие действия

Скачайте последнюю версию [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
