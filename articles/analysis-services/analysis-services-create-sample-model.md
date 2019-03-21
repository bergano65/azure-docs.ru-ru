---
title: Руководство. Добавление образца модели для сервера Azure Analysis Services | Документация Майкрософт
description: Это руководство содержит сведения о том, как добавить образец модели в службах Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 32c46f0a488d775275b3a367aa2913f034569041
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57903935"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>Руководство. Добавление образца модели с портала

Из этого руководства вы научитесь добавлять на свой сервер образец табличного шаблона базы данных Adventure Works. Образец модели представляет собой готовую версию образца модели данных "Интернет-продажи Adventure Works" (1200). Образец модели полезен для тестирования управления моделью, соединения со средствами и клиентскими приложениями, а также для выполнения запросов к данным модели. В рамках этого руководства используется [портал Azure](https://portal.azure.com) и [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) для выполнения следующих действий. 

> [!div class="checklist"]
> * Добавление готового образца табличной модели данных на сервер 
> * Подключение к модели с помощью SSMS

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="before-you-begin"></a>Перед началом работы

Для работы с этим учебником необходимы указанные ниже компоненты.

- Сервер Azure Analysis Services. Для получения дополнительных сведений см. статью [Создание сервера с помощью портала](analysis-services-create-server.md).
- Разрешения администратора сервера
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал](https://portal.azure.com/).

## <a name="add-a-sample-model"></a>Добавление образца модели

1. В области **Обзор** сервера выберите **Новая модель**.

    ![Создание образца модели](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. В разделе **Новая модель** > **Choose a datasource** (Выбор источника данных) установите флажок **Пример данных**, а затем нажмите кнопку **Добавить**.

    ![Выбор примеров данных](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. В области **Обзор** проверьте, добавлен ли образец модели `adventureworks`.

    ![Выбор примеров данных](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>Очистка ресурсов

Образец модели использует ресурсы кэш-памяти. Если образец модели не используется для тестирования, следует удалить его с сервера.

В следующих шагах описано, как удалить модель с сервера с помощью среды SSMS.

1. В среде SSMS выберите **Обозреватель объектов** и щелкните **Подключиться** > **Analysis Services**.

2. В поле **Подключение к серверу** вставьте имя сервера, затем в поле **Проверка подлинности** выберите **Active Directory — универсальная с поддержкой многофакторной проверки подлинности**, введите имя пользователя и нажмите кнопку **Подключиться**.

    ![Вход](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. В **обозревателе объектов** щелкните правой кнопкой мыши пример базы данных `adventureworks`, затем выберите **Удалить**.

    ![Удаление примера базы данных](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Дополнительная информация 

В этом руководстве было рассмотрено добавление базового образца модели на сервер. Когда есть шаблон базы данных, можно подключиться к нему из SQL Server Management Studio и добавить роли пользователей. Для получения дополнительных сведений перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Руководство. Настройка ролей администратора и пользователя сервера](analysis-services-database-users.md)


