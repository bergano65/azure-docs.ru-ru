---
title: Программное создание подписок Azure
description: Эта статья поможет вам составить представление о доступных вариантах программного создания подписок Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: a51a158700df52ced5288cf2c2f82ebcdf3de54d
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254027"
---
# <a name="create-azure-subscriptions-programatically"></a>Программное создание подписок Azure

Эта статья поможет вам составить представление о доступных вариантах программного создания подписок Azure.

С помощью различных интерфейсов REST API можно создать подписку для следующих типов соглашений Azure:

- Соглашение Enterprise (EA)
- Клиентское соглашение Майкрософт (MCA);
- Соглашение с партнером Майкрософт (ПСМ)

Для других типов соглашений нельзя создавать дополнительные подписки программными средствами с помощью интерфейсов REST API.

Требования и особенности процесса создания подписок зависят от типа соглашения и версии API. Ознакомьтесь со следующими статьями, которые относятся к конкретным сценариям:

Новейшие API-интерфейсы:

- [Создание подписок EA](programmatically-create-subscription-enterprise-agreement.md)
- [создание подписок MCA](programmatically-create-subscription-microsoft-customer-agreement.md);
- [создание подписок MPA](programmatically-create-subscription-microsoft-partner-agreement.md).

Если вы по-прежнему используете [предварительные версии API-интерфейсов](programmatically-create-subscription-preview.md), вы можете и дальше создавать подписки с их помощью. 

Кроме того, вы можете [создавать подписки на основе шаблона ARM](create-subscription-template.md). Шаблон ARM помогает автоматизировать создание подписки с использованием интерфейсов API. 

## <a name="next-steps"></a>Следующие шаги

* После создания подписки можно предоставить эту возможность другим пользователям и субъектам-службам. Дополнительные сведения см. статье [Предоставление доступа к созданию подписок Azure Enterprise (предварительная версия)](grant-access-to-create-subscription.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в руководстве по [упорядочиванию ресурсов с помощью групп управления Azure](../../governance/management-groups/overview.md).
