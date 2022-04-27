---
lab:
  title: 리소스 공급자 사용
  module: Setup
ms.openlocfilehash: 2cd48d0d9f67b9bab3e64452bf6199e1f438492a
ms.sourcegitcommit: e06fbeaa3bc15e4dbe99f72216dfeeb27f58babd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2022
ms.locfileid: "138765494"
---
# <a name="enable-resource-providers"></a>리소스 공급자 사용

Azure 구독에는 몇 가지 리소스 공급자를 등록해야 합니다. 이러한 공급자가 등록되어 있는지 확인하려면 다음 단계를 수행합니다.

1. 구독과 연결된 Microsoft 자격 증명을 사용하여 `https://portal.azure.com`에서 Azure Portal에 로그인합니다.
2. **홈** 페이지에서 **구독** 을 선택합니다(또는 **&#8801;** 메뉴에서 **모든 서비스** 를 선택하고 **일반** 범주에서 **구독** 선택).
3. Azure 구독을 선택합니다(구독이 여러 개이면 Azure Pass를 사용하여 만든 구독 선택).
4. 구독 블레이드의 왼쪽 창에 있는 **설정** 섹션에서 **리소스 공급자** 를 선택합니다.
5. 리소스 공급자 목록에서 다음 공급자가 등록되어 있는지 확인합니다(등록되어 있지 않은 공급자는 선택 후 **등록** 클릭).
    - Microsoft.BotService
    - Microsoft.Web
    - Microsoft.ManagedIdentity
    - Microsoft.Search
    - Microsoft.Storage
    - Microsoft.CognitiveServices
    - Microsoft.AlertsManagement
    - microsoft.insights
    - Microsoft.KeyVault
    - Microsoft.ContainerInstance
