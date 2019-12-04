---
title: Настройка параметров сервера — портал Azure — "база данных Azure для PostgreSQL" — "один сервер"
description: В этой статье описывается, как настроить параметры postgres в базе данных Azure для PostgreSQL с помощью портал Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: a9d078fe9aab12b9044733d17a1437801d5130a4
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74763682"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---single-server-via-the-azure-portal"></a>Настройка параметров сервера в базе данных Azure для PostgreSQL — Single Server с помощью портал Azure 
С помощью портала Azure можно вывести список параметров конфигурации для сервера базы данных Azure для PostgreSQL, а также отобразить и обновить их.

## <a name="prerequisites"></a>Технические условия
Чтобы приступить к выполнению этого руководства, вам потребуются следующие компоненты:
- [сервер базы данных Azure для PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="viewing-and-editing-parameters"></a>Просмотр и изменение параметров
1. Откройте [портал Azure](https://portal.azure.com).

2. Выберите сервер базы данных Azure для PostgreSQL.

3. В разделе **Параметры** выберите **Параметры сервера**. На странице отобразится список параметров, их значения и описания.
![Страница общих сведений для параметров](./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png)

4. Нажмите кнопку **раскрывающегося списка**, чтобы просмотреть возможные значения параметров перечисляемого типа, например client_min_messages.
![Раскрывающийся список для перечисляемого типа](./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png)

5. Выберите кнопку с буквой **i** (информация) или наведите на нее указатель мыши, чтобы просмотреть диапазон возможных значений числовых параметров, например cpu_index_tuple_cost.
![Кнопка информации](./media/howto-configure-server-parameters-in-portal/4-information-button.png)

6. При необходимости воспользуйтесь **полем поиска**, чтобы сузить область поиска до определенного параметра. Поиск выполняется по имени и описанию параметров.
![Результаты поиска](./media/howto-configure-server-parameters-in-portal/5-search.png)

7. Измените значения требуемых параметров. Все изменения, вносимые в этом сеансе, выделены фиолетовым цветом. Изменив значения, можно нажать кнопку **Сохранить**. Также вы можете нажать кнопку **Отменить**, чтобы отменить изменения.
![Сохранение или отмена изменений](./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png)

8. Если вы сохранили новые значения параметров, всегда можно восстановить значения по умолчанию, выбрав **Сбросить все к значениям по умолчанию**.
![Сбросить все к значениям по умолчанию](./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png)

## <a name="next-steps"></a>Дальнейшие действия
Кроме того, вы узнаете о таких возможностях, как:
- [Серверы базы данных Azure для PostgreSQL](concepts-servers.md)
- [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md)
