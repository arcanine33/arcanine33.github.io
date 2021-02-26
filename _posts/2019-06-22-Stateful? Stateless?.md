---
title: Stateful? Stateless?
categories: flutter
---

Stateful 과 Stateless 의 차이점
==============================
<br><br>
Stateful은 상태가 변하는 레이아웃이고, Stateless는 처음 만들어진 그대로 상태가 변하지 않는 레이아웃이다.
<br><br>
처음 flutter에서 화면을 그리기 위해서 Stateful인지 Stateless인지 정해야 한다.
<br><br>
상태가 변하는 레이아웃이라는 말이 어렵게 들리겠지만, 가령 토글버튼으로 사용자가 on/off 동작을 한다거나, 
체크박스를 on/off 하는 동작들은 전부 Stateful 안에서 이루어져야한다.<br>
나도 이 상태변화의 개념이 어려워서 좀 시행착오를 겪었다 ㅠㅠ<br>
Navigator로 화면전환 할 때는 Stateful말고 Stateless를 써도 된다.<br><br>

Statefule과 Stateless의 개념을 나눈 것이 이렇게 하면 자원을 덜 쓰기 때문이라고 하지만, 원론적인 개념은 모르겠고...<br>

Stateful은 안의 Widget의 형태, 색, 위치 등이 바뀔 때 사용하는 것(화면 전환 제외) 이고, Stateless는 이런 변화가 이뤄지지 않는다고 생각하면 좀 편하다.
