---
title: Использование поиска облаку в Azure Sentinel для обнаружения угроз | Документация Майкрософт
description: В этой статье описывается использование поиска облаку в Azure Sentinel для хранения данных.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: cabailey
ms.openlocfilehash: 5b347848c9c6a58a70ab1093a6f9c70b62f3f769
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74900458"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>Использование поиска облаку в Azure Sentinel для обнаружения угроз

> [!IMPORTANT]
> Поиск облаку в Azure Sentinel в настоящее время находится в общедоступной предварительной версии и постепенно сведен к клиентам.
> Эта функция предоставляется без соглашения об уровне обслуживания и не рекомендуется для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Используйте поиск облаку для создания интерактивных сеансов, позволяющих тестировать вновь созданные запросы по мере возникновения событий, получать уведомления из сеансов при обнаружении соответствия и при необходимости запускать расследования. Вы можете быстро создать сеанс облаку с помощью любого Log Analytics запроса.

- **Тестирование вновь созданных запросов по мере возникновения событий**
    
    Можно тестировать и корректировать запросы без конфликтов с текущими правилами, которые активно применяются к событиям. После подтверждения того, что эти новые запросы работают должным образом, можно легко преобразовать их в пользовательские правила генерации оповещений, выбрав параметр, который повышает уровень сеанса до предупреждения.

- **Получать уведомления при возникновении угрозы**
    
    Вы можете сравнить каналы данных угроз с агрегированными данными журнала и получать уведомления в случае совпадения. Каналы данных угроз — это непрерывные потоки данных, связанные с потенциальными или текущими угрозами, поэтому уведомление может указывать на потенциальную угрозу для Организации. Создайте сеанс облаку, а не настраиваемое правило генерации оповещений, если вы хотите получать уведомление о возможной ошибке, не изменяя настройки настраиваемого правила генерации оповещений.

- **Запуск исследований**
    
    Если имеется активное исследование, включающее в себя ресурс, например узел или пользователь, вы можете просмотреть определенные (или любые) действия в данных журнала по мере их возникновения в этом ресурсе. Вы можете получать уведомления о наступлении этого действия.


## <a name="create-a-livestream-session"></a>Создание сеанса облаку

Вы можете создать сеанс облаку на основе существующего запроса поиска или создать сеанс с нуля.

1. В портал Azure перейдите к пункту **Sentinel** > **управление угрозами** > **поиске**.

2. Создание сеанса облаку из запроса поиска:
    
    1. На вкладке **запросы** выберите запрос поиска для использования.
    2. Щелкните запрос правой кнопкой мыши и выберите **Добавить в облаку**. Пример.
    
    > [!div class="mx-imgBorder"]
    > ![создать сеанс облаку из запроса поиска Sentinel Azure](./media/livestream/livestream-from-query.png)

3. Создание сеанса облаку с нуля: 
    
    1. Выбор вкладки " **облаку** "
    2. Выберите **Переход к облаку**.
    
4. На панели **облаку** :
    
    - Если вы начали облаку из запроса, проверьте запрос и внесите необходимые изменения.
    - Если вы начали облаку с нуля, создайте запрос. 

5. Выберите **воспроизвести** на панели команд.
    
    В строке состояния на панели команд указывается, работает ли сеанс облаку или приостановлен. В следующем примере выполняется сеанс:
    
    > [!div class="mx-imgBorder"]
    > ![создать сеанс облаку из службы Поиск меток Azure](./media/livestream/livestream-session.png)

6. Выберите **сохранить** на панели команд.
    
    Если не выбрать **приостановить**, сеанс продолжит выполняться, пока вы не выйдете из портал Azure.

## <a name="view-your-livestream-sessions"></a>Просмотр сеансов облаку

1. В портал Azure перейдите к пункту **Sentinel** > **управление угрозами** ** > Поиск** > **облаку** .

2. Выберите сеанс облаку, который нужно просмотреть или изменить. Пример.
    
    > [!div class="mx-imgBorder"]
    > ![создать сеанс облаку из запроса поиска Sentinel Azure](./media/livestream/livestream-tab.png)
    
    Откроется выбранный сеанс облаку для воспроизведения, приостановки, редактирования и т. д.

## <a name="receive-notifications-when-new-events-occur"></a>Получать уведомления при возникновении новых событий

Поскольку уведомления облаку для новых событий используют Уведомления портал Azure, эти уведомления отображаются при каждом использовании портал Azure. Пример.

![Уведомление портал Azure для облаку](./media/livestream/notification.png)

Выберите уведомление, чтобы открыть панель **облаку** .
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>Повышение уровня сеанса облаку до оповещения

Вы можете повысить уровень сеанса облаку до нового оповещения, выбрав **повысить уровень для оповещения** на панели команд в соответствующем сеансе облаку:

> [!div class="mx-imgBorder"]
> ![повысить облаку сеанса до оповещения](./media/livestream/elevate-to-alert.png)

Это действие открывает мастер создания правил, который предварительно заполняется запросом, связанным с сеансом облаку.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как использовать поиск облаку в Azure Sentinel. Ознакомьтесь с дополнительными сведениями об Azure Sentinel в соответствующих статьях.

- [Упреждающее слежение за угрозами](hunting.md)
- [Использование записных книжек для выполнения автоматизированного поисковых кампаний](notebooks.md)
