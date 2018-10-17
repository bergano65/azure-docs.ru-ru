---
title: включение файла
description: включение файла
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 09/27/2018
ms.author: diberry
ms.openlocfilehash: 420757c5b7f6d19bb0abe87232e75497b7b97322
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019113"
---
1. Откройте Visual Studio 2017 Community.
1. Создайте новый проект **Консольное приложение (.Net Core)** и присвойте ему имя `QnaMakerQuickstart`. Для остальных параметров оставьте значения по умолчанию.
1. В обозревателе решений щелкните правой кнопкой мыши имя проекта, **QnaMakerQuickstart**, а затем выберите **Управление пакетами NuGet**.
1. В окне NuGet выберите **Браузер**, найдите файл **Newtonsoft.JSON** и установите этот пакет. Этот пакет используется для синтаксического анализа кода JSON, возвращаемого из HTTP-ответа QnA Maker. 