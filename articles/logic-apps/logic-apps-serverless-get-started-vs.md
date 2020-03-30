---
title: Создайте первое приложение без серверов в Visual Studio
description: Создание, развертывание и управление приложением без серверов с помощью приложений логики Azure и функций Azure в Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981163"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Создайте свое первое приложение без серверов, используя приложения логики Azure и функции Azure в Visual Studio

Вы можете быстро разрабатывать и развертывать облачные приложения, используя безсерверные инструменты и возможности в Azure, такие как [Azure Logic Apps](../logic-apps/logic-apps-overview.md) и [Azure Functions.](../azure-functions/functions-overview.md) В этой статье показано, как в Visual Studio приступить к созданию бессерверного приложения на основе приложения логики, которое вызывает функцию Azure. Дополнительные сведения о бессерверных решениях в Azure см. в статье [Обзор бессерверных компонентов Azure с функциями и Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы создать приложение без серверов в Visual Studio, вам нужно:

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* Следующие инструменты. Если у вас их нет, загрузите и установите их.

  * [Visual Studio 2019, 2017, или 2015 (Сообщество или другое издание)](https://aka.ms/download-visual-studio). 
  В этом кратком руководстве используется бесплатная версия Visual Studio Community 2017.

    > [!IMPORTANT]
    > При установке Visual Studio 2019 или 2017 обязательно выберите рабочую нагрузку **разработки Azure**.

  * [Microsoft Azure SDK для .NET (версия 2.9.1 или более поздняя).](https://azure.microsoft.com/downloads/) 
  Дополнительные сведения о [пакете Azure SDK для .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Средства Azure Logic Apps для необходимой версии Visual Studio:

    * [Визуальная студия 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017.](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Вы можете скачать и установить Azure Logic Apps Tools непосредственно из Visual Studio Marketplace, или узнать, [как установить это расширение изнутри Visual Studio.](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions) 
    После завершения установки перезагрузите Visual Studio.

  * [Azure выполняет основные инструменты](https://www.npmjs.com/package/azure-functions-core-tools) для локальной отладки функций.

* Доступ к Интернету при использовании встроенного Logic App Designer.

  Конструктору требуется подключение к Интернету, чтобы создать ресурсы в Azure и считать свойства и данные из соединителей в приложении логики. 
  Например, если вы используете соединитель Dynamics CRM Online, конструктор проверяет экземпляр CRM и получает информацию для отображения доступных свойств по умолчанию и пользовательских свойств.

## <a name="create-a-resource-group-project"></a>Создание проекта группы ресурсов

Чтобы начать работу, создайте для бессерверного приложения [проект группы ресурсов Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md). В Azure вы создаете ресурсы в *группе ресурсов,* которая является логической коллекцией, которую использует для организации, управления и развертывания ресурсов для всего приложения в качестве единого актива. Группа ресурсов для бессерверного приложения в Azure содержит ресурсы для Azure Logic Apps и службы "Функции Azure". Узнайте больше о [группах ресурсов Azure и ресурсах](../azure-resource-manager/management/overview.md).

1. Запустите Visual Studio и вопийте с помощью учетной записи Azure.

1. В меню **Файл** выберите **Создать** > **Проект**.

   ![Создание проекта в Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. В разделе **Установленные** выберите **Visual C#** или **Visual Basic**. Затем выберите **группу** > ресурсов Cloud**Azure.**

   > [!NOTE]
   > Если проекта группы **облачных ресурсов** или **группы ресурсов Azure** не существует, убедитесь, что вы установили SDK Azure для Visual Studio.

   Если вы используете Visual Studio 2019, сделайте следующее:

   1. В новой поле **проекта** выберите шаблон проекта **Azure Resource Group** для Visual C или Visual Basic, а затем выберите **Следующий.**

   1. Предоставьте имя и другую информацию о проекте, которую вы хотите использовать для группы ресурсов Azure. Когда все будет готово, выберите **Создать**.

1. Дайте проекту имя и местоположение, а затем выберите **OK.**

   Visual Studio предлагает выбрать шаблон из списка шаблонов. 
   В этом примере используется шаблон Azure quickStart, чтобы можно было создать приложение без серверов, включаввв в себя логическое приложение и вызов функции Azure.

   > [!TIP]
   > В сценариях, где не требуется предварительное развертывание решения в группу ресурсов Azure, можно использовать пустой шаблон **Logic App,** который просто создает пустое приложение логики.

1. Из **шаблонов Show из этого списка местоположений** выберите **Azure quickStart (github.com/Azure/azure-quickstart-templates).**

1. В поле поиска введите "logic-app" в качестве фильтра. Из результатов выберите **шаблон 101-логики-приложения и функции-приложения.**

   ![Выберите шаблон Azure быстрыйстарт](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio создаст и откроет решение для проекта группы ресурсов. 
   Выбранный вами шаблон Azure quickStart создает шаблон развертывания под названием azuredeploy.json в проекте группы ресурсов. Этот шаблон развертывания включает определение простого приложения логики, которое вызывается запросом HTTP, вызывает функцию Azure и возвращает результат в виде ответа HTTP.

   ![Новое бессерверное решение](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Затем разместите решение в Azure. Вы должны сделать это, прежде чем вы сможете открыть шаблон развертывания и просмотреть ресурсы для вашего приложения без серверов.

## <a name="deploy-your-solution"></a>Развертывание решения

Прежде чем открыть приложение логики в Logic App Designer в Visual Studio, необходимо иметь группу ресурсов Azure, которая уже развернута в Azure. Это позволит конструктору создавать подключения к ресурсам и службам из приложения логики. Для выполнения этой задачи выполните следующие действия, чтобы развернуть решение от Visual Studio до портала Azure:

1. В Solution Explorer из меню ярлыков вашего ресурсного проекта выберите **Развертывание** > **нового**.

   ![Создание развертывания для группы ресурсов](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Если они еще не выбраны, выберите подписку Azure и группу ресурсов, в которую вы хотите развернуть. Затем выберите **Развертывание**.

   ![Параметры развертывания](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. При отосвидении окна **параметров отсечения** укажите имена ресурсов для использования приложения логики и приложения функции Azure при развертывании, а затем сохраните настройки. Используйте для приложения-функции глобально уникальное имя.

   ![Ввод имен для приложения логики и приложения-функции](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Когда Visual Studio запускает развертывание решения в указанной группе ресурсов, состояние этого развертывания отображается в **окне вывода** Visual Studio. 
   После завершения развертывания приложение логики активируется на портале Azure.

## <a name="edit-your-logic-app-in-visual-studio"></a>Отобразите логическое приложение в Visual Studio

Чтобы отсеять приложение логики после развертывания, откройте приложение логики с помощью Logic App Designer в Visual Studio.

1. В Solution Explorer, из меню ярлыка файла azuredeploy.json, выберите **Open With Logic App Designer.**

   ![Открыть azuredeploy.json в логике App Designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Если у вас нет этой команды в Visual Studio 2019, убедитесь, что установлены последние обновления для Visual Studio.

1. После того, как появится поле **Logic App Properties,** под **подпиской**выберите подписку Azure, если она еще не выбрана. В **группе ресурсов**выберите группу ресурсов и место, где вы развернули решение, а затем выберите **OK.**

   ![Свойства приложения логики](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   В открывшемся окне конструктора приложений логики вы можете добавить новые действия или изменить рабочий процесс, а затем сохранить изменения.

   ![Открытое приложение логики в конструкторе приложений логики](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Создайте проект Функции Azure

Чтобы создать свой проект и функции функций, используя JavaScript, Python, F, PowerShell, Batch или Bash, выполните последующие действия в [работе с основными инструментами Azure Functions.](../azure-functions/functions-run-local.md) Для разработки функции Azure, используя в своем решении c-библиотеку, используйте библиотеку класса C, выстраиваясь в [публикации библиотеки класса .NET в качестве функционального приложения.](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)

## <a name="deploy-functions-from-visual-studio"></a>Развертывание функций из Visual Studio

Шаблон развертывания развертывает все функции Azure, которые есть в решении из репо Git, указанного переменными в файле azuredeploy.json. Если вы создаете и авторите проект Функции в своем решении, вы можете проверить этот проект в элементе `repo` управления исходным элементом Git (например, GitHub или Azure DevOps), а затем обновить переменную, чтобы шаблон развернул функцию Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Управление приложениями логики и просмотр журнала выполнения

Для логических приложений, уже развернутых в Azure, вы все еще можете отобразить, управлять, просматривать историю выполнения и отсроять эти приложения из Visual Studio.

1. Откройте меню **Представление** в Visual Studio и выберите **Cloud Explorer**.

1. Под **всеми подписками**выберите подписку Azure, связанную с логическими приложениями, которыми вы хотите управлять, а затем выберите **Apply.**

1. В разделе **Приложения логики** выберите это приложение логики. В контекстном меню для приложения логики выберите **Open with Logic App Editor** (Открыть в редакторе приложений логики).

   > [!TIP]
   > Если у вас нет этой команды в Visual Studio 2019, убедитесь, что установлены последние обновления для Visual Studio.

Теперь опубликованное приложение логики можно скачать в проект группы ресурсов. Таким образом, хотя вы, возможно, запустили логическое приложение на портале Azure, вы все равно можете импортировать и управлять этим приложением в Visual Studio. Дополнительные сведения см. в статье [Управление приложениями логики в Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Управление приложениями логики в Visual Studio](manage-logic-apps-with-visual-studio.md)
