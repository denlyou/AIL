# 유니티 (C#)

## 로그

```cs
Debug.Log("Message");
```

### 기본 스크립트

```cs
using UnityEngine;
using System.Collections;

public class testScript : MonoBehaviour {
	void Start () {
	   // 시작할 때 한번 수행
	}

	void Update () {
	   // 프레임 전환시마다 계속 수행
	}

}
```

### 키보드 입력 감지
```cs
void Update () {
  bool isSpaceInput = Input.GetKey (KeyCode.Space);
  if (isSpaceInput) {
   Debug.Log ("Spacebar pressed");
  }
}
```

- [bool `Input.GetKey(키값)`](https://docs.unity3d.com/ScriptReference/Input.GetKey.html)
  - [키값은 KeyCode객체 참조](https://docs.unity3d.com/ScriptReference/KeyCode.html)
  - 키가 누르고 있는 프레임 모두 감지
- `Input.GetKeyDown()` : 키가 처음 눌렸을때 감지
- `Input.GetKeyUp()` : 키에서 손을 땟을때 감지
- `Input.anyKeyDown` : 아무 키나 눌렸을때 감지


### 마우스 입력 감지
```cs
void Update () {
  if (Input.GetMouseButtonDown(0)) {
		Debug.Log ("Mouse left button click");
	}
}
```
- `Input.GetMouseButtonDown(버튼값)` : 눌렸을 때
- `Input.GetMouseButton(버튼값)` : 누르고 있는 중
- `Input.GetMouseButtonUp(버튼값)` : 손땟을 때
- `버튼값`은 Int
  - 0: 왼쪽
  - 1: 오른쪽
  - 2: 휠(가운데)
- Vector3 [`Input.mousePosition`](https://docs.unity3d.com/ScriptReference/Input-mousePosition.html) : 마우스 위치 반환 ([Vector3](https://docs.unity3d.com/ScriptReference/Vector3.html) 객체)

### 오브젝트 이동 (Transform)
- (준비중)
