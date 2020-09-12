---
title: Непрерывная интеграция и непрерывное развертывание на Azure IoT Edge устройствах — Azure IoT Edge
description: Настройка непрерывной интеграции и непрерывного развертывания с помощью YAML-Azure IoT Edge с Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0b68e354099a0038e1b418d6973eca2c774aed94
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89303702"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Непрерывная интеграция и непрерывное развертывание на Azure IoT Edge устройствах

Вы можете легко внедрить DevOps для приложений Azure IoT Edge, используя встроенные задания Azure IoT Edge в Azure Pipelines. В этой статье показано, как можно использовать функции непрерывной интеграции и непрерывного развертывания Azure Pipelines для быстрого и эффективного создания, тестирования и развертывания приложений в Azure IoT Edge с помощью YAML. Кроме того, можно [использовать классический редактор](how-to-continuous-integration-continuous-deployment-classic.md).

![Схема ветвей CI и CD для разработки и эксплуатации](./media/how-to-continuous-integration-continuous-deployment/model.png)

Из этой статьи вы узнаете, как использовать встроенные [Azure IOT Edge задачи](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/azure-iot-edge) для Azure pipelines создания конвейеров сборки и выпуска для решения IOT Edge. Каждая задача Azure IoT Edge, добавленная в конвейер, реализует одно из следующих четырех действий.

 | Действие | Описание |
 | --- | --- |
 | Сборка образов модулей | Принимает код решения IoT Edge и создает образы контейнеров.|
 | Отправка образов модулей | Отправляет образы модулей в указанный реестр контейнеров. |
 | Создать манифест развертывания | Принимает deployment.template.jsв файле и переменных, а затем создает окончательный файл манифеста развертывания IoT Edge. |
 | Развертывание на устройствах IoT Edge | Создает IoT Edge развертывания на одном или нескольких устройствах IoT Edge. |

Если не указано иное, процедуры, описанные в этой статье, не анализируют все функциональные возможности, доступные через параметры задачи. Дополнительные сведения см. в следующих разделах:

* [Версия задачи](https://docs.microsoft.com/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=classic#task-versions)
* **Дополнительно** — если применимо, укажите модули, которые не должны создаваться.
* [Параметры управления](https://docs.microsoft.com/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=classic#task-control-options)
* [Переменные среды](https://docs.microsoft.com/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#environment-variables)
* [Выходные переменные](https://docs.microsoft.com/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Предварительные требования

* Репозиторий Azure Repos. Если у вас его нет, вы можете [создать репозиторий Git в проекте](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav). В этой статье мы создали репозиторий, который называется **IoTEdgeRepo**.
* Решение IoT Edge, зафиксированное и отправленное в репозиторий. Если вы хотите создать пример решения для тестирования инструкций в этой статье, следуйте шагам, изложенным в статье [Использование Visual Studio Code для разработки и отладки модулей для Azure IoT Edge](how-to-vs-code-develop-module.md) или [Сведения об использовании Visual Studio 2017 для разработки и отладки модулей C# для Azure IoT Edge (предварительная версия)](how-to-visual-studio-develop-csharp-module.md). В этой статье мы создали решение в нашем репозитории с именем **иотеджесолутион**, которое содержит код для модуля с именем **filtermodule**.

   Все, что требуется в этой статье, — это папка решения, созданная по шаблону IoT Edge в Visual Studio Code или Visual Studio. Вам не требуется создавать, отправлять, развертывать или отлаживать этот код, чтобы продолжить. Эти процессы настраиваются в Azure Pipelines.

   Если вы создаете решение, сначала клонируйте репозиторий локально. Затем вы сможете создать решение непосредственно в папке репозитория. Кроме того, вы можете легко фиксировать и отправлять файлы из нее.

* Реестр контейнеров, в который можно отправлять образы модулей. Вы можете использовать [Реестр контейнеров Azure](https://docs.microsoft.com/azure/container-registry/) или сторонний реестр.
* Активный [центр Интернета вещей](../iot-hub/iot-hub-create-through-portal.md) Azure по крайней мере с двумя IOT Edge устройствами для тестирования отдельных этапов тестового и рабочего развертывания. Следуйте кратким руководствам, чтобы создать устройство IoT Edge в [Linux](quickstart-linux.md) или [Windows](quickstart.md).

Дополнительные сведения об использовании Azure Repos см. в статье [Share your code with Visual Studio 2015 and Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts) (Предоставление общего доступа к коду с помощью Visual Studio и Azure Repos).

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Создание конвейера сборки для непрерывной интеграции

В этом разделе показано, как создать конвейер сборки. Вы настраиваете автоматический запуск конвейера при внесении изменений в образец IoT Edge решение и публикацию журналов сборки.

1. Войдите в свою организацию Azure DevOps ( `https://dev.azure.com/{your organization}` ) и откройте проект, содержащий репозиторий решения для IOT Edge.

   ![Открытие своего проекта DevOps](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. В левой части меню проекта выберите **конвейеры**. Выберите **создать конвейер** в центре страницы. Если у вас уже есть конвейеры сборки, нажмите кнопку **создать конвейер** в правом верхнем углу.

    ![Создание нового конвейера сборки с помощью кнопки "создать конвейер"](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. На странице **где находится код?** выберите **Azure Repos Git `YAML` **. Если вы хотите использовать классический редактор для создания конвейеров сборки проекта, ознакомьтесь с [классическим руководством по редактору](how-to-continuous-integration-continuous-deployment-classic.md).

4. Выберите репозиторий, для которого создается конвейер.

    ![Выберите репозиторий для конвейера сборки](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. На странице **Настройка конвейера** выберите **начальный конвейер**. Если у вас есть существующий файл Azure Pipelines YAML, который вы хотите использовать для создания этого конвейера, можно выбрать **существующий файл Azure PIPELINES YAML** и указать ветвь и путь в репозитории в файле.

    ![Выберите начальный конвейер или существующий файл Azure Pipelines YAML, чтобы начать конвейер сборки.](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. На странице **Проверка конвейера YAML** можно щелкнуть имя по умолчанию, `azure-pipelines.yml` чтобы переименовать файл конфигурации конвейера.

   Выберите параметр **Показывать помощник** , чтобы открыть палитру **задачи** .

    ![Выберите команду "отобразить помощник", чтобы открыть палитру задач](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Чтобы добавить задачу, поместите курсор в конец YAML или в любое место, где нужно добавить инструкции для задачи. Найдите и выберите **Azure IOT Edge**. Заполните параметры задачи следующим образом. Затем выберите **Добавить**.

   | Параметр | Описание |
   | --- | --- |
   | Действие | Выберите **Сборка образы модулей**. |
   | .template.jsв файле | Укажите путь к **deployment.template.js** файлу в репозитории, содержащем ваше решение IOT Edge. |
   | Платформа по умолчанию | Выберите соответствующую операционную систему для модулей на основе целевого устройства IoT Edge. |

    ![Использование палитры задач для добавления задач в конвейер](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > После добавления каждой задачи редактор автоматически выделит добавленные строки. Чтобы предотвратить случайную перезапись, отмените выбор строк и введите новое место для следующей задачи, прежде чем добавлять дополнительные задачи.

8. Повторите эту процедуру, чтобы добавить еще три задачи со следующими параметрами:

   * Задача: **Azure IOT Edge**

       | Параметр | Описание |
       | --- | --- |
       | Действие | Выбор **образов модуля push-уведомлений**. |
       | Тип реестра контейнеров | Использовать тип по умолчанию: **Реестр контейнеров Azure**. |
       | Подписка Azure. | Выберите свою подписку. |
       | Реестр контейнеров Azure | Выберите реестр, который будет использоваться для конвейера. |
       | .template.jsв файле | Укажите путь к **deployment.template.js** файлу в репозитории, содержащем ваше решение IOT Edge. |
       | Платформа по умолчанию | Выберите соответствующую операционную систему для модулей на основе целевого устройства IoT Edge. |

   * Задача: **копирование файлов**

       | Параметр | Описание |
       | --- | --- |
       | Исходная папка | Исходная папка, из которой производится копирование. Empty является корнем репозитория. Используйте переменные, если файлы отсутствуют в репозитории. Например, `$(agent.builddirectory)`.
       | Содержимое | Добавьте две строки: `deployment.template.json` и `**/module.json` . |
       | Целевая папка | Укажите переменную `$(Build.ArtifactStagingDirectory)` . Дополнительные сведения о описании см. в разделе [переменные сборки](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) . |

   * Задача: **Публикация артефактов сборки**

       | Параметр | Описание |
       | --- | --- |
       | Путь для публикации | Укажите переменную `$(Build.ArtifactStagingDirectory)` . Дополнительные сведения о описании см. в разделе [переменные сборки](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) . |
       | Имя артефакта | Укажите имя по умолчанию: `drop` |
       | Расположение публикации артефакта | Использовать расположение по умолчанию: `Azure Pipelines` |

9. Выберите **сохранить** в раскрывающемся списке **сохранить и запустить** в правом верхнем углу.

10. Триггер непрерывной интеграции включен по умолчанию для конвейера YAML. Если вы хотите изменить эти параметры, выберите свой конвейер и нажмите кнопку **изменить** в правом верхнем углу. Выберите **Дополнительные действия** рядом с кнопкой **выполнить** в правом верхнем углу и перейдите к **триггерам**. **Непрерывная интеграция** отображается как включенная под названием вашего конвейера. Если вы хотите просмотреть подробные сведения об этом триггере, установите флажок **переопределить триггер непрерывной интеграции YAML** .

    ![Чтобы просмотреть параметры триггера конвейера, см. раздел Триггеры в разделе "другие действия".](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Перейдите к следующему разделу для создания конвейера выпуска.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Дальнейшие действия

* IoT Edge DevOps рекомендации в [Azure DevOps Starter для IOT Edge](how-to-devops-starter.md)
* Основные сведения о развертывании IoT Edge см. в статье [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md) (Основные сведения о развертываниях IoT Edge для отдельных устройств или в требуемом масштабе).
* Ознакомьтесь со статьей [Развертывание и мониторинг модулей IoT Edge в нужном масштабе (предварительная версия)](how-to-deploy-at-scale.md), чтобы узнать, как создавать, обновлять или удалять развертывание.
