---
title: Microsoft Угроза Моделирования Релиз 02/11/2020 - Azure
description: Документирование заметок о выпуске средства моделирования угроз
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 354707aec90375b4bf25aea6e1baa743d85f20aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624843"
---
# <a name="threat-modeling-tool-update-release-73002061---02112020"></a>Угроза Моделирование Инструмент обновления релиз 7.3.00206.1 - 02/11/2020

Версия 7.3.00206.1 инструмента моделирования угроз Microsoft (TMT) была выпущена 11 февраля 2020 года и содержит следующие изменения:

- Исправления ошибок

## <a name="notable-bug-fixes"></a>Известные исправления ошибок

### <a name="errors-related-to-priority-values-outside-of-the-expected-ranges"></a>Ошибки, связанные с значениями приоритета за пределами ожидаемых диапазонов

Некоторые клиенты сообщили о получении следующего сообщения об ошибке при открытии файлов, созданных в "Инструментмоделирования угроз 2016" или пользовательских шаблонов:

    System.InvalidOperationException: Invalid Priority value. Accepted values are [0..4] and 'High', 'Medium', 'Low' at ThreatModeling.Model.Threat.get_Priority()
    
    System.ArgumentOutOfRangeException: Accepted values are 'High', 'Medium', and 'Low' Parameter name: value Actual value was 5.6. at ThreatModeling.Model.Threat.set_Priority(String value)

Эта проблема была решена в этом выпуске.

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
