---
title: Marketo | Документация Майкрософт
description: Настройка управления интересами для Marketo.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 482d7a9662a79503bb2b197d5a6c63c9fa3c1c96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398525"
---
# <a name="configure-lead-management-in-marketo"></a>Настройка управления интересами в Marketo

В этой статье описывается, как настроить Marketo для обработки потенциальных клиентов Майкрософт.

1. Войдите в Marketo.
2. Выберите **Разработка студии**.
    ![Разработка студии Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Выберите **Новая форма**.
    ![Новая форма Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Заполните необходимые поля в новой форме, а затем выберите **Создать**.
    ![Создание новой формы Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  В сведениях о поле, выберите **Готово**.
    ![Завершение формы Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Утвердите и закройте окно.

6.  На вкладке MarketplaceLeadBacked выберите **Внедрить код**.
    ![Параметр с кодом внедрения Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Код внедрения Marketo отображает код, аналогичный приведенному ниже.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Скопируйте значения, указанные в коде внедрения, чтобы вы могли настроить идентификаторы **сервера**, **Munchkin** и **формы** в полях Marketo на портале облачных партнеров.

Используйте следующий пример в качестве руководства для получения необходимых идентификаторов из примера кода Marketo.

- Идентификатор сервера = **ys12**
- Идентификатор Munchkin = **123-PQR-789**
- Идентификатор формы = **1179**\
