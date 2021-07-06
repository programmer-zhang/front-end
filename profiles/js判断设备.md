> 编写代码过程中经常会遇到移动端和PC端的判断问题，提供几种判断方式

### 通过窗口大小进行判断
```
function judgeMobile() {
	if (document.documentElement.offsetWidth < 768 ||
		document.body.offsetWidth < 768) {
		return true;
	} else {
		return false;
	}
	window.addEventListener('resize', () => {
	if (document.documentElement.offsetWidth < 768 || 
		document.body.offsetWidth < 768) {
			return true;
		} else {
			return false;
		}
	});
}
```

### 通过userAgent进行判断
```
funcion judgeMobile() {
	let mobile =  /Android|webOS|iPhone|iPod|BlackBerry/i.test(navigator.userAgent);
	return mobile
}

funcion judgeMobile() {
	let sUserAgent = navigator.userAgent.toLowerCase();
	let bIsIpad = sUserAgent.match(/ipad/i) == "ipad";
	let bIsIphoneOs = sUserAgent.match(/iphone os/i) == "iphone os";
	let bIsMidp = sUserAgent.match(/midp/i) == "midp";
	let bIsUc7 = sUserAgent.match(/rv:1.2.3.4/i) == "rv:1.2.3.4";
	let bIsUc = sUserAgent.match(/ucweb/i) == "ucweb";
	let bIsAndroid = sUserAgent.match(/android/i) == "android";
	let bIsCE = sUserAgent.match(/windows ce/i) == "windows ce";
	let bIsWM = sUserAgent.match(/windows mobile/i) == "windows mobile";
	if (bIsIpad || bIsIphoneOs || bIsMidp || bIsUc7 || bIsUc || bIsAndroid || bIsCE || bIsWM) {
		return true;
	} else {
		return false;
	}
}
```
