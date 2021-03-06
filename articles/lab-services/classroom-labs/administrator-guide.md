---
title: Службы лаборатории Azure — руководства для администраторов | Документация Майкрософт
description: Это руководства поможет администраторам, которые создают учетные записи лаборатории и управляют ими с помощью служб лаборатории Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 638a90615d248b3c2829770432dd6a08eb4bb2fb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771740"
---
# <a name="azure-lab-services---administrator-guide"></a>Службы лаборатории Azure — руководства администратора
ИТ-администраторы, управляющие облачными ресурсами Организации, также обычно отвечают за настройку учетной записи лаборатории для своей организации. Администраторы или преподаватели создают учебные лаборатории в учетной записи лаборатории. В этой статье представлен общий обзор задействованных ресурсов Azure и рекомендации по их созданию.

![Высокоуровневое представление ресурсов Azure в учетной записи лаборатории](../media/administrator-guide/high-level-view.png)

- Учебные лаборатории размещаются в подписке Azure, принадлежащей службам лаборатории Azure.
- Учетные записи лаборатории, Галерея общих образов и версии изображений размещаются в вашей подписке.
- Вы можете использовать учетную запись лаборатории и коллекцию образов сегментов в одной группе ресурсов. На этой схеме они находятся в разных группах ресурсов. 

## <a name="subscription"></a>Subscription
Ваша организация имеет одну или несколько подписок Azure. Подписка используется для управления выставлением счетов и безопасностью для всех используемых в ней ресаурцес\сервицес Azure, включая учетные записи лаборатории.

Связь между лабораторной учетной записью и ее подпиской важна по следующим причинам:

- Счет выставляется через подписку, содержащую учетную запись лаборатории.
- Вы можете предоставить пользователям в клиенте Azure Active Directory (AD), связанном с подпиской, доступ к службам лаборатории Azure. Вы можете добавить пользователя в качестве учетной записи лаборатории овнер\контрибутор или в качестве создателя лаборатории.

Учебные лаборатории и их виртуальные машины полностью управляются. Для этого они размещаются в выделенной подписке, принадлежащей службам лаборатории Azure.

## <a name="resource-group"></a>Группа ресурсов
Подписка содержит одну или несколько групп ресурсов. Группы ресурсов используются для создания логических групп ресурсов Azure, используемых вместе в одном решении.  

При создании учетной записи лаборатории необходимо настроить группу ресурсов, содержащую учетную запись лаборатории. 

Группа ресурсов также необходима при создании [коллекции общих образов](#shared-image-gallery). Вы можете разместить учетную запись лаборатории и коллекцию общих образов в двух отдельных группах ресурсов, что типично, если вы планируете совместно использовать коллекцию изображений в различных решениях. Вы также можете разместить их в одной группе ресурсов.

При создании учетной записи лаборатории и автоматическом создании и присоединении общей коллекции образов в то же время учетная запись лаборатории и коллекция общих образов создаются в отдельных группах ресурсов по умолчанию. Это поведение можно увидеть при выполнении действий, описанных в этом руководстве: [Настройка общей коллекции образов во время создания учетной записи лаборатории](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). В изображении в верхней части этой статьи также используется эта конфигурация. 

Мы рекомендуем потратить некоторое время на планирование структуры групп ресурсов, так как после ее создания невозможно изменить группу ресурсов учетной записи лаборатории или общей коллекции образов. Если необходимо изменить группу ресурсов для этих ресурсов, необходимо удалить и повторно создать свою учетную запись лаборатории анд\ор Shared Image Gallery.

## <a name="lab-account"></a>Учетная запись лаборатории
Учетная запись лаборатории выступает в качестве контейнера для одной или нескольких лабораторных занятий. Если вы приступите к работе со службами лаборатории Azure, то обычно у вас есть только одна учетная запись лаборатории. При масштабировании использования в лаборатории вы можете позже выбрать создание дополнительных учетных записей лаборатории.

В следующем списке перечислены сценарии, в которых может оказаться полезным более одной учетной записи лаборатории:

- **Управление различными требованиями к политикам в учебных лабораториях** 

    При настройке учетной записи лаборатории задаются политики, которые применяются ко всем лабораторным занятиям в учетной записи лаборатории, например:
    - Виртуальная сеть Azure с общими ресурсами, к которым может получить доступ Лаборатория аудитории. Например, у вас может быть набор учебных занятий, которым необходим доступ к общему набору данных в виртуальной сети.
    - Образы виртуальных машин, которые учебные лаборатории могут использовать для создания виртуальных машин. Например, у вас может быть набор учебных занятий, которым требуется доступ к образу [виртуальной машины для обработки и анализа данных для Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) Marketplace.  

    Если у вас есть учебные лаборатории, имеющие уникальные требования к политикам, то может оказаться полезным создать отдельные учетные записи лаборатории для управления лабораторными занятиями отдельно.
- **Ограничение доступа к создателям лаборатории для конкретных занятий**  

    Когда пользователь добавляется в качестве создателя лаборатории, ему предоставляется доступ ко всем лабораторным занятиям в учетной записи лаборатории, включая лабораторные занятия, созданные другими авторами лабораторий. Чтобы разрешить авторам лабораторий управлять определенными лабораторными занятиями, можно создать отдельные учетные записи лаборатории, чтобы ограничить область их доступа. Например, вы можете создать отдельную учетную запись лаборатории для каждого отдела в университете. Например: одна учетная запись лаборатории для науки, а другая — для отдела математических операций и т. д.   
- **Разделить бюджет по учетной записи лаборатории**

    Вместо того чтобы выдавались все затраты на лабораторию работы с одной лабораторной учетной записью, может потребоваться более четко разделенный бюджет. Продолжая работу с примером в приведенном выше маркированном списке, можно создать учетную запись лаборатории для каждого отдела университетов, чтобы соответствующим образом отделить бюджет. С помощью службы "Управление затратами Azure" можно просмотреть затраты для каждой отдельной учетной записи лаборатории.
- **Изоляция пилотных лабораторных занятий от активных лабораторных занятий**

    В некоторых случаях требуется пилотное тестирование изменений политики в учетной записи лаборатории без потенциального влияния на активные лаборатории. В сценарии такого типа создание отдельной учетной записи лаборатории для пилотных целей позволяет изолировать изменения. 

## <a name="classroom-lab"></a>Учебная лаборатория
Лаборатория лаборатории содержит одну или несколько виртуальных машин, каждый из которых назначен определенному студенту. Как правило, вы можете заждаться:

- Наличие одной лабораторной лаборатории для каждого класса.
- Создайте новый набор учебных занятий в каждом полугодии (или для каждого временного интервала, предлагаемого вашим классом). Как правило, для классов, имеющих одни и те же потребности по образам, для совместного использования изображений в лабораториях и полугодиях следует использовать [коллекцию общих образов](#shared-image-gallery) .

При определении способа структурирования лабораторных занятий учитывайте следующие моменты.

- **Все виртуальные машины в лаборатории класса развертываются с тем же образом, что и опубликованный**. В результате, если у вас есть класс, который требует одновременной публикации различных образов лаборатории, для каждого из них необходимо создать отдельный учебный семинар.
- **Квота использования задается на уровне лаборатории и применяется ко всем пользователям в лаборатории**. Например, у вас может быть набор преподавателей, которым требуется доступ к виртуальным машинам класса для подготовки к обучению, но для преподавателей требуется только 10-часовая квота, а студенты, зарегистрированные в классе, требуют квоты в 40 часа. Чтобы задать различные квоты для пользователей, необходимо создать отдельные учебные лаборатории. Однако после задания квоты для конкретного пользователя можно добавить дополнительные часы.
- **Расписание запуска или завершения работы задается на уровне лаборатории и применяется ко всем виртуальным машинам в лаборатории**. Аналогично предыдущему моменту, если необходимо задать различные расписания для пользователей, необходимо создать отдельные учебные лаборатории. 

## <a name="shared-image-gallery"></a>Коллекция общих образов
Общая Коллекция образов прикрепляется к учетной записи лаборатории и служит центральным репозиторием для хранения образов. Изображение сохраняется в коллекции, когда преподаватель выбирает сохранение из виртуальной машины шаблона лабораторной лаборатории (ВМ). Каждый раз, когда преподаватель вносит изменения в виртуальную машину шаблона и сохраняет, новые версии образа сохраняются при сохранении предыдущих версий.

Преподаватели могут публиковать версию образа из коллекции общих образов при создании новой лабораторной среды. Хотя в галерее может храниться несколько версий образа, преподаватели могут выбирать только последнюю версию во время создания лаборатории.

Коллекция Shared Image Gallery — это необязательный ресурс, который вам может не понадобиться сразу же после нескольких учебных занятий. Однако использование общей коллекции образов имеет множество преимуществ, которые полезны при масштабировании для создания лабораторных занятий.

- **Позволяет сохранять версии шаблона образа виртуальной машины и управлять ими**.

    Это полезно при создании пользовательского образа или внесении изменений (программное обеспечение, конфигурация и т. д.) в образ из общедоступной коллекции Marketplace.  Например, для преподавателей требуется установить разные софтваре\тулинг. Вместо того, чтобы студенты могли вручную устанавливать эти необходимые компоненты самостоятельно, разные версии шаблона образа виртуальной машины можно сохранить в общей коллекции образов. Эти версии образа можно использовать при создании новых учебных занятий.
- **Включает шаринг\реусе образов виртуальных машин шаблонов во всех учебных лабораториях**.

    Это предотвращает необходимость настройки образа с нуля при каждом создании лабораторной лаборатории. Например, если предлагается несколько классов, которым требуется одно и то же изображение, этот образ нужно создать только один раз и сохранить в галерее Shared Images, чтобы его можно было совместно использовать в лабораторных занятиях.
- **Обеспечивает доступность образа с помощью репликации**.

    При сохранении в галерее общих образов из лабораторной среды образ автоматически реплицируется в другие регионы в пределах одного географического объекта. В случае сбоя в регионе Публикация шаблона виртуальной машины в учебной лаборатории не влияет на использование реплики образа в других регионах. Более того, он помогает в работе в сценариях публикации с несколькими виртуальными машинами, распространяющихся на использование разных реплик.

Для логической группировки общих образов существует несколько вариантов:

- Создание нескольких коллекций общих образов. Каждая учетная запись лаборатории может подключаться только к одной коллекции общих образов, поэтому для этого параметра также потребуется создать несколько учетных записей лаборатории.
- Также можно использовать одну коллекцию общих образов, совместно используемую несколькими учетными записями лаборатории. В этом случае каждая учетная запись лаборатории может включать только те образы, которые применимы к лабораторным занятиям, которые он содержит.

## <a name="naming"></a>Именование
По мере начала работы со службами лаборатории Azure рекомендуется устанавливать соглашения об именовании для групп ресурсов, учетных записей лаборатории, занятий Labs и общей коллекции образов. Хотя создаваемые соглашения об именовании будут уникальными для нужд Организации, в следующей таблице приведены общие рекомендации.

| Тип ресурса | Роль | Рекомендуемый шаблон | Примеры |
| ------------- | ---- | ----------------- | -------- | 
| Группа ресурсов | Содержит одну или несколько учетных записей лаборатории и одну или несколько коллекций общих образов | \<краткое имя Организации\>-\<Environment\>-RG<ul><li>**Сокращенное название организации** определяет имя Организации, которую поддерживает группа ресурсов.</li><li>**Среда** определяет среду для ресурса, например "тест" или "Рабочая".</li><li>**RG** означает тип ресурса "Группа ресурсов".</li></ul> | контосауниверситилабс — RG<br/>контосауниверситилабс-Test-RG<br/>контосауниверситилабс-произв. RG |
| Учетная запись лаборатории | Содержит одну или несколько лабораторий | \<краткое имя Организации\>-\<среды\>-La<ul><li>**Сокращенное название организации** определяет имя Организации, которую поддерживает группа ресурсов.</li><li>**Среда** определяет среду для ресурса, например "тест" или "Рабочая".</li><li>**La** означает тип ресурса "учетная запись лаборатории".</li></ul> | контосауниверситилабс-Ла<br/>масдептлабс-Ла<br/>сЦиенцедептлабс-Test-La<br/>сЦиенцедептлабс-произв-Ла |
| Учебная лаборатория | Содержит одну или несколько виртуальных машин |\<имя класса\>-\<времени\>-\<идентификатор преподавателя\><ul><li>**Имя класса** определяет имя класса, поддерживаемого лабораторией.</li><li>**Временные рамки** определяют период, в котором предлагается класс.</li>**Идентификатор образования** определяет преподавателя, которому принадлежит лаборатория.</li></ul> | CS1234-fall2019-JohnDoe<br/>CS1234-spring2019-JohnDoe | 
| Коллекция общих образов | Содержит одну или несколько версий образа виртуальной машины | \<коллекция\>"краткое имя организации" | контосауниверситилабсгаллери |

Дополнительные сведения об именовании других ресурсов Azure см. в разделе [соглашения об именовании для ресурсов Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="regions-or-locations"></a>Регионы или расположения
При настройке ресурсов служб лаборатории Azure необходимо указать регион или расположение центра обработки данных, в котором будет размещен ресурс. Ниже приведены дополнительные сведения о том, как регион\локатион влияет на каждый из следующих ресурсов, используемых в развертывании служб лаборатории.

- **группа ресурсов**

    Регион определяет центр обработки данных, где хранятся сведения о группе ресурсов. Ресурсы Azure, содержащиеся в группе ресурсов, могут находиться в разных регионах.
- **Лабораторная учетная запись или аудитория**

    Расположение учетной записи лаборатории указывает регион для этого ресурса. Учебные лаборатории, созданные в учетной записи лаборатории, могут быть развернуты в любом регионе в пределах одного географического региона. Конкретный регион, в который развертываются виртуальные машины лаборатории, автоматически выбирается в зависимости от емкости, доступной в регионе в это время.  
    Если администратор разрешает авторам лабораторий выбрать расположение лаборатории, то доступные для выбора расположения основаны на доступной региональной емкости при создании лаборатории.

    Расположение лаборатории для занятий также определяет, какие размеры вычислений виртуальной машины доступны для выбора. Некоторые размеры вычислений доступны только в определенных местах.
- **Коллекция общих образов**

    Регион указывает исходную область, в которой хранится первая версия образа, прежде чем она будет автоматически реплицирована в целевые регионы.
    
Общее правило заключается в том, чтобы задать регион\локатион ресурса, который ближе всего к пользователям. В учебных лабораториях это означает создание лабораторной лаборатории, ближайшей к вашим учащимся. Для онлайн-курсов, где студенты находятся по всему миру, необходимо использовать наиболее совладельца, чтобы создать централизованную лабораторию. Или Разделите класс на несколько учебных заработок в соответствии с регионом учащихся.

## <a name="vm-sizing"></a>Изменение размера виртуальной машины
Когда администраторы или создатели лабораторий создают учебную среду, они могут выбрать один из следующих размеров виртуальных машин в зависимости от потребностей своей аудитории. Помните, что доступные размеры вычислений зависят от региона, в котором находится учетная запись лаборатории.

| Размер | Спецификации | Предлагаемое использование |
| ---- | ----- | ------------- |
| Мелкая| <ul><li>2 ядра</li><li>3,5 ГБ ОЗУ</li></ul> | Этот размер лучше всего подходит для командной строки, открытия веб-браузера, веб-серверов с низким трафиком, небольших и средних баз данных. |
| Средние | <ul><li>4 ядра</li><li>7 ГБ ОЗУ</li></ul> | Этот размер лучше всего подходит для реляционных баз данных, кэширования в памяти и аналитики. |
| Средний (вложенная виртуализация) | <ul><li>4 ядра</li><li>16 ГБ ОЗУ</li></ul> | Этот размер лучше всего подходит для реляционных баз данных, кэширования в памяти и аналитики.  Этот размер также поддерживает вложенную виртуализацию. |
| Большие | <ul><li>8 ядер</li><li>32 ГБ ОЗУ</li></ul> | Этот размер лучше всего подходит для приложений, которым требуются более быстрые процессоры, повышенная производительность локальных дисков, большие базы данных и большие кэши памяти.  Этот размер также поддерживает вложенную виртуализацию. |
| Малый GPU (визуализация) | <ul><li>6 ядер</li><li>56 ГБ ОЗУ</li> | Этот размер лучше всего подходит для удаленной визуализации, потоковой передачи, игр и кодирования с помощью таких платформ, как OpenGL и DirectX. |
| Малый GPU (вычисление) | <ul><li>6 ядер</li><li>56 ГБ ОЗУ</li></ul> |Этот размер лучше всего подходит для ресурсоемких компьютерных приложений, таких как искусственный интеллект и глубокое обучение. |
| Средний GPU (визуализация) | <ul><li>12 ядер</li><li>112 ГБ ОЗУ</li></ul> | Этот размер лучше всего подходит для удаленной визуализации, потоковой передачи, игр и кодирования с помощью таких платформ, как OpenGL и DirectX. |

## <a name="manage-identity"></a>Управление удостоверениями
Существует два типа ролей, которые может иметь Администратор учетной записи лаборатории.

- **Владелец**

    Администратор, которому назначена роль **владельца** , имеет полный доступ к учетной записи лаборатории, включая право предоставления другим пользователям доступа к учетной записи лаборатории и добавления авторов лаборатории. Администратор, создающий учетную запись лаборатории по умолчанию, добавляется в качестве владельца.
- **Участник**

    Администратор, которому назначена роль участника, может изменять параметры учетной записи лаборатории, но не может предоставлять доступ другим пользователям. Они также могут добавлять в них создатели лабораторий.

При присоединении коллекции общих образов к лабораторной учетной записи доступ автоматически предоставляется обоим администраторам и авторам лабораторий, чтобы они могли просматривать и сохранять изображения в коллекции. 

## <a name="pricing"></a>Стоимость

### <a name="azure-lab-services"></a>Службы лабораторий Azure
Цены на службы лаборатории Azure описаны в следующей статье: [цены на службы лаборатории Azure](https://azure.microsoft.com/pricing/details/lab-services/).

Также необходимо учитывать цены на общую галерею образов, если вы планируете использовать ее для хранения версий образов и управления ими. 

### <a name="shared-image-gallery"></a>Коллекция общих образов
Создание коллекции общих образов и ее подключение к учетной записи лаборатории осуществляется бесплатно. Стоимость не взимается, пока вы не сохраните версию образа в галерее. Как правило, цены на использование общей коллекции образов довольно незначительны, но важно понимать, как она вычисляется, так как она не включена в цены на службы лаборатории Azure.  

### <a name="storage-charges"></a>Плата за хранилище
Для хранения версий образов общая Коллекция образов использует диски, управляемые стандартными жесткими дисками. Размер используемого диска, управляемого на жестком диске, зависит от размера хранимой версии образа. Сведения о ценах на [управляемые диски](https://azure.microsoft.com/pricing/details/managed-disks/)см. в следующей статье.


### <a name="replication-and-network-egress-charges"></a>Плата за репликацию и исходящий трафик сети
При сохранении версии образа с помощью виртуальной машины шаблона лаборатории, службы лаборатории сначала сохраняют его в исходном регионе, а затем автоматически реплицируют версию исходного образа в один или несколько целевых регионов. Важно отметить, что службы лаборатории Azure автоматически реплицируют версию исходного образа во все целевые регионы в географическом расположении, где находится лабораторная лаборатория. Например, если учебная лаборатория находится в географическом расположении США, то версия образа реплицируется в каждый из восьми регионов, существующих в США.

Плата за исходящий трафик происходит при репликации версии образа из исходного региона в дополнительные целевые регионы. Сумма начислена в зависимости от размера версии образа, когда данные образа изначально передаются исходящими из исходного региона.  Сведения о ценах см. в следующей статье: [сведения о ценах на пропускную способность](https://azure.microsoft.com/pricing/details/bandwidth/).

Клиенты, имеющие [решения для образовательных учреждений](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) , могут отказаться от оплаты за исходящий трафик. Для получения дополнительных сведений обратитесь к менеджеру по работе с учетными записями.  Дополнительные сведения см. в разделе **вопросы и ответы** в связанном документе, в частности вопрос «какие программы передачи данных существуют для учебных заказчиков и как они должны быть определены?»).

### <a name="pricing-example"></a>Пример тарификации
Чтобы получить представление о ценах, описанных выше, рассмотрим пример сохранения нашего шаблона в общей коллекции образов. Предположим, что следующие сценарии:

- Имеется один пользовательский образ виртуальной машины.
- Вы сохраняете две версии образа.
- Ваша лаборатория находится в США, в которой всего восемь регионов.
- Размер каждой версии образа составляет 32 ГБ; в результате стоимость диска, управляемого диском, составляет $1,54 в месяц.

Общая стоимость оценивается следующим образом:

Число образов × число версий × число реплик х управляемый диск — Цена

В этом примере стоимость:

1 пользовательский образ (32 ГБ) x 2 версии x 8 регионов США x $1,54 = $24,64 в месяц

### <a name="cost-management"></a>Управление затратами
Администратору учетной записи лаборатории важно управлять затратами путем пошагового удаления ненужных версий образов из коллекции. 

Не следует удалять репликацию в конкретные регионы в качестве способа снижения затрат (этот параметр существует в коллекции общих образов). Изменения репликации могут негативно сказаться на способности службы лаборатории Azure публиковать виртуальные машины из образов, сохраненных в общей коллекции образов.

## <a name="next-steps"></a>Дальнейшие действия
Пошаговые инструкции по созданию учетной записи лаборатории и лаборатории см. в руководстве по [настройке учетной записи лаборатории](tutorial-setup-lab-account.md) .
