---
title: Применение скидок на резервирование к хранилищу данных SQL Azure | Документация Майкрософт
description: Сведения о применении скидок на резервирование к хранилищу данных SQL Azure для экономии средств.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: banders
ms.openlocfilehash: 2520b9142c377bd84ac0d9c09b56ba17b6e42522
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226241"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Применение скидок на резервирование к хранилищу данных SQL Azure

После приобретения резервной мощности хранилища данных SQL Azure скидка на резервирование автоматически применяется к хранилищам данных, которые существуют в этом регионе. Скидка на резервирование применяется к потреблению, выданному единицей измерения cDWU хранилища данных SQL. Служба хранилища и работа в сетевые подключения оплачиваются по тарифу «по мере использования».

## <a name="reservation-discount-application"></a>Применение скидки на резервирование

Скидка на резервную мощность хранилища данных SQL применяется к запущенным хранилищам на почасовой основе. Если вы не развернули хранилище в течение часа, то резервная мощность будет потрачена впустую на этот час. Она не переносится.

После покупки, приобретенное резервирование сопоставляется с потреблением хранилища данных SQL, которое в любой момент запускается при запуске хранилищ. Если для нескольких хранилищ работа завершена, то скидки на резервирование автоматически распространяются на любые другие подходящие хранилища.

Для хранилищ, которые не работают в течение полного часа, резервирование автоматически применяется к другим соответствующим экземплярам в этот час.

## <a name="discount-examples"></a>Примеры скидок

В следующих примерах показано, как применяется скидка резервной мощности хранилища данных SQL в зависимости от развертываний.

- **Пример 1**: Ви покупаете 5 единиц с резервной мощностью 100 cDWU. Вы запускаете экземпляр DW1500c хранилища данных SQL в течение часа. В этом случае потребление выдано для 15 единиц с потреблением по 100 cDWU. Скидка на резервирование распространяется на 5 использованных единиц. Плата взимается по фактической ставке за оставшиеся 10 использованных единиц с потреблением по 100 cDWU.

- **Пример 2**. Ви покупаете 5 единиц с резервной мощностью 100 cDWU. Вы запускаете два экземпляр DW100c хранилища данных SQL в течение часа. В этом случае два события потребления выдано для 1 единицы с потреблением 100 cDWU. На оба события потребления распространяется скидка на резервирование. Оставшиеся 3 блока по 100 cDWU резервной мощности тратятся впустую и не переносятся для использования в будущем.

- **Пример 3**: Ви покупаете 1 единицу с резервной мощностью 100 cDWU. Вы запускаете два экземпляр DW100c хранилища данных SQL. Каждый работает по 30 минут. В этом случае, скидка на резервирование распространяется на оба события потребления. Оплата за потребление не взимается при использовании тарифов «оплаты по мере использования».

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами

- Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о резервировании в Azure см. по следующим ссылкам:

- [Основные сведения о резервировании в Azure](billing-save-compute-costs-reservations.md)
- [Просмотр транзакций резервирования](billing-view-reservations.md)
- [Получение транзакций резервирования и использования через API](billing-reservation-apis.md)
- [Управление резервированиями](billing-manage-reserved-vm-instance.md)
