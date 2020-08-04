---
title: Просмотр резервирований для ресурсов Azure | Документация Майкрософт
description: Узнайте, как просматривать резервирования на портале Azure.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: fd5d4248e9b1e645628ac8b87df3b4b0a16e2049
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288969"
---
# <a name="view-azure-reservations"></a>Просмотр резервирований Azure

Вы можете просматривать приобретенное резервирование и управлять им на портале Azure.

## <a name="permissions-to-view-a-reservation"></a>Разрешения на просмотр резервирования

Для просмотра резервирования и управления им необходимо иметь соответствующее разрешение читателя или владельца. По умолчанию при покупке резервирования вы вместе с администратором учетной записи можете получить роль владельца заказа на резервирование и самого резервирования. Чтобы разрешить другим просматривать резервирование, назначьте им роль **владельца** или **читателя** в заказе на резервирование или самом резервировании. Если добавить пользователя в подписку, которая предоставляется для выставления счетов за резервирование, его не будет автоматически добавлено в резервирование. 

Дополнительные сведения см. в разделе [Добавление или изменение пользователей, которые могут управлять резервированием](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Просмотр сведений о резервировании и использовании на портале Azure

Чтобы просмотреть сведения о резервировании как владелец или читатель, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите в раздел [Резервирования](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. Появится список резервирований, в котором можно выбрать роль владельца или читателя. Каждое резервирование показывает последний известный показатель использования в процентах.
4. Щелкните процент использования, чтобы просмотреть журнал использования и дополнительные сведения. Чтобы получить дополнительные сведения, просмотрите видео ниже.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>Получение резервирований и показателей использования с помощью API, PowerShell и CLI

Чтобы получить список всех резервирований, используйте следующие ресурсы:
- [API: список заказов на резервирование](/rest/api/reserved-vm-instances/reservationorder/list);
- [PowerShell: список заказов на резервирование](/powershell/module/azurerm.reservations/get-azurermreservationorder);
- [CLI: список заказов на резервирование](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list).

Вы также можете получить [сведения об использовании резервирования](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) с помощью API использования зарезервированных экземпляров. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Просмотр резервирований и показателей использования в Power BI

Для пользователей Power BI существует две возможности просмотра.
- Пакет содержимого. Данные о покупках и использовании резервирований доступны в [пакете содержимого Power BI Consumption Insights](/power-bi/desktop-connect-azure-cost-management). Создайте необходимые отчеты с помощью этого пакета содержимого. 
- Приложение для управления затратами. Используйте приложение [Управление затратами](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) для предварительно созданных отчетов, которые можно дополнительно настраивать.

## <a name="next-steps"></a>Дальнейшие действия

- [Управление резервированиями для ресурсов Azure](manage-reserved-vm-instance.md).
- [Общие сведения об использовании резервирования Azure для подписки с оплатой по мере использования](understand-reserved-instance-usage.md)
- [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](understand-reserved-instance-usage-ea.md)
- [Продажа Microsoft Azure Reserved VM Instances](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).
