---
title: Microsoft Угроза Моделирования Релиз 03/22/2020 - Azure
description: Документирование заметок о выпуске средства моделирования угроз
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146866"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Угроза Моделирование Инструмент обновления релиз 7.3.00316.1 - 03/22/2020

Версия 7.3.00316.1 инструмента моделирования угроз Microsoft (TMT) была выпущена 22 марта 2020 года и содержит следующие изменения:

- Улучшение специальных возможностей
- Исправления ошибок
- Новая функция ДиаграммаРидер

## <a name="notable-bug-fixes"></a>Известные исправления ошибок

### <a name="exporting-the-threat-list-to-csv"></a>Экспорт списка угроз в CSV

Экспорт в функцию CSV непоследовательно выбирал, какие месторождения из списка угроз будут экспортироваться. Теперь все поля из списка угроз будут экспортироваться в файл CSV. 

### <a name="ux-bugs"></a>ОШИБКи UX

- Меню справки в основном рабочем процессе (создание/открытие/анализ) и опыт редактора шаблонов теперь имеют согласованные параметры меню.
- Панель поиска в трафаретном стекле теперь имеет стандартный курсор и соответствующие метки были добавлены.

## <a name="new-features"></a>новые функции;

### <a name="diagramreader-feature-has-been-added"></a>Добавлена функция ДиаграммаReader

Новая функция Диаграммарид была добавлена в основное меню во время открытия модели. Эта функция преобразует графическое представление модели в текстовое повествование. 

## <a name="system-requirements"></a>Требования к системе

- Поддерживаемые операционные системы:
  - [Microsoft Windows 10 Годовщина Обновление](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) или позже
- Требуется версия .NET:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) или позже
- Дополнительные требования:
  - Подключение к Интернету для получения обновлений для инструмента, а также шаблонов

## <a name="documentation-and-feedback"></a>Документация и отзывы

- Документация по Threat Modeling Tool находится на сайте [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) и содержит информацию [об использовании средства](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started).

## <a name="next-steps"></a>Дальнейшие действия

Скачайте последнюю версию [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool).
