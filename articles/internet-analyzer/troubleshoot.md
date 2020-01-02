---
title: Устранение неполадок Azure Internet Analyzer | Документация Майкрософт
description: Справочник по устранению неполадок для Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a7216e697680bcc049d2ceb4caec74adfc1760b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897394"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Устранение неполадок Azure Internet Analyzer

Эта статья содержит инструкции по устранению распространенных проблем с анализатором Internet Analyzer.

## <a name="azure-portal"></a>Портал Azure
**"Недостаточно единиц измерения для этой системы показателей" в разделе "показатели"**

Обратите внимание на следующее.
- Данные измерений будут собираться только в том случае, если клиентский скрипт профиля Internet Analyzer внедрен в приложение, получающее реальный пользовательский трафик. Искусственный трафик (например, тесты производительности Azure WebApp) обычно не выполняет внедренный код JavaScript, поэтому никакие измерения не будут создаваться этим типом трафика.
- Временные ряды создаются один раз в час, поэтому необходимо подождать хотя бы время, необходимое для отображения новых данных измерения.
- Системы показателей создаются ежедневно (в конце каждого дня, в формате UTC).
- Системы показателей создаются, только если для выбранной комбинации фильтров было собрано более 100 измерений (тест, период времени, страна и т. д.).

## <a name="next-steps"></a>Дальнейшие действия
[Вопросы и ответы об Анализаторе Интернета](internet-analyzer-faq.md)
