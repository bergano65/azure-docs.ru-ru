---
title: Предоплата за разметку Azure Data Explorer, чтобы сэкономить деньги
description: Узнайте, как купить зарезервированную емкость Azure Data Explorer, чтобы сэкономить на затратах Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: bbb2108967353b851a0fa0ee610ec30380e3fc50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969268"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Предоплата за блоки разметки Azure Data Explorer с зарезервированной емкостью Azure Data Explorer

Экономьте деньги с помощью Azure Data Explorer, предоплату за единицы разметки по сравнению с ценами с оплатой по мере использования. С зарезервированной емкостью Azure Data Explorer вы берете на себя обязательство использовать Azure Data Explorer в течение одного или трех лет, чтобы получить значительную скидку на затраты azure Data Explorer на разметку. Чтобы приобрести зарезервированную емкость Azure Data Explorer, нужно только указать термин, он будет применяться ко всем развертываниям Azure Data Explorer во всех регионах.

Приобретая бронирование, вы предварительно оплачиваете расходы на разметку в течение одного или трех лет. Как только вы покупаете бронирование, наценка Azure Data Explorer, которая соответствует атрибутам бронирования, больше не взимается по тарифам с оплатой по мере использования. Кластеры Azure Data Explorer, которые уже запущены или недавно развернуты, автоматически получат выгоду. Это резервирование не охватывает вычисления, сетевые системы или сборы за хранение, связанные с кластерами. Зарезервированные мощности для этих ресурсов необходимо приобрести отдельно. В конце срока бронирования срок действия платежного пособия истекает, а единицы разметки Azure Data Explorer выставляются по цене оплаты по мере использования. Бронирование не обновляется автоматически. Для получения информации о ценах смотрите [страницу ценообразования Azure Data Explorer.](https://azure.microsoft.com/pricing/details/data-explorer/)

Зарезервированную емкость Azure Data Explorer можно на [портале Azure.](https://portal.azure.com) Чтобы купить зарезервированную емкость Azure Data Explorer:

* Вы должны быть владельцем хотя бы одной подписки Enterprise или Pay-As-You-Go.
* Для подписок с соглашением Enterprise параметр **Добавить зарезервированные экземпляры** следует включить на [портале EA](https://ea.azure.com). Кроме того, если эта настройка отключена, вы должны быть EA Admin по подписке.
* В программе поставщика облачных решений (CSP) только агенты-админы или агенты по продажам могут приобрести зарезервированную емкость Azure Data Explorer.

Для получения подробной информации о том, как корпоративные клиенты и клиенты Pay-As-You-Go взимаются за покупки бронирования, см.:
* [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) 
* [Понять использование бронирования Azure для подписки Pay-As-You-Go.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-markup-usage-before-purchase"></a>Определить правильное использование разметки перед покупкой

Размер резервирования должен основываться на общем количестве единиц разметки Azure Data Explorer, используемых существующими или скоро развернутыми кластерами Azure Data Explorer. Количество единиц разметки равно количеству ядер кластера кластера блоков блоков Azure Data Explorer в производстве (без учета Dev/test SKU). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Купить зарезервированную емкость Azure Data Explorer

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выберите **Все услуги** > **Reservations** > **Бронирование Покупка сейчас**. Выберите **Добавить**
1. В панели **типа «Выберите»** выберите **Azure Data Explorer,** чтобы приобрести новое резервирование для единиц разметки Azure Data Explorer. 
1. Выберите **Купить**
1. Заполните обязательные поля. 

    ![Страница покупки](media/pricing-reserved-capacity/purchase-page.png)

1. Просмотрите стоимость зарезервированного резервирования емкости Azure Data Explorer в разделе **Затраты.**
1. Нажмите кнопку **Купить**.
1. Выберите **Просмотреть резервирование**, чтобы просмотреть состояния покупки.

## <a name="cancellations-and-exchanges"></a>Отмена и обмен

Если вам необходимо отменить зарезервированное бронирование емкости Azure Data Explorer, может взиматься плата за досрочное прекращение в размере 12%. Возврат средств основан на самой низкой цене покупки или текущей цене бронирования. Максимальная сумма возмещенных средств — 50 000 долл. США в год. Полученные возмещенные средства включают оставшийся пропорциональный баланс за вычетом 12 % за досрочное аннулирование. Если нужно запросить отмену, перейдите к резервированию на портале Azure и выберите **Возместить**, чтобы создать запрос в службу поддержки.

Если необходимо изменить зарезервированное резервирование емкости Azure Data Explorer на другой термин, вы можете обменять его на другое резервирование, имевещее равное или большее значение. При обмене дата начала использования для нового резервирования не переносится из текущего резервирования. Срок использования (1 или 3 года) начинается с момента создания резервирования. Если нужно запросить обмен, перейдите в резервирование на портале Azure и выберите **Обмен**, чтобы создать запрос в службу поддержки.

Для получения дополнительной информации о том, как обменять или вернуть бронирование, [см.](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)

## <a name="manage-your-reserved-capacity-reservation"></a>Управление зарезервированной резервированием емкости

Скидка на резервирование единиц разметки Azure Data Explorer автоматически применяется к количеству единиц разметки, которые соответствуют зарезервированной области резервирования и атрибутам резервирования данных Azure Data Explorer. 


> [!NOTE]
> * Вы можете обновить область резервирования зарезервированных емкости Azure Data Explorer через [портал Azure,](https://portal.azure.com)PowerShell, CLI или через API.
> * Чтобы узнать, как управлять зарезервированным резервированием емкости Azure Data Explorer, [см.](../cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges.md)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о резервировании в Azure см. по следующим ссылкам:

* [Общие сведения о резервированиях в Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Управление Azure Reserved VM Instances](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Сведения о скидках на резервирование Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Общие сведения об использовании резервирования Azure для подписки с оплатой по мере использования](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
* [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Приобретение зарезервированных экземпляров Azure](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами

Если у вас есть вопросы или вам нужна помощь, [создайте запрос на поддержку.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
