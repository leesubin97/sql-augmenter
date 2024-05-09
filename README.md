# SQLAugment 라이브러리

SQLAugment 라이브러리는 자연어 질의와 관련된 유사 질문, 쿼리, 템플릿을 추출하고 처리하는 라이브러리입니다.(SQLAugment.jar)

## 환경 설정

- **Java Version**: Java 17
- **빌드 도구**: Gradle 7.3

## Dependency Lib

- **Annoy-Java (Spotify)**: Annoy-Java는 Spotify에서 개발한 라이브러리로, 유사도 검색 작업에 사용됩니다. 이 프로젝트에서는 Annoy-Java의 JAR 파일을 직접 포함.

- **JSON-Simple**: JSON-Simple은 JSON 데이터를 파싱하고 생성하기 위한 라이브러리로 사용됩니다. 버전 1.1.1을 사용.

- **Jackson 라이브러리 (2.13.0)**: Jackson 라이브러리는 JSON 데이터와 Java 객체 간의 직렬화 및 역직렬화 작업처리 사용.


## 라이브러리 사용 예
### 1. SQLAugmenter_AnnoyBuilder 선행작업 필요
python annoy 인덱스 생성 필요

### 2. SQLAugmenter_config 파일 설정

SQLAugmenter 라이브러리를 사용하기 위해서는 `SQLAugmenter_config` 파일을 설정해야 합니다.
이 파일은 라이브러리가 사용될때 필요한 설정 정보를 담았습니다.
아래에 설정 파일의 예제와 그 내용에 대한 설명이 명시되어있습니다.

#### 예제 SQLAugmenter_config 파일

```properties
similar.query.dataset.path=src/main/resources/similar_query_dataset.json
vectorise.api.url=http://ip주소/vectorise
vectorise.dimension=768
annoy.ANGULAR.filePath=src/main/resources/points.angular.annoy
annoy.EUCLIDEAN.filePath=src/main/resources/points.euclidean.annoy
annoy.DOT.filePath=src/main/resources/points.dot.annoy
```
#### SQLAugmenter_config 파일 설정 내용 설명

- similar.query.dataset.path: 유사 쿼리 데이터셋 파일의 경로를 지정합니다. 
이 파일은 similar_query_dataset.json과 같이 JSON 형식으로 저장되어야 합니다.
```json
{
    "data": [
        {
            "idx": 0,
            "question": "상품 가격이 4만원 이상인 대리점 서비스 명과 대리점명을 알려줄래요?",
            "query": "SELECT A.agency_name, P.product_service_name FROM Product_Service P INNER JOIN Agency A ON P.agency_id = A.agency_id WHERE P.price >= 40000;",
            "query_temp": "SELECT alias_name2.column_name1, alias_name1.column_name2 FROM table_name1 alias_name1 INNER JOIN Agency alias_name2 ON alias_name1.column_name3 = alias_name2.column_name3 WHERE alias_name1.column_name4 >= value1;"
        }
}
```
- vectorise.api.url: 벡터화 API의 엔드포인트 URL을 지정합니다.
- vectorise.dimension: 벡터 차원을 설정합니다.
- annoy.ANGULAR.filePath: Angular 거리 메트릭을 사용하는 Annoy 인덱스 파일의 경로를 설정합니다.
- annoy.EUCLIDEAN.filePath: Euclidean 거리 메트릭을 사용하는 Annoy 인덱스 파일의 경로를 설정합니다.
- annoy.DOT.filePath: Dot product 거리 메트릭을 사용하는 Annoy 인덱스 파일의 경로를 설정합니다.

위의 예제 설정 파일을 참고하여 필요한 경로와 값을 설정한 후 src/main/resources/config 디렉토리에 
SQLAugmenter_config.ini 파일을 저장하세요. 

## Annoy-Java 인덱스 유형

Annoy-Java 세 가지 주요 인덱스 유형 제공.

- **ANGULAR 인덱스 (IndexType.ANGULAR)**: 인덱스는 벡터 간의 각도에 기반한 유사성을 측정.
- **EUCLIDEAN 인덱스 (IndexType.EUCLIDEAN)**: 유클리드 거리 측정 방법을 사용하여 벡터 간의 거리를 측정.
- **DOT 인덱스 (IndexType.DOT)**: 벡터 간의 내적 연산을 사용하여 유사성을 측정.

## 메소드 설명

### `public List<String> getSimilarQuestionList(String nl, int searchCnt, IndexType indexType)`

자연어 질의를 입력하면 유사한 질문을 추출합니다.

- `nl` (String): 자연어 질의입니다.
- `searchCnt` (int): 검색할 유사 질문의 갯수입니다.
- `indexType` (IndexType): 사용할 인덱스 타입입니다.
- 반환 값: 유사한 질문의 목록을 반환합니다.

### `public List<String> getSimilarQueryList(String nl, int searchCnt, IndexType indexType)`

자연어 질의를 입력하면 유사한 쿼리를 추출합니다.

- `nl` (String): 자연어 질의입니다.
- `searchCnt` (int): 검색할 유사 쿼리의 갯수입니다.
- `indexType` (IndexType): 사용할 인덱스 타입입니다.
- 반환 값: 유사한 쿼리의 목록을 반환합니다.

### `public List<String> getSimilarQueryTempleteList(String nl, int searchCnt, IndexType indexType)`

자연어 질의를 입력하면 유사한 템플릿을 추출합니다.

- `nl` (String): 자연어 질의입니다.
- `searchCnt` (int): 검색할 유사 템플릿의 갯수입니다.
- `indexType` (IndexType): 사용할 인덱스 타입입니다.
- 반환 값: 유사한 템플릿의 목록을 반환합니다.

### `public List<Map<String, Object>> getSimilarDatasetList(String nl, int searchCnt, IndexType indexType)`

자연어 질의를 입력하면 유사한 질문, 쿼리, 템플릿 정보를 추출합니다.

- `nl` (String): 자연어 질의입니다.
- `searchCnt` (int): 검색할 유사 정보의 갯수입니다.
- `indexType` (IndexType): 사용할 인덱스 타입입니다.
- 반환 값: 유사한 정보의 목록을 반환합니다. 각 항목은 다음과 같은 맵(Map) 형식으로 구성됩니다.
  - `"idx"`: 정보의 인덱스
  - `"question"`: 유사한 질문
  - `"query"`: 유사한 쿼리
  - `"query_temp"`: 유사한 템플릿


## 메소드 사용예시
```java
SQLAugment sqlAugment = new SQLAugment();
		
// 유사 질의 출력 
List<String> similarQuestionList = sqlAugment.getSimilarQuestionList("대리점 별로 상품 서비스를 알아야합니다.", 3, IndexType.DOT);
		
```


