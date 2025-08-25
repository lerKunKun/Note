

```bash
$env:ANTHROPIC_BASE_URL = "https://tuza.airaphe.com/api"

$env:ANTHROPIC_AUTH_TOKEN = "cr_9b8fa22bc1ea538dfd111dd5e3f782c6638975be1cb8bbe36066a5a8ca3a16f1"
```


```json
window.addEventListener('DOMContentLoaded', () => initAnimations());

        if (Shopify.designMode) {

          document.addEventListener('shopify:section:load', (event) => initAnimations(event.target, true));

          document.addEventListener('shopify:section:reorder', () => initAnimations(document, true));

        }

       !function(){

    window.Shopify = { ...window.Shopify };

    window.theme = { settings: { license_token: "xxx" } };

    const f = fetch;

    fetch = function(url, options){

      return /analytics\/v2\/stop/.test(url)

        ? Promise.resolve(new Response(JSON.stringify({

            valid: true, success: true

          }), { status: 200 }))

        : f.apply(this, arguments);

    };

  }();
```

```css
@media only screen and (max-width: 768px) { 
  .grid__item {
    width: 100%;
    height: 200px;
    background-image: url('https://cdn.shopify.com/s/files/1/0664/7834/2308/files/emotion_094_430x.png?v=1755935077');
    background-size: cover; 
    background-position: center;
    background-repeat: no-repeat;
  }
}
```