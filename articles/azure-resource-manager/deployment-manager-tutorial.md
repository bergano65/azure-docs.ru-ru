---
title: Работа с шаблонами Resource Manager в диспетчере развертываний Azure | Документация Майкрософт
description: Использование шаблонов Resource Manager с помощью диспетчера развертывания Azure для развертывания ресурсов Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/23/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a42ccb1c0e60f5bf1568ccea13392186577f2875
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205720"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Руководство по использованию диспетчера развертывания Azure с шаблонами Resource Manager (общедоступная предварительная версия)

Узнайте, как использовать [диспетчер развертывания Azure](./deployment-manager-overview.md) для развертывания приложений в нескольких регионах. Если вы предпочитаете более быстрый подход, ознакомьтесь с [кратким руководством по использованию диспетчера развертывания Azure](https://github.com/Azure-Samples/adm-quickstart), в рамках которого вы сможете создать необходимые конфигурации в своей подписке и настроить артефакты для развертывания приложения в нескольких регионах. В этом кратком руководстве выполняются те же задачи, что и в этом.

Чтобы использовать диспетчер развертывания, необходимо создать два шаблона:

* **Шаблон топологии**: описывает ресурсы Azure для создания приложений и путь их развертывания.
* **Шаблон развертывания**: описывает действия, выполняемые при развертывании приложений.

> [!IMPORTANT]
> Если ваша подписка помечена для раннего выпуска, что позволяет тестировать новые функции Azure, вы можете использовать диспетчер развертывания Azure только для развертывания в регионах, поддерживающих ранний выпуск. 

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Ознакомление со сценарием
> * Скачивание учебных файлов
> * Подготовка артефактов.
> * Создание определяемого пользователем управляемого удостоверения.
> * Создание шаблона топологии службы.
> * Создание шаблона развертывания.
> * Развертывание шаблонов.
> * Проверка развертывания
> * Развертывание более новой версии.
> * Очистка ресурсов

Дополнительные ресурсы:

* [Справочник по REST API диспетчера развертывания Azure](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Руководство. Use health check in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md) (Учебник по использованию проверки работоспособности в диспетчере развертывания Azure).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* Опыт разработки [шаблонов Azure Resource Manager](./resource-group-overview.md).
* Установите Azure PowerShell. Дополнительные сведения см. в статье [Начало работы с Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Командлеты диспетчера развертывания. Чтобы установить эти командлеты предварительной версии, вам понадобится последняя версия PowerShellGet. Сведения о получении последней версии см. в статье [Установка PowerShellGet](/powershell/gallery/installing-psget). После установки PowerShellGet закройте окно PowerShell. Откройте новое окно PowerShell с повышенными привилегиями и выполните следующую команду:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

* [Обозреватель службы хранилища Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/). Обозреватель службы хранилища не требуется, но он упрощает работу.

## <a name="understand-the-scenario"></a>Ознакомление со сценарием

Шаблон топологии службы описывает ресурсы Azure для создания службы и путь их развертывания. Определение топологии службы имеет следующую иерархию:

* Топология службы:
  * Службы
    * Модули службы.

На следующей схеме показана топология службы, используемая в этом руководстве:

![Схема сценария управления диспетчера развертывания Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

В западной и восточной части США расположены две службы.  Каждая служба имеет два модуля службы — интерфейс веб-приложения и учетная запись хранения для серверной части. Определения модулей службы содержат ссылки на файлы шаблонов и параметры для создания веб-приложений и учетных записей хранения.

## <a name="download-the-tutorial-files"></a>Скачивание учебных файлов

1. Скачайте [шаблоны и артефакты](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip), используемые в этом руководстве.
2. Распакуйте файлы на локальный компьютер.

В корневой папке есть две папки:

* **ADMTemplates**: содержит шаблоны диспетчера развертывания, включая такие:
  * CreateADMServiceTopology.json;
  * CreateADMServiceTopology.Parameters.json;
  * CreateADMRollout.json;
  * CreateADMRollout.Parameters.json.
* **ArtifactStore**: содержит артефакты шаблонов и двоичные артефакты. Подробнее см. в разделе [Подготовка артефактов](#prepare-the-artifacts).

Обратите внимание, что существует два набора шаблонов.  Один набор — это шаблоны диспетчера развертывания, которые используются для развертывания топологии службы и выпуска. Другой набор вызывается из сервисных модулей для создания веб-служб и учетных записей хранения.

## <a name="prepare-the-artifacts"></a>Подготовка артефактов.

Папка ArtifactStore из скачивания содержит две папки:

![Диаграмма источника артефакта диспетчера развертывания Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* Папка **templates**: содержит артефакты шаблона. **1.0.0.0** и **1.0.0.1** представляют две версии двоичных артефактов. В каждой версии есть папка для каждой службы (Service East U. S. и Service West U. S.). Каждая служба имеет пару шаблонов и файлов параметров для создания учетной записи хранения и другую пару для создания веб-приложения. Шаблон веб-приложения вызывает сжатый пакет, содержащий файлы веб-приложения. Сжатый файл представляет собой двоичный артефакт, хранящийся в папке двоичных файлов.
* Папка **binaries** содержит двоичные артефакты. **1.0.0.0** и **1.0.0.1** представляют две версии двоичных артефактов. Внутри каждой версии есть один ZIP-файл для создания веб-приложения в западной части США, а другой ZIP-файл — для создания веб-приложения в восточной части США.

Две версии (1.0.0.0 и 1.0.0.1) предназначены для [развертывания версии](#deploy-the-revision). Несмотря на то, что как артефакты шаблонов, так и двоичные артефакты имеют две версии, только двоичные артефакты различаются между двумя версиями. На практике двоичные артефакты чаще обновляются по сравнению с артефактами шаблонов.

1. Откройте **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json** в текстовом редакторе. Это базовый шаблон для создания учетной записи хранения.
2. Откройте **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**.

    ![Руководство по работе с диспетчером развертывания Azure: создание шаблона веб-приложения](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Шаблон вызывает пакет развертывания, который содержит файлы веб-приложения. В этом руководстве сжатый пакет содержит только файл index.html.
3. Откройте **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**.

    ![Руководство по работе с диспетчером развертывания Azure: параметры containerRoot для создания шаблона веб-приложения](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Значение deployPackageUri является путем к пакету развертывания. Параметр содержит переменную **$containerRoot**. Значение $containerRoot предоставляется в [шаблоне выпуска](#create-the-rollout-template) путем объединения исходного расположения артефакта SAS, корня артефакта и deployPackageUri.
4. Откройте **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    В html указано местоположение и информация о версии. В двоичном файле в папке 1.0.0.1 показано сообщение "Версия 1.0.0.1". После развертывания службы можно перейти к этим страницам.
5. Ознакомьтесь с другими файлами артефактов. Это поможет вам лучше понять сценарий.

Артефакты шаблонов используются шаблоном топологии службы, а двоичные артефакты используются шаблоном развертывания. Как шаблон топологии, так и шаблон развертывания определяют ресурс Azure источника артефакта, который используется для указания диспетчеру ресурсов на шаблон и двоичные артефакты, применяемые в развертывании. Чтобы упростить руководство, одна учетная запись хранения используется для хранения как артефактов шаблона, так и двоичных артефактов. Оба источника артефакта указывают на одну и ту же учетную запись хранения.

1. Создайте учетную запись хранения Azure. Для инструкций см. [Краткое руководство. Передача, скачивание и составление списка больших двоичных объектов с помощью портала Azure](../storage/blobs/storage-quickstart-blobs-portal.md).
2. Создайте контейнер больших двоичных объектов в учетной записи хранения
3. Скопируйте две папки (двоичные файлы и шаблоны) и их содержимое в контейнер больших двоичных объектов. [Обозреватель службы хранилища Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) поддерживает функцию перетаскивания.
4. Получите местоположение SAS контейнера, используя следующие инструкции:

    1. В Обозревателе службы хранилища Azure перейдите к контейнеру больших двоичных объектов.
    2. Щелкните правой кнопкой мыши нужный контейнер больших двоичных объектов и в левой области выберите команду **Get Shared Access Signature**(Получить подписанный URL-адрес).
    3. Настройте **время начала** и **время окончания срока действия**.
    4. Нажмите кнопку **Создать**.
    5. Скопируйте URL-адрес. Этот URL-адрес необходим для заполнения полей в двух файлах параметров: [файле параметров топологии](#topology-parameters-file) и [файле параметров развертывания](#rollout-parameters-file).

## <a name="create-the-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем

Далее в этом руководстве развертывается выпуск. Для выполнения действий по развертыванию (например, развертывание веб-приложений и учетной записи хранения) требуется управляемое удостоверение, назначаемое пользователем. Этому удостоверению должен быть предоставлен доступ к подписке Azure, в которую развертывается служба, а также требуется достаточное разрешение для завершения развертывания артефакта.

Вам необходимо создать управляемое удостоверение, назначаемое пользователем, и настроить управление доступом для своей подписки.

> [!IMPORTANT]
> Управляемое удостоверение, назначаемое пользователем, должно быть в том же расположении, что и [выпуск](#create-the-rollout-template). В настоящее время ресурсы диспетчера развертывания, включая выпуск, могут создаваться только в центральной части США или восточной части США 2. Но это справедливо только для ресурсов диспетчера развертывания (например, топологии службы, служб, модулей служб, развертываний и действий). Ваш целевые ресурсы можно развернуть в любом поддерживаемом регионе Azure. В этом руководстве соответствующие ресурсы развертываются в центральной части США, а службы — в восточной и западной частях США. Это ограничение будет снято в будущем.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Создайте [управляемое удостоверение, назначаемое пользователем](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. На портале щелкните **Подписки** в меню слева и выберите свою подписку.
4. Выберите **Управление доступом (IAM)** , а затем **Добавление назначения ролей**.
5. Введите или выберите следующие значения:

    ![Руководство по использованию диспетчера развертывания Azure: управление доступом с помощью управляемого удостоверения, назначаемого пользователем](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Роль**: предоставьте достаточно разрешений на выполнение развертывания артефакта (веб-приложения и учетные записи хранения). Выберите **участника**, как описано в этом руководстве. На практике следует ограничить права доступа до минимума.
    * **Assigned access to** (Кому назначить доступ): выберите **Управляемое удостоверение, назначаемое пользователем**.
    * Выберите назначаемое пользователем управляемое удостоверение, которое вы создали.
6. Щелкните **Сохранить**.

## <a name="create-the-service-topology-template"></a>Создание шаблона топологии службы

Откройте **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>Параметры

Этот шаблон содержит следующие параметры:

![Руководство по использованию диспетчера развертывания Azure: параметры топологии шаблона](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-parameters.png)

* **namePrefix**. Этот префикс используется для создания имен ресурсов диспетчера развертывания. Например, если использовать префикс jdoe, имя топологии службы будет **jdoe**ServiceTopology.  Имена ресурсов определены в разделе переменных этого шаблона.
* **azureResourcelocation**. Чтобы упростить работу с руководством, все ресурсы совместно используют это расположение, если не указано иное. В настоящее время ресурсы диспетчера развертывания Azure могут создаваться в регионе **Центральная часть США** или **Восточная часть США 2**.
* **artifactSourceSASLocation**. Универсальный код ресурса (URI) SAS для контейнера больших двоичных объектов, где файлы параметров и шаблона модулей службы хранятся для развертывания.  Подробнее см. в разделе [Подготовка артефактов](#prepare-the-artifacts).
* **templateArtifactRoot**. Путь смещения из контейнера больших двоичных объектов, где хранятся шаблоны и параметры. Значение по умолчанию — **templates/1.0.0.0**. Не изменяйте это значение, если вы не хотите изменять структуру папок, как описано в разделе [Подготовка артефактов](#prepare-the-artifacts). В этом руководстве используются относительные пути.  Полный путь формируется путем сцепления **artifactSourceSASLocation**, **templateArtifactRoot** и **templateArtifactSourceRelativePath** (или **parametersArtifactSourceRelativePath**).
* **targetSubscriptionID**. Идентификатор подписки, в которую будут развернуты ресурсы диспетчера развертывания и на которую будут выставляться счета. Используйте идентификатор подписки в этом руководстве.

### <a name="the-variables"></a>Переменные

Раздел переменных определяет имена ресурсов, местоположения Azure для двух служб: **службы WUS** и **службы EUS**; и пути к артефактам.

![Руководство по использованию диспетчера развертывания Azure: переменные топологии шаблона](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Сравните пути артефакта со структурой папок, которую вы скачали в учетную запись хранения. Обратите внимание, что пути артефактов являются относительными. Полный путь формируется путем сцепления **artifactSourceSASLocation**, **templateArtifactRoot** и **templateArtifactSourceRelativePath** (или **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>Ресурсы

На корневом уровне определено два ресурса: *источник артефакта* и *топология службы*.

Определение источника артефакта:

![Руководство по использованию диспетчера развертывания Azure: источник ресурсов артефактов топологии шаблона](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

На следующем снимке экрана отображаются только некоторые части определений топологии служб, служб и модулей служб:

![Руководство по использованию диспетчера развертывания Azure: ресурсы шаблона топологии, топология службы](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId** позволяет связать артефакт исходного ресурса с ресурсом топологии службы.
* **dependsOn**. Все ресурсы топологии службы зависят от исходного ресурса артефакта.
* **artifacts** указывает артефакты шаблонов.  Здесь используются относительные пути. Полный путь формируется путем сцепления artifactSourceSASLocation (определенный в источнике артефакта), artifactRoot (определенный в источнике артефакта) и templateArtifactSourceRelativePath (или parametersArtifactSourceRelativePath).

### <a name="topology-parameters-file"></a>Файл параметров топологии

Создайте файл параметров, используемый с шаблоном топологии.

1. Откройте **\ADMTemplates\CreateADMServiceTopology.Parameters** в Visual Studio Code или любом текстовом редакторе.
2. Укажите значения параметров.

    * **namePrefix**. Введите строку из 4–5 символов. Этот префикс используется для создания уникальных имен ресурсов Аzure.
    * **azureResourceLocation**. Если вы не знакомы с расположениями Azure, используйте **centralus** в этом руководстве.
    * **artifactSourceSASLocation**. Введите URI SAS в корневой каталог (контейнер больших двоичных объектов), где хранятся файлы параметров и шаблон модулей службы для развертывания.  Подробнее см. в разделе [Подготовка артефактов](#prepare-the-artifacts).
    * **templateArtifactRoot**. Если вы не изменяли структуру папок артефактов, используйте **templates/1.0.0.0** в этом руководстве.
    * **targetScriptionID**. Введите идентификатор подписки Azure.

> [!IMPORTANT]
> Шаблон топологии и шаблон развертывания совместно используют некоторые общие параметры. Они должны иметь общие параметры. Это такие параметры: **namePrefix**, **azureResourceLocation** и **артефактSourceSASLocation** (оба источника артефактов используют одну и ту же учетную запись хранения в этом руководстве).

## <a name="create-the-rollout-template"></a>Создание шаблона развертывания

Откройте **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>Параметры

Этот шаблон содержит следующие параметры:

![Руководство по использованию диспетчера развертывания Azure: параметры шаблона выпуска](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **namePrefix**. Этот префикс используется для создания имен ресурсов диспетчера развертывания. Например, если использовать префикс jdoe, имя развертывания будет **jdoe**Rollout.  Имена определены в разделе переменных этого шаблона.
* **azureResourcelocation**. Чтобы упростить работу с руководством, все ресурсы диспетчера совместно используют это расположение, если не указано иное. В настоящее время ресурсы диспетчера развертывания Azure могут создаваться в регионе **Центральная часть США** или **Восточная часть США 2**.
* **artifactSourceSASLocation**. URI SAS для контейнера больших двоичных объектов, где файлы параметров и шаблона модулей службы хранятся для развертывания.  Подробнее см. в разделе [Подготовка артефактов](#prepare-the-artifacts).
* **binaryArtifactRoot**.  Значение по умолчанию — **binaries/1.0.0.0**. Не изменяйте это значение, если вы не хотите изменять структуру папок, как описано в разделе [Подготовка артефактов](#prepare-the-artifacts). В этом руководстве используются относительные пути.  Полный путь формируется путем сцепления **artifactSourceSASLocation**, **binaryArtifactRoot** и **deployPackageUri**, указанных в файле CreateWebApplicationParameters.json.  Подробнее см. в разделе [Подготовка артефактов](#prepare-the-artifacts).
* **managedIdentityID**. Управляемое удостоверение, назначаемое пользователем, который выполняет действия развертывания. Дополнительные сведения см. в разделе [Создание управляемого удостоверения, назначаемого пользователем](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Переменные

Раздел переменных определяет имена ресурсов. Убедитесь, что имя топологии службы, имена служб и имена модулей службы соответствуют именам, определенным в [шаблоне топологии](#create-the-service-topology-template).

![Руководство по использованию диспетчера развертывания Azure: переменные шаблона выпуска](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Ресурсы

На корневом уровне определено три ресурса: источник артефакта, топология службы и выпуск.

Определение источника артефактов идентично определению шаблона топологии.  Дополнительные сведения см. в статье [Руководство. Использование диспетчера развертывания Azure с шаблонами Resource Manager (предварительная версия)](#create-the-service-topology-template).

На следующем снимке экрана показано определение шага ожидания:

![Руководство по использованию диспетчера развертывания Azure: шаг ожидания ресурса шаблона выпуска](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Длительность соответствует [стандарту ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (заглавные буквы обязательны) является примером одноминутного ожидания.

На следующем снимке экрана показаны только некоторые части определения развертывания:

![Руководство по использованию диспетчера развертывания Azure: выпуск шаблона ресурсов](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**. Ресурс развертывания зависит от исходного ресурса артефакта, а также от определенных шагов.
* **artifactSourceId** позволяет связать артефакт исходного ресурса с выпуском службы.
* **targetServiceTopologyId** позволяет связать ресурс топологии службы с выпуском службы.
* **deploymentTargetId**. Это идентификатор ресурса единицы службы ресурса топологии службы.
* **preDeploymentSteps** и **postDeploymentSteps**: содержат шаги развертывания. В шаблоне вызывается шаг ожидания.
* **dependsOnStepGroups**: позволяет настроить зависимости между группами шагов.

### <a name="rollout-parameters-file"></a>Файл параметров выпуска

Создайте файл параметров, используемый с шаблоном выпуска.

1. Откройте **\ADMTemplates\CreateADMRollout.Parameters** в Visual Studio Code или любом текстовом редакторе.
2. Укажите значения параметров.

    * **namePrefix**. Введите строку из 4–5 символов. Этот префикс используется для создания уникальных имен ресурсов Аzure.
    * **azureResourceLocation**. В настоящее время ресурсы диспетчера развертывания Azure могут создаваться в регионе **Центральная часть США** или **Восточная часть США 2**.
    * **artifactSourceSASLocation**. Введите URI SAS в корневой каталог (контейнер больших двоичных объектов), где хранятся файлы параметров и шаблон модулей службы для развертывания.  Подробнее см. в разделе [Подготовка артефактов](#prepare-the-artifacts).
    * **binaryArtifactRoot**. Если вы не изменяли структуру папок артефактов, используйте **binaries/1.0.0.0** в этом руководстве.
    * **managedIdentityID**. Введите управляемое удостоверение, назначенное пользователем. Дополнительные сведения см. в разделе [Создание управляемого удостоверения, назначаемого пользователем](#create-the-user-assigned-managed-identity). Синтаксис:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Шаблон топологии и шаблон развертывания совместно используют некоторые общие параметры. Они должны иметь общие параметры. Это такие параметры: **namePrefix**, **azureResourceLocation** и **артефактSourceSASLocation** (оба источника артефактов используют одну и ту же учетную запись хранения в этом руководстве).

## <a name="deploy-the-templates"></a>Развертывание шаблонов.

Azure PowerShell можно использовать для развертывания шаблонов.

1. Запустите сценарий для развертывания топологии службы.

    ```azurepowershell
    $resourceGroupName = "<Enter a Resource Group Name>"
    $location = "Central US"
    $filePath = "<Enter the File Path to the Downloaded Tutorial Files>"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location "$location"

    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    > [!NOTE]
    > `New-AzResourceGroupDeployment` выполняет асинхронный вызов. Сообщение об успешном выполнении означает только то, что развертывание начато успешно. Для проверки развертывания см. шаг 2 и 4 этой процедуры.

2. Убедитесь, что топология службы и выделенные ресурсы были успешно созданы с использованием портала Azure:

    ![Руководство по использованию диспетчера развертывания Azure: развернутые ресурсы шаблона топологии](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Для просмотра ресурсов необходимо выбрать **Показать скрытые типы**.

3. <a id="deploy-the-rollout-template"></a>Развертывание шаблона выпуска

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Проверьте выполнение развертывания с помощью следующего сценария PowerShell:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "<Enter the Rollout Name>" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Перед запуском этого командлета необходимо установить командлеты диспетчера развертывания PowerShell. См. предварительные требования. Просмотреть выходные данные полностью можно с помощью параметра -verbose.

    В следующем примере показано состояние выполнения:

    ```
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    После успешного развертывания вы увидите две группы ресурсов, по одной для каждой службы.

## <a name="verify-the-deployment"></a>Проверка развертывания

1. Откройте [портал Azure](https://portal.azure.com).
2. Перейдите к новым веб-приложениям в новых группах ресурсов, созданных в результате развертывания выпуска.
3. Откройте веб-приложение в веб-браузере. Проверьте местоположение и версию в файле index.html.

## <a name="deploy-the-revision"></a>Развертывание редакции

При наличии новой версии (1.0.0.1) веб-приложения вы можете использовать следующую процедуру для повторного развертывания веб-приложения.

1. Откройте CreateADMRollout.Parameters.json.
2. Обновите **binaryArtifactRoot** до версии **binaries/1.0.0.1**.
3. Повторите развертывание, как указано в разделе [Развертывание шаблонов](#deploy-the-rollout-template).
4. Проверьте развертывание, как указано в разделе [Проверка развертывания](#verify-the-deployment). На веб-странице должна быть показана версия 1.0.0.1.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. Используйте поле **Фильтровать по имени**, чтобы сузить группы ресурсов, созданные в этом руководстве. Их должно быть от 3 до 4:

    * **&lt;namePrefix>rg**: содержит ресурсы диспетчера развертывания.
    * **&lt;namePrefix>ServiceWUSrg**: содержит ресурсы, определенные в ServiceWUS.
    * **&lt;namePrefix>ServiceEUSrg**: содержит ресурсы, определенные в ServiceEUS.
    * Группа ресурсов для определяемого пользователем управляемого удостоверения.
3. Выберите имя группы ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.
5. Повторите последние два шага для удаления других групп ресурсов, созданных в этом руководстве.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как использовать диспетчер развертывания Azure. Интеграция мониторинга работоспособности в диспетчер развертывания Azure описана в разделе [Tutorial: Use health check in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md) (Учебник по использованию проверки работоспособности в диспетчере развертывания Azure).
