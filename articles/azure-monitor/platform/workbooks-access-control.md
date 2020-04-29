---
title: Управление доступом к книгам Azure Monitor
description: Упрощение создания сложных отчетов с помощью предварительно созданных и настраиваемых параметризованных книг с контролем доступа на основе ролей
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77658427"
---
# <a name="access-control"></a>Управление доступом

Управление доступом в книгах подразумевает два действия:

* Необходим доступ для чтения данных в книге. Этот доступ контролируется стандартными [ролями Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) в ресурсах, используемых в книге. Книги не указывают и не настраивают доступ к этим ресурсам. Обычно пользователи получают такой доступ к этим ресурсам с помощью роли [читателя мониторинга](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) для этих ресурсов.

* Требуется доступ для сохранения книг

    - Для сохранения `("My")` частных книг не требуются дополнительные привилегии. Все пользователи могут сохранять частные книги, и только они могут просматривать эти книги.
    - Для сохранения книги в общей книге требуются права на запись в группе ресурсов. Эти привилегии обычно указываются ролью [участника мониторинга](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) , но их также можно задать с помощью роли *участника "книги* ".
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Стандартные роли с правами, связанными с книгой

[Средство чтения мониторинга](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) включает стандартные привилегии/Реад, которые будут использоваться средствами мониторинга (включая книги) для чтения данных из ресурсов.

[Участник мониторинга](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) включает общие `/write` привилегии, используемые различными средствами мониторинга для сохранения элементов ( `workbooks/write` включая привилегии на сохранение общих книг).
"Участник книг" добавляет в объект права доступа "книги и записи" для сохранения общих книг.
Для сохранения частных книг, которые доступны только для просмотра, Специальные права не требуются.

Для настраиваемого управления доступом на основе ролей:

Добавить `microsoft.insights/workbooks/write` для сохранения общих книг. Дополнительные сведения см. в разделе роль [участника книги](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) .

## <a name="next-steps"></a>Дальнейшие шаги

* [Узнайте](workbooks-visualizations.md) больше о книгах с множеством разнообразных вариантов визуализации.
