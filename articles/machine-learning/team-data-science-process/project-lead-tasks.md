---
title: Задачи руководителя проекта — процесс обработки и анализа данных группы
description: Структура задач руководителя, которые требуется выполнить в рамках командного проекта обработки и анализа данных.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4415759530985710c8376709db9955ba98066160
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61046163"
---
# <a name="tasks-for-the-project-lead-in-the-team-data-science-process"></a>Задачи руководителя проекта — процесс обработки и анализа данных группы

В этом руководстве описаны задачи, которые необходимо выполнить руководителю проекта для своей проектной группы. Целью является создание среды для совместной работы команды, которая стандартизирует [процесс обработки и анализа данных группы](overview.md) (TDSP). Платформа TDSP разработана корпорацией Майкрософт. Она поддерживает структурированную последовательность действий, позволяющих эффективно выполнять решения прогнозной аналитики в облаке. Описание ролей и задач сотрудников по стандартизации процесса обработки и анализа данных см. в статье о [ролях и задачах процесса обработки и анализа данных группы](roles-tasks.md).

**Руководитель проекта** управляет рутинной работой специалистов по обработке и анализу данных, посвященной одному конкретному проекту. Рабочий процесс задач, которые должен выполнить руководитель проекта, чтобы настроить среду, показан на следующем рисунке:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

В настоящее время в этом разделе рассматриваются задачи 1, 2 и 6 этого рабочего процесса для руководителей проекта.

> [!NOTE]
> Шаги по настройке среды команды TDSP для проекта с помощью Azure DevOps описаны в следующих инструкциях. Мы объясняем, как выполнить эти задачи с помощью Azure DevOps, так как на базе именно этой платформы мы реализуем TDSP в Майкрософт. Если в вашей группе для размещения кода используется другая платформа, задачи руководителя команды остаются прежними. Но способ выполнения этих задач будет отличаться.


## <a name="repositories-and-directories"></a>Репозитории и каталоги

В этом руководстве используются сокращенные имена для репозиториев и каталогов. С помощью этих имен можно легко отслеживать операции между репозиториями и каталогами. Эти обозначения (R для репозиториев Git и D для локальных каталогов на DSVM) используются в следующих разделах:

- **R3**: репозиторий команды **ProjectTemplate** в Git, который настраивает руководитель группы.
- **R5**: репозиторий в Git, настроенный для вашего проекта.
- **D3**: локальный каталог, клонированный из R3.
- **D5**: локальный каталог, клонированный из R5.


## <a name="0-prerequisites"></a>0. Технические условия

Предварительные требования заключаются в выполнении задач, назначенных менеджеру группы и руководителю команды, которые описаны в статье [Group Manager tasks](group-manager-tasks.md) (Задачи менеджера группы) и [Team lead tasks for a data science team](team-lead-tasks.md) (Задачи руководителя команды) соответственно. 

Итак, прежде чем приступить к задачам руководителя команды, следует выполнить следующие требования: 

- Менеджер группы должен настроить **группу Azure DevOps Services** (или учетную запись группы на другой платформе для размещения кода).
- Руководитель команды должен настроить **репозиторий TeamProjectTemplate** (R3) в учетной записи группы на платформе для размещения кода, которую планируется использовать.
- Руководитель команды предоставил вам **право** создавать репозитории в учетной записи группы для команды.
- На вашем компьютере должна быть установлена система Git. Если вы используете виртуальную машину обработки и анализа данных, система Git уже установлена и вы можете приступать к работе. Если система не установлена, см. [описание платформ и средств](platforms-and-tools.md#appendix).  
- Если вы используете **виртуальную машину для обработки и анализа данных Windows**, установите на компьютере [диспетчер учетных данных Git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows). Откройте файл README.md, прокрутите его до раздела **Download and Install** (Скачивание и установка) и выберите *latest installer* (последний установщик). Откроется страница последнего установщика. Загрузите EXE-файл установщика и запустите его. 
- Если вы используете **Виртуальную машину для обработки и анализа данных Linux**, создайте открытый ключ SSH для этой виртуальной машины и добавьте его в группу Azure DevOps Services. Дополнительные сведения об SSH см. в разделе о **создании открытого ключа SSH** в [описании платформ и средств](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Создание репозитория проекта (R5)

- Войдите в группу Azure DevOps Services по ссылке *https://\<имя Azure DevOps Services\>.visualstudio.com*. 
- В разделе **Недавние проекты и команды** щелкните **Обзор**. В появившемся окне содержится список всех проектов на базе Azure DevOps Services. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Выберите имя проекта, в котором вы собираетесь создать репозиторий проекта. В этом примере щелкните **MyTeam**. 
- Затем щелкните **Перейти**, чтобы перейти на домашнюю страницу проекта **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Щелкните **Совместная работа над кодом**, чтобы перейти на домашнюю страницу Git проекта.  

    ![4.](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Щелкните стрелку вниз в верхнем левом углу и выберите **+ New repository** (+ Новый репозиторий). 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- В окне **Создание нового репозитория** введите имя репозитория Git проекта. Выберите в качестве типа репозитория **Git**. В этом примере используется имя *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Для создания репозитория Git проекта ***DSProject1*** щелкните **Создать**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Заполнение репозитория проекта DSProject1

На этом этапе нужно заполнить репозиторий (R5) проекта **DSProject1** из репозитория (R3) с шаблоном проекта. Процедура заполнения использует каталоги D3 и D5 на локальной DSVM в качестве промежуточных расположений для хранения и обработки. Таким образом, путь заполнения является таким: R3 -> D3 -> D5 -> R5.

Если требуется настроить репозиторий проекта **DSProject1** в соответствии с потребностями проекта, это можно сделать на предпоследнем этапе следующей процедуры. Ниже приведена сводка шагов, используемых для заполнения содержимого репозитория проекта **DSProject1**. Отдельные шаги соответствуют подразделам в процедуре заполнения:

- Клонируйте репозиторий с шаблоном проекта в локальный каталог: R3 команды клонируется в локальный каталог D3.
- Клонируйте репозиторий DSProject1 в локальный каталог: R5 команды клонируется в локальный каталог D5.
- Скопируйте клонированное содержимое шаблона проекта в локальный клон репозитория DSProject1:  содержимое D3 копируется в D5.
- Настройте локальный каталог D5 (необязательно).
- Передача локального содержимого DSProject1 в репозитории команды: содержимое D5 добавляется в R5 группы.


### <a name="clone-your-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Клонирование репозитория с шаблоном проекта (R3) в каталог (D3) на локальном компьютере

На локальном компьютере создайте каталог:

- *C:\GitRepos\MyTeamCommon* для Windows; 
- *$home/GitRepos/MyTeamCommon* для Linux.

Перейдите в этот каталог. Затем выполните следующую команду, чтобы клонировать репозиторий с шаблоном проекта на локальный компьютер. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
При использовании Azure DevOps в качестве платформы для размещения кода формат *URL-адреса HTTPS репозитория с шаблоном проекта* обычно выглядит следующим образом:

 ***https://\<имя Azure DevOps Services\>.visualstudio.com/\<имя проекта\>/_git/\<имя репозитория с шаблоном проекта\>***. 

В нашем примере это:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

При использовании Azure DevOps в качестве платформы для размещения кода формат *URL-адреса SSH репозитория с шаблоном проекта* обычно выглядит следующим образом:

***ssh://\<имя Azure DevOps Services\>\@\<имя Azure DevOps Services\>.visualstudio.com:22/\<имя проекта>/_git/\<имя репозитория с шаблоном проекта\>.*** 

В нашем примере это:

***SSH://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Клонирование репозитория (R5) DSProject1 в каталог (D5) на локальном компьютере

Перейдите в каталог **GitRepos** и выполните следующую команду, чтобы клонировать репозиторий проекта на локальный компьютер. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

При использовании Azure DevOps в качестве платформы для размещения кода формат _URL-адреса HTTPS репозитория проекта_ обычно выглядит следующим образом: ***https://\<имя Azure DevOps Services\>.visualstudio.com/\<имя командного проекта>/_git/<имя репозитория проекта\>***. В нашем примере это: ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Если вы используете Azure DevOps как платформы размещения кода, как правило, _URL-адрес SSH репозитория проекта_ является _ssh: / / < имя службы Azure DevOps\>@< имя службы Azure DevOps\>.visualstudio.com:22/ < Your Project Name\>/\_git / < имя репозитория проекта\>. В этом примере у нас есть ***ssh://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Копирование содержимого D3 в D5 

Теперь на локальном компьютере необходимо скопировать содержимое _D3_ в _D5_, за исключением метаданных Git в каталоге Git. Задание будет выполнено с помощью следующих скриптов. Убедитесь, что введен полный и правильный путь к каталогам. Исходная папка — для команды (_D3_); целевая папка — для проекта (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Теперь в папке _DSProject1_ содержатся все файлы (исключая git), скопированные из _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Теперь в папке _DSProject1_ содержатся все файлы (исключая метаданные git), скопированные из _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Настройка D5 (необязательно)

Если проекту требуются какие-либо определенные каталоги или документы, отличающиеся от тех, которые можно получить из шаблона проекта (скопированного в каталог D5 на предыдущем шаге), теперь можно настроить содержимое D5. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-azure-devops-services"></a>Добавление содержимого DSProject1 D5 в R5 в группе Azure DevOps Services

Теперь необходимо отправить содержимое **_DSProject1_** в репозиторий _R5_ в проекте Azure DevOps Services в группе. 


- Перейдите в каталог **D5**. 
- Используйте следующие команды git, чтобы добавить содержимое из **D5** в **R5**. Команды одинаковы для систем Windows и Linux. 
    
    git status git add .
    git commit -m"push from win DSVM" git push
    
- Зафиксируйте изменения и отправьте их. 

> [!NOTE]
> Если вы впервые фиксируете изменения в репозиторий Git, вам нужно настроить глобальные параметры *user.name* и *user.email* перед выполнением команды `git commit`. Выполните эти две команды:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Если вы фиксируете изменения в нескольких репозиториях Git, используйте одинаковые имя и адрес электронной почты во время фиксации в каждом из них. Использование одних и тех же имени и адреса электронной почты пригодится вам позднее, когда вы создадите панель мониторинга PowerBI для отслеживания своих действий в нескольких репозиториях Git.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Создание и подключение хранилища файлов Azure в качестве ресурсов проекта (необязательно)

Если вам нужно создать хранилище файлов Azure для предоставления общего доступа к данным, таким как необработанные данные проекта, или компонентам, созданным для проекта, чтобы все участники проекта имели доступ к одним и тем же базам данных из различных DSVM, выполните инструкции из раздела 3 и 4 статьи [Team Lead tasks](team-lead-tasks.md) (Задачи руководителя команды). 


## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены ссылки на подробные описания ролей и задач, определенных командным процессом обработки и анализа данных.

- [Задачи менеджера группы для команды обработки и анализа данных](group-manager-tasks.md)
- [Задачи руководителя для команды обработки и анализа данных](team-lead-tasks.md)
- [Задачи руководителя проекта для команды обработки и анализа данных](project-lead-tasks.md)
- [Задачи отдельных участников для команды обработки и анализа данных](project-ic-tasks.md)
