---
title: Отодевание и управление логическими приложениями с помощью Visual Studio с помощью Cloud Explorer
description: Отобразить, обновлять, управлять, добавлять в элемент управления исходными данными и развертывать логические приложения с помощью Visual Studio с помощью Cloud Explorer
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.custom: mvc
ms.date: 10/29/2019
ms.openlocfilehash: 73df5b7f10e038b6894996eb83dec7b6914a4536
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803196"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Управление приложениями логики в Visual Studio

Хотя вы можете создавать, отсеивать, управлять и развертывать логические приложения на [портале Azure,](https://portal.azure.com)вы также можете использовать Visual Studio, когда вы хотите добавить свои логические приложения для управления исходными данными, опубликовать различные версии и создать шаблоны [управления ресурсами Azure](../azure-resource-manager/management/overview.md) для различных сред развертывания. С помощью Visual Studio Cloud Explorer вы можете найти приложения логики и другие ресурсы Azure и управлять ими. Например, вы можете открывать, загружать, редактировать, запускать, просматривать журнал запусков, а также отключать и включать приложения логики, которые уже развернуты на портале Azure. Если вы ранее не работали с Azure Logic Apps в Visual Studio, [узнайте о создании приложений логики с помощью Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

Вы также можете [управлять логическими приложениями на портале Azure.](manage-logic-apps-with-azure-portal.md)

> [!IMPORTANT]
> Развертывание или публикация приложения логики из Visual Studio перезаписывает версию этого приложения на портале Azure. Поэтому, если вы вносите изменения на портале Azure, которые нужно сохранить, [обновите приложение логики в Visual Studio](#refresh) с портала Azure перед следующим развертыванием или публикацией из Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, [зарегистрируйтесь для получения бесплатной учетной записи Azure](https://azure.microsoft.com/free/).

* Скачайте и установите эти средства, если вы еще этого не сделали:

  * [Visual Studio 2019, 2017 или 2015 — выпуски Community или выше](https://aka.ms/download-visual-studio). В этом кратком руководстве используется бесплатная версия Visual Studio Community 2017.

    > [!IMPORTANT]
    > При установке Visual Studio 2019 или 2017 обязательно выберите рабочую нагрузку **разработки Azure**.
    > Для получения дополнительной информации смотрите в отделе [управления ресурсами, связанными с учетными записями Azure, в Visual Studio Cloud Explorer.](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view)

    Чтобы установить Cloud Explorer для Visual Studio 2015, [загрузите Cloud Explorer из Visual Studio Marketplace.](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015) Для получения дополнительной информации смотрите [в отделе ресурсов Управления, связанных с учетными записями Azure, в Cloud Explorer Visual Studio (2015).](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015)

  * [Azure SDK (2.9.1 или более позднее)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Новейшие средства Azure Logic Apps для расширения Visual Studio нужной версии:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Вы можете скачать и установить Azure Logic Apps Tools непосредственно из Visual Studio Marketplace, или узнать, [как установить это расширение изнутри Visual Studio.](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions) После завершения установки перезагрузите Visual Studio.

* Доступ к Интернету при использовании встроенного конструктора Logic Apps

  Конструктору требуется подключение к Интернету, чтобы создать ресурсы в Azure и считать свойства и данные из соединителей в приложении логики. Например, если вы используете соединитель Dynamics CRM Online, конструктор проверяет экземпляр CRM и получает информацию для отображения доступных свойств по умолчанию и пользовательских свойств.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Поиск приложений логики

В Visual Studio с помощью Cloud Explorer можно найти все приложения логики, которые связаны с подпиской Azure и развернуты на портале Azure.

1. Запустите Visual Studio. В меню **Представление** выберите **Cloud Explorer**.

1. В Cloud Explorer выберите **управление счетами.** Выберите подписку Azure, связанную с логическими приложениями, а затем выберите **Apply.** Пример:

   ![Выберите "Управление счетами"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. В зависимости от выполняемого поиска (по **группам ресурсов** или **их типам**), выполните действия ниже.

   * **Группы ресурсов**: Cloud Explorer отображает все группы ресурсов, связанные с выбранной подпиской Azure. Расширьте группу ресурсов, содержащую приложение логики, а затем выберите приложение логики.

   * **Типы ресурсов**: после выбора своей подписки Azure разверните **Приложения логики**. Когда Cloud Explorer отобразит все развернутые приложения логики, связанные с подпиской, выберите свое приложение логики.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Открытие в Visual Studio

В Visual Studio можно открывать логические приложения, ранее созданные и развертываемые либо непосредственно через портал Azure, либо в качестве проектов группы ресурсов Azure с Visual Studio.

1. Откройте Cloud Explorer и найдите свое приложение логики.

1. В меню ярлыка приложения логики выберите **Open с редактором Logic App.**

   > [!TIP]
   > Если у вас нет этой команды в Visual Studio 2019, убедитесь, что установлены последние обновления для Visual Studio.

   В этом примере показаны приложения логики по типу ресурсов, поэтому они будут отображаться в разделе **Приложения логики**.

   ![Открытие развернутого приложения логики с портала Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   После того, как приложение логики откроется в Logic Apps Designer, в нижней части дизайнера, вы можете выбрать **вид кода,** так что вы можете просмотреть основную структуру определения определения логики приложения. Если вы хотите создать шаблон развертывания для приложения логики, узнайте о [загрузке шаблона Azure Resource Manager](#download-logic-app) для этого приложения. Узнайте больше о [шаблонах Resource Manager](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Скачивание из Azure

Вы можете загрузить приложения логики с [портала Azure](https://portal.azure.com) и сохранить их в виде шаблонов [Azure Resource Manager](../azure-resource-manager/management/overview.md). Затем вы можете локально изменить шаблоны в Visual Studio и настроить приложения логики для разных сред развертывания.  При загрузке приложений логики автоматически *параметризуются* их определения внутри [шаблонов Resource Manager](../azure-resource-manager/templates/overview.md), которые также используют формат JSON.

1. В Visual Studio, открыть Cloud Explorer. Найдите и выберите логическое приложение, которое вы хотите загрузить из Azure.

1. В меню ярлыка этого приложения выберите **Open с редактором Logic App.**

   > [!TIP]
   > Если у вас нет этой команды в Visual Studio 2019, убедитесь, что установлены последние обновления для Visual Studio.

   Откроется конструктор приложений логики, отображая соответствующее приложение. Для анализа определения и структуры приложения логики в нижней части дизайнера выберите **Code View.**

1. На панели инструментов дизайнера выберите **Download**.

   ![Скачать логическое приложение с портала Azure](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Когда появится запрос указать расположение, перейдите к нему и сохраните шаблон Resource Manager для определения этого приложения логики в виде JSON-файла.

   Определение вашего приложения логики появится в подразделе `resources` внутри шаблона Resource Manager. Теперь вы можете изменить определение приложения логики и шаблон Resource Manager с помощью Visual Studio. Вы также можете добавить шаблон в качестве [проекта группы ресурсов Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) в решение Visual Studio. Узнайте о [проектах группы ресурсов Azure для логических приложений в Visual Studio.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Ссылка на интеграционную учетную запись

Для создания логических приложений для сценариев интеграции предприятий для бизнеса (B2B) можно связать приложение логики с ранее созданной [учетной записью интеграции,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) которая существует в том же регионе, что и приложение логики. Интеграционная учетная запись содержит артефакты B2B, такие как торговые партнеры, соглашения, схемы и карты, и позволяет приложению логики использовать разъемы B2B для проверки XML и кодирования или декодирования плоских файлов. Хотя эту ссылку можно [создать с помощью портала Azure,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)вы также можете использовать Visual Studio после выполнения [предпосылок,](#requirements)и ваше приложение логики существует как файл JSON (.json) в [проекте Группы ресурсов Azure.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) Узнайте о [проектах группы ресурсов Azure для логических приложений в Visual Studio.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project)

1. В Visual Studio откройте проект Azure Resource Group, содержащий логическое приложение.

1. В Solution Explorer откройте **<логику-название приложения>.json** файла ярлык, и выберите **Open With Logic App Designer**. (Клавиатура: Ctrl и L)

   ![Открыть файл приложения .json логики с дизайнером приложения логики](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Если у вас нет этой команды в Visual Studio 2019, проверьте, есть ли у вас последние обновления для Visual Studio и расширения Azure Logic Apps Tools.

1. Убедитесь, что дизайнер logic App фокусируется, выбрав вкладку или поверхность дизайнера, чтобы окно Properties отображало свойство **учетной записи интеграции** для приложения логики.

   ![Окно свойства - свойство "Интеграционный счет"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Если окно Properties еще не открыто, из меню **View** выберите **окно Свойства.** (Клавиатура: Пресс F4)

1. Откройте список свойств **интеграционного счета** и выберите учетную запись интеграции, которую вы хотите связать с вашим логическим приложением, например:

   ![Открыть список объектов недвижимости "Интеграционный счет"](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Когда вы закончите, не забудьте сохранить решение Visual Studio.

При установке свойства **учетной записи интеграции** в Visual Studio и сохранении приложения логики в виде шаблона Azure Resource Manager этот шаблон также включает декларацию параметров для выбранной учетной записи интеграции. Для получения дополнительной информации о параметрах шаблонов и логических приложениях [см.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Изменение местоположения развертывания

В Visual Studio, если приложение логики существует как файл JSON (.json) в [проекте Группы ресурсов Azure,](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) который используется для автоматизации развертывания, это приложение логики настроено на тип местоположения и определенное местоположение. Это место является либо регионом Azure, либо существующей [средой служб интеграции (ISE).](connect-virtual-network-vnet-isolated-environment.md)

Чтобы изменить тип или местоположение приложения логики, необходимо открыть определение рабочего процесса приложения (.json) от Solution Explorer с помощью Logic App Designer. Вы не можете изменить эти свойства с помощью Cloud Explorer.

> [!IMPORTANT]
> Изменение типа местоположения от [**среды службы интеграции**](connect-virtual-network-vnet-isolated-environment-overview.md) от **зоны** к [среде службы](logic-apps-pricing.md#fixed-pricing) интеграции влияет на модель ценообразования приложения логики, которая используется для выставления счетов, [ограничений,](logic-apps-limits-and-config.md#integration-account-limits) [поддержки интеграционных учетных записей](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus)и так далее. Прежде чем выбрать другой тип местоположения, убедитесь, что вы понимаете, какое влияние оказывает сячка на приложение логики.

1. В Visual Studio откройте проект Azure Resource Group, содержащий логическое приложение.

1. В Solution Explorer `<logic-app-name>.json` откройте меню ярлыка файла и выберите **Open With Logic App Designer.** (Клавиатура: Ctrl и L)

   ![Открыть файл приложения .json логики с дизайнером приложения логики](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Если у вас нет этой команды в Visual Studio 2019, проверьте, есть ли у вас последние обновления для Visual Studio и расширения Azure Logic Apps Tools.

1. Убедитесь, что конструктор logic App фокусируется, выбрав вкладку или поверхность дизайнера, чтобы в окне Свойств были показаны свойства **типа и** **местоположения** для приложения логики. Тип местоположения проекта устанавливается либо в **регионе,** либо в **интеграционных службах окружающей среды.**

   ![Окно свойств - "Выбрать тип местоположения" & свойства "Местоположение"](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Если окно Properties еще не открыто, из меню **View** выберите **окно Свойства.** (Клавиатура: Пресс F4)

1. Чтобы изменить тип местоположения, откройте список свойств **типа «Выберите место»** и выберите нужный тип местоположения.

   Например, если тип местоположения — это **среда службы интеграции,** можно выбрать **регион.**

   ![Свойство "Выбрать тип местоположения" - изменение типа местоположения](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Чтобы изменить конкретное местоположение, откройте список свойств **Местоположение.** На основе типа местоположения выберите местоположение, которое вы хотите, например:

   * Выберите другой регион Azure:

     ![Откройте список свойств "Location", выберите другой регион Azure](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Выберите другой ISE:

     ![Откройте список свойств "Местоположение", выберите другой ISE](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Когда вы закончите, не забудьте сохранить решение Visual Studio.

При изменении типа или местоположения в Visual Studio и сохранении приложения логики в виде шаблона azure Resource Manager этот шаблон также включает в себя параметры для этого типа и местоположения. Для получения дополнительной информации о параметрах шаблонов и логических приложениях [см.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Обновление из Azure

Если вы измените приложение логики на портале Azure, чтобы сохранить эти изменения, необходимо обновить версию этого приложения с изменениями в Visual Studio.

* В Visual Studio, на панели инструментов Logic App Designer, выберите **Refresh**.

  -или-

* В Cloud Explorer Visual Studio откройте контекстное меню приложения логики и выберите **Обновить**.

![Обновление приложения логики с изменениями](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Публикация обновлений приложения логики

Когда вы будете готовы развернуть обновления приложения логики от Visual Studio до Azure на панели инструментов Logic App Designer, выберите **Publish.**

![Публикация обновленного приложения логики на портале Azure](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Запуск приложения логики вручную

В Visual Studio вы можете вручную запустить приложение логики, развернутое в Azure. На панели инструментов Logic App Designer выберите **Run Trigger.**

![Ручной запуск триггера для приложения логики](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Просмотр журнала выполнения

Чтобы проверить состояние приложения логики и диагностировать проблемы, связанные с запуском этого приложения, можно просмотреть подробные сведения для запусков в Visual Studio, например входные и выходные данные.

1. В Cloud Explorer откройте контекстное меню приложения логики и выберите **Открыть журнал запусков**.

   ![Открытая история запуска для приложения логики](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Чтобы просмотреть сведения об определенном запуске, дважды щелкните его. Пример:

   ![Просмотр информации о конкретном запуске](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Для сортировки таблицы по свойствам выберите заголовок столба для этого свойства.

1. Расширьте шаги, входы и выводы которых вы хотите просмотреть, например:

   ![Просмотр входных и выходных данных для каждого шага](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Отключение и включение приложения логики

Остановить запуск триггера в следующий раз, когда будет выполнено его условие, можно без удаления приложения логики. Отключение приложения логики предотвращает создание и запуск обработчиком Logic Apps будущих экземпляров рабочего процесса для этого приложения. В Cloud Explorer откройте контекстное меню приложения логики и выберите **Отключить**.

![Отпугивайте приложение логики в Облачном исследователе](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

> [!NOTE]
> При отключении приложения логики новые экземпляры запуска не создаются. Работа всех выполняющихся и ожидающих выполнения запусков будет продолжаться до завершения, что может занять некоторое время.

Для повторной активации приложения логики в Cloud Explorer откройте меню ярлыков приложения логики и выберите **Enable.**

![Включить приложение логики в Облачном исследователе](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

## <a name="delete-your-logic-app"></a>Удаление приложения логики

Чтобы удалить приложение логики с портала Azure, в Cloud Explorer откройте его контекстное меню и выберите **Удалить**.

![Удалить логическое приложение с портала Azure](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

> [!NOTE]
> При удалении приложения логики новые экземпляры запуска не создаются. Всех выполняющиеся и ожидающие выполнения запуски будут отменены. При наличии тысяч операций запуска отмена может занять значительное время. 

## <a name="troubleshooting"></a>Устранение неполадок

При открытии проекта приложения логики в конструкторе Logic Apps может не отображаться параметр для выбора подписки Azure. Вместо этого приложение логики откроется не с той подпиской Azure, которую вы хотите использовать. Это происходит, потому что после открытия JSON-файла для приложения логики Visual Studio кэширует первую выбранную подписку для использования в будущем. Чтобы устранить эту проблему, попробуйте одно из этих действий:

* Переименуйте JSON-файл приложения логики. Кэш подписки зависит от имени файла.

* Чтобы удалить ранее выбранные подписки для *всех* логических приложений в вашем решении, удалите в каталоге решения скрытую папку настроек Visual Studio (.vs). Это расположение хранит сведения о подписке.

## <a name="next-steps"></a>Дальнейшие действия

Из этой статьи вы узнали, как управлять развернутыми приложениями логики в Visual Studio. Теперь ознакомьтесь с настройкой определений приложения логики для развертывания:

> [!div class="nextstepaction"]
> [Создание определений для приложений логики с помощью JSON](../logic-apps/logic-apps-author-definitions.md)
