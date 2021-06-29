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
