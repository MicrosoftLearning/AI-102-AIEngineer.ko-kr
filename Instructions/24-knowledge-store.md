---
lab:
  title: Azure Cognitive Search를 사용하여 지식 저장소 만들기
  module: Module 12 - Creating a Knowledge Mining Solution
ms.openlocfilehash: 8d7008ee592d4a76f24ca26a4b192c7dd5cbe9ea
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "137819434"
---
# <a name="create-a-knowledge-store-with-azure-cognitive-search"></a>Azure Cognitive Search를 사용하여 지식 저장소 만들기

Azure Cognitive Search에서는 인식 기술 보강 파이프라인을 사용하여 문서에서 AI 생성 필드를 추출한 다음 검색 인덱스에 포함합니다. 인덱스를 인덱싱 프로세스의 기본 출력으로 고려할 수 있지만 인덱스에 포함된 보강 데이터는 다른 방식으로 유용할 수도 있습니다. 예를 들면 다음과 같습니다.

- 인덱스는 기본적으로 인덱싱된 레코드를 나타내는 JSON 개체의 컬렉션이므로 데이터 오케스트레이션 프로세스에 통합하는 경우 Azure Data Factory와 같은 도구를 사용하여 개체를 JSON 파일로 내보내는 것이 유용할 수 있습니다.
- 분석 및 보고를 위해 Microsoft Power BI와 같은 도구를 사용하여 인덱스 레코드를 테이블의 관계형 스키마로 정규화할 수도 있습니다.
- 인덱싱 프로세스 중에 문서에서 포함된 이미지를 추출하면 해당 이미지를 파일로 저장할 수 있습니다.

이 연습에서는 브로셔 및 호텔 리뷰의 정보를 사용해 고객의 여행 계획을 지원하는 가상의 여행사인 *Margie's Travel* 용으로 지식 저장소를 구현합니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 이미 복제했다면 Visual Studio Code에서 해당 리포지토리를 열고, 그렇지 않으면 다음 단계에 따라 리포지토리를 지금 복제합니다.

1. Visual Studio Code 시작
2. 팔레트를 열고(Shift+Ctrl+P 누르기) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

> **참고**: **[Create an Azure Cognitive Search solution](22-azure-search.md)** 연습을 이전에 완료했으며 구독에 해당 연습의 Azure 리소스가 아직 남아 있으면 이 섹션을 건너뛰고 **검색 솔루션 만들기** 섹션부터 시작하면 됩니다. 그렇지 않은 경우에는 아래 단계에 따라 필요한 Azure 리소스를 프로비전합니다.

1. 웹 브라우저에서 `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. 구독의 **리소스 그룹** 을 확인합니다.
3. 제한된 구독을 사용 중이어서 리소스 그룹이 제공되어 있다면 해당 리소스 그룹을 선택하여 속성을 확인합니다. 그렇지 않은 경우에는 원하는 이름으로 새 리소스 그룹을 만들고 그룹이 만들어지면 해당 그룹으로 이동합니다.
4. 리소스 그룹의 **개요** 페이지에서 **구독 ID** 및 **위치** 를 확인합니다. 후속 단계에서 이러한 값과 리소스 그룹의 이름이 필요합니다.
5. Visual Studio Code에서 **24-knowledge-store** 폴더를 확장하고 **setup.cmd** 를 선택합니다. 이 배치 스크립트를 사용하여 필요한 Azure 리소스를 만드는 데 필요한 Azure CLI(명령줄 인터페이스)를 실행합니다.
6. **24-knowledge-store** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기** 를 선택합니다.
7. 터미널 창에서 다음 명령을 입력하여 Azure 구독에 대해 인증된 연결을 설정합니다.

    ```
    az login --output none
    ```

8. 메시지가 표시되면 Azure 구독에 로그인합니다. 그런 다음 Visual Studio Code로 돌아가서 로그인 프로세스가 완료될 때까지 기다립니다.
9. 다음 명령을 실행하여 Azure 위치를 나열합니다.

    ```
    az account list-locations -o table
    ```

10. 출력에서 리소스 그룹 위치에 해당하는 **Name** 값을 찾습니다(예를 들어 미국 동부에 해당하는 이름은 *eastus* 임).
11. **setup.cmd** 스크립트에서 구독 ID, 리소스 그룹 이름 및 위치 이름에 해당하는 값을 사용하여 **subscription_id**, **resource_group** 및 **location** 변수 선언을 수정합니다. 그런 다음, 변경 사항을 저장합니다.
12. **24-knowledge-store** 폴더의 터미널에서 다음 명령을 입력하여 스크립트를 실행합니다.

    ```
    setup
    ```
    > **참고**: Search CLI 모듈은 미리 보기 상태이므로 *- Running ..* 프로세스에서 실행이 중단될 수 있습니다. 포함되어 있습니다. 실행이 2분 넘게 중단되면 Ctrl+C를 눌러 장기 실행 작업을 취소한 다음 스크립트를 종료할 것인지를 묻는 메시지가 표시되면 **N** 을 선택합니다. 그러면 실행이 정상적으로 완료됩니다.
    >
    > 스크립트 실행이 실패하면 올바른 변수 이름을 적용하여 스크립트를 저장했는지 확인하고 다시 시도합니다.

13. 스크립트가 완료되면 표시되는 출력을 검토하고 Azure 리소스에 대한 다음 정보를 확인합니다(나중에 해당 값이 필요함).
    - 스토리지 계정 이름
    - 스토리지 연결 문자열
    - Cognitive Services 계정
    - Cognitive Services 키
    - 검색 서비스 엔드포인트
    - 검색 서비스 관리자 키
    - 검색 서비스 쿼리 키

14. Azure Portal에서 리소스 그룹을 새로 고쳐 Azure Storage 계정, Azure Cognitive Services 리소스 및 Azure Cognitive Search 리소스가 포함되어 있는지 확인합니다.

## <a name="create-a-search-solution"></a>검색 솔루션 만들기

이제 필요한 Azure 리소스가 준비되었으므로 다음 구성 요소로 이루어진 검색 솔루션을 만들 수 있습니다.

- Azure Storage 컨테이너의 문서를 참조하는 **데이터 원본**
- 문서에서 AI 생성 필드를 추출하는 기술 보강 파이프라인을 정의하는 **기술 세트** 이 기술 세트에 따라 *지식 저장소* 에서 생성될 *프로젝션* 도 정의됩니다.
- 문서 레코드의 검색 가능 세트를 정의하는 **인덱스**
- 데이터 원본에서 문서를 추출하여 기술 세트를 적용한 다음 인덱스를 채우는 **인덱서** 인덱싱 프로세스에서는 지식 저장소의 기술 세트에 정의되어 있는 프로젝션도 유지됩니다.

이 연습에서는 Azure Cognitive Search REST 인터페이스를 사용하여 JSON 요청을 제출해 이러한 구성 요소를 만듭니다.

### <a name="prepare-json-for-rest-operations"></a>REST 작업용 JSON 준비

이 연습에서는 REST 인터페이스를 사용해 Azure Cognitive Search 구성 요소의 JSON 정의를 제출합니다.

1. Visual Studio Code의 **24-knowledge-store** 폴더에서 **create-search** 폴더를 확장하고 **data_source.json** 을 선택합니다. 이 파일에는 **margies-knowledge-data** 데이터 원본의 JSON 정의가 포함되어 있습니다.
2. **YOUR_CONNECTION_STRING** 자리 표시자는 Azure Storage 계정의 연결 문자열로 바꿉니다. 이 연결 문자열은 다음과 같습니다.

    ```
    DefaultEndpointsProtocol=https;AccountName=ai102str123;AccountKey=12345abcdefg...==;EndpointSuffix=core.windows.net
    ```

    Azure Portal의 스토리지 계정 **액세스 키** 페이지에서 연결 문자열을 확인할 수 있습니다.

3. 업데이트된 JSON 파일을 저장하고 닫습니다.
4. **create-search** 폴더에서 **skillset.json** 을 엽니다. 이 파일에는 **margies-knowledge-skillset** 기술 세트의 JSON 정의가 포함되어 있습니다.
5. 기술 세트 정의 맨 위에 있는 **cognitiveServices** 요소에서 **YOUR_COGNITIVE_SERVICES_KEY** 자리 표시자를 Cognitive Services 리소스의 키 중 하나로 바꿉니다.

    Azure Portal의 Cognitive Services 리소스 **키 및 엔드포인트** 페이지에서 키를 확인할 수 있습니다.

6. 기술 세트의 기술 컬렉션 끝부분에서 **Microsoft.Skills.Util.ShaperSkill** 기술 **define-projection** 을 찾습니다. 이 기술은 프로젝션에 사용할 보강된 데이터용 JSON 구조를 정의합니다. 인덱서에서 처리하는 각 문서에 대해 파이프라인이 지식 저장소에서 이 구조를 유지합니다.
7. 기술 세트 파일 아래쪽에서 해당 기술 세트에는 **knowledgeStore** 정의도 포함되어 있음을 확인합니다. 이 정의에는 지식 저장소를 만들 Azure Storage 계정의 연결 문자열과 **프로젝션** 컬렉션이 들어 있습니다. 이 기술 세트에는 세 가지 *프로젝션 그룹* 이 포함되어 있습니다.
    - 기술 세트에 있는 쉐이퍼 기술에 대한 **knowledge_projection** 출력을 기반으로 한 *개체* 프로젝션을 포함하는 그룹.
    - 문서에서 추출된 이미지 데이터의 **normalized_images** 컬렉션을 기반으로 한 *파일* 프로젝션을 포함하는 그룹.
    - 다음 *테이블* 프로젝션을 포함하는 그룹.
        - **KeyPhrases**: 자동으로 생성된 키 열과 쉐이퍼 기술의 **knowledge_projection/key_phrases/** 컬렉션 출력에 매핑된 **keyPhrase** 열을 포함합니다.
        - **Locations**: 자동으로 생성된 키 열과 쉐이퍼 기술의 **knowledge_projection/key_phrases/** 컬렉션 출력에 매핑되는 **location** 열을 포함합니다.
        - **ImageTags**: 자동으로 생성된 키 열과 쉐이퍼 기술의 **knowledge_projection/image_tags/** 컬렉션 출력에 매핑되는 **tag** 열을 포함합니다.
        - **Docs**: 자동으로 생성된 키 열, 그리고 쉐이퍼 기술에서 테이블에 사전 할당되지 않은 모든 **knowledge_projection** 출력값을 포함합니다.
8. **storageConnectionString** 값의 **YOUR_CONNECTION_STRING** 자리 표시자를 스토리지 계정의 연결 문자열로 바꿉니다.
9. 업데이트된 JSON 파일을 저장하고 닫습니다.
10. **create-search** 폴더에서 **index.json** 을 엽니다. 이 파일에는 **margies-knowledge-index** 인덱스의 JSON 정의가 포함되어 있습니다.
11. 인덱스의 JSON을 검토한 후 내용을 변경하지 않고 파일을 닫습니다.
12. **create-search** 폴더에서 **indexer.json** 을 엽니다. 이 파일에는 **margies-knowledge-indexer** 인덱서의 JSON 정의가 포함되어 있습니다.
13. 인덱서의 JSON을 검토한 후 내용을 변경하지 않고 파일을 닫습니다.

### <a name="submit-rest-requests"></a>REST 요청 제출

검색 솔루션 구성 요소를 정의하는 JSON 개체를 준비했으므로 REST 인터페이스에 JSON 문서를 제출하여 해당 구성 요소를 만들 수 있습니다.

1. **create-search** 폴더에서 **create-search.cmd** 를 엽니다. 이 배치 스크립트는 cURL 유틸리티를 사용해 Azure Cognitive Search 리소스의 JSON 정의를 REST 인터페이스에 제출합니다.
2. **YOUR_SEARCH_URL** 및 **YOUR_ADMIN_KEY** 변수 자리 표시자를 각각 **Url**, 그리고 Azure Cognitive Search 리소스의 **관리 키** 중 하나로 바꿉니다.

    Azure Portal의 Azure Cognitive Search 리소스 **개요** 및 **키** 페이지에서 이러한 값을 확인할 수 있습니다.

3. 업데이트된 배치 파일을 저장합니다.
4. **create-search** 폴더를 마우스 오른쪽 단추로 클릭하고 **통합 터미널에서 열기** 를 선택합니다.
5. **create-search** 폴더의 터미널 창에서 다음 명령을 입력하여 배치 스크립트를 실행합니다.

    ```
    create-search
    ```

6. 스크립트 실행이 완료되면 Azure Portal의 Azure Cognitive Search 리소스 페이지에서 **인덱서** 페이지를 선택하고 인덱싱 프로세스가 완료될 때까지 기다립니다.

    **새로 고침** 을 선택하여 인덱싱 작업 진행률을 추적할 수 있습니다. 인덱싱이 완료되려면 1분 정도 걸릴 수 있습니다.

    > **팁**: 스크립트 실행이 실패하면 **data_source.json** 및 **skillset.json** 파일과 **create-search.cmd** 파일에 추가한 자리 표시자를 확인합니다. 잘못 입력한 자리 표시자를 수정한 후에는 스크립트를 다시 실행하기 전에 Azure Portal 사용자 인터페이스를 사용하여 검색 리소스에서 만든 구성 요소를 삭제해야 할 수 있습니다.

## <a name="view-the-knowledge-store"></a>지식 저장소 확인

기술 세트를 사용하는 인덱서를 실행하여 지식 저장소를 만든 후에는 인덱싱 프로세스에서 추출된 보강 데이터가 지식 저장소 프로젝션에 유지됩니다.

### <a name="view-object-projections"></a>개체 프로젝션 보기

Margie’s Travel의 기술 세트에서 정의된 *개체* 프로젝션은 인덱싱된 각 문서에 대한 JSON 파일로 구성됩니다. 해당 파일은 기술 세트 정의에 지정된 Azure Storage 계정의 Blob 컨테이너에 저장됩니다.

1. Azure Portal에서 이전에 만든 Azure Storage 계정을 확인합니다.
2. 왼쪽 창에서 **스토리지 탐색기** 탭을 선택하여 Azure Portal의 스토리지 탐색기 인터페이스에서 스토리지 계정을 확인합니다.
2. **BLOB 컨테이너** 를 펼쳐 스토리지 계정의 컨테이너를 표시합니다. 원본 데이터가 저장되는 **margies** 컨테이너 외에도 **margies-images** 및 **margies-knowledge** 의 두 가지 새 컨테이너가 있어야 합니다. 해당 컨테이너는 인덱싱 프로세스에 의해 생성되었습니다.
3. **margies-knowledge** 컨테이너를 선택합니다. 해당 컨테이너에는 인덱싱된 각 문서에 대한 폴더가 포함되어야 합니다.
4. 아무 폴더나 열고 폴더에 포함되어 있는 **knowledge-projection.json** 파일을 다운로드하여 엽니다. 각 JSON 파일에는 여기에 표시된 대로 기술 세트에서 추출한 보강 데이터를 포함하여 인덱싱된 문서의 표현이 포함되어 있습니다.

```
{
    "file_id":"abcd1234....",
    "file_name":"Margies Travel Company Info.pdf",
    "url":"https://store....blob.core.windows.net/margies/...pdf",
    "language":"en",
    "sentiment":0.83164644241333008,
    "key_phrases":[
        "Margie’s Travel",
        "Margie's Travel",
        "best travel experts",
        "world-leading travel agency",
        "international reach"
        ],
    "locations":[
        "Dubai",
        "Las Vegas",
        "London",
        "New York",
        "San Francisco"
        ],
    "image_tags":[
        "outdoor",
        "tree",
        "plant",
        "palm"
        ]
}
```

이와 같이 *개체* 프로젝션을 만드는 기능을 사용하면 엔터프라이즈 데이터 분석 솔루션에 통합될 수 있는 보강 데이터 개체를 생성할 수 있습니다. 예를 들어, 추가 처리 또는 데이터 웨어하우스로 로드하기 위해 Azure Data Factory 파이프라인으로 JSON 파일을 수집합니다.

### <a name="view-file-projections"></a>파일 프로젝션 보기

기술 세트에 정의된 *파일* 프로젝션은 인덱싱 프로세스 중 문서에서 추출된 각 이미지에 대한 JPEG 파일을 만듭니다.

1. Azure Portal의 스토리지 탐색기 인터페이스에서 **margies-images** Blob 컨테이너를 선택합니다. 이 컨테이너는 이미지를 포함하는 각 문서에 대한 폴더를 포함합니다.
2. 폴더를 열고 내용을 확인합니다. 각 폴더에는 하나 이상의 \*.jpg 파일이 포함되어 있습니다.
3. 이미지 파일을 열어 문서에서 추출한 이미지가 포함되어 있는지 확인합니다.

이와 같이 *파일* 프로젝션을 생성하는 기능은 대량의 문서에서 포함된 이미지를 추출하는 효율적인 방법을 제공합니다.

### <a name="view-table-projections"></a>테이블 프로젝션 보기

기술 세트에 정의된 *테이블* 프로젝션은 보강 데이터의 관계형 스키마를 형성합니다.

1. Azure Portal의 스토리지 탐색기 인터페이스에서 **테이블** 을 펼칩니다.
2. **문서** 테이블을 선택하여 해당 열을 확인합니다. 열에는 몇 가지 표준 Azure Storage 테이블 열이 포함되어 있습니다. 해당 열을 숨기려면 **열 옵션** 을 수정하여 다음 열만 선택합니다.
    - **document_id**(인덱싱 프로세스에서 자동으로 생성되는 키 열)
    - **file_id**(인코딩된 파일 URL)
    - **file_name**(문서 메타데이터에서 추출된 파일 이름)
    - **language**(문서가 작성된 언어)
    - **sentiment** 문서에 대해 계산된 감정 점수.
    - **url** Azure Storage의 문서 Blob에 대한 URL.
3. 인덱싱 프로세스에서 만든 다른 테이블을 표시합니다.
    - **ImageTags**(태그가 표시되는 문서의 **document_id** 를 포함하는 개별 이미지 태그에 대한 행 포함).
    - **KeyPhrases**(문구가 표시되는 문서의 **document_id** 를 포함하는 개별 핵심 문구에 대한 행 포함).
    - **Locations**(위치가 표시되는 문서의 **document_id** 를 포함하는 개별 위치에 대한 행 포함).

*테이블* 프로젝션을 만드는 기능을 활용하면 Microsoft Power BI 등을 사용하여 관계형 스키마를 쿼리하는 분석 및 보고 솔루션을 빌드할 수 있습니다. 자동으로 생성된 키 열을 사용하여 쿼리에서 테이블을 조인할 수 있습니다. 예를 들어 특정 문서에서 언급된 모든 위치를 반환할 수 있습니다.

## <a name="more-information"></a>추가 정보

Azure Cognitive Search를 사용하여 지식 저장소를 만드는 방법에 대한 자세한 내용은 [Azure Cognitive Search 설명서](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro)를 참조하세요.
