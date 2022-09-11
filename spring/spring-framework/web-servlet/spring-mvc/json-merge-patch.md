# (Spring) JSON Merge Patch를 이용한 PATCH API 구현

## `PUT` 의 문제점과 `PATCH` 의 필요성

예를 들어 연락처를 관리하는 API를 만들고 있다고 가정해 보겠습니다. 서버에는 다음과 같은 JSON 문서로 표현될 수 있는 리소스가 있습니다.

```json
{
  "id": 1,
  "name": "다용도 브러쉬",
	"brand": "3M",
  "price": 8000,
  "category": {
    "id": 35,
  }
}
```

John이 선임 엔지니어로 승진했고 연락처 목록을 최신 상태로 유지하려고 한다고 가정해 보겠습니다. 아래와 같이 `PUT` 요청을 사용하여 이 리소스를 수정할 수 있습니다.

```json
{
  "id": 1,
  "name": "John Appleseed",
  "work": {
    "title": "Senior Engineer",
    "company": "Acme"
  },
  "phones": [
    {
      "phone": "0000000000",
      "type": "mobile"
    }
  ]
}

```

그러나 `PUT`을 사용하면 리소스의 단일 필드를 수정해야 하는 경우에도 리소스의 전체 표현을 보내야 하므로 일부 상황에서는 바람직하지 않을 수 있습니다.

현재 HTTP/1.1 프로토콜을 정의하는 문서 중 하나인 [RFC 7231](https://tools.ietf.org/html/rfc7231)에서 `PUT` HTTP 메소드가 어떻게 정의되어 있는지 살펴보겠습니다.

### PUT Method

`PUT` 메서드는 타겟 리소스의 상태가 요청 메시지의 페이로드에 포함된 표현(representation)에 의해 정의된 상태로 생성되거나 교체되도록 요청합니다. 따라서 정의에 따라 `PUT` 메서드는 다음 용도로 사용됩니다.

- 리소스 생성(Creating resources)
- 현재 리소스의 상태를 주어진 리소스로 대치(replace)하기

여기서 핵심은 `PUT` 페이로드가 리소스의 새로운 표현(representation)이어야 한다는 것입니다. 따라서 리소스를 부분적으로 수정하기 위한 것은 아닙니다. 이 격차를 채우기 위해 `PATCH` 메서드가 만들어졌으며 현재 [RFC 5789](https://tools.ietf.org/html/rfc5789)에 정의되어 있습니다.

### PATCH Method

`PATCH` 메소드는 요청 엔터티에 설명된 변경 사항들(a set of changes)이 요청 URI로 식별되는 리소스에 적용되도록 요청합니다. PATCH 요청의 페이로드에는 현재 서버에 저장된 리소스를 수정하여 새 버전을 생성하는 방법을 설명하는 일련의 지침이 포함되어 있습니다. 이에 대해 자세히 살펴보겠습니다.

## `PATCH` 형식을 처리하기 위한 방법

`PATCH` 메서드의 정의에는 요청 페이로드에 리소스가 어떻게 수정되는 지를 설명하는 지침들이 포함되어야 하고 해당 지침들의 집합이 `Content-Type`으로 식별된다는 언급 외에(apart from) 요청 페이로드의 형식을 규정(enforce)하지는 않습니다.

리소스가 어떻게 `PATCH` 되는 지를 설명하는 두 가지 형식을 살펴보겠습니다.

### JSON Patch

JSON Patch는 JSON 문서에 적용될 일련의 연산을 표현하는 형식입니다. [RFC 6902](https://tools.ietf.org/html/rfc6902)에 정의되어 있으며 `application/json-patch+json` 미디어 타입으로 식별됩니다.

JSON Patch 문서는 객체의 배열을 나타내며 각 객체는 타겟 JSON 문서에 적용할 단일 작업을 나타냅니다.

```
PATCH /contacts/1 HTTP/1.1
Host: example.org
Content-Type: application/json-patch+json

[
		{ "op": "test", "path": "/a/b/c", "value": "foo" },
    { "op": "remove", "path": "/a/b/c" },
    { "op": "add", "path": "/a/b/c", "value": [ "foo", "bar" ] },
    { "op": "replace", "path": "/a/b/c", "value": 42 },
    { "op": "move", "from": "/a/b/c", "path": "/a/b/d" },
    { "op": "copy", "from": "/a/b/d", "path": "/a/b/e" }
]
```

각 작업 객체에는 값이 정확히 하나인 `op` 멤버가 있어야 합니다. 이 `op` 멤버의 값은 아래 표와 같이 수행할 작업을 나타냅니다.

| Operation | Description |
| --- | --- |
| add | 타겟 위치에 값을 추가합니다. 지정된 위치에 값이 있으면 대체됩니다. |
| remove | 타겟 위치에 있는 값을 제거합니다. |
| replace | 타겟 위치에 있는 값을 대체합니다. |
| move | 지정된 위치의 값을 제거하고 타겟 위치에 추가합니다. |
| copy | 지정된 위치의 값을 타겟 위치로 복사합니다. |
| test | 타겟 위치의 값이 지정된 값과 같은지 테스트합니다. |

예를 들어 John의 직업명을 바꾸는 요청은 다음과 같습니다.

```
PATCH /contacts/1 HTTP/1.1
Host: example.org
Content-Type: application/json-patch+json

[
  { "op": "replace", "path": "/work/title", "value": "Senior Engineer" }
]

```

### JSON Merge Patch

JSON Merge Patch는 최종적으로 수정된 문서와 거의 유사한 문법(syntax)을 사용하여 타겟 JSON 문서에 대한 변경 사항을 설명하는 형식입니다. [RFC 7396](https://tools.ietf.org/html/rfc7396)에 정의되어 있으며 `application/merge-patch+json` 미디어 타입으로 식별됩니다.

JSON Merge Patch 문서를 처리하는 서버는 제공된 패치의 내용을 타겟 문서의 현재 내용과 비교하여 변경해야할 것들을 결정합니다.

- 머지 패치가 타겟 문서에 포함되지 않은 멤버를 포함하고 있다면, 해당 멤버는 추가(add)됩니다.
- 타겟 문서에 멤버가 포함되어 있으면 값은 대체됩니다.
- 머치 패치의 null 값은 타겟 문서의 기존 값이 제거됨을 나타냅니다.
- 타겟 문서의 다른 값들은 변경되지 않은 상태로 유지됩니다.

```json
// 변경 전 기존데이터
{
    "name": "john",
    "age": "29",
    "home": {
        "address": "seoul",
        "tel": "02-3333-4444"
    }
}

```

```json
// PATCH 메소드를 이용한 변경요청
{
    "age": "30",
    "home": {
        "tel": null
    }
}

```

```json
// 변경 후 데이터
{
	"name": "john",
    "age": "30",
    "home": {
        "address": "seoul"
    }
}

```

## Spring에서 J**SON Merge Patch 구현하기**

### JSON-P: JSON 처리를 위한 Java API

JSON-P 1.0는 JSR 353에 정의되어 있으며, Java EE에서의 JSON 처리를 공식적으로 지원합니다. 또한 JSR 374에 정의된 JSON-P 1.1은 JSON Patch 및 JSON Merge Patch 형식에 대한 지원을 Java EE에 도입했습니다. JSON-P는 단지 인터페이스 집합의 API입니다. 따라서 이 API를 활용한 작업을 하려면 [Apache Johnzon](https://johnzon.apache.org/) 와 같은 구현체가 필요합니다.

`build.gradle`

```yaml
implementation 'org.apache.johnzon:johnzon-core:${johnzon.version}'
```

JSON-P에 있는 인터페이스들은 다음과 같습니다.

| Type | Description |
| --- | --- |
| Json | JSON을 처리하는 객체들을 생성하기 위한 팩토리 클래스. |
| JsonPatch | RFC 6902에 정의된 JSON Patch의 구현을 나타냅니다. |
| JsonMergePatch | RFC 7396에 정의된 JSON Merge Patch의 구현을 나타냅니다. |
| JsonValue | object, array, number, string, true, false 또는 null일 수 있는 불변인 JSON 값을 나타냅니다. |
| JsonStructure | JSON의 두 가지 구조화된 유형(object 와 array)에 대한 슈퍼 타입. |

### 컨트롤러 메소드 작성

다음은 JSON Merge Patch 형식으로 상품 리소스의 수정을 요청하는 HTTP 요청 메세지입니다.

```
PATCH /products/3 HTTP/1.1
Content-Type: application/merge-patch+json
Content-Length: 46

{
  "name" : "modify name",
  "price" : 99999
}
```

이 요청을 처리할 수 있는 컨트롤러 메소드를 구현해 봅니다.

```java
@PatchMapping(path = "/products/{productId}", consumes = "application/merge-patch+json")
@ResponseStatus(HttpStatus.OK)
public void patchProduct(
        @PathVariable Long productId,
        @RequestBody JsonMergePatch patchDocument
) {
    productService.patchProduct(productId, patchDocument);
}
```

- `consumes = "application/merge-patch+json"` : 미디어 타입을 `application/merge-patch+json` 으로 설정하여 JSON Merge Patch를 식별합니다.
- `JsonMergePatch` : 위에서 설명한 JSON-P의 인터페이스 중의 하나입니다. RFC 7396에 정의된 JSON Merge Patch의 구현을 나타냅니다.

> 스프링 MVC는 미디어 타입이 `application/json` 또는 `application/*+json` 인 HTTP 요청의 본문을 JSON 문서로 인식합니다([MappingJackson2HttpMessageConverter](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/converter/json/MappingJackson2HttpMessageConverter.html) 참조). 따라서 추가적으로 `application/merge-patch+json` 미디어 타입을 처리하기 위해 `HttpMessageConverter<T>`를 구현할 필요는 없습니다.
> 

### Patch 적용

다음으로 컨트롤러에서 요청받은 Patch 문서를 타겟 리소스에 패치해야 합니다. JSON 패치와 JSON Merge Patch 모두 JSON 문서에서 작동합니다. 따라서 자바 빈(리소스)에 패치를 적용하려면, 먼저 자바 빈을 [JsonStructure](https://javaee.github.io/javaee-spec/javadocs/javax/json/JsonStructure.html) 혹은 [JsonValue](https://javaee.github.io/javaee-spec/javadocs/javax/json/JsonValue.html) 와 같은 JSON-P 유형으로 변환해야 합니다. 그런 다음 패치를 적용하고 패치된 문서를 자바 빈으로 다시 변환합니다.

이러한 변환(conversion)은 JSON-P 유형들과 함께 동작할 수 있는 Jackson의 [확장 모듈](https://github.com/FasterXML/jackson-datatype-jsr353)을 통해 처리할 수 있습니다. 이 확장 모듈을 사용하면, 일반적인 Jackson 프로세싱(데이터 바인딩 처리)처럼 JSON을 [JsonValue](https://javaee.github.io/javaee-spec/javadocs/javax/json/JsonValue.html)들로 읽고 `JsonValue`들을 JSON으로 쓸 수 있습니다.

`build.gradle`

```yaml
implementation 'com.fasterxml.jackson.datatype:jackson-datatype-jsr353:${jackson.version}'
```

추가한 확장 모듈을 [ObjectMapper](https://fasterxml.github.io/jackson-databind/javadoc/2.9/com/fasterxml/jackson/databind/ObjectMapper.html)에 등록합니다.

```java
@Bean
public ObjectMapper objectMapper() {
    ObjectMapper objectMapper = new ObjectMapper();
    objectMapper.registerModule(new JavaTimeModule());
    objectMapper.registerModule(new JSR353Module()); //jsr-353,jsr-374 module
    return objectMapper;
}
```

[ObjectMapper](https://fasterxml.github.io/jackson-databind/javadoc/2.9/com/fasterxml/jackson/databind/ObjectMapper.html)에 대한 구성을 마쳤으니, 이를 주입받아 JSON Merge Patch 기능을 수행하는 메소드를 작성할 수 있습니다.

```java
public <T> T mergePatch(JsonMergePatch mergePatch, T targetBean, Class<T> beanClass) {

    // Convert the Java bean to a JSON document
    JsonValue target = mapper.convertValue(targetBean, JsonValue.class);

    // Apply the JSON Merge Patch to the JSON document
    JsonValue patched = mergePatch.apply(target);

    // Convert the JSON document to a Java bean and return it
    return mapper.convertValue(patched, beanClass);
}
```

작성한 메소드를 여러 모듈에서 활용할 수 있도록 스프링 컨테이너에 등록합니다.

```java
@Component
public class JsonMergePatchMapper<T> {

    private ObjectMapper objectMapper;

    public JsonMergePatchMapper(ObjectMapper objectMapper) {
        this.objectMapper = objectMapper;
    }

    public T apply(JsonMergePatch patch, T targetBean) {
        JsonStructure target = objectMapper.convertValue(targetBean, JsonStructure.class);

        JsonValue patched = patch.apply(target);

        return objectMapper.convertValue(patched, (Class<T>)targetBean.getClass());
    }
}
```

### 서비스 작성

이제 위에서 작성한 `JsonMergePatchMapper` 와 컨트롤러에서 전달 받은 아규먼트를 활용하여 패치를 수행하는 서비스를 작성할 수 있습니다.

```java
@RequiredArgsConstructor
@Service
public class ProductService {
		private final JsonMergePatchMapper<Product> mergePatchMapper;
		private final ProductRepository productRepository;

		@Transactional
		public Product patchProduct(Long productId, JsonMergePatch patchDocument) {
		    Product targetResource = findProduct(productId);
		    Product patchedProduct = mergePatchMapper.apply(patchDocument, targetResource);
		    productRepository.save(patchedProduct);
		    return patchedProduct;
		}
}
```

### 유효성 검사

PATCH API의 경우, 리소스에서 변경하고 싶은 필드만 선택적으로 PATCH 문서에 추가하여 변경을 요청할 수 있습니다. 따라서 `@NotNull` 과 같은 어노테이션을 이용한 Bean Validation 검증은 PATCH 문서와 변경하려는 리소스가 병합된 이후에 하는 것이 좋습니다.

`javax.validation.Validator` 를 주입받아 Bean validation을 검증하는 메소드를 작성합니다.

```java
private void validatePatchedProduct(Product patchedProduct) {
    ProductValidationBean validationBean = productMapper.toValidationBean(patchedProduct);
    Set<ConstraintViolation<ProductDto>> violations = validator.validate(productDto);
    if (!violations.isEmpty()) {
        throw new IllegalArgumentException();
    }
}
```

- `productMapper.toProductValidationBean(patchedProduct)` : 패치된 객체(`patchedProduct`)를 검증 객체에 매핑하기 위해 [MapStruct](https://mapstruct.org/)를 사용하였습니다. JPA 엔터티(`Product`)에 Bean Validation 어노테이션을 사용하지 않기 위해 검증을 위한 객체를 따로 만들었습니다.

MapStruct를 이용한 매퍼 인터페이스

```java
@Mapper(componentModel = "spring")
public interface ProductMapper {
    ProductMapper INSTANCE = Mappers.getMapper(ProductMapper.class);

    ProductValidationBean toValidationBean(Product product);
}
```

Bean Validation 어노테이션을 사용한 검증 객체

```java
public class ProductValidationBean {

    @NotBlank
    private String name;
    
    ...
}
```

이제 작성한 유효성 검사 메소드를 서비스 메소드에 추가합니다.

```java
@Transactional
public Product patchProduct(Long productId, JsonMergePatch patchDocument) {
    Product product = findProduct(productId);
    Product patchedProduct = jsonMergePatchMapper.apply(patchDocument, product);
		validatePatchedProduct(patchedProduct);
    productRepository.save(patchedProduct);
    return patchedProduct;
}
```

### 작성한 PATCH API의 문제점

위와 같은 방식으로 Json Merge Patch를 구현할 때, JPA를 이용하는 경우, 엔터티의 어떤 필드도 변경이 가능합니다. 따라서 외부의 요인에 의해서 절대 변경되어선 안되는 데이터가 수정될 수 있습니다.

이 문제를 해결하기 위해서는 다음과 같이 Patch 문서의 key에 접근하여 검증을 수행할 필요가 있습니다.

```java
@PatchMapping(path = "/{productId}", consumes = "application/merge-patch+json")
@ResponseStatus(HttpStatus.OK)
public void patchProduct(
        @PathVariable Long productId,
        @RequestBody JsonMergePatch patchDocument
) {
		JsonValue jsonValue = patchDocument.toJsonValue();
		jsonValue.asJsonObject().keySet().stream()
		        .forEach(key -> {
		            // key에 대한 검증
		        });
    productService.patchProduct(productId, patchDocument);
}
```

또 다른 방법으로, 요청 받을 수 있는 필드로 구성된 DTO를 선언하여 요청 값을 전달 받은 뒤, 컨트롤러 내부에서 `JsonMergePatch` 객체로 변환합니다.

```java
@PatchMapping(path = "/{productId}")
@ResponseStatus(HttpStatus.OK)
public void patchProduct(
        @PathVariable Long productId,
        @RequestBody ProductPatchRequest productPatchRequest
) {
    JsonValue jsonValue = objectMapper.convertValue(sellerProductPatchValidationBean, JsonValue.class);
    JsonMergePatch patchDocument = Json.createMergePatch(jsonValue);
    Product patchedProduct = mergeMapper.apply(patchDocument, product);

    sellerProductService.updatePatchedProduct(patchedProduct);
}
```

다만 이 경우에는 리소스의 필드를 삭제할 목적으로 해당 필드에 `null` 값을 할당하여 요청하더라도, 컨트롤러 내부에서 `JsonMergePatch` 객체를 생성하는 과정에서 `null` 값이 있는 필드는 생성되지 않고 누락됩니다. 따라서 해당 필드가 삭제되지 않습니다. 이는 Json Merge Patch를 완벽히 구현했다고 볼 수는 없으며, REST API에서 Uniform Interface 조건의 Self descriptive를 위배합니다.

---

**참조**

[https://cassiomolin.com/2019/06/10/using-http-patch-in-spring/](https://cassiomolin.com/2019/06/10/using-http-patch-in-spring/)
