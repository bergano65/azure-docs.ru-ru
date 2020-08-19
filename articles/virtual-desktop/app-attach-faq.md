---
title: Часто задаваемые вопросы о присоединении приложения MSIX виртуальных рабочих столов Windows в Azure
description: Часто задаваемые вопросы о подключении приложения MSIX к виртуальному рабочему столу Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556338"
---
# <a name="msix-app-attach-faq"></a>Часто задаваемые вопросы о присоединении приложения MSIX

В этой статье содержатся ответы на часто задаваемые вопросы о подключении приложения MSIX для виртуальных рабочих столов Windows.

## <a name="does-msix-app-attach-use-fslogix"></a>Присоединяет ли приложение MSIX использовать Фслогикс?

Присоединение приложения MSIX не использует Фслогикс. Однако присоединение приложений и Фслогикс предназначены для совместной работы, чтобы обеспечить удобство работы пользователей.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Можно ли использовать подключение приложения MSIX за пределами виртуального рабочего стола Windows?

Да, присоединение приложения MSIX — это функция, входящая в состав Windows 10 Корпоративная, которую можно использовать за пределами виртуального рабочего стола Windows. Однако нет плоскости управления для подключения приложения MSIX за пределами виртуального рабочего стола Windows.

## <a name="how-do-i-get-an-msix-package"></a>Разделы справки получить пакет MSIX?

Ваш поставщик программного обеспечения предоставит вам пакет MSIX. Кроме того, MSIX пакеты можно преобразовать в MSIX. Узнайте больше [о том, как переместить существующие установщики в MSIX](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix).

## <a name="which-operating-systems-support-msix-app-attach"></a>Какие операционные системы поддерживают присоединение приложения MSIX?

Многосеансовая поддержка Windows 10 Корпоративная и Windows 10 Корпоративная.

## <a name="next-steps"></a>Дальнейшие действия

Если вы хотите узнать больше о присоединении приложения MSIX, ознакомьтесь с нашим [обзорным](what-is-app-attach.md) [глоссарием](app-attach-glossary.md). В противном случае приступайте к [настройке присоединения приложения](app-attach.md).