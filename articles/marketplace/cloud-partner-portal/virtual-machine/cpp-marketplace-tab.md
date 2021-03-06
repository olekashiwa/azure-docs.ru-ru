---
title: Вкладка "виртуальные машины Marketplace" в Портал Cloud Partner для Azure Marketplace
description: Описание вкладки Marketplace, используемой при создании предложения виртуальной машины в Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 283274986c753fc8ad05b9b7b0dd87aea956bcce
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75762898"
---
# <a name="virtual-machine-marketplace-tab"></a>Вкладка Marketplace виртуальной машины

Вкладка **Marketplace** на странице **Новое предложение** позволяет предоставить потенциальным клиентам сведения о маркетинговых и торговых предложениях, юридическую информацию и соглашения. Здесь вы также можете управлять потенциальными клиентами, полученными из Marketplace. Полная форма страницы состоит из четырех разделов: **Overview** (Обзор), **Marketing Artifacts** (Маркетинговые артефакты), **Lead Management** (Управление потенциальными клиентами) и **Legal** (Юридическая информация).


## <a name="overview-section"></a>Раздел общих сведений
В этом разделе вы публикуете общие сведения о предложении Azure Marketplace.  Символ звездочки (*) возле имени поля означает, что оно является обязательным.

![Раздел "Обзор" на вкладке "Marketplace" для виртуальных машин](./media/publishvm_008.png)

В следующей таблице описаны назначение и содержимое этих полей. Обязательные поля помечены звездочкой (*).

|  **Поле**                |     **Описание**                                                          |
|  ---------                |     ---------------                                                          |
| **Заголовок\***                 | Название предложения (часто длинное официальное имя). Этот заголовок будет отображаться на видном месте в marketplace.  Максимальная длина составляет 50 символов. |
| **Сводка\***               | Краткое описание назначения или функции решения.  Максимальная длина составляет 100 символов. |
| **\* длинной сводки**          | Назначение или функция решения.  Максимальная длина составляет 256 символов. |
| **Description\***           | Описание решения.  Максимальная длина составляет 3000 символов. Поддерживается простое HTML-форматирование. |
| **\* канала торгового посредника Microsoft CSP** | Вы можете получить доступ к партнерским каналам поставщика облачных решений (CSP).  Дополнительные сведения о маркетинге вашего предложения с помощью партнерских каналов Microsoft CSP см. в статье [поставщики облачных решений](../../cloud-solution-providers.md) . |
| **Идентификатор маркетингового\***  | Уникальный URL-адрес, который будет связан с этим предложением. Обычно включает имя организации и имя решения. Максимальная длина составляет 50 символов.  Пример. <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Предварительный просмотр идентификаторов подписок\*** | Добавьте от 1 до 100 идентификаторов подписок пользователей, которые смогут использовать режим предварительной версии. Эти разрешенные подписки получат доступ к предложению сразу после его публикации (до официального запуска). |
| **Полезные ссылки**          | Добавьте URL-адреса в документацию, заметки о выпуске, часто задаваемые вопросы и т. д. |
| **Предлагаемые категории\*** | Выберите до двух (2) категорий, включая первичную и вторичную категории (необязательно). Выберите до двух (2) подкатегорий для каждой первичной и (или) вторичной категории. Если подкатегории не выбраны, предложение будет по-прежнему доступно для выбранной категории. |
|  |  |


## <a name="marketing-artifacts-section"></a>Раздел Marketing Artifacts (Маркетинговые артефакты)

Это второй раздел делится на три подраздела: **логотипы**, **снимки экрана** и **видео**. Из маркетинговых артефактов обязательными являются только логотипы, но мы рекомендуем заполнить их все, чтобы повысить привлекательность предложения для клиентов. 

![Раздел Marketing Artifacts (Маркетинговые артефакты) на вкладке Marketplace в форме "Новое предложение" для виртуальных машин](./media/publishvm_009.png)

В следующей таблице описаны назначение и содержимое этих полей. Обязательные поля помечены звездочкой (*).

|  **Поле**                |     **Описание**                                                          |
|  ---------                |     ---------------                                                          |
| *Логотипы*  |  |
| **Малый\***                 | Растровое изображение размером 40 x 40 пикселей в формате ICO                                                      |
| **Средний\***                | Растровое изображение размером 90 x 90 пикселей в формате ICO                                                      |
| **Крупный\***                 | Растровое изображение размером 115 x 115 пикселей в формате ICO                                                   |
| **Широкие\***                  | Растровое изображение размером 255 x 115 пикселей в формате ICO                                                    |
| **Имиджевый**                  | Растровое изображение размером 815 x 290 пикселей.  Не является обязательным, но после отправки имиджевого логотипа вы не сможете его удалить. |
| *Снимки экрана*  | Не являются обязательными. Не более пяти снимков экрана на один номер SKU. |
| **Название**                  | Имя или заголовок <!-- TODO - max char length? none specified in UI -->                               |
| **Изображение**                 | Изображение снимка экрана размером 533 x 324 пикселя                                         |
| *Видео*  |  |
| **Название**                  | Имя или заголовок  <!-- TODO - max char length? -->                              |
| **Ссылка**                  | URL-адрес видео, размещенного на сайте YouTube или Vimeo.                                        |
| **Эскиз**             | Растровое изображение размером 533 x 324 пикселя                                                               |
|   |   |

### <a name="logo-guidelines"></a>Рекомендации по логотипам

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Все логотипы, передаваемые на Портале Cloud Partner, должны соответствовать следующим рекомендациям:

*  Дизайн Azure отличается простой цветовой палитрой. Используйте в логотипах небольшое число основных и дополнительных цветов.
*  Цвета темы портала Azure — белый и черный. Поэтому постарайтесь не использовать эти цвета для фона своих логотипов. Используйте цвета, которые сделают ваши логотипы четко видимыми на портале Azure. Мы рекомендуем простые основные цвета. Если вы используете прозрачный фон, то логотип и текст не должны быть белого, черного или синего цвета.
*  Не используйте в логотипе градиентный фон.
*  Старайтесь не размещать в логотипе текст, даже название компании или торговую марку. Логотип должен быть "плоским". Также избегайте градиентов.
*  Не растягивайте логотип.

#### <a name="hero-logo"></a>Имиджевый логотип

Имиджевый логотип не является обязательным, но после отправки вы не сможете его удалить.  Имиджевый логотип должен следовать таким рекомендациям:

*  Фон имиджевого логотипа не может быть черным, белым или прозрачным.
*  Не используйте светлые цвета в качестве фона имиджевого логотипа.  Отображаемое имя издателя, название плана и развернутая сводка предложения отображаются в белом цвете и должны выделяться относительно фона.
*  Избегайте использования только текстовых элементов при разработке имиджевого логотипа.  Когда предложение публикуется, в имиджевый логотип программным образом вставляются отображаемое имя издателя, название плана, развернутая сводка предложения и кнопка "Создать". 
* Добавьте неиспользуемый прямоугольник в правой части имиджевого логотипа размером 415 x 100 пикселей со смещением в 370 пикселей от левого края.  

Например, следующий имиджевый логотип предназначен для Службы контейнеров Azure.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Пример имиджевого логотипа для Службы контейнеров Azure](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Пример маркетинговой информации 

На следующем рисунке показано, как маркетинговая информация отображается на главной странице продукта Microsoft Windows Server.

![Пример страницы продукта для Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Раздел Lead Management (Управление потенциальными клиентами)

В третьем разделе собираются сведения о потенциальных клиентах, сгенерированных благодаря вашим предложениям Azure Marketplace. Здесь предлагаются следующие параметры хранения (из раскрывающегося списка) для этой информации о потенциальных клиентах.

* **Нет** (вариант по умолчанию) — сведения о потенциальных клиентах не собираются.
* Таблица Azure — данные сохраняются в таблицу Azure, для которой указана строка подключения.
* Dynamics CRM Online — данные сохраняются в экземпляр [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/), для которого указаны URL-адрес и учетные данные для аутентификации.
* Конечная точка HTTPS — данные сохраняются в указанную конечную точку HTTPS в формате полезных данных JSON.
* Marketo — данные сохраняются в экземпляр [Marketo](https://www.marketo.com/), для которого указаны идентификатор сервера, идентификатор Munchkin и идентификатор формы.
* SalesForce — данные сохраняются в базу данных [Salesforce](https://www.salesforce.com/), для которой указан идентификатор объекта.

После успешной публикации предложения проходит автоматическая проверка подключения для передачи сведений о потенциальных клиентах, а затем в настроенное назначение автоматически отправляется тестовый потенциальный клиент. Сведения о потенциальных клиентах следует постоянно администрировать и своевременно обновлять при любых изменениях в архитектуре управления клиентами.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Юридический раздел

В этом последнем разделе вы задаете необходимые юридические документы, необходимые для каждого предложения.  

|  **Поле**                    |     **Описание**                                        |
|  ---------                    |     ---------------                                        |
| **URL-адрес политики конфиденциальности\***      | URL-адрес опубликованной политики конфиденциальности.                          |
| **Использовать стандартный контракт?\***  |   |
| **Условия использования\***            | Политика в виде файла с обычным текстом или простого кода HTML.                       |
|  |  |


## <a name="next-steps"></a>Дальнейшие действия

На вкладке [Поддержка](./cpp-support-tab.md) вы предоставляете технические ресурсы и ресурсы поддержки пользователей для предложения.
