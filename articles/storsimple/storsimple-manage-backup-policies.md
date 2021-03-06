---
title: "StorSimple 백업 정책 관리 | Microsoft Docs"
description: "StorSimple Manager 서비스를 사용하여 수동 백업, 백업 일정 및 백업 보존을 만들고 관리하는 방법을 설명합니다."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: d1834bc8-d520-4463-82ae-3b32fabd021c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 89382bbed05034f7bb61df2c5d1b09461da44651
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-backup-policies"></a>StorSimple 관리자 서비스를 사용하여 백업 정책 관리
> [!NOTE]
> StorSimple의 클래식 포털은 사용되지 않습니다. StorSimple 장치 관리자는 사용 중단 일정에 따라 자동으로 새 Azure Portal로 이동합니다. 이 이동에 대한 메일 및 포털 알림을 받게 됩니다. 이 문서도 곧 사용 중지됩니다. 이동과 관련된 자세한 내용은 [FAQ: Azure Portal로 이동](storsimple-8000-move-azure-portal-faq.md)을 참조하세요.

[!INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## <a name="overview"></a>개요
이 자습서에서는 StorSimple Manager 서비스 **Backup 정책** 페이지를 사용하여 StorSimple 볼륨에 대한 백업 프로세스 및 백업 보존을 제어하는 방법을 설명합니다. 수동 백업을 완료하는 방법도 설명합니다.

**Backup 정책** 페이지에서는 백업 정책을 관리하고 로컬과 클라우드 스냅숏을 예약할 수 있습니다. (Backup 정책이 볼륨의 컬렉션에 대한 백업 보존 및 백업 일정을 구성하는데 사용됩니다.) Backup 정책을 통해 동시에 여러 볼륨의 스냅숏을 사용할 수 있습니다. 이 백업 정책에서 생성된 백업은 크래시 일관성이 있는 복사본임을 의미합니다. 이 페이지는 백업 정책, 해당 형식, 연결된 볼륨, 보존된 백업의 수 및 이 정책을 사용하도록 설정하는 옵션을 나열합니다.

**Backup 정책** 페이지에서 다음 필드 중 하나 이상의 기존 백업 정책을 필터링 할 수도 있습니다.

* **정책 이름** – 정책과 연결된 이름입니다. 다음과 같은 다양한 유형의 정책이 포함됩니다.
  
  * 사용자가 명시적으로 만든 예약된 정책입니다.
  * 볼륨 생성 시 이 볼륨 옵션에 대한 기본 백업이 활성화되면 만들어지는 자동 정책입니다. 이 정책은 사용자가 Azure 클래식 포털에서 구성한 StoreSimple 볼륨의 이름을 참조한 볼륨 이름에서 VolumeName_Default로 이름이 지정됩니다.  자동 정책을 사용하면 22시 30분 장치 시간에서 시작하는 일일 클라우드 스냅숏이 발생됩니다.
  * 원래 StorSimple 스냅숏 관리자에서 만든 가져온 정책입니다. 이 정책에서 가져온 StorSimple 스냅숏 관리자 호스트를 설명하는 태그가 포함됩니다.
* **볼륨** – 정책과 연결된 볼륨입니다. 백업을 만들 때 백업 정책과 연관된 모든 볼륨이 함께 그룹화됩니다.
* **마지막으로 성공한 백업** –이 정책을 사용하여 수행된 마지막으로 성공한 백업 시간과 날짜입니다.
* **다음 백업** –이 정책에서 시작될 다음 예약 백업의 시간과 날짜입니다.
* **일정** – 백업 정책과 연관된 일정의 수입니다.

이 페이지에서 수행할 수 있는 자주 사용 되는 작업은 다음과 같습니다.

* 백업 정책 추가 
* 일정 추가 또는 수정 
* 백업 정책 삭제 
* 수동 백업 수행 
* 여러 볼륨과 일정의 사용자 지정 백업 정책 만들기 

## <a name="add-a-backup-policy"></a>백업 정책 추가
자동 백업을 예약하려면 백업 정책을 추가합니다. StorSimple 장치에 대한 백업 정책을 추가하려면 Azure 클래식 포털에서 아래 단계를 수행합니다. 정책을 추가한 후 일정을 정의할 수 있습니다( [일정 추가 또는 수정](#add-or-modify-a-schedule)참조).

[!INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]

![동영상 사용 가능](./media/storsimple-manage-backup-policies/Video_icon.png) **동영상 사용 가능**

로컬 또는 클라우드 백업 정책을 만드는 방법을 보여 주는 동영상을 시청하려면 [여기](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/)를 클릭하세요.

## <a name="add-or-modify-a-schedule"></a>일정 추가 또는 수정
StorSimple 장치의 기존 백업 정책에 첨부된 일정을 추가하거나 수정할 수 있습니다. 일정을 추가하거나 수정하려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## <a name="delete-a-backup-policy"></a>백업 정책 삭제
StorSimple 장치에서 백업 정책을 삭제하려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>수동 백업 수행
단일 볼륨에 대한 주문형(수동) 백업을 만들려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="create-a-custom-backup-policy-with-multiple-volumes-and-schedules"></a>여러 볼륨과 일정의 사용자 지정 백업 정책 만들기
다중 볼륨 및 일정이 포함된 사용자 백업 정책을 만들려면 Azure 클래식 포털에서 다음 단계를 수행합니다.

[!INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]

## <a name="next-steps"></a>다음 단계
[StorSimple Manager 서비스를 사용하여 StorSimple 장치를 관리](storsimple-manager-service-administration.md)하는 방법을 자세히 알아봅니다.

