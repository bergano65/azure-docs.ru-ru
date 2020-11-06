---
title: Создание пользовательской точки восстановления для выделенного пула SQL
description: Создание точки восстановления для выделенного пула SQL.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c0835fb48d00fe5277732f34fd6b0de1448f6a78
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332130"
---
# <a name="user-defined-restore-points"></a>Определенные пользователем точки восстановления

Из этой статьи вы узнаете, как создать новую определяемую пользователем точку восстановления для выделенного пула SQL в Azure синапсе Analytics с помощью портал Azure.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Создание определяемых пользователем точек восстановления с помощью портал Azure

Пользовательские точки восстановления также можно создавать с помощью портал Azure.

1. Войдите в учетную запись [портал Azure](https://portal.azure.com/) .

2. Перейдите к выделенному пулу SQL, для которого нужно создать точку восстановления.

3. На панели слева выберите **Обзор** и щелкните **+ создать точку восстановления**. Если кнопка Создать точку восстановления не включена, убедитесь, что выделенный пул SQL не приостановлен.

    ![Новая точка восстановления](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Укажите имя для определенной пользователем точки восстановления и нажмите кнопку **Применить**. Срок хранения по умолчанию для определенных пользователем точек восстановления составляет семь дней.

    ![Имя точки восстановления](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Восстановление существующего выделенного пула SQL](restore-sql-pool.md)

