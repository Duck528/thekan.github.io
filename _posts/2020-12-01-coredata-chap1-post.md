## CoreData 기본 구성요소 

대부분의 간단한 경우, Xcode에서 `코어데이터 사용` 체크박스를 선택한 후 자동으로 생성되는 코드를 이용해도 충분할 때가 많다. 하지만 내부적으로 어떻게 동작하는지 이해하지 않고 자동으로 만들어진 코드만을 사용한다면 `마이그레이션`, `멀티 스레드 상황` 등 조금 더 복잡한 케이스에선 대응하기 어려워 질 수 있다. 따라서 `CoreData`를 구성하는 핵심적인 역할을 하는 클래스를 짚고 넘어갈 필요가 있다. 

코어데이터에서 핵심적인 역할을 하는 클래스는 크게 4가지로 나눌 수 있으며, 각 클래스의 역할이 무엇인지 정리해보자.
1. NSManagedObjectModel
2. NSPersistentStore
3. NSPersistentStoreCoordinator
4. NSManagedObjectContext


### NSManagedObjectModel 

`NSManagedObjectModel`은 일반적으로 데이터베이스에서 사용되는 `스키마`와 비슷하게 생각될 수 있다. 이 `NSManagedObjectModel`에는 프로퍼티와 `NSManagedObjectModel` 사이의 관계가 정의되어 있다. 이렇게 기술하면 이 자체가 `스키마`와 동일하게 보일 수 있지만, 코어데이터가 어떤 데이터베이스를 사용하는지 여부에 따라 `NSManagedObjectModel`은 스키마가 될 수도 아닐수도 있다. 뒤에 다시 설명하도록 하겠지만 `CoreData`가 `SQLite`를 사용할 경우 `NSManagedObjectModel`은 `스키마`가 된다. 하지만, `CoreData`에서 `SQLite`는 `persistent store`의 여러 타입 중 하나이기 때문에 이를 `스키마`와 동일하게 보는건 옳지 않다. 

### NSPersistentStore

`NSPersistentStore`는 개발자가 사용하기로 결정한 저장매체에서 데이터를 `읽기/쓰기` 역할을 수행한다. 총 4개의 `NSPersistentStore`를 기본으로 제공한다.

#### NSSQLiteStoreType 

이름에서 암시하듯 `SQLite` 사용한다. 대부분의 iOS 프로젝트에서 사용되며 `Xcode`에서 `default`로 사용하는 타입이기도 하다. `코어데이터`가 지원하는 타입 중 유일한 `non-atomic store type` 이다


#### NSXMLStoreType (only available on OSX)

이름에서 암시하듯 `XML`을 이용한다. 가장 사람이 읽기 쉬운 저장매체이며 `atomic store type`이다. 


#### NSBinaryStoreType 

`binary data file`을 사용하며 `atomic store type`이다. 아마 실제 애플리케이션에서 사용될 일은 거의 없을 것이다.

#### NSInMemoryStoreType

이 `PersistentStoreType`은 `Persistent`하지 않다. 앱이 종료될 경우 저장된 내용은 모두 삭제된다. 주로 유닛테스트 용도로 사용된다.

#### CustomStoreType 

만약, `JSON` 또는 `CSV`를 사용하고 싶다면 `NSIncrementalStore`를 상속받아 직접 구현하면 된다. 아래 구현가이드를 참고
(https://developer.apple.com/library/archive/documentation/DataManagement/Conceptual/IncrementalStorePG/Introduction/Introduction.html

### NSPersistentStoreCoordinator 

`NSPersistentStoreCoordinator`는 `NSManagedObjectModel`과 `NSPersistentStore` 사이의 다리 역할을 한다. 개발자는 이 클래스로 인해 현재 사용하고 있는 `PersistentStoreType`이 `NSSQLiteStoreType`인지 혹은 `NSXMLStoreType`인지 정확히 알 필요 없이 그대로 사용가능하다. 즉, `NSManagedObjectModel`을 이해하고 이를 바탕으로 `NSPersistentStore`에 값을 가져오거나 저장하는 역할을 수행한다. 

### NSManagedObjectContext

