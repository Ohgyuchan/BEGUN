## 3. GetX

GetX는 매우 가볍고 강력한 솔루션이며, 고성능 상태 관리, 지능형 종속성 주입, 라우트 관리 기능을 제공
* 성능
  * 성능과 리소스 소비의 최소화에 집중
  * Streams, ChangeNotifier를 사용하지 않음
* 생산성
  * 쉽고 간결한 구문 사용
  * 사용하지 않는 리소스는 메모리에서 자동 제거

* 조직화
  * 화면, 프레젠테이션 로직, 비즈니스 로직, 종속성 주입, 네비게이션을 완전 분리 가능

### 3.1. 라우트 관리
GetX를 사용하면 **context**없이 라우트를 관리할 수 있다. 따라서 코드가 더 간결해지고 쉬워진다.

GetX를 통한 라우트 관리를 위해서는 **MaterialApp**를 **GetMateralApp**으로 변경해야한다.

    // 기존
    MaterialApp(
      home: HomePage(),
    )
    
    // 변경: GetMaterialApp
    GetMaterialApp(
      home: HomePage(),
     )
#### 3.1.1. Navigation
#### 3.1.1.1. Get.to

    Get.to(NextPage()); // NextPage로 이동

#### 3.1.1.2. Get.back

    Get.back(); // 이전 화면으로 back. Navigation.pop()과 동일

#### 3.1.1.3. Get.toNamed

    Get.toNamed('/next'); // 미리 설정해둔 라우트 이름을 통해 새로운 화면으로 이동
    
    // 라우트 이름 설정 예시
    GetMaterialApp(
      home:HomePage(),
      getPages: [
        GetPage(name: '/next', page: () => NextPage()),
      ],
    )
    
#### 3.1.1.4. Get.off

    Get.off(NextPage()); // 다음 화면으로 이동하면서 이전 화면을 아예 스택에서 제거. 이전 화면으로 돌아갈 필요가 없을 때 사용.
    Get.offAll(NextPage()); // GetOff()가 이전 화면 하나만 없앤다면 Get.offAll()은 이전의 모든 화면을 없애고, 다음 화면으로 이동.
    
#### 3.1.2. Snackbar
#### 3.1.2.1. Get.snackbar

    // 제목과 메시지를 설정하면 해당 내용으로 Snackbar를 띄운다. 지속시간(duration), 방향(snackPosition), 배경색(backgroundColor) 등 여러 설정들을 추가할 수 있다.
    Get.snackbar('title', 'content', snackPosition: SnackPosition.TOP);
    
    // Get.snackbar()와 거의 동일
    Get.showSnackbar(
      GetBar(
        title: 'title',
        message: 'message',
        duration: Duration(second: 2),
        snackPosition: SnackPosition.TOP,
      ),
    );
    
#### 3.1.3. Dialog
#### 3.1.3.1. Get.defaultDialog
Dialog를 화면에 띄어준다. 확인/취소 시에 실행할 함수(**onConfirm, onCancel**), 확인/취소 텍스트(**textConfirm, textCancel**) 배경색(**backgroundColor**)등 여러 옵션 설정 가능
    Get.defaultDialog(title: 'title', middleText: 'Dialog');
    
#### 3.1.3.2. Get.dialog
**Get.defaultDialog()** 와 달리 원래 사용하던 **Dialog** 위젯을 가져와서 사용할 수 있다.
따라서 새로 시작하는 프로젝트에서 GetX를 적용할 때는 **Get.defaultDialog()** 를 사용하는 것이 좋지만, 원래 존재하는 프로젝트에 GetX를 적용할 때는 **Get.dialog()** 를 통해 기존 **Dialog** 위젯을 복사하여 빠르게 작업하는 편이 좋을 것 같다.

    Get.dialog(
      Dialog(
        child: Container(
          height: 100,
          child: Center(
            child: Text('Dialog),
          ),
        ),
      ),
    );
    
#### 3.1.4. BottomSheet
#### 3.1.4.1. Get.bottomSheet()
내부에 들어갈 위젯만 넣어주면 해당 위젯을 포함하는 BottomSheet를 보여준다.

    Get.bottomSheet(
      Container(
        height: 100,
        child: Center(
          child: Text('bottomSheet'),
        ),
      ),
    )

## 3.2. 상태관리(StateMagement)
#### 3.2.1. simple 방식
reactive방식에 비해 메모리를 적게 사용한다는 장점

#### 3.2.1.1. 변수 선언
GetController 를 extend하는 Controller 클래스를 선언하고, 초기값을 0으로 설정한 count 변수 선언
  
    class Controller extends GetxController {
      var count = 0;
    }
  
#### 3.2.1.2. GetBuilder()
GetBuilder 를 통해 화면에 count 변수를 보여준다. 이때 init 을 설정하지 앟으면 에러가 발생하는 것을 유의

    GetBuilder<Controller>(
      init: Controller(),
      builder: (_) => Text('Clicks: ${_.count}'),
    )

#### 3.2.1.3. update()
변수의 값이 변할 때 이를 화면에 리는 역할을 하는 함수

    class Controller extends GetxController (
      var count = 0;
      void increment() {
        count++;
        update();
      }
    )
update()는 ChangeNotifier의 notifyListeners()와 동일한 역할을 수행. 변수의 값이 바뀌는 연산을한 후 update()를 호출하는 함수( increment() ) 를 정의

#### 3.2.1.4. Get.find()

> Get.find(), Get.put() 은 simple, reactive 두 방식 모두에서 사용된다.

Get.find() 을 사용하여 increment()을 호출하는 버튼을 만들어 텍스트 아래에 배치

    TextButton(
       onPressed: Get.find<Controller>().increment,
       child: Text('increment')) 
    )

하지만 이대로 Rebuild 시 Get.find<Controller>() 에서 에러가 발생할 것이다. 이것은 Get.find<Controller>() 가 Controller 를 찾는 시점이 GetBuilder() 의 init 에서 Controller 를 등록하기 이전이라서 그렇다.
 #### 3.2.1.5. Get.put()
 위의 문제를 해결하기 위해서 Get.put()을 사용
 우선 build() 내부에 controller 변수를 선언하며, 이때 Get.put()을 통해 Controller 를 등록.
 
