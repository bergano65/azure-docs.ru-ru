---
title: Условия соответствия обработчика правил Azure CDN от Verizon Premium | Документация Майкрософт
description: Справочная документация по условиям соответствия для обработчика правил сети доставки содержимого Azure от Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: ecec7c3c5e4cc72ee637eb91033b9f2b0f158f1a
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872551"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Условия соответствия обработчика правил Azure CDN от Verizon Premium

В этой статье подробно описаны доступные условия соответствия для [обработчика правил](cdn-verizon-premium-rules-engine.md) сети доставки содержимого (CDN) Azure от Verizon Premium.

Вторая часть правила — это условие соответствия. Условие соответствия определяет различные типы запросов, для которых будет выполнен ряд функций.

Например, с помощью условия соответствия вы сможете:

- Отфильтровать запросы содержимого в определенном расположении.
- Отфильтровать запросы, отправленные с определенного IP-адреса или из определенной страны или региона.
- Отфильтровать запросы по информации из заголовка.

Новейшие условия соответствия см. в [документации для обработчика правил Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio).


## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о сети доставки содержимого Azure](cdn-overview.md)
- [Справочник по обработчику правил](cdn-verizon-premium-rules-engine-reference.md)
- [Условные выражения обработчика правил](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Возможности обработчика правил](cdn-verizon-premium-rules-engine-reference-features.md)
- [Переопределение стандартного поведения через HTTP с помощью обработчика правил](cdn-verizon-premium-rules-engine.md)
