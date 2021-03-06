---
title: Неизменяемое хранилище BLOB-объектов — хранилище Azure
description: Служба хранилища Azure предлагает поддержку WORM для хранения больших двоичных объектов, что позволяет хранить данные в нестираемом и неизменяемом состоянии на протяжении определенного интервала времени.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 92bfa4f13467763fd88b9ae993554aef69355d75
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555232"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Хранение критически важных для бизнеса данных большого двоичного объекта с неизменяемым хранилищем

Неизменяемое хранилище для хранилища BLOB-объектов Azure позволяет пользователям хранить критически важные для бизнеса объекты данных в ЧЕРВе (однократная запись, считывание из множества). Это состояние делает их нестираемыми и неизменяемыми в течение определенного пользователем интервала времени. В течение интервала хранения большие двоичные объекты можно создавать и читать, но нельзя изменять или удалять. Неизменяемое хранилище доступно для учетных записей общего назначения версии 2 и хранилища BLOB-объектов во всех регионах Azure.

Сведения о том, как задать и отменить юридические удержания или создать политику хранения на основе времени с помощью портал Azure, PowerShell или Azure CLI, см. в статье [Настройка политик неизменности для хранилища BLOB-объектов и управление ими](storage-blob-immutability-policies-manage.md).

## <a name="about-immutable-blob-storage"></a>Сведения о неизменяемом хранилище BLOB-объектов

Неизменяемое хранилище помогает организациям здравоохранения, финансовым учреждениям и связанным отраслям&mdash;особенно в организациях брокера дилеров&mdash;для безопасного хранения данных. Неизменяемое хранилище можно также использовать в любом сценарии для защиты критически важных данных от изменения или удаления.

Распространенные приложения включают следующее.

- **Соответствие нормативным требованиям**: хранение данных в неизменяемом виде в хранилище BLOB-объектов Azure помогает организациям соответствовать SEC 17a-4(f), CFTC 1.31(d), FINRA и другим нормам. Технический технический документ, Кохассет, содержит сведения о том, как неизменяемые адреса хранилища этих нормативных требований можно скачать с помощью [портала службы доверия Майкрософт](https://aka.ms/AzureWormStorage). [Центр управления безопасностью Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) содержит подробные сведения о наших сертификатах соответствия.

- **Безопасное хранение документов**. неизменяемое хранилище для хранилища BLOB-объектов Azure гарантирует, что данные не могут быть изменены или удалены любым пользователем, включая пользователей с правами администратора учетной записи.

- **Юридическая**информация. неизменяемое хранилище для хранилища BLOB-объектов Azure позволяет пользователям хранить конфиденциальную информацию, которая важна для судебного разбирательства или использования в бизнесе, в состоянии подтверждения незаконного пользования до тех пор, пока удержание не будет удалено. Эта функция не ограничивается только юридическими вариантами использования, но также может рассматриваться как удержание на основе событий или блокировка предприятия, где требуется защищать данные на основе триггеров событий или корпоративной политики.

Неизменяемое хранилище поддерживает следующие функции:

- **[Поддержка политики хранения на основе времени](#time-based-retention-policies)** . пользователи могут задавать политики для хранения данных в течение указанного интервала. Если задана политика хранения на основе времени, большие двоичные объекты можно создавать и читать, но нельзя изменять и удалять. По истечении срока хранения большие двоичные объекты могут быть удалены, но не перезаписаны.

- **[Поддержка политики юридических удержаний](#legal-holds)** . Если интервал хранения неизвестен, пользователи могут задать юридические удержания для хранения неизменяемых данных до тех пор, пока не будет сброшено юридическое удержание.  Если задана политика юридических удержаний, большие двоичные объекты можно создавать и читать, но нельзя изменять и удалять. Каждое юридическое удержание связано с определенным пользователем буквенно-цифровым тегом (например, ИДЕНТИФИКАТОРом варианта, именем события и т. д.), используемым в качестве строки идентификатора. 

- **Поддержка для всех уровней хранилища BLOB-объектов**: политики WORM не зависят от уровня хранилища BLOB-объектов Azure и применяются ко всем уровням: горячим, холодным и архивным. Это позволяет пользователям переносить данные на наиболее оптимизированный по стоимости уровень для своей рабочей нагрузки, сохраняя их неизменность.

- **Настройка уровня контейнера**: на уровне контейнера пользователи могут настроить временные политики периода удержания и теги юридического удержания. Используя простые параметры уровня контейнера, пользователи могут создавать и блокировать политики хранения на основе времени, расширять интервалы хранения, устанавливать и очищать юридические удержания и многое другое. Эти политики применяются для всех имеющихся и новых больших двоичных объектов в контейнере.

- **Поддержка ведения журнала аудита**. Каждый контейнер включает журнал аудита политики. Она отображает до семи команд хранения на основе времени для заблокированных политик хранения на основе времени и содержит идентификатор пользователя, тип команды, метки времени и интервал хранения. Для юридического удержания журнал содержит идентификатор пользователя, тип команды, отметки времени и теги юридического удержания. Этот журнал сохраняется в течение времени существования политики в соответствии с нормативными рекомендациями 17A-4 (f). В [журнале действий Azure](../../azure-monitor/platform/activity-logs-overview.md) представлен более полный журнал всех действий плоскости управления. При включении [журналов диагностики Azure](../../azure-monitor/platform/resource-logs-overview.md) на ней соводятся операции плоскости данных. Пользователь постоянно несет ответственность за хранение этих журналов в соответствии с нормативами или другими требованиями.

## <a name="how-it-works"></a>Принципы работы

Функция хранения данных в неизменяемом виде хранилища BLOB-объектов Azure поддерживает два типа WORM или неизменяемых политик: период удержания на основе времени и юридическое удержание. Если для контейнера применяется политика хранения на основе времени или юридическая информация, все существующие большие двоичные объекты переходят в неизменяемое состояние червя менее чем за 30 секунд. Все новые большие двоичные объекты, передаваемые в этот контейнер, также будут перемещены в неизменяемое состояние. После того как все большие двоичные объекты перешли в неизменяемое состояние, будет подтверждена неизменяемая политика, а все операции перезаписи или удаления для существующих и новых объектов в неизменяемом контейнере не допускаются.

Удаление контейнеров и учетных записей хранения запрещено, если в контейнере или учетной записи хранения есть большие двоичные объекты, защищенные неизменяемой политикой. Операция удаления контейнера завершится ошибкой, если существует хотя бы один большой двоичный объект с заблокированной политикой хранения на основе времени или юридическим удержанием. Операция удаления учетной записи хранения завершится ошибкой, если имеется по крайней мере один контейнер червя с юридическим удержанием или BLOB-объектом с активным интервалом хранения.

### <a name="time-based-retention-policies"></a>Политики хранения на основе времени

> [!IMPORTANT]
> Политика хранения на основе времени должна быть *заблокирована* , чтобы большой двоичный объект находился в совместимом неизменяемом состоянии (с записью и удалением) для 17A-4 (f) и других нормативных требований. Рекомендуется блокировать политику в разумное время, обычно менее 24 часов. Начальное состояние примененной политики хранения на основе времени *разблокируется*, что позволяет протестировать функцию и внести изменения в политику перед ее блокировкой. В то время как *незаблокированное* состояние обеспечивает защиту от неизменности, мы не рекомендуем использовать *незаблокированное* состояние для любых целей, отличных от краткосрочных версий функций. 

Когда политика хранения на основе времени применяется в контейнере, все большие двоичные объекты контейнера остаются в неизменяемом состоянии в течение *действующего* периода удержания. Период эффективного удержания для существующих BLOB-объектов равен разнице между временем их создания и интервалом удержания, определяемым пользователем.

Для новых BLOB-объектов период эффективного удержания равен интервалу удержания, определяемого пользователем. Так как пользователи могут расширять интервал удержания, функция хранения данных в неизменяемом виде будет использовать самое последнее его значение для вычисления периода эффективного удержания.

Например, предположим, что пользователь создает политику хранения на основе времени с интервалом хранения, равным пяти годам. Существующий большой двоичный объект в этом контейнере, _testblob1_, был создан один год назад. Эффективный срок хранения для _testblob1_ составляет четыре года. Когда в контейнер передается новый большой двоичный объект _testblob2_, эффективный срок хранения для нового большого двоичного объекта составляет пять лет.

Незаблокированная политика хранения, основанная на времени, рекомендуется только для тестирования компонентов, а политика должна быть заблокирована, чтобы быть совместимой с 17A-4 (f) и другими нормативными требованиями. После блокировки политики хранения, основанной на времени, невозможно удалить политику и разрешить не более пяти увеличений срока действия. Дополнительные сведения о настройке и блокировке политик хранения на основе времени см. в статье [Настройка политик неизменности для хранилища BLOB-объектов и управление ими](storage-blob-immutability-policies-manage.md).

К политикам хранения применяются следующие ограничения.

- Для учетной записи хранения максимальное число контейнеров с заблокированными неизменяемыми политиками на основе времени — 1 000.
- Минимальный интервал периода удержания составляет один день, Максимальное значение — 146 000 дней (400 лет).
- Для контейнера максимальное число изменений для расширения интервала хранения заблокированных неизменяемых политик на основе времени равно 5.
- Для контейнера не более семи журналов аудита политики хранения на основе времени сохраняются для заблокированной политики.

### <a name="legal-holds"></a>Юридические удержания

Все имеющиеся и новые большие двоичные объекты, которые находятся в юридическом удержании, остаются в неизменяемом состоянии, пока юридическое удержание не будет снято. Дополнительные сведения о настройке и снятии юридических удержаний см. в статье [Настройка политик неизменности для хранилища BLOB-объектов и управление ими](storage-blob-immutability-policies-manage.md).

Контейнер может иметь одновременно и юридическое удержание, и политику хранения на основе времени. Все большие двоичные объекты в этом контейнере остаются в неизменяемом состоянии, пока все юридические удержания не будут сняты, даже если период эффективного хранения истек. И наоборот, большой двоичный объект остается в неизменяемом состоянии до тех пор, пока не истечет период эффективного хранения, хотя все юридические удержания сняты.

В следующей таблице показаны типы операций с хранилищем BLOB-объектов, отключенных для различных неизменяемых сценариев. Дополнительные сведения см. в документации по [службе BLOB-объектов Azure REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) .

|Сценарий  |Состояние больших двоичных объектов  |Недопустимые операции с большими двоичными объектами  |
|---------|---------|---------|
|Эффективный интервал удержания большого двоичного объекта еще не истек, и (или) установлено юридическое удержание     |Неизменяемость: защита от удаления и от записи         | Помещение большого двоичного объекта<sup>1</sup>, помещение блока<sup>1</sup>, помещение блока списка<sup>1</sup>, удаление контейнера, удаление большого двоичного объекта, задание метаданных большого двоичного объекта, страницы размещения, задание свойств большого двоичного объекта, моментальных снимков большого двоичного объекта, добавочного копирования         |
|Срок действия интервала эффективного хранения большого двоичного объекта истек.     |Защита только от записи (разрешены операции удаления)         |Вставка большого двоичного объекта<sup>1</sup>, вставка блока<sup>1</sup>, вставка списка блоков<sup>1</sup>, установка метаданных большого двоичного объекта, вставка страницы, установка свойства большого двоичного объекта, создание моментального снимка большого двоичного объекта, инкрементное копирование больших двоичных объектов, добавление блока         |
|Все юридические удержания сняты, а в контейнере не задана политика хранения на основе времени     |Изменяемый         |Нет         |
|Политика WORM не создана (период удержания на основе времени или юридическое удержание)     |Изменяемый         |Нет         |

<sup>1</sup> приложение позволяет этим операциям создать новый большой двоичный объект один раз. Все последующие операции перезаписи существующего пути к большому двоичному объекту в неизменяемом контейнере не допускаются.

К юридическим удержаниям относятся следующие ограничения.

- Для учетной записи хранения максимальное количество контейнеров с параметром юридического удержания составляет 1000.
- Для контейнера максимальное количество тегов юридического удержания составляет 10.
- Минимальная длина тега юридического удержания состоит из трех буквенно-цифровых символов. Максимальная длина составляет 23 алфавитно-цифровых символов.
- Для контейнера не более 10 журналов аудита политики юридического хранения сохраняются в течение политики.

## <a name="pricing"></a>Стоимость

За использование этой функции не взимается дополнительная плата. Цены на неизменяемые данные изменяются так же, как и изменяемые данные. Сведения о ценах на хранилище BLOB-объектов Azure см. на [странице цен на службу хранилища Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Вопросы и ответы

**Можно ли предоставить документацию по соответствию червя?**

Да. Чтобы документировать соответствие требованиям, корпорация Майкрософт сохранила начальную независимую оценку, специализирующуюся на управлении записями и информационным руководством, Кохассет связывается, чтобы оценить неизменяемое хранилище BLOB-объектов и его соответствие требованиям, относящимся к отрасли финансовых услуг. Кохассет подтвердила, что неизменяемое хранилище BLOB-объектов, используемое для хранения больших двоичных объектов на основе времени в состоянии червя, соответствует требованиям к хранению КФТК правила 1.31 (c)-(d), ФИНРА правил 4511 и с Rule 17A-4. Корпорация Майкрософт нацелена на этот набор правил, так как они представляют наиболее полное нормативное руководство по хранению записей для финансовых учреждений. Отчет Кохассет доступен в [центре управления безопасностью Майкрософт](https://aka.ms/AzureWormStorage). Чтобы запросить письмо об аттестации от корпорации Майкрософт по соответствию вирусам-червям, обратитесь в службу поддержки Azure.

**Используется ли эта возможность только для блочных BLOB-объектов или ее можно использовать также для страничных и добавочных BLOB-объектов?**

Неизменяемое хранилище можно использовать с любым типом больших двоичных объектов, так как он задан на уровне контейнера, но рекомендуется использовать ЧЕРВь для контейнеров, в основном хранящих блочные BLOB-объекты. В отличие от блочных больших двоичных объектов, все новые страничные BLOB-объекты и добавочные большие двоичные объекты должны быть созданы вне контейнера червя, а затем скопированы в. После копирования этих больших двоичных *объектов в контейнер червя Добавление дополнительных объектов* к большому двоичному объекту или изменение страничного BLOB-объекта разрешено. Установка политики-червя для контейнера, в котором хранятся виртуальные жесткие диски (страничные BLOB-объекты) для любых активных виртуальных машин, не рекомендуется, так как он блокирует диск виртуальной машины.

**Нужно ли создавать новую учетную запись хранения для использования этой функции?**

Нет, вы можете использовать неизменяемое хранилище с любыми существующими или только что созданными учетными записями общего назначения версии 2 или хранилища BLOB-объектов. Эта функция предназначена для использования с блочными BLOB-объектами в учетных записях GPv2 и хранилища BLOB-объектов. Учетные записи хранения общего назначения v1 не поддерживаются, но их можно легко обновить до версии v2 общего назначения. Сведения об обновлении существующей учетной записи хранения общего назначения версии 1 см. в статье [Обновление учетной записи хранения](../common/storage-account-upgrade.md).

**Можно ли применить политику хранения с юридическим удержанием и временем?**

Да, в контейнере одновременно могут одновременно храниться и юридические удержания, и политика хранения на основе времени. Все большие двоичные объекты в этом контейнере остаются в неизменяемом состоянии, пока все юридические удержания не будут сняты, даже если период эффективного хранения истек. И наоборот, большой двоичный объект остается в неизменяемом состоянии до тех пор, пока не истечет период эффективного хранения, хотя все юридические удержания сняты.

**Являются ли политики с юридическими удержаниями только для юридических материалы или в других сценариях использования?**

Нет, Юридическая информация — это просто общий термин, используемый для политики хранения, не основанной на времени. Его не нужно использовать для материалы, связанных с судебными отношениями. Политики юридических удержаний полезны для отключения перезаписи и удаления для защиты важных данных корпоративного червя, если срок хранения неизвестен. Вы можете использовать его в качестве политики предприятия для защиты критически важных рабочих нагрузок ЧЕРВей или использовать его в качестве промежуточной политики, прежде чем для пользовательского триггера событий потребуется использовать политику хранения на основе времени. 

**Можно ли удалить _Заблокированную_ политику хранения на основе времени или юридическую блокировку?**

Из контейнера можно удалить только незаблокированные политики хранения на основе времени. Если политика хранения на основе времени заблокирована, ее нельзя удалить. допускаются только действующие расширения срока хранения. Теги юридических удержаний можно удалить. При удалении всех юридических тегов это юридическое удержание удаляется.

**Что произойдет при попытке удалить контейнер с *заблокированной* политикой хранения на основе времени или юридическим удержанием?**

Операция "Удалить контейнер" не будет выполнена, если в контейнере существует хотя бы один большой двоичный объект с заблокированной политикой хранения на основе времени или юридическим удержанием. Операция "Удалить контейнер" выполнится, если не существует большого двоичного объекта с действующим интервалом периода удержания и нет юридических удержаний. Прежде чем удалить контейнер, необходимо сначала удалить большие двоичные объекты.

**Что произойдет при попытке удалить учетную запись хранения с WORM-контейнером, который имеет *заблокированную* политику хранения на основе времени или юридическое удержание?**

Удаление учетной записи хранилища не будет выполнено, если там присутствует хотя бы один WORM-контейнер с юридическим удержанием или BLOB-объект с действующим интервалом периода удержания. Прежде чем удалить учетную запись хранения, необходимо сначала удалить все контейнеры WORM. Сведения об удалении контейнера см. в предыдущем вопросе.

**Можно ли перемещать данные по различным уровням BLOB-объектов (горячий, прохладный и холодный уровень доступа), когда он находится в неизменяемом состоянии?**

Да, можно использовать команду Set BLOB Tier для перемещения данных по уровням больших двоичных объектов, сохраняя их в соответствии с неизменяемым состоянием. Функция хранения данных в неизменяемом виде в хранилище поддерживается на горячем, холодном и архивном уровнях хранения больших двоичных объектов.

**Что произойдет, если не внести оплату, при том что срок интервала периода удержания еще не истек?**

В случае неоплаты будут применяться обычные правила хранения данных в соответствии с условиями, указанными в условиях вашего контракта с корпорацией Майкрософт.

**Предлагаете ли вы пробный или льготный период, чтобы просто попробовать эту функцию?**

Да. Когда политика хранения на основе времени создается впервые, она находится в *незаблокированном* состоянии. В этом состоянии можно вносить любые нужные изменения, например увеличение или уменьшение, и даже удаление политики. После блокировки политика остается заблокированной до истечения периода удержания. Эта политика блокировки предотвращает удаление и изменение интервала хранения. Мы настоятельно рекомендуем использовать *разблокированное* состояние только для пробных целей и заблокировать политику в течение 24 часов, чтобы не подвергать риску соответствие требованиям SEC 17a-4(f) и другим нормам.

**Можно ли использовать обратимое удаление вместе с неизменяемыми политиками больших двоичных объектов?**

Да. [Обратимое удаление для хранилища BLOB-объектов Azure](storage-blob-soft-delete.md) применяется ко всем контейнерам в учетной записи хранения независимо от политики хранения на основе юридических удержаний или времени. Рекомендуется включить обратимое удаление для дополнительной защиты перед применением и подтверждением политик неизменного червя.

**Где доступна функция?**

Неизменяемое хранилище доступно в общедоступных регионах Azure, Azure для Китая и государственных организаций. Если неизменяемое хранилище недоступно в вашем регионе, обратитесь в службу поддержки и azurestoragefeedback@microsoft.comэлектронной почты.

## <a name="next-steps"></a>Дальнейшие действия

[Настройка политик неизменности для хранилища BLOB-объектов и управление ими](storage-blob-immutability-policies-manage.md)