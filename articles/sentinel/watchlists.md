---
title: Использование Azure Sentinel ватчлистс
description: В этой статье описывается, как использовать Azure Sentinel ватчлистс для исследования угроз, импорта бизнес-данных, создания списков разрешений и обогащения данных событий.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 25252b73f25a96f85d5e2cf1d68b76f9eaa3ca75
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979803"
---
# <a name="use-azure-sentinel-watchlists"></a>Использование Azure Sentinel ватчлистс

Azure Sentinel ватчлистс включает сбор данных из внешних источников данных для корреляции с событиями в среде Azure Sentinel. После создания можно использовать ватчлистс в поиске, правилах обнаружения, поиске угроз и отклике модули PlayBook. Ватчлистс хранятся в рабочей области Sentinel Azure в виде пар "имя-значение" и кэшируются для обеспечения оптимальной производительности запросов и низкой задержки.

Распространенные сценарии использования ватчлистс:

- Оперативное **исследование угроз** и реагирование на инциденты с помощью быстрого импорта IP-адресов, хэшей файлов и других данных из файлов CSV. После импорта можно использовать пары "имя-значение" списка воспроизведения для соединений и фильтров в правилах оповещений, поиске угроз, книгах, записных книжках и общих запросах.

- **Импорт бизнес-данных** в виде ватчлистс. Например, можно импортировать списки пользователей с привилегированным доступом к системе или уволенных сотрудников, а затем использовать списка воспроизведения для создания списков разрешений и запретов, используемых для обнаружения пользователей в сети и предотвращения их входа в сеть.

- **Сокращение выносливости предупреждений**. Создайте списки разрешений для подавления оповещений от группы пользователей, например пользователей с разрешенных IP-адресов, которые выполняют задачи, которые обычно активируют оповещение, и предотвращают появление предупреждений о неблагоприятных событиях.

- **Обогащение данных о событиях**. Используйте ватчлистс, чтобы расширить данные событий с помощью сочетаний «имя-значение», полученных из внешних источников данных.

## <a name="create-a-new-watchlist"></a>Создание нового списка воспроизведения

1. В портал Azure перейдите к конфигурации **Sentinel Azure**  >  **Configuration**  >  **списка воспроизведения** , а затем выберите **Добавить новый**.

    > [!div class="mx-imgBorder"]
    > ![создать списка воспроизведения](./media/watchlists/sentinel-watchlist-new.png)

1. На странице **Общие** укажите имя, описание и псевдоним для списка воспроизведения, а затем нажмите кнопку **Далее**.

    > [!div class="mx-imgBorder"]
    > ![Страница "Общие" списка воспроизведения](./media/watchlists/sentinel-watchlist-general.png)

1. На странице **источник** выберите тип набора данных, отправьте файл, а затем нажмите кнопку **Далее**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="Исходная страница списка воспроизведения" lightbox="./media/watchlists/sentinel-watchlist-source.png":::


1. Просмотрите сведения, убедитесь, что они верны, а затем нажмите кнопку **создать**.

    > [!div class="mx-imgBorder"]
    > ![Страница проверки списка воспроизведения](./media/watchlists/sentinel-watchlist-review.png)

    После создания списка воспроизведения появляется уведомление.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="Исходная страница списка воспроизведения" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::


## <a name="use-watchlists-in-queries"></a>Использование ватчлистс в запросах

1. В портал Azure перейдите в раздел Конфигурация **Sentinel Azure**  >  **Configuration**  >  **списка воспроизведения**, выберите списка воспроизведения, который требуется использовать, а затем щелкните **Просмотр в log Analytics**.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="Исходная страница списка воспроизведения" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::


1. Элементы в списка воспроизведения автоматически извлекаются для запроса и отображаются на вкладке **результаты** . В приведенном ниже примере показаны результаты извлечения полей **ServerName** и **IPAddress** .

    > [!NOTE]
    > Отметка времени в запросах будет пропускаться как в пользовательском интерфейсе запроса, так и в запланированных предупреждениях.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="Исходная страница списка воспроизведения" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
## <a name="use-watchlists-in-analytics-rules"></a>Использование ватчлистс в правилах аналитики

Чтобы использовать ватчлистс в правилах аналитики, в портал Azure перейдите в раздел **Azure Sentinel**  >  **Configuration**  >  **аналитика**конфигурации Sentinel Azure и создайте правило с помощью `_GetWatchlist('<watchlist>')` функции в запросе.

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="Исходная страница списка воспроизведения" lightbox="./media/watchlists/sentinel-watchlist-analytics-rule.png":::


## <a name="view-list-of-watchlists-aliases"></a>Просмотреть список псевдонимов ватчлистс

Чтобы получить список псевдонимов списка воспроизведения, в портал Azure перейдите в раздел Общие журналы **Azure Sentinel**  >  **General**  >  **Logs**и выполните следующий запрос: `_GetWatchlistAlias` . Список псевдонимов можно просмотреть на вкладке **результаты** .

> [!div class="mx-imgBorder"]
> ![ватчлистс списка](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Дальнейшие действия
В этом документе вы узнали, как использовать ватчлистс в Azure Sentinel для обогащения данных и улучшения исследований. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.
- Узнайте, как [отслеживать свои данные и потенциальные угрозы](quickstart-get-visibility.md).
- Узнайте, как приступить к [обнаружению угроз с помощью Azure Sentinel](tutorial-detect-threats.md).
- [Используйте книги](tutorial-monitor-your-data.md) для мониторинга данных.

