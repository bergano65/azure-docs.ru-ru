---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67184989"
---
Как и в случае с маркерами доступа, если маркер Azure AD не задан, необходимо выполнить обработку события TokenRequired или реализовать метод tokenRequired для протокола делегирования.

Событие можно обрабатывать синхронно, задав свойство аргументов события.
