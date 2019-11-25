---
title: Непрерывная интеграция и непрерывное развертывание в Azure IoT Edge | Документация Майкрософт
description: Настройка непрерывной интеграции и непрерывного развертывания в Azure IoT Edge с использованием Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 85f77d1132af63681ee92cfd2bde82a71d8ed999
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457242"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Непрерывная интеграция и непрерывное развертывание в Azure IoT Edge

Вы можете легко внедрить DevOps для приложений Azure IoT Edge, используя встроенные задания Azure IoT Edge в Azure Pipelines. В этой статье показано, как с помощью функций непрерывной интеграции и непрерывного развертывания, предоставляемых Azure Pipelines, быстро и эффективно выполнять сборку, тестирование и развертывание приложений в Azure IoT Edge. 

![Схема ветвей CI и CD для разработки и эксплуатации](./media/how-to-ci-cd/cd.png)

Из этой статьи вы узнаете, как с помощью встроенных задач Azure IoT Edge в Azure Pipelines создать два конвейера для вашего решения IoT Edge. There are four actions can be used in the Azure IoT Edge tasks.
   - **Azure IoT Edge - Build Module images** takes your IoT Edge solution code and builds the container images.
   - **Azure IoT Edge - Push Module images** pushes module images to the container registry you specified.
   - **Azure IoT Edge - Generate Deployment Manifest** takes a deployment.template.json file and the variables, then generates the final IoT Edge deployment manifest file.
   - **Azure IoT Edge - Deploy to IoT Edge devices** helps create IoT Edge deployments to single/multiple IoT Edge devices.

## <a name="prerequisites"></a>Технические условия

* Репозиторий Azure Repos. Если у вас его нет, вы можете [создать репозиторий Git в проекте](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Решение IoT Edge, зафиксированное и отправленное в репозиторий. Если вы хотите создать пример решения для тестирования инструкций в этой статье, следуйте шагам, изложенным в статье [Использование Visual Studio Code для разработки и отладки модулей для Azure IoT Edge](how-to-vs-code-develop-module.md) или [Сведения об использовании Visual Studio 2017 для разработки и отладки модулей C# для Azure IoT Edge (предварительная версия)](how-to-visual-studio-develop-csharp-module.md).
   * Все, что требуется в этой статье, — это папка решения, созданная по шаблону IoT Edge в Visual Studio Code или Visual Studio. Вам не требуется создавать, отправлять, развертывать или отлаживать этот код, чтобы продолжить. Необходимо настроить эти процессы в Azure Pipelines. 
   * Если вы создаете решение, сначала клонируйте репозиторий локально. Затем вы сможете создать решение непосредственно в папке репозитория. Кроме того, вы можете легко фиксировать и отправлять файлы из нее. 
* Реестр контейнеров, в который можно отправлять образы модулей. Вы можете использовать [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) или сторонний реестр. 
* Активный [Центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) по крайней мере с одним устройством IoT Edge для тестирования отдельных этапов тестового и рабочего развертывания. Следуйте кратким руководствам, чтобы создать устройство IoT Edge в [Linux](quickstart-linux.md) или [Windows](quickstart.md).


Дополнительные сведения об использовании Azure Repos см. в статье [Share your code with Visual Studio 2015 and Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts) (Предоставление общего доступа к коду с помощью Visual Studio и Azure Repos).

## <a name="configure-continuous-integration"></a>Настройка непрерывной интеграции
В этом разделе показано, как создать конвейер сборки. Настройте конвейер на автоматический запуск при регистрации любых изменений в примере решения IoT Edge и публикации журналов сборки.

>[!NOTE]
>В этой статье используется визуальный конструктор Azure DevOps. Прежде чем выполнять действия, описанные в этом разделе, отключите эту предварительную версию функции для нового интерфейса создания конвейера YAML. 
>1. В Azure DevOps щелкните значок своего профиля, а затем выберите **Функции предварительной версии**.
>2. Отключите **новый интерфейс для создания конвейеров YAML**. 
>
>Дополнительные сведения см. в разделе о [создании конвейера сборки](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Sign into your Azure DevOps organization (**https:\//dev.azure.com/{your organization}/** ) and open the project that contains your IoT Edge solution repository.

   В этой статье мы создали репозиторий, который называется **IoTEdgeRepo**. Этот репозиторий содержит **IoTEdgeSolution** с кодом для модуля под названием **filtermodule**. 

   ![Открытие своего проекта DevOps](./media/how-to-ci-cd/init-project.png)

2. Перейдите к Azure Pipelines в проекте. Откройте вкладку **Сборки** и выберите **Новый конвейер**. Если же конвейеры сборки уже есть, нажмите кнопку **Создать**. Затем выберите **Новый конвейер сборки**.

    ![Создание конвейера сборки](./media/how-to-ci-cd/add-new-build.png)

3. Следуйте инструкциям на экране для создания конвейера. 

   1. Укажите сведения об источнике для нового конвейера сборки. Выберите **Azure Repos Git** в качестве источника, а затем выберите проект, репозиторий и ветвь, в которой расположен код решения IoT Edge. Затем выберите **Продолжить**. 

      ![Выбор источника конвейера](./media/how-to-ci-cd/pipeline-source.png)

   2. Выберите **Пустое задание** вместо шаблона. 

      ![Начало работы с пустым проектом](./media/how-to-ci-cd/start-with-empty.png)

4. После создания конвейера вы перейдете в редактор конвейера. В описании конвейера выберите правильный пул агентов, в зависимости от целевой платформы: 
    
   * Если вы хотите компилировать модули на платформе amd64 для контейнеров Linux, выберите **Hosted Ubuntu 1604** (Размещение в Ubuntu 1604).

   * Если вы хотите создавать модули на платформе amd64 для контейнеров Windows 1809, вам необходимо [настроить агента в Windows в локальной среде](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * If you would like to build your modules in platform arm32v7 or arm64 for Linux containers, you need to [set up self-hosted agent on Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Настройка пула агентов сборки](./media/how-to-ci-cd/configure-env.png)

5. Конвейер предварительно настроен с заданием **Agent job 1** (Задание агента 1). Select the plus sign ( **+** ) to add three tasks to the job: **Azure IoT Edge** twice, **Copy Files** once and **Publish Build Artifacts** once. (Наведите указатель мыши на имя каждой задачи, чтобы увидеть кнопку **Добавить**.)

   ![Добавление задачи Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   When all four tasks are added, your Agent job looks like the following example:
    
   ![Три задачи в конвейер сборки](./media/how-to-ci-cd/add-tasks.png)

6. Выберите первую задачу **Azure IoT Edge**, чтобы изменить ее. This task builds all modules in the solution with the target platform that you specify.

   * **Display name**: Accept the default **Azure IoT Edge - Build module images**.
   * **Action**: Accept the default **Build module images**. 
   * **.template.json file**: Select the ellipsis ( **...** ) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. 
   * **Default platform**: Select the appropriate platform for your modules based on your target IoT Edge device. 
   * **Output variables**: The output variables include a reference name that you can use to configure the file path where your deployment.json file will be generated. Укажите какое-нибудь запоминающееся справочное имя, например **edge**. 

7. Выберите вторую задачу **Azure IoT Edge**, чтобы изменить ее. Эта задача передает все образы модулей в выбранный реестр контейнеров.

   * **Display name**: The display name is automatically updated when the action field changes. 
   * **Action**: Use the dropdown list to select **Push module images**. 
   * **Container registry type**: Select the type of container registry that you use to store your module images. Форма меняется в зависимости от выбранного типа реестра. Если вы выберете **Реестр контейнеров Azure**, используйте раскрывающиеся списки, чтобы выбрать подписку Azure и имя реестра контейнеров. При выборе **универсального реестра контейнеров** щелкните **Создать**, чтобы создать подключение к службе реестра. 
   * **.template.json file**: Select the ellipsis ( **...** ) and navigate to the **deployment.template.json** file in the repository that contains your IoT Edge solution. 
   * **Default platform**: Select the same platform as your built module images.

   Если для размещения образов модулей вы используете несколько реестров контейнеров, создайте дубликат этой задачи и выберите в нем другой реестр контейнеров, затем разместите действие **Bypass module(s)** (Обойти модуль/модули) в разделе дополнительных параметров, чтобы пропускать образы, не имеющие отношение к конкретному реестру.

8. Select the **Copy Files** task to edit it. Use this task to copy files to the artifact staging directory.

   * **Display name**: Copy Files to: Drop folder.
   * **Contents**: Put two lines in this section, `deployment.template.json` and `**/module.json`. These two types of files are the inputs to generate IoT Edge deployment manifest. Need to be copied to the artifact staging folder and published for release pipeline.
   * **Target Folder**: Put the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn about the description.

9. Выберите задачу **Publish Build Artifacts** (Публикация артефактов сборки), чтобы изменить его. Provide artifact staging directory path to the task so that the path can be published to release pipeline.
   
   * **Display name**: Publish Artifact: drop.
   * **Path to publish**: Put the variable `$(Build.ArtifactStagingDirectory)`. See [Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) to learn about the description.
   * **Artifact name**: drop.
   * **Artifact publish location**: Azure Pipelines.


10. Откройте вкладку **Триггеры** и установите флажок **Enable continuous integration** (Включить непрерывную интеграцию). Убедитесь, что включена ветвь, содержащая ваш код.

    ![Включение триггера непрерывной интеграции](./media/how-to-ci-cd/configure-trigger.png)

11. Сохраните новый конвейер сборки кнопкой **Сохранить**.

Этот конвейер настроен на автоматический запуск при отправке нового кода в репозиторий. Последняя задача, публикация артефактов конвейера, активирует конвейер выпуска. Перейдите к следующему разделу для создания конвейера выпуска. 

## <a name="configure-continuous-deployment"></a>настройка непрерывного развертывания;
В этом разделе описано, как создать конвейер выпуска, настроенный для автоматического запуска при получении артефакта от конвейера сборки и отображения журналов сборки в Azure Pipelines.

Create a new pipeline, and add a new stage 

1. На вкладке **Releases** (Выпуски) выберите **+ New pipeline** (Создать конвейер). Если у вас уже есть конвейеры выпуска, нажмите кнопку **+ Создать** и щелкните **+ Создать конвейер выпуска**.  

    ![Добавление конвейера выпуска](./media/how-to-ci-cd/add-release-pipeline.png)

2. Когда будет предложено выбрать шаблон, выберите с **пустым заданием**.

    ![Начало работы с пустым заданием](./media/how-to-ci-cd/start-with-empty-job.png)

3. Ваш новый конвейер выпуска инициализируется с одним этапом, который называется **Этап 1**. Rename Stage 1 to **dev** and treat it as a test environment. Usually, continuous deployment pipelines have multiple stages including **dev**, **staging** and **prod**. You can create more based on your DevOps practice. Закройте окно сведений об этапе после его переименования. 

4. Свяжите выпуск с артефактами сборки, опубликованными конвейером сборки. Щелкните **Add** (Добавить) в области артефактов.

   ![Добавление артефактов](./media/how-to-ci-cd/add-artifacts.png)  
    
5. На **странице добавления артефакта** выберите тип источника **Сборка**. Затем выберите созданный проект и конвейер сборки. а затем щелкните **Добавить**.

   ![Добавление артефакта сборки](./media/how-to-ci-cd/add-an-artifact.png)

6. Откройте триггеры артефакта и выберите переключатель для включения триггера непрерывного развертывания. Теперь новый выпуск будет создаваться каждый раз, когда новая сборка становится доступной.

   ![Настройка триггера непрерывного развертывания](./media/how-to-ci-cd/add-a-trigger.png)

7. The **dev** stage is preconfigured with one job and zero tasks. From the pipeline menu, select **Tasks** then choose the **dev** stage.  Выберите число заданий и задач, чтобы настроить задачи на этом этапе.

    ![Configure dev tasks](./media/how-to-ci-cd/view-stage-tasks.png)

8. In the **dev** stage, you should see a default **Agent job**. Вы можете настроить сведения о задании агента, но задача развертывания не зависит от платформы, поэтому вы можете использовать **Hosted VS2017** или **Hosted Ubuntu 1604** в **пуле агентов** (или любой другой агент, управляемый вами). 

9. Select the plus sign ( **+** ) to add two task. Search for and add **Azure IoT Edge** twice.

    ![Add tasks for dev](./media/how-to-ci-cd/add-task-qa.png)

10. Select the first **Azure IoT Edge** task and configure it with the following values:

    * **Display name**: The display name is automatically updated when the action field changes. 
    * **Action**: Use the dropdown list to select **Generate deployment manifest**. В случае изменения значения действия для соответствия также обновляется отображаемое имя задачи.
    * **.template.json file**: Put the path `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`. The path is published from build pipeline.
    * **Default platform**: Choose the same value when building the module images.
    * **Output path**: Put the path `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. This path is the final IoT Edge deployment manifest file.

    These configurations helps replace the module image URLs in the `deployment.template.json` file. The **Generate deployment manifest** also helps replace the variables with the exact value you defined in the `deployment.template.json` file. In VS/VS Code, you are specifying the actual value in a `.env` file. In Azure Pipelines, you set the value in Release Pipeline Variables tab. Move to Variables tab and configure the Name and Value as following.

    * **ACR_ADDRESS**: Your Azure Container Registry address. 
    * **ACR_PASSWORD**: Your Azure Container Registry password.
    * **ACR_USER**: Your Azure Container Registry username.

    If you have other variables in your project, you can specify the name and value in this tab. The **Generate deployment manifest** can only recognize the variables are in `${VARIABLE}` flavor, make sure you are using this in your `*.template.json` files.

    ![Configure variables for release pipeline](./media/how-to-ci-cd/configure-variables.png)

10. Select the second **Azure IoT Edge** task and configure it with the following values:

    * **Display name**: The display name is automatically updated when the action field changes. 
    * **Action**: Use the dropdown list to select **Deploy to IoT Edge devices**. В случае изменения значения действия для соответствия также обновляется отображаемое имя задачи.
    * **Azure subscription**: Select the subscription that contains your IoT Hub.
    * **IoT Hub name**: Select your IoT hub. 
    * **Choose single/multiple device**: Choose whether you want the release pipeline to deploy to one device or multiple devices. 
      * При развертывании на одно устройство введите **идентификатор устройства IoT Edge**. 
      * При развертывании на нескольких устройствах следует указать **целевое условие** устройства. The target condition is a filter to match a set of IoT Edge devices in IoT Hub. Если в качестве условия вы решите использовать теги устройства, не забудьте обновить соответствующие теги устройств в двойниках устройств Центра Интернета вещей. Обновите **идентификатор развертывания** и **приоритет развертывания IoT Edge** в разделе дополнительных параметров. Дополнительные сведения о создании развертывания для нескольких устройств см. в статье [Общие сведения об автоматических развертываниях IoT Edge для отдельных устройств или в требуемом масштабе](module-deployment-monitoring.md).
    * Expand Advanced Settings, select **IoT Edge deployment ID**, put the variable `$(System.TeamProject)-$(Release.EnvironmentName)`. This maps the project and release name with your IoT Edge deployment ID.

11. Выберите **Сохранить**, чтобы сохранить изменения в новом конвейере выпуска. Вернитесь в представление конвейера, выбрав **Конвейер** в меню. 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Проверка CI/CD для IoT Edge с использованием конвейеров сборки и выпуска

Чтобы запустить задание сборки, можно создать фиксацию в репозитории исходного кода или активировать задание вручную. В этом разделе вы вручную запускаете конвейер CI/CD для проверки правильности его работы. Затем вы проверите успешность развертывания.

1. Перейдите в конвейер сборки, созданный в начале этой статьи. 

2. Чтобы запустить задание сборки в конвейере сборки, нажмите кнопку **Очередь**, как показано на следующем снимке экрана.

    ![Ручной триггер](./media/how-to-ci-cd/manual-trigger.png)

3. Выберите задание сборки, чтобы просмотреть ход его выполнения. If the build pipeline is completed successfully, it triggers a release to **dev** stage. 

    ![Журналы сборки](./media/how-to-ci-cd/build-logs.png)

4. The successful **dev** release creates IoT Edge deployment to target IoT Edge devices.

    ![Release to dev](./media/how-to-ci-cd/pending-approval.png)

5. Click **dev** stage to see release logs.

    ![Журналы выпуска](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>Дальнейшие действия
* IoT Edge DevOps best practices sample in [Azure DevOps Project for IoT Edge](how-to-devops-project.md)
* Основные сведения о развертывании IoT Edge см. в статье [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Основные сведения о развертываниях IoT Edge для отдельных устройств или в требуемом масштабе).
* Ознакомьтесь со статьей [Развертывание и мониторинг модулей IoT Edge в нужном масштабе (предварительная версия)](how-to-deploy-monitor.md), чтобы узнать, как создавать, обновлять или удалять развертывание.
