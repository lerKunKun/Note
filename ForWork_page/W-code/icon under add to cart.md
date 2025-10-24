![[Pasted image 20250610092707.png]]
```html
<!-- Add Google Material Icons -->

<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">

  

<div class="shipping-features">

    <div class="feature-left">

        <span class="material-icons">local_shipping</span>

        <div class="feature-text">

            <div class="feature-title">Free Shipping Over $79</div>

            <div class="feature-subtitle">*Same-Day Shipping— for Orders Before 12pm PST</div>

        </div>

    </div>

    <div class="features-right">

        <div class="feature-item">

            <span class="material-icons">lock_person</span>

            <div class="feature-text">

                <div class="feature-title">Secure Checkout</div>

            </div>

        </div>

        <div class="feature-item">

            <span class="material-icons">payments</span>

            <div class="feature-text">

                <div class="feature-title">Easy Returns</div>

            </div>

        </div>

    </div>

</div>

  

<style>

.shipping-features {

    display: flex;

    justify-content: space-between;

    align-items: flex-start;

    padding: 20px;

    background: #f8f6f4;

}

  

.feature-left {

    display: flex;

    align-items: flex-start;

    gap: 12px;

    flex: 1;

}

  

.features-right {

    display: flex;

    flex-direction: column;

    gap: 15px;

}

  

.feature-item {

    display: flex;

    align-items: center;

    gap: 12px;

}

  

.material-icons {

    font-size: 24px;

    color: #333;

}

  

.feature-text {

    display: flex;

    flex-direction: column;

}

  

.feature-title {

    font-size: 14px;

    font-weight: 500;

    color: #333;

    margin-bottom: 2px;

}

  

.feature-subtitle {

    font-size: 12px;

    color: #666;

    margin-top: 2px;

}

</style>
```



![[Pasted image 20250611092344.png]]
```html
<!-- Add Google Material Icons -->

  

<link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">

  

  

<div class="shipping-features">

  <div class="feature-item">

    <span class="material-icons">local_shipping</span>

    <div class="feature-text">

      <div class="feature-title">FREE SHIPPING OVER $79</div>

    </div>

  </div>

  <div class="feature-item">

    <span class="material-icons">security</span>

    <div class="feature-text">

      <div class="feature-title">SECURE CHECKOUT</div>

    </div>

  </div>

  <div class="feature-item">

    <span class="material-icons">cached</span>

    <div class="feature-text">

      <div class="feature-title">100% MONEY BACK</div>

    </div>

  </div>

  <div class="feature-item">

    <span class="material-icons">credit_card</span>

    <div class="feature-text">

      <div class="feature-title">EASY RETURNS</div>

    </div>

  </div>

</div>

  

  

<style>

.shipping-features {

    display: grid;

    grid-template-columns: 1fr 1fr;

    grid-template-rows: 1fr 1fr;

    gap: 20px;

    padding: 20px;

    background: #f5f5f5;

    border-top: 1px solid #e0e0e0;

    border-bottom: 1px solid #e0e0e0;

}

  

.feature-item {

    display: flex;

    align-items: center;

    gap: 12px;

}

  

.material-icons {

    font-size: 28px;

    color: #333;

}

  

.feature-text {

    display: flex;

    flex-direction: column;

}

  

.feature-title {

    font-size: 13px;

    font-weight: 600;

    color: #333;

    letter-spacing: 0.5px;

    text-transform: uppercase;

}

  

/* Responsive design */

@media (max-width: 768px) {

    .shipping-features {

        flex-direction: column;

        gap: 15px;

        padding: 15px;

    }

    .feature-item {

        justify-content: flex-start;

    }

}

</style>
```
[D:\development\frontend\demo\view\view-0610-04.html](file:///d%3A/development/frontend/demo/view/view-0610-04.html)
