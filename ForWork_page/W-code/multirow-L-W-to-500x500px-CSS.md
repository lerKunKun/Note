100 * 100 center

```css
img {
  width: 100px;
  height: 100px;
}
.image-with-text__media {
  position: relative;
  overflow: hidden;
}

.image-with-text__media img {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  max-width: 100%;
  max-height: 100%;
}
```

```css
<style>
/* 默认样式，适用于桌面端 */
.color-swatch__image {
  width: 80px;
  height: 120px;
}

/* 针对屏幕宽度小于或等于 768px 的设备（手机端） */
@media screen and (max-width: 768px) {
  .color-swatch__image {
    width: 60px;  /* 移动端宽度可根据需要调整 */
    height: auto; /* 保持图片的宽高比例 */
  }
}
</style>
```

all 500 * 500
```css
img {
  width: 500px;
  height: 500px;
}
```
![[Pasted image 20250422172648.png]]

![[Pasted image 20250422172451.png]]

```css
<style>
.color-swatch__image{
 width: 80px;
 height: 120px;
}

/* 针对屏幕宽度小于或等于 768px 的设备（手机端） */
@media screen and (max-width: 768px) {
  .color-swatch__image {
    width: 60px;  /* 移动端宽度可根据需要调整 */
    height: 90px; 
  }
}
</style>
```


	img {
	    width: 100%;
		  height: 100%;
	}

moblie（废弃）
``` css
@media screen and (max-width: 768px) {
    .pf-74_ {
        flex-direction: column;
        align-items: center;
        text-align: center;
        gap: 20px;
        max-width: 500px;
        max-height: 500px;
        width: 500px;
        height: 500px;
        margin: 0 auto;
        overflow: auto;
    }
    
    .pf-75_, .pf-82_ {
        flex-direction: column;
        gap: 15px;
    }
    
    .pf-text-1 {
        margin-left: 0;
    }
}
```

all 500 * 500 （废弃）
```css
.pf-74_ {
    max-width: 500px;
    max-height: 500px;
    width: 500px;
    height: 500px;
    margin: 0 auto;
    overflow: auto;
    display: flex;
    justify-content: space-between;
    padding: 0 20px;
}

@media screen and (max-width: 768px) {
    .pf-74_ {
        flex-direction: column;
        align-items: center;
        text-align: center;
        gap: 20px;
    }
    
    .pf-75_, .pf-82_ {
        flex-direction: column;
        gap: 15px;
    }
    
    .pf-text-1 {
        margin-left: 0;
    }
}
```