---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184989"
---
Как и в случае с маркерами доступа, если маркер Azure AD не задан, необходимо выполнить обработку события TokenRequired или реализовать метод tokenRequired для протокола делегирования.

Событие можно обрабатывать синхронно, задав свойство аргументов события.
