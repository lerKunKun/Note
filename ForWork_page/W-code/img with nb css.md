Custom columns 
img with liquid
all css
![[Pasted image 20250529101203.png]]

liquid css
![[Pasted image 20250529101151.png]]


if image px need change
square 1:1
![[Pasted image 20250529105418.png]]
css    less div css div ps>img px
```css
 .img{
 width: 520px;
 height: auto;
 aspect-ratio: 1 / 1;
 object-fit: cover;
 object-position: center bottom;
 border-radius: 20px;
}
```

img-musk.css
```css
.media--transparent {
  position: relative; /* 为绝对定位的图片建立参考 */
  width: 520px; /* 固定宽度 */
  height: 520px; /* 固定高度 */
  margin: 0 auto; /* 居中显示 */
  overflow: hidden; /* 隐藏超出部分 */
}

.media--transparent img {
  display: block;
  width: 100%;
  max-width: 520px;
  height: 520px;
  object-fit: cover;
  object-position: bottom;
}
```



```html
<div style="width: 100%;">

    <h3 style="margin-top: 0; margin-bottom: 0; font-weight: 600; font-size: 35px; line-height: 1.5em; text-transform: uppercase; text-align: center;">3D Comfort and Easy Maintenance</h3>

    <p style="margin: 5px 0 25px; background-image: linear-gradient(90deg, #fbf9f9 5%, #6AB2FF 20%, #ff9AB3 80%, #fbf9f9 95%); height: 10px;"> </p>

    <p style="margin-top: 0; margin-bottom: 0; font-weight: 400; font-size: 20px; line-height: 1.5em; text-align: center;" data-pf-type="Paragraph3">Enjoy the <strong>3D cup</strong> design that <strong>molds perfectly to your shape</strong>, providing natural<strong> lift and support</strong>. Washable and reusable, these inserts <strong>maintain their stickiness after cleaning</strong>, ensuring <strong>long-lasting </strong>comfort and adaptability for any outfit.</p>

    <div style="width: 100%; display: flex;">

        <div style="width: 50%; display: flex; align-items: center; flex-direction: column;" class="box1">

            <img style="margin-top:20px; height: 120px; width: 180px; object-fit: cover; border-radius: 8px; box-shadow: 0 8px 16px rgba(0,0,0,0.2); border: 8px solid white;" src="https://cdn.shopify.com/s/files/1/0587/6033/1325/files/1_9330a43b-64e5-4042-947d-45a604fa0b2a.png?v=1731738816&width=1024" alt="">

            <div style="margin: 0; color: #333; font-size:12px; font-weight: 600; text-transform: uppercase; margin-top: 10px;">3d cup design</div>

            <div style="margin: 5px 0 0; background-image: linear-gradient(90deg, #fbf9f9 10%, #6AB2FF 40%, #ff9AB3 60%, #fbf9f9 90%); height: 10px; width: 100px; border-radius: 20px; display: inline-block;"> </div>

        </div>

        <div style="width: 50%; display: flex; align-items: center; flex-direction: column;" class="box1">

            <img style="margin-top:20px; height: 120px; width: 180px; object-fit: cover; border-radius: 8px; box-shadow: 0 8px 16px rgba(0,0,0,0.2); border: 8px solid white;" src="https://cdn.shopify.com/s/files/1/0587/6033/1325/files/3_0b7922a3-4f72-4e08-8483-cf9f66af30d3.png?v=1731738816&width=1024" alt="">

            <div style="margin: 0; color: #333; font-size:12px; font-weight: 600; text-transform: uppercase; margin-top: 10px;">washable & reusable</div>

            <div style="margin: 5px 0 0; background-image: linear-gradient(90deg, #fbf9f9 10%, #6AB2FF 40%, #ff9AB3 60%, #fbf9f9 90%); height: 10px; width: 100px; border-radius: 20px; display: inline-block;"></div>

        </div>

    </div>

</div>
```

```bash
wsl --install -d Ubuntu
```