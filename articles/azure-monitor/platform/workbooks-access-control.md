---
title: Контроль доступа к трудовым книжки Azure Monitor
description: Упрощение сложной отчетности с помощью заранее построенных и пользовательских параметризированных трудовых книжек с управлением доступом на основе роли
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 20116ab105e4eb12875ba3cb279fb261eb5c70e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658427"
---
# <a name="access-control"></a>Управление доступом

Контроль доступа в трудовых книжках относится к двум вещам:

* Доступ, необходимый для чтения данных в трудовой книжке. Этот доступ контролируется стандартными [ролями Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) на ресурсах, используемых в рабочей книге. В учебниках не указывается и не настраиваются доступ к этим ресурсам. Пользователи, как правило, получают такой доступ к этим ресурсам, используя роль [читателя мониторинга](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) на этих ресурсах.

* Доступ, необходимый для сохранения трудовых книжек

    - Сохранение `("My")` частных трудовых книжек не требует дополнительных привилегий. Все пользователи могут сохранить частные трудовые книжки, и только они могут видеть эти трудовые книжки.
    - Сохранение общих трудовых книжек требует привилегий написания в группе ресурсов, чтобы сохранить трудовую книжку. Эти привилегии обычно определяются ролью [участника мониторинга,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) но также могут быть установлены через роль *участника workbooks.*
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Стандартные роли с привилегиями, связанными с трудовой книжкой

[Мониторинг Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) включает в себя стандартные /прочитанные привилегии, которые будут использоваться инструментами мониторинга (включая трудовые книжки) для чтения данных с ресурсов.

[Элемент мониторинга](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) включает общие `/write` привилегии, используемые `workbooks/write` различными инструментами мониторинга для сохранения элементов (включая привилегию сохранения общих трудовых книжек).
"Автор трудовых книжек" добавляет привилегии "трудовые книжки/записи" к объекту, чтобы сохранить общие трудовые книжки.
Пользователям не требуется никаких особых привилегий для сохранения частных трудовых книжек, которые они могут видеть только.

Для пользовательского управления доступом на основе ролей:

Добавить, `microsoft.insights/workbooks/write` чтобы сохранить общие трудовые книжки. Для получения более [подробной](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) информации см.

## <a name="next-steps"></a>Дальнейшие действия

* [Начать](workbooks-visualizations.md) узнавать больше о рабочих книгах много богатых вариантов визуализации.
