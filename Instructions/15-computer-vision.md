---
lab:
  title: Computer Vision을 사용하여 이미지 분석
  module: Module 8 - Getting Started with Computer Vision
ms.openlocfilehash: f2ee18ff682d53e9fd554749ed2b9cbaa9b03611
ms.sourcegitcommit: 7191e53bc33cda92e710d957dde4478ee2496660
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2022
ms.locfileid: "147041669"
---
# <a name="analyze-images-with-computer-vision"></a>Computer Vision을 사용하여 이미지 분석

Computer Vision은 소프트웨어 시스템이 이미지를 분석하여 시각적 입력을 해석하는 데 사용할 수 있는 인공 지능 기능입니다. Microsoft Azure에서 **Computer Vision** Cognitive Service는 흔히 수행하는 Computer Vision 작업용으로 미리 작성된 모델을 제공합니다. 이러한 작업으로는 이미지를 분석하여 캡션과 태그 추천, 일반적인 개체, 주요 건물, 유명인, 브랜드 감지, 성인 콘텐츠 유무 확인 등이 있습니다. Computer Vision 서비스를 사용하여 이미지 색 및 형식을 분석한 다음 "스마트 자르기" 썸네일 이미지를 생성할 수도 있습니다.

## <a name="clone-the-repository-for-this-course"></a>이 과정용 리포지토리 복제

이 랩에서 작업을 수행 중인 환경에 **AI-102-AIEngineer** 코드 리포지토리를 아직 복제하지 않았다면 다음 단계에 따라 리포지토리를 지금 복제합니다. 리포지토리를 복제한 경우에는 Visual Studio Code에서 복제한 폴더를 엽니다.

1. Visual Studio Code를 시작합니다.
2. 팔레트를 열고(Shift+Ctrl+P) **Git: Clone** 명령을 실행하여 `https://github.com/MicrosoftLearning/AI-102-AIEngineer` 리포지토리를 로컬 폴더(아무 폴더나 관계없음)에 복제합니다.
3. 리포지토리가 복제되면 Visual Studio Code에서 폴더를 엽니다.
4. 리포지토리의 C# 코드 프로젝트를 지원하는 추가 파일이 설치되는 동안 기다립니다.

    > **참고**: 빌드 및 디버그에 필요한 자산을 추가하라는 메시지가 표시되면 **나중에** 를 선택합니다.

## <a name="provision-a-cognitive-services-resource"></a>Cognitive Services 리소스 프로비전

구독에 아직 없는 경우 **Cognitive Services** 리소스를 프로비전해야 합니다.

1. `https://portal.azure.com`의 Azure Portal을 열고 Azure 구독과 연관된 Microsoft 계정을 사용하여 로그인합니다.
2. **&#65291;리소스 만들기** 단추를 선택하고 *cognitive services* 를 검색한 후에 다음 설정을 사용하여 **Cognitive Services** 리소스를 만듭니다.
    - **구독**: ‘Azure 구독’
    - **리소스 그룹**: 리소스 그룹 선택 또는 만들기(제한된 구독을 사용 중이라면 새 리소스 그룹을 만들 권한이 없을 수도 있으므로 제공된 리소스 그룹 사용)
    - **지역**: 사용 가능한 지역을 선택합니다.
    - **이름**: *고유 이름 입력*
    - **가격 책정 계층**: 표준 S0
3. 필요한 확인란을 선택하고 리소스를 만듭니다.
4. 배포가 완료될 때까지 기다린 다음, 배포 세부 정보를 봅니다.
5. 리소스가 배포되면 해당 리소스로 이동하여 **키 및 엔드포인트** 페이지를 확인합니다. 다음 절차에서 이 페이지에 표시되는 키 중 하나와 엔드포인트가 필요합니다.

## <a name="prepare-to-use-the-computer-vision-sdk"></a>Computer Vision SDK 사용 준비

이 연습에서는 Computer Vision SDK를 사용해 이미지를 분석하는 부분 구현 클라이언트 애플리케이션을 완성합니다.

> **참고**: **C#** 또는 **Python** 용 SDK 사용을 선택할 수 있습니다. 아래 단계에서 선호하는 언어에 적합한 작업을 수행하세요.

1. Visual Studio Code의 **탐색기** 창에서 **15-computer-vision** 폴더를 찾은 다음 언어 기본 설정에 따라 **C-Sharp** 또는 **Python** 폴더를 확장합니다.
2. **image-analysis** 폴더를 마우스 오른쪽 단추로 클릭하고 통합 터미널을 엽니다. 그런 다음 언어 기본 설정에 적합한 명령을 실행하여 Computer Vision SDK 패키지를 설치합니다.

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-computervision==0.7.0
```
    
3. **image-analysis** 폴더의 내용을 표시하여 구성 설정용 파일이 포함되어 있음을 확인합니다.
    - **C#** : appsettings.json
    - **Python**: .env

    구성 파일을 열고 Cognitive Service 리소스용 **엔드포인트** 및 인증 **키** 를 반영하여 해당 파일에 포함된 구성 값을 업데이트합니다. 변경 내용을 저장합니다.
4. **image-analysis** 폴더에는 클라이언트 애플리케이션용 코드 파일이 포함되어 있습니다.

    - **C#** : Program.cs
    - **Python**: image-analysis.py

    코드 파일을 열고 파일 맨 윗부분의 기존 네임스페이스 참조 아래에 있는 **네임스페이스 가져오기** 주석을 찾습니다. 그런 다음 이 주석 아래에 다음 언어별 코드를 추가하여 Computer Vision SDK를 사용하는 데 필요한 네임스페이스를 가져옵니다.

**C#**

```C#
// import namespaces
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
```

**Python**

```Python
# import namespaces
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials
```
    
## <a name="view-the-images-you-will-analyze"></a>분석할 이미지 확인

이 연습에서는 Computer Vision 서비스를 사용해 여러 이미지를 분석합니다.

1. Visual Studio Code에서 **image-analysis** 폴더와 이 폴더에 포함된 **images** 폴더를 차례로 확장합니다.
2. 각 이미지 파일을 차례로 선택하여 Visual Studio Code에 표시합니다.

## <a name="analyze-an-image-to-suggest-a-caption"></a>이미지를 분석하여 캡션 추천

이제 SDK를 사용해 Computer Vision 서비스를 호출하고 이미지를 분석할 준비가 되었습니다.

1. 클라이언트 애플리케이션용 코드 파일(**Program.cs** 또는 **image-analysis.py**)의 **Main** 함수에서 구성 설정 로드를 위한 코드가 제공되어 있음을 확인합니다. 그런 다음 **Computer Vision 클라이언트 인증** 주석을 찾습니다. 그 후에 이 주석 아래에 다음 언어별 코드를 추가하여 Computer Vision 클라이언트 개체를 만들고 인증합니다.

**C#**

```C#
// Authenticate Computer Vision client
ApiKeyServiceClientCredentials credentials = new ApiKeyServiceClientCredentials(cogSvcKey);
cvClient = new ComputerVisionClient(credentials)
{
    Endpoint = cogSvcEndpoint
};
```

**Python**

```Python
# Authenticate Computer Vision client
credential = CognitiveServicesCredentials(cog_key) 
cv_client = ComputerVisionClient(cog_endpoint, credential)
```

2. **Main** 함수의 방금 추가한 코드 아래에 있는 코드가 이미지 파일 경로를 지정한 다음 다른 2개 함수(**AnalyzeImage** 및 **GetThumbnail**)에 이미지 경로를 전달함을 확인합니다. 이러한 함수는 아직 완전히 구현되지 않았습니다.

3. **AnalyzeImage** 함수의 **검색할 기능 지정** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C#
// Specify features to be retrieved
List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>()
{
    VisualFeatureTypes.Description,
    VisualFeatureTypes.Tags,
    VisualFeatureTypes.Categories,
    VisualFeatureTypes.Brands,
    VisualFeatureTypes.Objects,
    VisualFeatureTypes.Adult
};
```

**Python**

```Python
# Specify features to be retrieved
features = [VisualFeatureTypes.description,
            VisualFeatureTypes.tags,
            VisualFeatureTypes.categories,
            VisualFeatureTypes.brands,
            VisualFeatureTypes.objects,
            VisualFeatureTypes.adult]
```
    
4. **AnalyzeImage** 함수의 **이미지 분석 가져오기** 주석 아래에 다음 코드(나중에 코드를 더 추가할 위치를 나타내는 주석 포함)를 추가합니다.

**C#**

```C
// Get image analysis
using (var imageData = File.OpenRead(imageFile))
{    
    var analysis = await cvClient.AnalyzeImageInStreamAsync(imageData, features);

    // get image captions
    foreach (var caption in analysis.Description.Captions)
    {
        Console.WriteLine($"Description: {caption.Text} (confidence: {caption.Confidence.ToString("P")})");
    }

    // Get image tags


    // Get image categories


    // Get brands in the image


    // Get objects in the image


    // Get moderation ratings
    

}            
```

**Python**

```Python
# Get image analysis
with open(image_file, mode="rb") as image_data:
    analysis = cv_client.analyze_image_in_stream(image_data , features)

# Get image description
for caption in analysis.description.captions:
    print("Description: '{}' (confidence: {:.2f}%)".format(caption.text, caption.confidence * 100))

# Get image tags


# Get image categories 


# Get brands in the image


# Get objects in the image


# Get moderation ratings

```
    
5. 변경 내용을 저장하고 **image-analysis** 폴더의 통합 터미널로 돌아옵니다. 그런 후에 다음 명령을 입력하여 **images/street.jpg** 인수를 사용해 프로그램을 실행합니다.

**C#**

```
dotnet run images/street.jpg
```

**Python**

```
python image-analysis.py images/street.jpg
```
    
6. 출력을 살펴봅니다. **street.jpg** 이미지에 대해 제한된 캡션이 포함되어 있습니다.
7. 이번에는 **images/building.jpg** 인수를 사용하여 프로그램을 다시 실행해 **building.jpg** 이미지에 대해 생성되는 캡션을 확인합니다.
8. 이전 단계를 반복하여 **images/person.jpg** 파일에 대한 캡션을 생성합니다.

## <a name="get-suggested-tags-for-an-image"></a>이미지의 추천 캡션 가져오기

이미지 내용에 대한 단서를 제공하는 관련 *태그* 를 식별하면 유용한 경우가 있습니다.

1. **AnalyzeImage** 함수의 **이미지 태그 가져오기** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get image tags
if (analysis.Tags.Count > 0)
{
    Console.WriteLine("Tags:");
    foreach (var tag in analysis.Tags)
    {
        Console.WriteLine($" -{tag.Name} (confidence: {tag.Confidence.ToString("P")})");
    }
}
```

**Python**

```Python
# Get image tags
if (len(analysis.tags) > 0):
    print("Tags: ")
    for tag in analysis.tags:
        print(" -'{}' (confidence: {:.2f}%)".format(tag.name, tag.confidence * 100))
```

2. 변경 내용을 저장한 다음 **images** 이미지 폴더의 각 이미지 파일별로 프로그램을 한 번씩 실행합니다. 프로그램을 실행할 때마다 이미지 캡션 외에 추천 태그 목록도 표시됨을 확인합니다.

## <a name="get-image-categories"></a>이미지 범주 가져오기

Computer Vision 서비스는 이미지 범주를 추천할 수 있으며 각 범주 내에서 잘 알려진 주요 건물을 식별할 수 있습니다.

1. **AnalyzeImage** 함수의 **이미지 범주 가져오기** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get image categories
List<LandmarksModel> landmarks = new List<LandmarksModel> {};
Console.WriteLine("Categories:");
foreach (var category in analysis.Categories)
{
    // Print the category
    Console.WriteLine($" -{category.Name} (confidence: {category.Score.ToString("P")})");

    // Get landmarks in this category
    if (category.Detail?.Landmarks != null)
    {
        foreach (LandmarksModel landmark in category.Detail.Landmarks)
        {
            if (!landmarks.Any(item => item.Name == landmark.Name))
            {
                landmarks.Add(landmark);
            }
        }
    }
}

// If there were landmarks, list them
if (landmarks.Count > 0)
{
    Console.WriteLine("Landmarks:");
    foreach(LandmarksModel landmark in landmarks)
    {
        Console.WriteLine($" -{landmark.Name} (confidence: {landmark.Confidence.ToString("P")})");
    }
}

```

**Python**

```Python
# Get image categories
if (len(analysis.categories) > 0):
    print("Categories:")
    landmarks = []
    for category in analysis.categories:
        # Print the category
        print(" -'{}' (confidence: {:.2f}%)".format(category.name, category.score * 100))
        if category.detail:
            # Get landmarks in this category
            if category.detail.landmarks:
                for landmark in category.detail.landmarks:
                    if landmark not in landmarks:
                        landmarks.append(landmark)

    # If there were landmarks, list them
    if len(landmarks) > 0:
        print("Landmarks:")
        for landmark in landmarks:
            print(" -'{}' (confidence: {:.2f}%)".format(landmark.name, landmark.confidence * 100))

```
    
2. 변경 내용을 저장한 다음 **images** 폴더의 각 이미지 파일별로 프로그램을 한 번씩 실행합니다. 프로그램을 실행할 때마다 이미지 캡션과 태그 외에 추천 범주 목록, 그리고 인식된 주요 건물도 표시됨을 확인합니다(특히 **building.jpg** 이미지의 경우).

## <a name="get-brands-in-an-image"></a>이미지에서 브랜드 가져오기

브랜드 이름이 표시되어 있지 않아도 로고를 통해 시각적으로 인식 가능한 브랜드도 있습니다. Computer Vision 서비스는 잘 알려진 수천 가지 브랜드를 식별하도록 학습이 완료된 상태입니다.

1. **AnalyzeImage** 함수의 **이미지에서 브랜드 가져오기** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get brands in the image
if (analysis.Brands.Count > 0)
{
    Console.WriteLine("Brands:");
    foreach (var brand in analysis.Brands)
    {
        Console.WriteLine($" -{brand.Name} (confidence: {brand.Confidence.ToString("P")})");
    }
}
```

**Python**

```Python
# Get brands in the image
if (len(analysis.brands) > 0):
    print("Brands: ")
    for brand in analysis.brands:
        print(" -'{}' (confidence: {:.2f}%)".format(brand.name, brand.confidence * 100))
```
    
2. 변경 내용을 저장한 다음 **images** 이미지 폴더의 각 이미지 파일별로 프로그램을 한 번씩 실행합니다. 프로그램을 실행할 때마다 식별된 브랜드를 살펴봅니다(특히 **person.jpg** 이미지의 경우).

## <a name="detect-and-locate-objects-in-an-image"></a>이미지에서 개체 감지 및 찾기

*개체 감지* 는 이미지 내의 개별 개체를 식별하고 해당 위치를 경계 상자로 표시하는 특정 형식의 Computer Vision 기능입니다.

1. **AnalyzeImage** 함수의 **이미지에서 개체 가져오기** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get objects in the image
if (analysis.Objects.Count > 0)
{
    Console.WriteLine("Objects in image:");

    // Prepare image for drawing
    Image image = Image.FromFile(imageFile);
    Graphics graphics = Graphics.FromImage(image);
    Pen pen = new Pen(Color.Cyan, 3);
    Font font = new Font("Arial", 16);
    SolidBrush brush = new SolidBrush(Color.Black);

    foreach (var detectedObject in analysis.Objects)
    {
        // Print object name
        Console.WriteLine($" -{detectedObject.ObjectProperty} (confidence: {detectedObject.Confidence.ToString("P")})");

        // Draw object bounding box
        var r = detectedObject.Rectangle;
        Rectangle rect = new Rectangle(r.X, r.Y, r.W, r.H);
        graphics.DrawRectangle(pen, rect);
        graphics.DrawString(detectedObject.ObjectProperty,font,brush,r.X, r.Y);

    }
    // Save annotated image
    String output_file = "objects.jpg";
    image.Save(output_file);
    Console.WriteLine("  Results saved in " + output_file);   
}
```

**Python**

```Python
# Get objects in the image
if len(analysis.objects) > 0:
    print("Objects in image:")

    # Prepare image for drawing
    fig = plt.figure(figsize=(8, 8))
    plt.axis('off')
    image = Image.open(image_file)
    draw = ImageDraw.Draw(image)
    color = 'cyan'
    for detected_object in analysis.objects:
        # Print object name
        print(" -{} (confidence: {:.2f}%)".format(detected_object.object_property, detected_object.confidence * 100))
        
        # Draw object bounding box
        r = detected_object.rectangle
        bounding_box = ((r.x, r.y), (r.x + r.w, r.y + r.h))
        draw.rectangle(bounding_box, outline=color, width=3)
        plt.annotate(detected_object.object_property,(r.x, r.y), backgroundcolor=color)
    # Save annotated image
    plt.imshow(image)
    outputfile = 'objects.jpg'
    fig.savefig(outputfile)
    print('  Results saved in', outputfile)
```
    
2. 변경 내용을 저장한 다음 **images** 이미지 폴더의 각 이미지 파일별로 프로그램을 한 번씩 실행합니다. 프로그램을 실행할 때마다 감지된 개체를 살펴봅니다. 각 실행 후에는 코드 파일과 같은 폴더에 생성된 **objects.jpg** 파일을 표시하여 주석이 추가된 개체를 확인합니다.

## <a name="get-moderation-ratings-for-an-image"></a>이미지의 조정 등급 가져오기

일부 대상 그룹에게는 적합하지 않은 이미지도 있으므로 조정을 적용하여 성인용 이미지나 폭력적인 이미지를 식별해야 할 수도 있습니다.

1. **AnalyzeImage** 함수의 **조정 등급 가져오기** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Get moderation ratings
string ratings = $"Ratings:\n -Adult: {analysis.Adult.IsAdultContent}\n -Racy: {analysis.Adult.IsRacyContent}\n -Gore: {analysis.Adult.IsGoryContent}";
Console.WriteLine(ratings);
```

**Python**

```Python
# Get moderation ratings
ratings = 'Ratings:\n -Adult: {}\n -Racy: {}\n -Gore: {}'.format(analysis.adult.is_adult_content,
                                                                    analysis.adult.is_racy_content,
                                                                    analysis.adult.is_gory_content)
print(ratings)
```
    
2. 변경 내용을 저장한 다음 **images** 이미지 폴더의 각 이미지 파일별로 프로그램을 한 번씩 실행합니다. 프로그램을 실행할 때마다 각 이미지의 등급을 살펴봅니다.

> **참고**: 이전 작업에서는 메서드 하나를 사용해 이미지를 분석한 다음 코드를 계속 추가하여 결과를 구문 분석하고 표시했습니다. SDK에서는 캡션 추천, 태그 식별, 개체 감지 등에 사용할 수 있는 개별 메서드도 제공하므로 가장 적절한 메서드를 사용하여 필요한 정보만 반환할 수 있습니다. 그러면 반환해야 하는 데이터 페이로드 크기가 줄어듭니다. 자세한 내용은 [.NET SDK 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) 또는 [Python SDK 설명서](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/computervision?view=azure-python)를 참조하세요.

## <a name="generate-a-thumbnail-image"></a>썸네일 이미지 생성

기본적으로 표시하려는 주체가 새 이미지 치수 내에 포함되도록 이미지를 잘라서 작은 이미지 버전인 *썸네일* 을 만들어야 하는 경우도 있습니다.

1. 코드 파일에서 **GetThumbnail** 함수를 찾은 다음 **썸네일 생성** 주석 아래에 다음 코드를 추가합니다.

**C#**

```C
// Generate a thumbnail
using (var imageData = File.OpenRead(imageFile))
{
    // Get thumbnail data
    var thumbnailStream = await cvClient.GenerateThumbnailInStreamAsync(100, 100,imageData, true);

    // Save thumbnail image
    string thumbnailFileName = "thumbnail.png";
    using (Stream thumbnailFile = File.Create(thumbnailFileName))
    {
        thumbnailStream.CopyTo(thumbnailFile);
    }

    Console.WriteLine($"Thumbnail saved in {thumbnailFileName}");
}
```

**Python**

```Python
# Generate a thumbnail
with open(image_file, mode="rb") as image_data:
    # Get thumbnail data
    thumbnail_stream = cv_client.generate_thumbnail_in_stream(100, 100, image_data, True)

# Save thumbnail image
thumbnail_file_name = 'thumbnail.png'
with open(thumbnail_file_name, "wb") as thumbnail_file:
    for chunk in thumbnail_stream:
        thumbnail_file.write(chunk)

print('Thumbnail saved in.', thumbnail_file_name)
```
    
2. 변경 내용을 저장한 다음 **images** 이미지 폴더의 각 이미지 파일별로 프로그램을 한 번씩 실행합니다. 프로그램을 실행할 때마다 각 이미지에 대해 코드 파일과 같은 폴더에 생성되는 **thumbnail.jpg** 파일을 엽니다.

## <a name="more-information"></a>추가 정보

이 연습에서는 Computer Vision 서비스의 몇 가지 이미지 분석 및 조작 기능을 살펴보았습니다. 이 서비스에는 텍스트 읽기, 얼굴 감지 및 기타 Computer Vision 작업용 기능도 포함되어 있습니다.

**Computer Vision** 서비스를 사용하는 방법에 대한 자세한 내용은 [Computer Vision 설명서](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)를 참조하세요.
