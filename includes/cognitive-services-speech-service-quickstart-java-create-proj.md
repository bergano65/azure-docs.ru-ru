---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 8a8611218f47e5600fc02efef24aa7912b630522
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010966"
---
1. Запустите Eclipse.

1. В средстве запуска Eclipse в поле **Рабочая область** введите имя нового каталога. Затем выберите **Запустить**.

   ![Снимок экрана средства запуска Eclipse](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. После этого отобразится главное окно интегрированной среды разработки Eclipse. Если отобразится экран приветствия, закройте его.

1. В строке меню Eclipse создайте новый проект, выбрав **Файл** > **Создать** > **Проект**.

1. Откроется диалоговое окно **Новый проект** . Выберите **Проект Java**, а затем щелкните **Далее**.

   ![Снимок экрана диалогового окна нового проекта с выделенной строкой "Проект Java"](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. Запускается мастер создания проекта Java. В поле **Имя проекта** введите **quickstart** и в качестве среды выполнения выберите **JavaSE-1.8**. Выберите **Готово**.

   ![Снимок экрана с изображением мастера создания проекта Java](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Если появится окно **Open Associated Perspective?** (Открыть связанную перспективу?), выберите **Open Perspective** (Открыть перспективу).

1. В **обозревателе пакетов** щелкните правой кнопкой мыши проект **quickstart**. В контекстном меню выберите **Настроить** > **Convert to Maven Project** (Преобразовать в проект Maven).

   ![Снимок экрана обозревателя пакетов](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Откроется окно **Create new POM** (Создать новый POM). В поле **Идентификатор группы** введите **com.microsoft.cognitiveservices.speech.samples**, а в поле **Идентификатор артефакта** — **quickstart**. Затем нажмите кнопку **Готово**.

   ![Снимок экрана окна создания нового POM](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Откройте файл **pom.xml** и измените его.

   * В конце файла перед закрывающим тегом `</project>` создайте элемент `repositories` со ссылкой на репозиторий Maven для пакета SDK службы "Речь", как показано ниже.

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

   * Также добавьте элемент `dependencies`, указав пакет SDK службы "Речь" версии 1.4.0 в виде зависимости:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * Сохраните изменения.
