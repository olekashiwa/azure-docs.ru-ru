---
title: Службы автоматизации Azure и сопоставления рабочих областей Log Analytics
description: В этой статье описываются сопоставления, разрешенные между учетной записью службы автоматизации и рабочей областью Log Analytics для поддержки решения.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849519"
---
# <a name="workspace-mappings"></a>Сопоставления рабочей области

При включении таких решений, как Управление обновлениями, Отслеживание изменений и Инвентаризация или решения Запуск и остановка виртуальных машин в нерабочее время, для связывания Log Analytics рабочей области и учетной записи службы автоматизации поддерживаются только определенные регионы. Это сопоставление применяется только к учетной записи службы автоматизации и Log Analytics рабочей области. Ресурсы, сообщающие учетной записи службы автоматизации или Log Analytics рабочей области, могут находиться в других регионах.

## <a name="supported-mappings"></a>Поддерживаемые сопоставления

Поддерживаемое сопоставление показано в следующей таблице.

|**Регион рабочей области Log Analytics**|**Регион учетной записи службы автоматизации**|
|---|---|
|**США**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Канада**||
|CanadaCentral|CanadaCentral|
|**Азиатско-Тихоокеанский регион**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Европа**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> сопоставление EastUS для рабочих областей log Analytics с учетными записями службы автоматизации не является точным соответствием региона, но является правильным сопоставлением.

<sup>2</sup> из-за емкости ограничений регион недоступен при создании новых ресурсов. Сюда входят учетные записи службы автоматизации и рабочие области Log Analytics. Однако уже существующие связанные ресурсы в регионе должны продолжать работать.

## <a name="unlink-workspace"></a>Удаление связи с рабочей областью

Если вы решили, что вам больше не нужно интегрировать учетную запись службы автоматизации с Log Analytics рабочей областью, вы можете удалить связь учетной записи непосредственно с портал Azure. Прежде чем продолжать, сначала необходимо удалить Управление обновлениями, Отслеживание изменений и инвентаризацию, или Запуск и остановка виртуальных машин в нерабочее время решения, если они используются. Если не удалить их, этот процесс не будет продолжен. Найдите в статье решение, которое вы импортировали, чтобы узнать, как его удалить.

Удалив эти решения, выполните следующие действия, чтобы отменить привязку учетной записи службы автоматизации.

> [!NOTE]
> Некоторые решения, в том числе решения для мониторинга Azure SQL более ранних версий, могли создать ресурсы службы автоматизации. Их также потребуется удалить, прежде чем удалить связь с рабочей областью.

1. На портале Azure откройте свою учетную запись службы автоматизации и на странице этой учетной записи выберите **Связанная рабочая область** в разделе **Связанные ресурсы** слева.

2. На странице "Отмена связи с рабочей областью" нажмите кнопку **Отменить связь с рабочей областью**. Появится запрос на подтверждение продолжения.

3. Пока служба автоматизации Azure пытается отменить привязку учетной записи к рабочей области Log Analytics, вы можете отслеживать ход выполнения этой задачи в меню **Уведомления**.

Если вы используете решение управления обновлениями, при необходимости можно удалить следующие элементы, которые больше не нужны после удаления решения.

* Обновление расписаний. Каждый элемент имеет имя, которое соответствует созданному вами развертыванию обновления.

* Группы гибридных рабочих ролей, созданные для решения. каждый из них будет называться аналогично `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`).

Если вы используете решение запуска и остановки виртуальных машин в нерабочее время, при необходимости можно удалить следующие элементы, которые больше не нужны после удаления решения.

* Расписание запуска и остановки виртуальной машины.
* Рабочие роли Runbook запуска и остановки виртуальной машины.
* Переменные

Кроме того, вы можете удалить связь рабочей области с учетной записью службы автоматизации из рабочей области Log Analytics. В рабочей области выберите **учетная запись службы автоматизации** в разделе **связанные ресурсы**. На странице Учетная запись службы автоматизации выберите отменить **связь с учетной записью**.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как подключить следующие решения:

Управление обновлениями и Отслеживание изменений и инвентаризации:

* Из [виртуальной машины](../automation-onboard-solutions-from-vm.md)
* Из [учетной записи службы автоматизации](../automation-onboard-solutions-from-automation-account.md)
* При [просмотре нескольких компьютеров](../automation-onboard-solutions-from-browse.md)
* Из [модуля Runbook](../automation-onboard-solutions.md)

Запуск и остановка виртуальных машин в нерабочее время

* [Развертывание Запуск и остановка виртуальных машин в нерабочее время](../automation-solution-vm-management.md)
