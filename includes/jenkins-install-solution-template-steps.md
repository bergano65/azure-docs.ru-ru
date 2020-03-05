---
author: tomarcher
ms.service: jenkins
ms.topic: include
ms.date: 03/03/2020
ms.author: tarcher
ms.openlocfilehash: 2468dc72881755a2990e8ddf8112d7fe27f64f4d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274453"
---
## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure
* Доступ к SSH в командной строке компьютера (например, в оболочке Bash или [PuTTY](https://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>Создание виртуальной машины Jenkins на основе шаблона решения
Jenkins поддерживает модель, в которой делегаты сервера Jenkins работают с одним или несколькими агентами, чтобы позволить одной установке Jenkins размещать большое количество проектов или предоставлять различные среды, необходимые для сборки или тестирования. Шаги, представленные в этом разделе, помогут вам установить и настроить сервер Jenkins в Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>Подключение к Jenkins

Перейдите к виртуальной машине (например, `http://jenkins2517454.eastus.cloudapp.azure.com/`) в веб-браузере. Консоль Jenkins недоступна по незащищенному протоколу HTTP, поэтому на странице приведены инструкции по безопасному доступу к консоли Jenkins с компьютера с использованием туннеля SSH.

![Разблокировка Jenkins](./media/jenkins-install-solution-template-steps/jenkins-ssh-instructions.png)

Настройте туннель на странице из командной строки, используя команду `ssh`. Для этого замените `username` именем администратора виртуальной машины, указанным ранее при настройке виртуальной машины на основе шаблона решения.

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

После запуска туннеля перейдите по адресу `http://localhost:8080/` с локального компьютера. 

Получите первоначальный пароль, выполнив команду ниже в командной строке при подключении к виртуальной машине Jenkins через SSH.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

При первом использовании разблокируйте панель мониторинга Jenkins с помощью этого пароля.

![Разблокировка Jenkins](./media/jenkins-install-solution-template-steps/jenkins-unlock.png)

Выберите **Install suggested plugins** (Установить предлагаемые подключаемые модули) на следующей странице и создайте учетную запись администратора Jenkins для доступа к панели мониторинга Jenkins.

![Экземпляр Jenkins готов!](./media/jenkins-install-solution-template-steps/jenkins-welcome.png)

Сервер Jenkins готов к созданию кода.

## <a name="create-your-first-job"></a>Создание первого задания

Выберите **Create new jobs** (Создать задания) в консоли Jenkins. Назовите задание **mySampleApp**, выберите **Freestyle project** (Универсальный проект) и нажмите **ОК**.

![Создание задания](./media/jenkins-install-solution-template-steps/jenkins-new-job.png) 

Выберите вкладку **Управление исходным кодом**, включите **Git** и введите следующий URL-адрес в поле **URL-адрес репозитория**: `https://github.com/spring-guides/gs-spring-boot.git`

![Определение репозитория Git](./media/jenkins-install-solution-template-steps/jenkins-job-git-configuration.png) 

Выберите вкладку **Создание**, нажмите **Добавление шага сборки** и **Invoke Gradle script** (Вызов сценария Gradle). Выберите **Use Gradle Wrapper** (Использовать программу-оболочку Gradle), затем введите значение `complete` для параметра **Wrapper location** (Расположение программы-оболочки) и `build` для параметра **Задачи**.

![Использование программы-оболочки Gradle для сборки](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-config.png) 

Выберите **Advanced** (Дополнительно) и введите `complete` в поле **Root Build script** (Корневой скрипт сборки). Щелкните **Сохранить**.

![Установка дополнительных параметров на этапе сборки программы-оболочки Gradle](./media/jenkins-install-solution-template-steps/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>Сборка кода

Выберите **Build Now** (Собрать сейчас), чтобы скомпилировать код и создать пакет для примера приложения. По завершении сборки выберите для проекта ссылку **Рабочая область**.

![Перейдите в рабочую область, чтобы получить JAR-файл из сборки](./media/jenkins-install-solution-template-steps/jenkins-access-workspace.png) 

Перейдите к `complete/build/libs` и проверьте наличие `gs-spring-boot-0.1.0.jar`, чтобы удостовериться в успешности сборки. Теперь сервер Jenkins готов к сборке ваших собственных проектов в Azure.

## <a name="troubleshooting-the-jenkins-solution-template"></a>Устранение неполадок шаблона решения Jenkins

Если вы столкнулись с ошибками, которые касаются шаблона решения Jenkins, сообщите о проблеме в [репозиторий Jenkins на GitHub](https://github.com/azure/jenkins/issues).

## <a name="next-steps"></a>Next Steps

> [!div class="nextstepaction"]
> [Использование агентов виртуальных машин для непрерывной интеграции с Jenkins.](/azure/jenkins-azure-vm-agents)