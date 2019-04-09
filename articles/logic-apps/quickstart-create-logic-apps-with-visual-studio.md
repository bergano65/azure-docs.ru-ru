---
title: Создание автоматизированных рабочих процессов с помощью Visual Studio — Azure Logic Apps
description: Автоматизируйте задачи, бизнес-процессы и рабочие процессы для сценариев корпоративной интеграции с помощью Azure Logic Apps и Visual Studio.
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: quickstart
ms.custom: mvc
ms.reviewer: klam, LADocs
ms.suite: integration
ms.date: 04/02/2019
ms.openlocfilehash: 10ed3ec8b29048a7ede51a6d98e9f1ebb7f44cf6
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862987"
---
# <a name="quickstart-create-automated-tasks-processes-and-workflows-with-azure-logic-apps---visual-studio"></a>Краткое руководство. Создание автоматизированных задач, операций и рабочих процессов с помощью Azure Logic Apps в Visual Studio

С помощью [Azure Logic Apps](../logic-apps/logic-apps-overview.md) и Visual Studio можно создавать рабочие процессы, которые автоматизируют задачи и процессы для интеграции приложений, данных, систем и служб в предприятиях и организациях. В кратком руководстве показано, как разрабатывать и создавать эти рабочие процессы путем создания приложений логики в Visual Studio и их развертывания в Azure в облаке. Хотя эти задачи можно выполнять на портале Azure, Visual Studio позволяет добавлять приложения логики в систему управления версиями, публиковать различные версии и создавать шаблоны Azure Resource Manager для различных сред развертывания.

Если вы не знакомы с Azure Logic Apps и хотите ознакомиться с основными понятиями, см. [руководство по созданию приложения логики на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md). Конструктор приложений логики на портале Azure и в Visual Studio работает аналогичным образом.

Здесь создается то же приложение логики, что и в кратком руководстве портала Azure, но с помощью Visual Studio. Это приложение логики отслеживает RSS-канал веб-сайта и отправляет сообщения электронной почты для каждого нового элемента, опубликованного на сайте. По завершении приложение логики будет выглядеть как этот высокоуровневый рабочий процесс:

![Готовое приложение логики](./media/quickstart-create-logic-apps-with-visual-studio/overview.png)

<a name="prerequisites"></a>

Прежде чем начать, убедитесь в наличии следующих необходимых при работе с этим руководством элементов:

* Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>.

* Скачайте и установите эти средства, если вы еще этого не сделали:

  * <a href="https://aka.ms/download-visual-studio" target="_blank">Visual Studio 2019, 2017 или 2015 — выпуски Community или выше</a>. 
  В этом кратком руководстве используется бесплатная версия Visual Studio Community 2017.

    > [!IMPORTANT]
    > При установке Visual Studio 2019 или 2017 обязательно выберите рабочую нагрузку **разработки Azure**.
    > Если используется Visual Studio 2019, Cloud Explorer может открыть конструктор приложений логики на портале Azure. Открытие встроенного конструктора приложений логики пока не поддерживается.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Пакет Microsoft Azure SDK для .NET (2.9.1 или более поздней версии)</a>. Дополнительные сведения о <a href="https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet">пакете Azure SDK для .NET</a>.

  * <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * Средства Azure Logic Apps для необходимой версии Visual Studio:

    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019" target="_blank">Visual Studio 2019</a>
    
    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017" target="_blank">Visual Studio 2017</a>
    
    * <a href="https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015" target="_blank">Visual Studio 2015</a>
  
    Вы можете скачать и установить средства Azure Logic Apps напрямую из Visual Studio Marketplace или узнать, <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">как установить это расширение из Visual Studio</a>. 
    После завершения установки перезагрузите Visual Studio.

* Доступ к Интернету при использовании встроенного конструктора приложений логики

  Конструктору требуется подключение к Интернету, чтобы создать ресурсы в Azure и считать свойства и данные из соединителей в приложении логики. 
  Например, если вы используете соединитель Dynamics CRM Online, конструктор проверяет экземпляр CRM и получает информацию для отображения доступных свойств по умолчанию и пользовательских свойств.

* Учетная запись, поддерживаемая Logic Apps, например Office 365 Outlook, Outlook.com или Gmail. Сведения о дополнительных поставщиках см. в <a href="https://docs.microsoft.com/connectors/" target="_blank">списке соединителей</a>. Это приложение логики использует Office 365 Outlook. Если вы используете другой поставщик, общие шаги те же, но пользовательский интерфейс может немного отличаться.

## <a name="create-azure-resource-group-project"></a>Создание проекта группы ресурсов Azure

Чтобы начать работу, создайте [проект группа ресурсов Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). Узнайте больше о [группах ресурсов Azure и ресурсах](../azure-resource-manager/resource-group-overview.md).

1. Запустите Visual Studio и войдите в учетную запись Azure.

1. В меню **Файл** выберите **Создать** > **Проект**. (Или нажмите клавиши Ctrl+Shift+N.)

   ![В меню "Файл" выберите "Создать > Проект".](./media/quickstart-create-logic-apps-with-visual-studio/create-new-visual-studio-project.png)

1. В разделе **Установленные** выберите **Visual C#** или **Visual Basic**. Выберите **Облако** > **Группа ресурсов Azure**. Назовите свой проект, например:

   ![Создание проекта группы ресурсов Azure](./media/quickstart-create-logic-apps-with-visual-studio/create-azure-cloud-service-project.png)

   > [!NOTE]
   > Если не существует категория **Cloud** или проект **Группа ресурсов Azure**, убедитесь, что у вас установлен пакет Azure SDK для Visual Studio.

   Если вы используете Visual Studio 2019, сделайте следующее:

   1. В поле **Создать проект** выберите шаблон проекта **Группа ресурсов Azure** для языка C# или Visual Basic, а затем нажмите кнопку **Далее**.

   1. Укажите имя группы ресурсов Azure, которую вы хотите использовать, и другие сведения о проекте. Когда все будет готово, выберите **Создать**.

1. В списке шаблонов выберите **Приложение логики**.

   ![Выбор шаблона Logic App](./media/quickstart-create-logic-apps-with-visual-studio/select-logic-app-template.png)

   После того, как в Visual Studio будет создан проект, в обозревателе решений откроется ваше решение.

   ![Обозреватель решений с новым решением приложения логики и файлом развертывания](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-solution-created.png)

   В решении файл **LogicApp.json** — это не только хранилище определения для приложения логики, но и шаблон Azure Resource Manager, который можно настроить для развертывания.

## <a name="create-blank-logic-app"></a>Создание пустого приложения логики

Создав проект группы ресурсов Azure, создайте и настройте приложение логики, начиная с **шаблона пустого приложения логики**.

1. В обозревателе решений откройте контекстное меню для файла **LogicApp.json**. 
   Выберите действие **Открыть в конструкторе приложений логики**. (Или нажмите клавиши Ctrl+L.)

   ![Открытие файла JSON приложения логики с помощью конструктора приложений логики](./media/quickstart-create-logic-apps-with-visual-studio/open-logic-app-designer.png)

1. В поле **Подписка** выберите подписку Azure, которую вы хотите использовать. 
   В поле **Группа ресурсов** выберите **Создать новый...**. При этом будет создана группа ресурсов Azure.

   ![Выберите подписку, группу ресурсов и расположение.](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-location.png)

   Visual Studio требуется подписка Azure и группа ресурсов для создания и развертывания ресурсов, связанных с приложением логики и подключениями.

   | Параметр | Пример значения | ОПИСАНИЕ |
   | ------- | ------------- | ----------- |
   | Список профилей пользователя | Contoso <br> jamalhartnett@contoso.com | По умолчанию это учетная запись, используемая для входа |
   | **Подписка** | Оплата по мере использования <br> (jamalhartnett@contoso.com) | Имя подписки Azure и связанной учетной записи |
   | **Группа ресурсов** | MyLogicApp-RG <br> (Западная часть США) | Группа ресурсов Azure и расположение для хранения и развертывания ресурсов приложения логики |
   | **Расположение** | MyLogicApp-RG2 <br> (Западная часть США) | Другое расположение, если вы не хотите использовать расположение группы ресурсов |
   ||||

1. Открывается конструктор Logic Apps и отображается страница с вводным видео и часто используемыми триггерами. 
   Прокрутите видео и триггеры. В разделе **Шаблоны** выберите **Пустое приложение логики**.

   ![Выбор пункта "Пустое приложение логики"](./media/quickstart-create-logic-apps-with-visual-studio/choose-blank-logic-app-template.png)

## <a name="build-logic-app-workflow"></a>Рабочий процесс сборки приложения логики

Затем добавьте [триггер](../logic-apps/logic-apps-overview.md#logic-app-concepts), который срабатывает при появлении нового элемента RSS-канала. Каждое приложение логики должно начинаться с триггера, который срабатывает при удовлетворении определенных критериев. При каждой активации триггера обработчик Logic Apps создает экземпляр приложения логики для выполнения рабочего процесса.

1. В конструкторе приложений логики введите "rss" в поле поиска. Выберите триггер **При публикации элемента веб-канала**.

   ![Создание приложений логики путем добавления триггера и действий](./media/quickstart-create-logic-apps-with-visual-studio/add-trigger-logic-app.png)

   Триггер теперь будет отображаться в конструкторе:

   ![Триггер RSS появляется в конструкторе приложения логики](./media/quickstart-create-logic-apps-with-visual-studio/rss-trigger-logic-app.png)

1. Чтобы завершить создание приложения логики, следуйте рабочему процессу в [кратком руководстве на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md#add-rss-trigger), а затем вернитесь к этой статье.

   По завершении приложение логики может выглядеть следующим образом:

   ![Готовое приложение логики](./media/quickstart-create-logic-apps-with-visual-studio/finished-logic-app.png)

1. Чтобы сохранить приложение логики, сохраните решение Visual Studio. (Или нажмите клавиши Ctrl + S.)

Теперь, прежде чем протестировать приложение логики, разверните его в Azure.

## <a name="deploy-logic-app-to-azure"></a>Развертывание приложения логики в Azure

Перед запуском приложения логики разверните приложение из Visual Studio в Azure за несколько шагов.

1. В обозревателе решений в контекстном меню проекта выберите **Развернуть** > **Создать**. Если отобразится запрос на вход в учетную запись Azure, выполните его.

   ![Создайте развертывание приложения логики](./media/quickstart-create-logic-apps-with-visual-studio/create-logic-app-deployment.png)

1. Для этого развертывания сохраните подписку, группу ресурсов и другие настройки по умолчанию для Azure. Когда вы будете готовы, выберите **Развернуть**.

   ![Развертывание приложения логики в группу ресурсов Azure](./media/quickstart-create-logic-apps-with-visual-studio/select-azure-subscription-resource-group-deployment.png)

1. Если появится диалоговое окно **Изменение параметров**, укажите имя ресурса приложения логики для использования в развертывании, а затем сохраните свои параметры, например:

   ![Указание имени развертывания для приложения логики](./media/quickstart-create-logic-apps-with-visual-studio/edit-parameters-deployment.png)

   При запуске развертывания его состояние отобразится в окне **Выходные данные** Visual Studio. 
   Если состояние не отображается, откройте список **Показать вывод из** и выберите свою группу ресурсов Azure.

   ![Вывод информации о состоянии развертывания](./media/quickstart-create-logic-apps-with-visual-studio/logic-app-output-window.png)

   Если выбранные соединители требуют ввода данных, может открыться окно PowerShell в фоновом режиме с запросом на ввод необходимых паролей или секретных ключей. После ввода этих сведений развертывание продолжится.

   ![Окно PowerShell при развертывании](./media/quickstart-create-logic-apps-with-visual-studio/logic-apps-powershell-window.png)

   После развертывания приложение логики будет работать в реальном времени на портале Azure и проверять RSS-канал в зависимости от заданного расписания (каждую минуту). 
   Если в RSS-канале есть новые элементы, приложение логики отправляет сообщение электронной почты для каждого нового элемента. 
   В противном случае приложение логики ожидает до следующего интервала перед повторной проверкой.

   Например, ниже приведены примеры электронных писем, которые отправляет это приложение логики. 
   Если сообщения электронной почты не приходят, проверьте папку нежелательной почты.

   ![Outlook отправляет сообщение электронной почты для каждого нового элемента RSS](./media/quickstart-create-logic-apps-with-visual-studio/outlook-email.png)

   Технически, когда триггер проверяет веб-канал RSS и находит новые элементы, он срабатывает, и подсистема Logic Apps создает для приложения логики экземпляр рабочего процесса, в котором выполняются действия.
   Если триггер не находит новые элементы, он не срабатывает и "пропускает" создание экземпляра рабочего процесса.

Итак, вы успешно создали и развернули приложение логики с помощью Visual Studio! Чтобы управлять приложением логики и просматривать его журнал выполнения, ознакомьтесь со статьей [Manage logic apps with Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) (Управление приложениями логики в Visual Studio).

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ненужную группу ресурсов, содержащую приложение логики и связанные ресурсы.

1. Войдите на <a href="https://portal.azure.com" target="_blank">портал Azure</a> с учетной записью, использованной для создания приложения логики.

1. В главном меню Azure выберите **Группы ресурсов**.
Выберите группу ресурсов для своего приложения логики, а затем — **Обзор**.

1. На вкладке **Обзор** выберите поле **Удалить группу ресурсов**. Введите имя группы ресурсов для подтверждения и нажмите кнопку **Удалить**.

   !["Группы ресурсов > Обзор > Удалить группу ресурсов"](./media/quickstart-create-logic-apps-with-visual-studio/delete-resource-group.png)

1. Удалите решение Visual Studio со своего локального компьютера.

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">форум Azure Logic Apps</a>.
* Отправить идею по поводу возможности или проголосовать за нее вы можете на <a href="https://aka.ms/logicapps-wish" target="_blank">сайте отзывов пользователей Logic Apps</a>.

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы создавали, развертывали и выполняли свое приложение логики с помощью Visual Studio. Дополнительные сведения о выполнении расширенного развертывания для приложений логики и управлении им c помощью Visual Studio см. в этих статьях:

> [!div class="nextstepaction"]
> * [Управление приложениями логики в Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
> * [Создание шаблонов развертывания приложения логики с помощью Visual Studio](../logic-apps/logic-apps-create-deploy-template.md)
