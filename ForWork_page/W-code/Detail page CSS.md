Custom columns
like this
![[Pasted image 20250417105009.png]]

```css
	.icons-with-text__icon__icon {
	  width: 120px;
	  height: 120px;
	  border-radius: 50%;
	}
	.icons-with-text__icon-item {
	  padding: 15px;
	  border-radius: 15px;
	  background-color: #f6f6f6;
	}
```

update
```css
.icons-with-text__icon__icon {
  width: 180px;
  height: 180px;
  border-radius: 50%;
}
.icons-with-text__icon-item {
  padding: 15px;
  border-radius: 15px;
  background-color: #f6f6f6;
}
.icons-with-text__icon__icon img {
  border-radius: 50%;
}
```

ultimate
```css
.icons-with-text__icon__icon {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  overflow: hidden;
}

.icons-with-text__icon-item {

  padding: 15px;

  border-radius: 15px;

  background-color: #f6f6f6;

}

.icons-with-text__icon__icon img {

  width: 100%;

  height: 100%;

  border-radius: 50%;

  object-fit: cover;

  object-position: center;

}
```