---
title: Управление службой Creator в Azure Maps
description: Из этой статьи вы узнаете, как управлять службой Creator в Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 945c7035c2eeada3fea358489460f80c40438950
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595943"
---
# <a name="manage-azure-maps-creator"></a>Управление службой Creator в Azure Maps

Служба Creator в Azure Maps позволяет создавать частные данные схем помещений. С помощью API Azure Maps и модуля схем помещений можно разрабатывать интерактивные и динамические веб-приложения для схем помещений. В настоящее время служба Creator доступна только в США при использовании ценовой категории S1.

В этой статье описаны действия, необходимые для создания и удаления ресурса Creator в учетной записи Azure Maps.

## <a name="create-creator-resource"></a>Создание ресурса Creator

1. Войдите на [портал Azure](https://portal.azure.com)

2. Выберите свою учетную запись службы Azure Maps. Если вы не видите учетную запись Azure Maps в разделе **Последние ресурсы**, перейдите в меню портала Azure. Щелкните **Все ресурсы**. Найдите и выберите свою учетную запись службы Azure Maps.

    ![Домашняя страница портала Azure Maps](./media/how-to-manage-creator/select-maps-account.png)

3. На странице учетной записи Azure Maps выберите элемент **Обзор** в разделе **Creator**. Щелкните **Создать**, чтобы создать ресурс Creator в Azure Maps.

    ![Страница создания ресурса Creator в Azure Maps](./media/how-to-manage-creator/creator-blade-settings.png)

4. Введите имя и расположение для ресурса Creator. В настоящее время Creator поддерживается только в США. Щелкните **Review + create** (Просмотреть и создать).

   ![Страница для ввода сведений об учетной записи Creator](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Проверьте параметры и нажмите кнопку **Создать**.

    ![Страница подтверждения параметров учетной записи Creator](./media/how-to-manage-creator/creator-create.PNG)

6. По завершении развертывания вы увидите страницу с сообщением об успешном выполнении или ошибке.

   ![Страница состояния развертывания ресурса](./media/how-to-manage-creator/creator-resource-created.png)

7. Щелкните **Перейти к ресурсу**. На странице представления ресурса Creator отображается состояние ресурса Creator и выбранный демографический регион.

    ![Страница состояния ресурса Creator](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >На странице ресурса Creator можно вернуться к учетной записи Azure Maps, которой он принадлежит. Для этого щелкните учетную запись Azure Maps.

## <a name="delete-creator-resource"></a>Удаление ресурса Creator

Чтобы удалить ресурс Creator, перейдите к учетной записи Azure Maps. В разделе **Creator** выберите **Обзор**. Нажмите кнопку **Удалить** .

>[!WARNING]
>При удалении ресурса Creator учетной записи Azure Maps также будут удалены наборы данных, наборы фрагментов и наборы состояний функции, созданные с помощью служб Creator.

![Страница Creator с кнопкой "Удалить"](./media/how-to-manage-creator/creator-delete.png)

Нажмите кнопку **Удалить** и введите имя ресурса Creator, чтобы подтвердить удаление. После удаления ресурса вы увидите страницу подтверждения, как показано на рисунке ниже:

![Страница Creator с сообщением о подтверждении удаления](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>Аутентификация

Creator наследует параметры службы контроля доступа Azure Maps (IAM). Все вызовы API для доступа к данным должны отправляться с соблюдением правил проверки подлинности и авторизации.

Данные об использовании Creator записываются в диаграммы использования и журнал действий Azure Maps.  Дополнительные сведения см. в статье [Управление аутентификацией в Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

## <a name="access-to-creator-services"></a>Доступ к службам Creator

Службы Creator доступны только в расположении, выбранном во время создания. Если вызовы служб Creator поступают извне выбранного расположения, пользователю будет возвращено сообщение об ошибке. Чтобы осуществлять вызовы извне выбранного расположения, к URL-адресу службы нужно добавить географический префикс для выбранных расположений. Например, если Creator создается в США, все вызовы службы преобразования нужно отправлять по адресу `us.atlas.microsoft.com/conversion/convert`.

Кроме того, все данные, импортированные в Creator, нужно передавать в то же географическое расположение, где находится ресурс Creator. Например, если Creator подготовлен к работе в США, все необработанные данные нужно отправлять по адресу `us.atlas.microsoft.com/mapData/upload`.

## <a name="next-steps"></a>Дальнейшие действия

Общие сведения об использовании Creator для создания схем помещений:

> [!div class="nextstepaction"]
> [Data — Upload Preview](creator-indoor-maps.md#upload-a-drawing-package) (Данные — предварительная версия отправки)

> [!div class="nextstepaction"]
> [Преобразование данных](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Набор данных](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Набор фрагментов](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Набор состояний функции](creator-indoor-maps.md#feature-statesets)

Сведения об использовании Creator для отрисовки схем помещений в приложении:

> [!div class="nextstepaction"]
> [Руководство по использованию Creator в Azure Maps](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Динамические стили для схем помещений](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Использование модуля схем помещений](how-to-use-indoor-module.md)