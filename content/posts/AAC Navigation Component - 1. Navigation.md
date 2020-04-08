---
title: "[Android Jetpack] AAC Navigation Component - 1. Navigation 소개 및 시작하기"
date: 2020-04-01T16:16:32+09:00
draft: false

---

# [Android Jetpack] AAC Navigation Component - 1. Navigation 소개 및 시작하기

Android Jetpack의 Navigation Component는 화면끼리의 전환과 흐름을 시각적으로 볼 수 있도록 도와주는 AAC(Android Architecture Component) 라이브러리입니다. 



## Navigation의 기본 원칙

> 참고: 프로젝트에서 Navigation Component를 사용하지 않더라도 이러한 설계 원칙을 따라야 합니다.

### 1. 고정 시작점(Fixed start destination)

모든 앱은 사용자가 런처에서 실행할 때 처음으로 표시되는 화면과 사용자가 Back 버튼을 누른 후 마지막으로 보여지는 화면인 고정 시작점이 있어야합니다.

모든 앱은 고정 시작점이 있어야합니다. 고정 시작점이란 사용자가 앱을 런처에서 실행할 때 처음으로 보여지는 화면과 사용자가 Back 버튼을 누른 후 마지막으로 도착하는 화면입니다.

> 참고: 앱에 일회성 설정 또는 일련의 로그인 화면이 있을 수 있습니다. 조건부 화면은 일부 경우에만 사용자에게 표시되므로 시작 화면으로 고려해서는 안 됩니다.



### 2. Navigation 상태는 스택으로 표현

Navigation의 상태는 스택으로 표현됩니다. 앱이 처음 실행되면 시작점을 가지는 스택을 만듭니다. 이것을 백 스택이라고 부르며 스택의 최상단은 현재 표시되고 있는 화면을 나타내고, 이전 스택들은 방문했던 목적지들의 기록을 나타냅니다.

![navigation-backstack1](/images/navigation-component1/navigation-backstack1.png)



### 3. 앱에서 Up and  Back 버튼은 동일하게 작동

화면 하단의 시스템 메뉴인 Back 버튼은 백 스택을 탐색하는데 사용됩니다. Back 버튼을 누르면 현재 표시되고 있는 화면이 백 스택 상단에서 사리지고 이전 화면으로 이동합니다. Up 버튼은 화면 상단 App Bar에 위치하며 Back 버튼과 동일하게 작동합니다. 

![navigation-backstack2](/images/navigation-component1/navigation-backstack2.png)



### 4. Up 버튼으로는 앱 종료 불가

앱의 시작점에 있는 경우 Up 버튼은 앱을 종료하지 않으므로 표시되지 않습니다. 하지만 Back 버튼은 표시되며 앱을 종료시킵니다.

다른 앱에서 딥 링크를 호출하여 실행되면 Up 버튼은 딥 링크를 트리거한 앱이 아닌 [시뮬레이션된 백 스택](#5-수동-탐색을-시뮬레이션하는-딥-링크)을 탐색합니다. 하지만 Back 버튼을 누를 시 다른 앱으로 돌아갑니다.



### 5. 수동 탐색을 시뮬레이션하는 딥 링크

사용자는 수동 또는 딥 링크를 이용하여 특정 화면으로 이동 할 수 있습니다. 두가지 경우 다 동일한 백 스택을 가져야 되며 Up 버튼 또는 Back 버튼을 사용하여 시작점으로 이동 할 수 있어야합니다. 

딥 링크를 호출하면 앱의 기존 백 스택이 새로운 백 스택으로 대체됩니다. 이 때 백 스택은 앱을 수동으로 탐색하여 얻을 수 있는 백 스택과 동일해야합니다. 이는 앱의 백 스택이 사실적이어야 하므로 중요합니다.



## 환경 설정

> 참고: Navigation Component를 사용하려면 Android 스튜디오 3.3 이상을 사용해야 합니다.

앱의 `build.gradle` 파일에 다음 종속성을 추가합니다.

~~~groovy
dependencies {
	def nav_version = "2.3.0-alpha01"
	
	// Java language implementation
	implementation "androidx.navigation:navigation-fragment:$nav_version"
	implementation "androidx.navigation:navigation-ui:$nav_version"
	
	// Kotlin
	implementation "androidx.navigation:navigation-fragment-ktx:$nav_version"
	implementation "androidx.navigation:navigation-ui-ktx:$nav_version"
	
	// Testing Navigation
	androidTestImplementation "androidx.navigation:navigation-testing:$nav_version"
}
~~~



## Navigation Graph

Navigation Graph는 모든 목적지(Activity, Fragment 등)와 Action을 포함하는 리소스 파일이며 앱의 모든 탐색 경로를 가집니다.

아래와 같이 Navigation Graph는 Action을 이용하여 목적지에서 목적지로 이동하는 앱의 흐름을 시각적으로 보여줍니다.

![img](https://developer.android.com/images/topic/libraries/architecture/navigation-design-graph-top-level.png)



### 프로젝트에 Navigation Graph를 추가

1. 프로젝트 창에서 우클릭으로 **New > Android Resource File**을 클릭합니다. 
2. **File name**에 이름을 입력합니다. `ex) nav_graph`
3. **Resource type**을 Navigation을 선택하고 OK를 누릅니다.



### Navigation Editor

Graph를 추가한 후 Navigation Editor에서는 Graph를 시각적으로 편집하거나 XML을 편집 할 수 있습니다.

![nav_graph](/images/navigation-component1/nav_graph.png)

1. **Destinations 패널**: 현재 Navigation Editor에 있는 Host와 모든 목적지를 나타냅니다.
2. **Graph Editor**: Navigation Graph를 시각적으로 표시합니다.
3. **Attributes**: Navigation Graph에서 현재 선택된 항목의 속성을 보여줍니다.
4. **View mode**: Design 뷰, XML 코드를 볼 수 있는 Code 뷰, 화면을 나누어주는 Split 뷰를 전환 할 수 있습니다.

**Code** 뷰를 클릭하면 아래와 같은 XML 코드를 확인 할 수 있습니다.

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            android:id="@+id/nav_graph">

</navigation>
~~~



###Activity에 NavHost 추가

Acitity는 NavHost를 통해 Navigation을 관리하게 됩니다. NavHost는 Navigation Graph에 정의된 목적지들이 전환되는 컨테이너의 역할을 합니다.

NavHost는 NavHostFragment로 구현되어 있으며, 화면 이동에 관련된 모든 활동을 처리합니다.

NavHost를 구현하기 위해선 name 속성과 navGraph 속성을 추가해 주어야 합니다.

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <fragment
        android:id="@+id/nav_host_fragment"
        android:name="androidx.navigation.fragment.NavHostFragment"
        android:layout_width="0dp"
        android:layout_height="0dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:navGraph="@navigation/nav_graph"
        app:defaultNavHost="true" />

</androidx.constraintlayout.widget.ConstraintLayout>
~~~

- `android:name` 속성은 `NavHost`가 구현된 클래스(`NavHostFragment`)를 지정합니다.
- `app:navGraph` 속성은 `NavHostFragment`와 Navigation Graph를 연결합니다.
- `app:defaultNavHost="true"` 속성은 `NavHostFragment`가 시스템으로부터 Back 버튼을 차단합니다.



### Navigation Graph에 목적지 추가

1. 화면 상단 **New Destination**을 클릭한 후 **Create new destination**을 클릭합니다.
2. 표시된 다이얼로그에서 Fragment를 생성합니다.

![create_fragment](/Users/trycatch/programming/web/Dev-Blog/public/images/navigation-component1/create_fragment.png)

**Code** 뷰를 클릭하면 아래와 같은 XML 코드를 확인 할 수 있습니다.

~~~xml
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/nav_graph"
    app:startDestination="@id/blankFragment">

    <fragment
        android:id="@+id/blankFragment"
        android:name="com.trycatch.sample.BlankFragment"
        android:label="fragment_blank"
        tools:layout="@layout/fragment_blank" />
</navigation>
~~~



### 목적지 연결

![action](/Users/trycatch/programming/web/Dev-Blog/public/images/navigation-component1/action.gif)