# Gradle에서 알아야 하는 다섯 가지

### **1. Gradle is a general-purpose build tool**

Gradle은 범용 빌드 도구이다. Gradle을 사용하면 빌드하려는 대상이나 수행 방법에 대한 가정들이 거의 없기 때문에 어떤 소프트웨어든 빌드할 수 있다.

Gradle allows you to build any software, because it makes few assumptions about what you’re trying to build or how it should be done.

### **2. The core model is based on tasks**

핵심 모델은 작업(task)들을 기반으로 한다. Gradle은 작업(task, units of work)들의 비순환 방향 그래프(DAG, Directed Acyclic Graphs)로 빌드를 모델링한다. 이것은 빌드가 기본적으로 작업들의 집합을 구성하고 작업들을 의존성에 따라 연결하여 DAG를 생성한다는 것을 의미한다. 일단 작업 그래프가 생성되면, Gradle은 어떤 작업을 어떤 순서로 실행해야 하는지 결정하고 실행한다.

아래 다이어그램은 두 가지 작업 그래프 예시(하나는 추상적이고 다른 하나는 구체적)를 보여준다. 작업(task)들 간의 의존성들은 화살표로 나타낸다.

![https://docs.gradle.org/current/userguide/img/task-dag-examples.png](https://docs.gradle.org/current/userguide/img/task-dag-examples.png)

*Figure 1. Two examples of Gradle task graphs*

거의 모든 빌드 프로세스를 이러한 방식으로 작업 그래프로 모델링할 수 있으며, 이것이 Gradle이 매우 유연한 이유 중 하나이다. 그리고 이 작업 그래프는 [작업 의존 메커니즘](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html#sec:task_dependencies)을 통해  플러그인과 사용자 작성 빌드 스크립트로 정의될 수 있다.

작업들은 다음으로 구성된다.

- 동작들(Actions) — 파일 복사 또는 소스 컴파일과 같은 작업 수행의 일부.
- 입력들(Inputs) — 동작(Action)들이 사용하거나 작동시키는 값들, 파일들, 디렉터리들.
- 출력들(Outputs) — 동작(Action)들이 수정하거나 생성한 파일들과 디렉터리들.

위 모두는 작업이 수행해야 하는 일에 따라 선택적이다. [standard lifecycle tasks](https://docs.gradle.org/current/userguide/base_plugin.html#sec:base_tasks)과 같은 일부 작업들에는 동작들이 없다. 이런 작업들은 단순히 편의상 여러 작업들을 함께 그룹화(aggregate)한다.

실행할 작업은 선택할 수 있다. 필요한 작업만 지정하여 그 이상의 일을 하지 않고 시간을 절약한다. 단위 테스트만 실행하려는 경우 이를 수행하는 작업(일반적으로 `test`)을 선택한다. 애플리케이션을 패키징하려는 경우 대부분의 빌드에는 이를 위한 `assemble` 작업이 있다.

마지막으로 Gradle의 [증분 빌드(incremental build)](https://docs.gradle.org/current/userguide/more_about_tasks.html#sec:up_to_date_checks)는 강력하고 안정적이다. 따라서 실제로 정리(clean)를 수행하고 싶지 않은 경우, `clean` 작업(task)을 피하면서도 빠르게 빌드를 실행한다.

### **3. Gradle has several fixed build phases**

Gradle은 3단계로 빌드 스크립트를 평가하고 실행한다.

1. 초기화(Initialization): 빌드를 위한 환경을 설정하고 참여할 프로젝트들을 결정한다.
2. 구성(Configuration): 빌드에 대한 작업(task) 그래프를 구성한 다음 사용자가 실행하려는 작업을 기반으로 실행해야 하는 작업들과 순서를 결정한다.
3. 실행(Execution): 구성 단계의 끝에서 선택한 작업들을 실행한다.

이 단계들이 Gradle의 [빌드 생명주기(Build Lifecycle)](https://docs.gradle.org/current/userguide/build_lifecycle.html#build_lifecycle)는 만든다.

### ****4. Gradle is extensible in more ways than one****

번들로 제공되는 빌드 로직만 사용하여 Gradle로 프로젝트를 빌드할 수 있다면 좋지만 대부분 불가능하다. 대부분의 빌드들은 커스텀 빌드 로직을 추가해야 하는 특수한 요구 사항들이 있다. Gradle은 다음과 같이 확장할 수 있는 몇 가지 메커니즘을 제공한다.

- [작업 유형을 커스텀하기(Custom task types)](https://docs.gradle.org/current/userguide/custom_tasks.html#custom_tasks)
    - 빌드가 현재 작업(task)이 할 수 없는 어떤 일을 수행하도록 하고 싶을 때, 간단히 자신의 작업 유형을 작성할 수 있다. 일반적으로 사용자 정의 작업 유형에 대한 소스 파일을 [buildSrc](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:build_sources) 디렉토리 또는 패키지된 플러그인에 넣는 것이 가장 좋다. 그런 다음 Gradle에서 제공하는 것과 마찬가지로 사용자 지정 작업 유형을 사용할 수 있다.
- 작업 동작들을 커스텀하기(Custom task actions)
    - [Task.doFirst()](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html#org.gradle.api.Task:doFirst(org.gradle.api.Action))와 [Task.doLast()](https://docs.gradle.org/current/dsl/org.gradle.api.Task.html#org.gradle.api.Task:doLast(org.gradle.api.Action)) 메서드들을 이용해 작업 전후에 실행되는 커스텀 빌드 로직를 추가(attach)할 수 있다.
- 프로젝트와 작업에 대한 [추가 속성](https://docs.gradle.org/current/userguide/writing_build_scripts.html#sec:extra_properties)([Extra properties](https://docs.gradle.org/current/userguide/writing_build_scripts.html#sec:extra_properties) on projects and tasks)
    - 프로젝트 또는 작업(task)에 사용자가 직접 정의한 속성들을 추가한 다음, 직접 정의한 동작(action)이나 다른 빌드 로직에 사용할 수 있다.
    
    ```groovy
    plugins {
        id 'java-library'
    }
    
    ext {
        springVersion = "3.1.0.RELEASE"
        emailNotification = "build@master.org"
    }
    
    sourceSets.all { ext.purpose = null }
    
    sourceSets {
        main {
            purpose = "production"
        }
        test {
            purpose = "test"
        }
        plugin {
            purpose = "production"
        }
    }
    
    tasks.register('printProperties') {
        doLast {
            println springVersion
            println emailNotification
            sourceSets.matching { it.purpose == "production" }.each { println it.name }
        }
    }
    ```
    
    - 위의 예시 처럼 `ext` 속성을 이용해 사용자가 직접 정의한 속성을 추가하여 사용할 수 있다.
- **규칙 지정하기**(Custom conventions)
    - 컨벤션들은 빌드를 단순화하는 강력한 방법이다. 이를 통해 사용자는 쉽게 이해하고 사용할 수 있다. 이는 [자바 빌드](https://docs.gradle.org/current/userguide/building_java_projects.html#building_java_projects)와 같이 표준 프로젝트 구조 및 명명 규칙들을 사용하는 빌드에서 볼 수 있다. 개인은 컨벤션을 제공하는 자체 플러그인을 작성할 수 있다. 빌드 관련 측면에 대한 기본값들을 구성하기만 하면 된다.
- [A custom model](https://docs.gradle.org/current/userguide/implementing_gradle_plugins.html#modeling_dsl_like_apis)
    - Gradle을 사용하면 작업(task), 파일 및 종속성(dependency) 구성(configuration)을 넘어 새로운 개념(concept)들을 빌드에 도입할 수 있다. 소스 집합(*[source sets](https://docs.gradle.org/current/userguide/building_java_projects.html#sec:java_source_sets)*)의 개념을 빌드에 추가하는 대부분의 언어 플러그인들에서 이를 확인할 수 있다.

### **5. Build scripts operate against an API**

Gradle의 빌드 스크립트를 실행 가능한 코드로 보는 것은 쉽다. 이는 실제로 그렇기 때문이다. 하지만 이는 세부 구현 사항이다. **잘 설계된 빌드 스크립트는 소프트웨어를 빌드하는 데 필요한 단계를 설명하는 것이다. 해당 단계가 작업을 수행하는 방법을 설명하는 것이 아니다.** 이것이 작업 유형들(task types)과 플러그인들을 정의할 때 해야하는 일이다.

Gradle의 파워와 유연성은 빌드 스크립트가 코드라는 사실에서 비롯된다는 오해가 있다. 이것은 사실이 아니다. Gradle에 힘을 제공하는 것은 기반 모델과 API이다. 권장사항에서 권장하는 대로, [**빌드 스크립트에 많은 명령형(imperative) 로직을 작성하는 것은 피하는 것이 좋다.**](https://docs.gradle.org/current/userguide/authoring_maintainable_build_scripts.html#sec:avoid_imperative_logic_in_scripts)

하지만 빌드 스크립트를 실행 가능한 코드로 보는 것이 유용한 한 가지 영역이 있다. 어떻게 빌드 스크립트의 구문이 Gradle의 API에 매핑되는지를 이해하는 것이다. [Groovy DSL Reference](https://docs.gradle.org/current/dsl/)와 [Javadocs](https://docs.gradle.org/current/javadoc/)로 구성된 API 문서는 메서드와 속성을 리스트하고 클로저와 동작(action)들을 참조한다. 빌드 스크립트 문맥 내에서 이것이 의미하는 바는 무엇일까? [Groovy Build Script Primer](https://docs.gradle.org/current/userguide/groovy_build_script_primer.html#groovy_build_script_primer)를 확인하여 해당 질문에 대한 답변을 알아본다. 이를 통해 API 문서를 효과적으로 사용할 수 있을 것이다.

Gradle이 JVM에서 실행되기 때문에 빌드 스크립트는 표준 [Java API](https://docs.oracle.com/javase/8/docs/api)도 사용할 수 있다. Groovy 빌드 스크립트는 Groovy API들을 추가로 사용할 수 있으며 Kotlin 빌드 스크립트는 Kotlin API들을 사용할 수 있다.

### 참조

[https://docs.gradle.org/current/userguide/what_is_gradle.html](https://docs.gradle.org/current/userguide/what_is_gradle.html)
