---
layout: post
Title:  "Spring @ComponentScan의 다양한 설정들"
---

## @ComponentScan Annotaion
@Componentscan 어노테이션은 @Component어노테이션 및 streotype(@Service, @Repository, @Controller.)어노테이션이 부여된 Class들을 ==자동으로 Scan하여 Bean으로 등록해주는 역할==을 하는 어노테이션입니다.
ApplicationContext 설정 클래스에 @ComponentScan("패키지 경로")을 적어두면 해당 패키지에 있는 @Controller, @Service, @Repogitory, @Component 객체들이 DI 컨테이너에 등록됩니다.

아래의 예시를 통해 이해해보자.

```cpp
package com.package1.component;

@Component
class Component1{
   ...
   
   public Component1(){
       System.out.println("Component1 Constructor");
   }
   ...
   
}
```

```cpp
@Configuration
@ComponentScan("com.package1.component")
class AppContextConfig{
    ...
}
```
```cpp
...
ApplicationContext context = new AnnotationConfigApplicationContext(AppContextConfig.class);
...
```
```cpp
Component1 Constructor
```
위 코드를 보면 "com.package1.component" 패키지에 있는 Component1 클래스가 DI 컨테이너에 등록되어, 생성자가 호출된 것을 볼 수 있다. 이처럼 간단한 @ComponentScan 이지만, 프로젝트의 규모가 커지고 패키지가 많아지면, 단순히 패키지 경로만으론 컴포넌트들을 원하는대로 불러오기는 어려울 것이다.
- - -

## @ComponentScan의 다양한 설정
##### 1. basePackages
basePackages는 기본 설정 값이다. 해당 경로를 포함하는 하위 패키지를 모두 스캔한다.

```cpp
@Configuration
@ComponentScan("com.package1.component")
class AppContextConfig{
    ...
}
```
```cpp
@Configuration
@ComponentScan(basePackages = {"com.package1.component"})
class AppContextConfig{
    ...
}
```
- - -
##### 2. useDefaultFilters
useDefaultFilters는 기본값이 true이고 유효한 패키지 경로에서 @Controller, @Service, @Repogitory, @Component 등의 어노테이션이 사용된 객체를 DI 컨테이너에 등록한다.

```cpp
@Configuration
@ComponentScan(basePackages = "com.package1.component", useDefaultFilters = false)
class AppContextConfig{
    ...
}
```
위와같이 useDefaultFilters를 바꾸고 코드를 다시 실행시켜 보면, "Component1 Constructor" 문구가 출력되지 않는다.
- - -
##### 3. includeFilters & excludeFilters
다시 @Component 어노테이션이 사용된 객체를 불러오고 싶으면,  includeFilters 설정을 사용하면 된다. includeFilters는 특정 조건을 만족하는 클래스만을 스캔하도록 지정한다. includeFilters에서 사용할 수 있는 필터 타입은 다음과 같다.

- FilterType.ANNOTATION
- FilterType.ASPECTJ
- FilterType.ASSIGNABLE_TYPE
- FilterType.REGEX
- FilterType.CUSTOM

FilterType.ANNOTATION은 어노테이션을 기준으로 객체를 가져온다.
```cpp
@Configuration
@ComponentScan(
    basePackages = "com", 
    useDefaultFilters = false,
    includeFilters = {
        @Filter(
            type = FilterType.ANNOTATION, 
            classes = {Component.class, Repository.class, Service.class, Controller.class}
        )
    }
)

public class AppContextConfig {
    ...
} 
```
useDefaultFilters = false 설정을 했지만,  FilterType.ANNOTATION 필터를 사용하여 @Component, @Repository, @Service, @Controller 어노테이션이 적용된 객체를 가져온다.
- - -
####**필자는 필요한 class만 가져오길 원했기 떄문에 FilterType.ANNOTATION을 사용하였다. (메모: 프로젝트 ServletConfig 참조)**
- - -
FilterType.ASPECTJ는 AspectJ 패턴을 사용하여 객체를 가져온다.
```cpp
@Configuration
@ComponentScan(
    basePackages = "com", 
    useDefaultFilters = false,
    includeFilters = {
        @Filter(
            type = FilterType.ASPECTJ, 
            pattern = {"com.study.spring.*"}
        )
    }
)
public class AppContextConfig {
    ...
}
```
FilterType.ASPECTJ 필터를 사용하여 com.study.spring의 모든 객체를 가져온다. AspectJ 패턴은 다음에 하나의 챕터에서 온전히 다룬다.

FilterType.ASSIGNABLE_TYPE은 클래스를 기준으로 객체를 가져온다. classes에 할당할 수 있는 클래스, 즉 상속이나 구현한 클래스까지 포함한다.
```cpp
@Configuration
@ComponentScan(
    basePackages = "com", 
    useDefaultFilters = false,
    includeFilters = {
        @Filter(
            type = FilterType.ASSIGNABLE_TYPE, 
            classes = {Component1.class}
        )
    }
)
public class AppContextConfig {
    ...
}
```
FilterType.ASSIGNABLE_TYPE 필터를 사용하여 Component1 객체를 가져온다.

FilterType.REGEX는 정규식을 기준으로 객체를 가져온다.
```cpp
@Configuration
@ComponentScan(
    basePackages = "com", 
    useDefaultFilters = false,
    includeFilters = {
        @Filter(
            type = FilterType.REGEX, 
            pattern = {".*component.*"}
        )
    }
)
public class AppContextConfig {
    ...
}
```
FilterType.REGEX 필터를 사용하여, 패키지와 객체 명에 component 라는 글자가 포함되어있는 것을 가져온다.

FilterType.CUSTOM은 개발자가 직접 만든 필터를 적용한다. 필터는 TypeFilter 인터페이스를 구현하여 만들 수 있다.
```cpp
public class CustomFilter implements TypeFilter {
    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory){
      ...
    }
}
```
excludeFilters는 includeFilters와 사용법을 동일하다. 해당 기준의 객체를 불러오는 것이 아니라 제외한다는 점에서만 다르다.
- - -
##### 4. lazyInit
기본적으로 ComponentScan을 통해 찾은 클래스들은, 바로 초기화되어 컨텍스트에 빈으로 등록된다. 하지만 lazyInit 설정을 사용하면, 실제 해당 클래스가 사용될 때 초기화 한다.
```cpp
@Configuration
@ComponentScan(basePackages = {"com.package1.component"}, lazyInit = true)
class AppContextConfig{
    ...
}
```