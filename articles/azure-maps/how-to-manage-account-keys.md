---
title: Управление учетной записью и ключами службы Azure Maps | Документация Майкрософт
description: Вы можете управлять учетной записью и ключами доступа службы "Карты Azure" с помощью портала Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 81746279e935f9fa5b6ef00bc8d31e75f4d3bd20
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103050"
---
# <a name="manage-your-azure-maps-account-and-keys"></a>Управление учетной записью и ключами службы Azure Maps

Вы можете управлять учетной записью и ключами службы "Карты Azure" через портал Azure. Если у вас есть учетная запись и ключ, вы можете реализовать API-интерфейсы на своем веб-сайте или в мобильном приложении.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-a-new-account"></a>Создание учетной записи

1. Войдите на [портале Azure](https://portal.azure.com).

2. Выберите **Создать ресурс** в верхнем левом углу окна портала Azure.

3. Найдите и выберите **Maps**. Щелкните **Создать**.

4. Введите сведения о новой учетной записи.

![Ввод данных учетной записи на портале](./media/how-to-manage-account-keys/new-account-portal.png)

## <a name="manage-keys-on-the-account-page"></a>Управление ключами на странице учетной записи

Создав учетную запись, вы получите два случайно сгенерированных ключа. Чтобы получить данные карты или создать экземпляр карты JavaScript, используйте ключи для аутентификации в API службы Azure Maps.

Ключи можно найти на портале Azure. Перейдите к учетной записи. Затем выберите **Ключи** в меню.

![Управление ключами учетной записи на портале](./media/how-to-manage-account-keys/account-keys-portal.png)

На этой странице вы можете скопировать ключи или создать новые.

## <a name="delete-an-account"></a>Удаление учетной записи

Вы можете удалить учетную запись с портала Azure. Перейдите на страницу сведений об учетной записи и щелкните **Удалить**.

![Удаление учетной записи на портале](./media/how-to-manage-account-keys/account-delete-portal.png)

Откроется страница подтверждения. Вы можете подтвердить удаление вашей учетной записи, введя ее имя.

## <a name="next-steps"></a>Дополнительная информация

* Сведения об управлении ценовой категорией учетной записи Azure Maps см. здесь:
    > [!div class="nextstepaction"] 
    > [Как управлять ценовой категорией для учетной записи Azure Maps](./how-to-manage-pricing-tier.md)

* Узнайте, как просматривать метрики использования API для учетной записи Azure Maps:
    > [!div class="nextstepaction"] 
    > [Просмотр метрик использования](./how-to-view-api-usage.md)
