```css
._xButton_1vm0o_2 {
  font-size: 14px;
}
._modalContainer_rds7d_2 ._modalContent_rds7d_22._size-standard_rds7d_34 ._myCustomDialog_rds7d_29 ._titleHelpText_rds7d_40 {
    font-size: 14px;
}
._modalContainer_rds7d_2 ._modalContent_rds7d_22 ._myCustomDialog_rds7d_29 ._closeText_rds7d_174 {
    font-size: 12px;
}
._modalContainer_rds7d_2 ._modalContent_rds7d_22._size-standard_rds7d_34 ._myCustomDialog_rds7d_29 ._titleText_rds7d_37 {
    font-size: 21px;
}

/* 移动端背景加半透明遮罩 */
@media screen and (max-width: 749px) {
  .pp-modal .ecomsend__Modal__Content {
    position: relative; /* 关键：确保伪元素定位生效 */
    z-index: 0;
  }

  .pp-modal .ecomsend__Modal__Content::after {
    content: "";
    position: absolute;
    inset: 0;
    background-color: rgba(0, 0, 0, 0.4); 
    z-index: 1;
    pointer-events: none; /* 避免挡住输入框等操作 */
  }

  /* 保证内容在遮罩上方显示 */
  .pp-modal .ecomsend__Modal__CustomDialogWrapper {
    position: relative;
    z-index: 2;
  }
}
@media screen and (max-width: 749px) {
  .pp-modal .ecomsend__Modal__TitleHelpText {
    color: #ffffff !important; /* 移动端副标题颜色 */
  }
}

```