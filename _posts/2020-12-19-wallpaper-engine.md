---
layout: post
title: 월페이퍼 엔진 스크립트(Button, fade in/out)
category: WallpaperEngine
---
각종 보드의 핀맵이나 정의들을 항상 찾기 번거로워 배경화면에서 바로 이용할 수 있는 방안을 고민해서 만들어 보았다.
<p align="center"><img src="https://user-images.githubusercontent.com/48232366/102642342-ddff4900-41a0-11eb-964c-1e9017dba8e8.gif"></p>

***

드래그 앤 드랍으로 화면을 구성해준다.
<p align="center"><img src="https://user-images.githubusercontent.com/48232366/102640934-97a8ea80-419e-11eb-87dd-a0334b6071ac.JPG"></p>

<p align="center"><img src="https://user-images.githubusercontent.com/48232366/102640938-98da1780-419e-11eb-81f8-4b03712e6a0c.JPG"></p>

속성창을 통해 스크립트 편집을 할 수 있다.
<p align="center"><img src="https://user-images.githubusercontent.com/48232366/102640940-9972ae00-419e-11eb-95c4-ac4d287d335c.JPG"></p>

***

메인 배경(wallpaper) 스크립트
```javascript
'use strict';
let timer=0;
shared.work_state=0;  //shared=외부 변수 참조
shared.bt_state=0;
/**
 * @param {Boolean} value - for property 'visible'
 */
//레이어 메인 반복문
export function update(value) {
	switch(shared.bt_state)
	{
		case 'ardu on':
			fade_in("UNO");
			fade_in("NANO");
			break;
		case 'ardu off':
			fade_out("UNO");
			fade_out("NANO");
			break;
		default:
			console.log('break');
			break;
	}
	return value;
}

//fade in
export function fade_in(value) {
	if(thisScene.getLayer(value).alpha<=1) //alpha=투명도
	{
		timer+= engine.frametime; //월페이퍼 엔진 프레임타임
		thisScene.getLayer(value).alpha=timer*0.7;
	}
	console.log('fade_in');  //콘솔 확인용
	console.log(value);
}

//fade out
export function fade_out(value) {
	if(thisScene.getLayer(value).alpha>=0)
	{
		shared.work_state=1;
		timer-= engine.frametime;
		thisScene.getLayer(value).alpha=timer*0.7;
	}
	else
	{
		shared.work_state=0;
		shared.bt_state=0;
	}
	console.log('fade_out');
	console.log(value);
}
```

버튼(arduino_bt) 스크립트
```javascript
'use strict';

/**
 * @param {Boolean} value - for property 'visible'
 */
export function update(value) {
	return value;
}

/**
 * @param {Boolean} value - for property 'visible'
 */
//레이어 초기화
export function init(value) {
	thisScene.getLayer("UNO").alpha=0; //투명도 0
	thisScene.getLayer("NANO").alpha=0;
	thisScene.getLayer("UNO").visible=true;  //보이기 허용
	thisScene.getLayer("NANO").visible=true;
	return value;
}

/**
 * @param {ICursorEvent} event
 */
//레이어 클릭 이벤트 함수
export function cursorClick(event) {
	if(shared.work_state==0){
		if(shared.bt_state=='ardu on')  //외부 변수에 클릭 레이어 저장
			shared.bt_state='ardu off';
		else if(shared.bt_state==0)
			shared.bt_state='ardu on';
		console.log("ardu on");
	}
}
```

***

스크립트 실행
<p align="center"><img src="https://user-images.githubusercontent.com/48232366/102641583-a643d180-419f-11eb-88b8-258c60a467cc.gif"></p>
