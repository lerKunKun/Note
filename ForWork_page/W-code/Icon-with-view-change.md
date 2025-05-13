
like this
![[Pasted image 20250417105155.png]]



```liquid
{%- style -%}
  .section-{{ section.id }}-padding {
    padding-top: {{ section.settings.padding_top | times: 0.75 | round: 0 }}px;
    padding-bottom: {{ section.settings.padding_bottom | times: 0.75 | round: 0 }}px;
    {% unless section.settings.container_width == 'normal' %}
      max-width: {{ section.settings.container_width }}rem;
    {% endunless %}
  }

  .icon-with-view-change-{{ section.id }} .quote-container {
    background-color: white;
    border-radius: 0.8rem;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
    padding: 1.5rem;
    margin-bottom: 2rem;
    position: relative;
    min-height: 100px;
  }

  .icon-with-view-change-{{ section.id }} .quote-text {
    font-size: 1.1rem;
    line-height: 1.6;
    color: #333;
    text-align: center;
    margin: 0 1.5rem;
  }

  .icon-with-view-change-{{ section.id }} .quote-icon {
    color: #888;
    font-size: 1.5rem;
  }

  .icon-with-view-change-{{ section.id }} .quote-left {
    position: absolute;
    left: 1rem;
    top: 1rem;
  }

  .icon-with-view-change-{{ section.id }} .quote-right {
    position: absolute;
    right: 1rem;
    bottom: 1rem;
  }

  .icon-with-view-change-{{ section.id }} .magazine-logos {
    display: flex;
    justify-content: space-around;
    align-items: center;
    flex-wrap: wrap;
    gap: 1.5rem;
  }

  .icon-with-view-change-{{ section.id }} .magazine-logo-btn {
    background: none;
    border: none;
    cursor: pointer;
    opacity: 0.7;
    transition: opacity 0.3s, transform 0.3s;
    padding: 0.5rem;
    position: relative;
  }

  .icon-with-view-change-{{ section.id }} .magazine-logo-btn:hover,
  .icon-with-view-change-{{ section.id }} .magazine-logo-btn.active {
    opacity: 1;
    transform: translateY(-5px);
  }

  .icon-with-view-change-{{ section.id }} .magazine-logo {
    max-width: 120px;
    max-height: 60px;
    width: auto;
    height: auto;
  }

  @media screen and (min-width: 750px) {
    .section-{{ section.id }}-padding {
      padding-top: {{ section.settings.padding_top }}px;
      padding-bottom: {{ section.settings.padding_bottom }}px;
    }
  }
{%- endstyle -%}

<div class="color-scheme-{{ section.id }} color-{{ section.settings.color_scheme }} gradient section-{{ section.id }}-padding">
  <div class="page-width">
    <div class="icon-with-view-change-{{ section.id }}">
      {%- if section.settings.title != blank -%}
        <h2 class="h1 text-center">{{ section.settings.title }}</h2>
      {%- endif -%}

      <div class="quote-container">
        <span class="quote-icon quote-left">&ldquo;</span>
        {% for block in section.blocks %}
          <div class="quote-text quote-{{ block.id }}" style="display: {% if forloop.first %}block{% else %}none{% endif %};">
            {{ block.settings.quote_text }}
          </div>
        {% endfor %}
        <span class="quote-icon quote-right">&rdquo;</span>
      </div>

      <div class="magazine-logos">
        {% for block in section.blocks %}
          <button 
            class="magazine-logo-btn {% if forloop.first %}active{% endif %}" 
            data-quote="quote-{{ block.id }}"
            {{ block.shopify_attributes }}
          >
            {%- if block.settings.logo_image != blank -%}
              {{ 
                block.settings.logo_image 
                | image_url: width: 200
                | image_tag: 
                  loading: 'lazy',
                  class: 'magazine-logo',
                  widths: '50, 100, 150, 200',
                  alt: block.settings.logo_image.alt | escape
              }}
            {%- else -%}
              <span>{{ block.settings.logo_text }}</span>
            {%- endif -%}
          </button>
        {% endfor %}
      </div>
    </div>
  </div>
</div>

<script type="text/javascript">
  document.addEventListener('DOMContentLoaded', function() {
    var sectionId = '{{ section.id }}';
    var sectionClass = '.icon-with-view-change-' + sectionId;
    var section = document.querySelector(sectionClass);
    
    if (!section) return;
    
    var logoButtons = section.querySelectorAll('.magazine-logo-btn');
    var quoteContents = section.querySelectorAll('.quote-text');
    
    for (var i = 0; i < logoButtons.length; i++) {
      logoButtons[i].onclick = function() {
        var quoteClass = this.getAttribute('data-quote');
        
        // 隐藏所有引用
        for (var j = 0; j < quoteContents.length; j++) {
          quoteContents[j].style.display = 'none';
        }
        
        // 显示当前引用
        var activeQuote = section.querySelector('.' + quoteClass);
        if (activeQuote) {
          activeQuote.style.display = 'block';
        }
        
        // 更新按钮状态
        for (var k = 0; k < logoButtons.length; k++) {
          logoButtons[k].classList.remove('active');
        }
        this.classList.add('active');
      };
    }
  });
  
  // Shopify 编辑器支持
  if (Shopify && Shopify.designMode) {
    document.addEventListener('shopify:section:load', function(event) {
      if (event.detail.sectionId == '{{ section.id }}') {
        var sectionId = '{{ section.id }}';
        var sectionClass = '.icon-with-view-change-' + sectionId;
        var section = document.querySelector(sectionClass);
        
        if (!section) return;
        
        var logoButtons = section.querySelectorAll('.magazine-logo-btn');
        var quoteContents = section.querySelectorAll('.quote-text');
        
        for (var i = 0; i < logoButtons.length; i++) {
          logoButtons[i].onclick = function() {
            var quoteClass = this.getAttribute('data-quote');
            
            // 隐藏所有引用
            for (var j = 0; j < quoteContents.length; j++) {
              quoteContents[j].style.display = 'none';
            }
            
            // 显示当前引用
            var activeQuote = section.querySelector('.' + quoteClass);
            if (activeQuote) {
              activeQuote.style.display = 'block';
            }
            
            // 更新按钮状态
            for (var k = 0; k < logoButtons.length; k++) {
              logoButtons[k].classList.remove('active');
            }
            this.classList.add('active');
          };
        }
      }
    });
  }
</script>

{% schema %}
{
  "name": "Icon with View Change",
  "settings": [
    {
      "type": "text",
      "id": "title",
      "default": "In The Press",
      "label": "Title"
    },
    {
      "type": "select",
      "id": "color_scheme",
      "options": [
        {
          "value": "accent-1",
          "label": "Accent 1"
        },
        {
          "value": "accent-2",
          "label": "Accent 2"
        },
        {
          "value": "background-1",
          "label": "Background 1"
        },
        {
          "value": "background-2",
          "label": "Background 2"
        },
        {
          "value": "inverse",
          "label": "Inverse"
        }
      ],
      "default": "background-1",
      "label": "Color scheme"
    },
    {
      "type": "select",
      "id": "container_width",
      "options": [
        {
          "value": "80",
          "label": "Extra small"
        },
        {
          "value": "100",
          "label": "Small"
        },
        {
          "value": "120",
          "label": "Medium"
        },
        {
          "value": "normal",
          "label": "Normal"
        }
      ],
      "default": "normal",
      "label": "Container width"
    },
    {
      "type": "header",
      "content": "Padding"
    },
    {
      "type": "range",
      "id": "padding_top",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "Padding top",
      "default": 36
    },
    {
      "type": "range",
      "id": "padding_bottom",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "Padding bottom",
      "default": 36
    }
  ],
  "blocks": [
    {
      "type": "magazine",
      "name": "Magazine",
      "settings": [
        {
          "type": "richtext",
          "id": "quote_text",
          "label": "Quote text",
          "default": "<p>She's as bright as the sun!! ✨ ✨ Elastic fabric and tummy control design. It gives you such a natural and comfortable fit.</p>"
        },
        {
          "type": "image_picker",
          "id": "logo_image",
          "label": "Magazine logo"
        },
        {
          "type": "text",
          "id": "logo_text",
          "label": "Logo text (fallback)",
          "default": "Magazine Name",
          "info": "Used if no logo image is selected"
        }
      ]
    }
  ],
  "presets": [
    {
      "name": "Icon with View Change",
      "blocks": [
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>She's as bright as the sun!! ✨ ✨ Elastic fabric and tummy control design. It gives you such a natural and comfortable fit.</p>",
            "logo_text": "VOGUE"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>Flaunt your curves with confidence in Women's High Waisted Bikini Sets. Designed for tummy control, these swimsuits feature a flattering high waist and a color block design that accentuates your figure.</p>",
            "logo_text": "InStyle"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>Get ready to turn heads at the beach or poolside with our Tummy Control Swimsuits. The high waisted bottoms provide extra coverage and support, while the color block pattern adds a stylish touch.</p>",
            "logo_text": "Marie Claire"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>Experience ultimate comfort and style with our Color Block Two Piece Drawstring Bathing Suit.</p>",
            "logo_text": "BAZAAR"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>The combination of a high waist and color block design creates a visually appealing look that enhances your natural curves.</p>",
            "logo_text": "ELLE"
          }
        }
      ]
    }
  ]
}
{% endschema %}


```


version 2
```liuquid
{%- style -%}
  .section-{{ section.id }}-padding {
    padding-top: {{ section.settings.padding_top | times: 0.75 | round: 0 }}px;
    padding-bottom: {{ section.settings.padding_bottom | times: 0.75 | round: 0 }}px;
    {% unless section.settings.container_width == 'normal' %}
      max-width: {{ section.settings.container_width }}rem;
    {% endunless %}
  }

  .icon-with-view-change-{{ section.id }} .section-title {
    margin-bottom: 2rem;
    font-size: calc(2rem + 0.5vw);
    text-align: center;
    font-weight: 600;
    color: {{ section.settings.title_color }};
    position: relative;
  }
  
  .icon-with-view-change-{{ section.id }} .section-title:after {
    content: "";
    display: block;
    width: 80px;
    height: 3px;
    background: {{ section.settings.title_underline_color }};
    margin: 15px auto 0;
  }

  .icon-with-view-change-{{ section.id }} .quote-container {
    background-color: {{ section.settings.quote_background }};
    border-radius: {{ section.settings.quote_border_radius }}px;
    box-shadow: 0 8px 30px rgba(0, 0, 0, 0.08);
    padding: 2.5rem;
    margin-bottom: 2.5rem;
    position: relative;
    min-height: 120px;
    transition: all 0.3s ease;
  }

  .icon-with-view-change-{{ section.id }} .quote-text {
    font-size: {{ section.settings.quote_font_size }}px;
    line-height: 1.7;
    color: {{ section.settings.quote_text_color }};
    text-align: center;
    margin: 0 2rem;
    font-style: italic;
    transition: all 0.3s ease;
  }

  .icon-with-view-change-{{ section.id }} .quote-text p {
    margin: 0;
  }

  .icon-with-view-change-{{ section.id }} .quote-icon {
    color: {{ section.settings.quote_icon_color }};
    font-size: 2rem;
    opacity: 0.5;
  }

  .icon-with-view-change-{{ section.id }} .quote-left {
    position: absolute;
    left: 1.5rem;
    top: 1.5rem;
  }

  .icon-with-view-change-{{ section.id }} .quote-right {
    position: absolute;
    right: 1.5rem;
    bottom: 1rem;
  }

  .icon-with-view-change-{{ section.id }} .magazine-logos {
    display: flex;
    justify-content: space-around;
    align-items: center;
    flex-wrap: wrap;
    gap: 2rem;
    padding: 0.5rem;
  }

  .icon-with-view-change-{{ section.id }} .magazine-logo-btn {
    background: none;
    border: none;
    cursor: pointer;
    opacity: 0.6;
    transition: all 0.4s ease;
    padding: 0.75rem;
    position: relative;
    filter: grayscale({{ section.settings.logo_grayscale }}%);
    border-radius: 8px;
  }

  .icon-with-view-change-{{ section.id }} .magazine-logo-btn:hover,
  .icon-with-view-change-{{ section.id }} .magazine-logo-btn.active {
    opacity: 1;
    transform: translateY(-8px);
    filter: grayscale(0%);
    {% if section.settings.logo_hover_shadow %}
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.07);
    {% endif %}
  }

  .icon-with-view-change-{{ section.id }} .magazine-logo {
    max-width: {{ section.settings.logo_max_width }}px;
    max-height: {{ section.settings.logo_max_height }}px;
    width: auto;
    height: auto;
  }
  
  .icon-with-view-change-{{ section.id }} .logo-text {
    font-weight: 600;
    font-size: 1.1rem;
    color: {{ section.settings.logo_text_color }};
    letter-spacing: 0.5px;
  }

  @media screen and (min-width: 750px) {
    .section-{{ section.id }}-padding {
      padding-top: {{ section.settings.padding_top }}px;
      padding-bottom: {{ section.settings.padding_bottom }}px;
    }
    
    .icon-with-view-change-{{ section.id }} .quote-container {
      margin-left: auto;
      margin-right: auto;
      max-width: 85%;
    }
  }
  
  @media screen and (max-width: 749px) {
    .icon-with-view-change-{{ section.id }} .section-title {
      font-size: 1.5rem;
    }
    
    .icon-with-view-change-{{ section.id }} .quote-container {
      padding: 2rem 1.5rem;
    }
    
    .icon-with-view-change-{{ section.id }} .quote-text {
      font-size: {{ section.settings.quote_font_size | minus: 2 }}px;
      margin: 0 1.5rem;
    }
    
    .icon-with-view-change-{{ section.id }} .magazine-logos {
      gap: 1rem;
    }
    
    .icon-with-view-change-{{ section.id }} .magazine-logo {
      max-width: {{ section.settings.logo_max_width | times: 0.8 }}px;
      max-height: {{ section.settings.logo_max_height | times: 0.8 }}px;
    }
  }
{%- endstyle -%}

<div class="color-scheme-{{ section.id }} color-{{ section.settings.color_scheme }} gradient section-{{ section.id }}-padding">
  <div class="page-width">
    <div class="icon-with-view-change-{{ section.id }}">
      {%- if section.settings.title != blank -%}
        <h2 class="section-title">{{ section.settings.title }}</h2>
      {%- endif -%}

      <div class="quote-container">
        <span class="quote-icon quote-left">&ldquo;</span>
        {% for block in section.blocks %}
          <div class="quote-text quote-{{ block.id }}" style="display: {% if forloop.first %}block{% else %}none{% endif %};">
            {{ block.settings.quote_text }}
          </div>
        {% endfor %}
        <span class="quote-icon quote-right">&rdquo;</span>
      </div>

      <div class="magazine-logos">
        {% for block in section.blocks %}
          <button 
            class="magazine-logo-btn {% if forloop.first %}active{% endif %}" 
            data-quote="quote-{{ block.id }}"
            {{ block.shopify_attributes }}
          >
            {%- if block.settings.logo_image != blank -%}
              {{ 
                block.settings.logo_image 
                | image_url: width: 200
                | image_tag: 
                  loading: 'lazy',
                  class: 'magazine-logo',
                  widths: '50, 100, 150, 200',
                  alt: block.settings.logo_image.alt | escape
              }}
            {%- else -%}
              <span class="logo-text">{{ block.settings.logo_text }}</span>
            {%- endif -%}
          </button>
        {% endfor %}
      </div>
    </div>
  </div>
</div>

<script type="text/javascript">
  document.addEventListener('DOMContentLoaded', function() {
    var sectionId = '{{ section.id }}';
    var sectionClass = '.icon-with-view-change-' + sectionId;
    var section = document.querySelector(sectionClass);
    
    if (!section) return;
    
    var logoButtons = section.querySelectorAll('.magazine-logo-btn');
    var quoteContents = section.querySelectorAll('.quote-text');
    
    for (var i = 0; i < logoButtons.length; i++) {
      logoButtons[i].onclick = function() {
        var quoteClass = this.getAttribute('data-quote');
        
        // 隐藏所有引用
        for (var j = 0; j < quoteContents.length; j++) {
          quoteContents[j].style.display = 'none';
        }
        
        // 显示当前引用
        var activeQuote = section.querySelector('.' + quoteClass);
        if (activeQuote) {
          activeQuote.style.display = 'block';
        }
        
        // 更新按钮状态
        for (var k = 0; k < logoButtons.length; k++) {
          logoButtons[k].classList.remove('active');
        }
        this.classList.add('active');
      };
    }
  });
  
  // Shopify 编辑器支持
  if (Shopify && Shopify.designMode) {
    document.addEventListener('shopify:section:load', function(event) {
      if (event.detail.sectionId == '{{ section.id }}') {
        var sectionId = '{{ section.id }}';
        var sectionClass = '.icon-with-view-change-' + sectionId;
        var section = document.querySelector(sectionClass);
        
        if (!section) return;
        
        var logoButtons = section.querySelectorAll('.magazine-logo-btn');
        var quoteContents = section.querySelectorAll('.quote-text');
        
        for (var i = 0; i < logoButtons.length; i++) {
          logoButtons[i].onclick = function() {
            var quoteClass = this.getAttribute('data-quote');
            
            // 隐藏所有引用
            for (var j = 0; j < quoteContents.length; j++) {
              quoteContents[j].style.display = 'none';
            }
            
            // 显示当前引用
            var activeQuote = section.querySelector('.' + quoteClass);
            if (activeQuote) {
              activeQuote.style.display = 'block';
            }
            
            // 更新按钮状态
            for (var k = 0; k < logoButtons.length; k++) {
              logoButtons[k].classList.remove('active');
            }
            this.classList.add('active');
          };
        }
      }
    });
  }
</script>

{% schema %}
{
  "name": "Icon with View Change",
  "settings": [
    {
      "type": "text",
      "id": "title",
      "default": "In The Press",
      "label": "Title"
    },
    {
      "type": "color",
      "id": "title_color",
      "label": "Title color",
      "default": "#333333"
    },
    {
      "type": "color",
      "id": "title_underline_color",
      "label": "Title underline color",
      "default": "#6D388B"
    },
    {
      "type": "header",
      "content": "Quote Style"
    },
    {
      "type": "color",
      "id": "quote_background",
      "label": "Quote background color",
      "default": "#ffffff"
    },
    {
      "type": "color",
      "id": "quote_text_color",
      "label": "Quote text color",
      "default": "#333333"
    },
    {
      "type": "color",
      "id": "quote_icon_color",
      "label": "Quote icon color",
      "default": "#888888"
    },
    {
      "type": "range",
      "id": "quote_font_size",
      "min": 14,
      "max": 28,
      "step": 1,
      "unit": "px",
      "label": "Quote font size",
      "default": 18
    },
    {
      "type": "range",
      "id": "quote_border_radius",
      "min": 0,
      "max": 20,
      "step": 1,
      "unit": "px",
      "label": "Quote border radius",
      "default": 8
    },
    {
      "type": "header",
      "content": "Logo Style"
    },
    {
      "type": "range",
      "id": "logo_max_width",
      "min": 80,
      "max": 200,
      "step": 10,
      "unit": "px",
      "label": "Maximum logo width",
      "default": 120
    },
    {
      "type": "range",
      "id": "logo_max_height",
      "min": 40,
      "max": 100,
      "step": 5,
      "unit": "px",
      "label": "Maximum logo height",
      "default": 60
    },
    {
      "type": "range",
      "id": "logo_grayscale",
      "min": 0,
      "max": 100,
      "step": 10,
      "unit": "%",
      "label": "Logo grayscale",
      "default": 100,
      "info": "100% means black and white, 0% means full color"
    },
    {
      "type": "checkbox",
      "id": "logo_hover_shadow",
      "label": "Add shadow effect on hover",
      "default": true
    },
    {
      "type": "color",
      "id": "logo_text_color",
      "label": "Logo text color",
      "default": "#555555",
      "info": "For when no logo image is selected"
    },
    {
      "type": "select",
      "id": "color_scheme",
      "options": [
        {
          "value": "accent-1",
          "label": "Accent 1"
        },
        {
          "value": "accent-2",
          "label": "Accent 2"
        },
        {
          "value": "background-1",
          "label": "Background 1"
        },
        {
          "value": "background-2",
          "label": "Background 2"
        },
        {
          "value": "inverse",
          "label": "Inverse"
        }
      ],
      "default": "background-1",
      "label": "Color scheme"
    },
    {
      "type": "select",
      "id": "container_width",
      "options": [
        {
          "value": "80",
          "label": "Extra small"
        },
        {
          "value": "100",
          "label": "Small"
        },
        {
          "value": "120",
          "label": "Medium"
        },
        {
          "value": "normal",
          "label": "Normal"
        }
      ],
      "default": "normal",
      "label": "Container width"
    },
    {
      "type": "header",
      "content": "Padding"
    },
    {
      "type": "range",
      "id": "padding_top",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "Padding top",
      "default": 36
    },
    {
      "type": "range",
      "id": "padding_bottom",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "Padding bottom",
      "default": 36
    }
  ],
  "blocks": [
    {
      "type": "magazine",
      "name": "Magazine",
      "settings": [
        {
          "type": "richtext",
          "id": "quote_text",
          "label": "Quote text",
          "default": "<p>She's as bright as the sun!! ✨ ✨ Elastic fabric and tummy control design. It gives you such a natural and comfortable fit.</p>"
        },
        {
          "type": "image_picker",
          "id": "logo_image",
          "label": "Magazine logo"
        },
        {
          "type": "text",
          "id": "logo_text",
          "label": "Logo text (fallback)",
          "default": "Magazine Name",
          "info": "Used if no logo image is selected"
        }
      ]
    }
  ],
  "presets": [
    {
      "name": "Icon with View Change",
      "blocks": [
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>She's as bright as the sun!! ✨ ✨ Elastic fabric and tummy control design. It gives you such a natural and comfortable fit.</p>",
            "logo_text": "VOGUE"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>Flaunt your curves with confidence in Women's High Waisted Bikini Sets. Designed for tummy control, these swimsuits feature a flattering high waist and a color block design that accentuates your figure.</p>",
            "logo_text": "InStyle"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>Get ready to turn heads at the beach or poolside with our Tummy Control Swimsuits. The high waisted bottoms provide extra coverage and support, while the color block pattern adds a stylish touch.</p>",
            "logo_text": "Marie Claire"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>Experience ultimate comfort and style with our Color Block Two Piece Drawstring Bathing Suit.</p>",
            "logo_text": "BAZAAR"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>The combination of a high waist and color block design creates a visually appealing look that enhances your natural curves.</p>",
            "logo_text": "ELLE"
          }
        }
      ]
    }
  ]
}
{% endschema %}

```



version3
```liqiuid
{%- style -%}
  .section-{{ section.id }}-padding {
    padding-top: {{ section.settings.padding_top | times: 0.75 | round: 0 }}px;
    padding-bottom: {{ section.settings.padding_bottom | times: 0.75 | round: 0 }}px;
    {% unless section.settings.container_width == 'normal' %}
      max-width: {{ section.settings.container_width }}rem;
    {% endunless %}
  }

  .icon-with-view-change-{{ section.id }} .section-title {
    margin-bottom: 2rem;
    font-size: calc(2rem + 0.5vw);
    text-align: center;
    font-weight: 600;
    color: {{ section.settings.title_color }};
    position: relative;
  }
  
  .icon-with-view-change-{{ section.id }} .section-title:after {
    content: "";
    display: block;
    width: 80px;
    height: 3px;
    background: {{ section.settings.title_underline_color }};
    margin: 15px auto 0;
  }

  .icon-with-view-change-{{ section.id }} .quote-container {
    background-color: {{ section.settings.quote_background }};
    border-radius: {{ section.settings.quote_border_radius }}px;
    box-shadow: 0 8px 30px rgba(0, 0, 0, 0.08);
    padding: 2.5rem;
    margin-bottom: 2.5rem;
    position: relative;
    min-height: 120px;
    transition: all 0.3s ease;
  }

  .icon-with-view-change-{{ section.id }} .quote-text {
    font-size: {{ section.settings.quote_font_size }}px;
    line-height: 1.7;
    color: {{ section.settings.quote_text_color }};
    text-align: center;
    margin: 0 2rem;
    font-style: italic;
    transition: all 0.3s ease;
  }

  .icon-with-view-change-{{ section.id }} .quote-text p {
    margin: 0;
  }

  .icon-with-view-change-{{ section.id }} .quote-icon {
    color: {{ section.settings.quote_icon_color }};
    font-size: 2rem;
    opacity: 0.5;
  }

  .icon-with-view-change-{{ section.id }} .quote-left {
    position: absolute;
    left: 1.5rem;
    top: 1.5rem;
  }

  .icon-with-view-change-{{ section.id }} .quote-right {
    position: absolute;
    right: 1.5rem;
    bottom: 1rem;
  }

  .icon-with-view-change-{{ section.id }} .magazine-logos {
    display: flex;
    justify-content: space-around;
    align-items: center;
    flex-wrap: wrap;
    gap: 2rem;
    padding: 0.5rem;
  }

  .icon-with-view-change-{{ section.id }} .magazine-logo-btn {
    background: none;
    border: none;
    cursor: pointer;
    opacity: 0.6;
    transition: all 0.4s ease;
    padding: 0.75rem;
    position: relative;
    filter: grayscale({{ section.settings.logo_grayscale }}%);
    border-radius: 8px;
  }

  .icon-with-view-change-{{ section.id }} .magazine-logo-btn:hover,
  .icon-with-view-change-{{ section.id }} .magazine-logo-btn.active {
    opacity: 1;
    transform: translateY(-8px);
    filter: grayscale(0%);
    {% if section.settings.logo_hover_shadow %}
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.07);
    {% endif %}
  }

  .icon-with-view-change-{{ section.id }} .magazine-logo {
    max-width: {{ section.settings.logo_max_width }}px;
    max-height: {{ section.settings.logo_max_height }}px;
    width: auto;
    height: auto;
  }
  
  .icon-with-view-change-{{ section.id }} .logo-text {
    font-weight: 600;
    font-size: 1.1rem;
    color: {{ section.settings.logo_text_color }};
    letter-spacing: 0.5px;
  }

  @media screen and (min-width: 750px) {
    .section-{{ section.id }}-padding {
      padding-top: {{ section.settings.padding_top }}px;
      padding-bottom: {{ section.settings.padding_bottom }}px;
    }
    
    .icon-with-view-change-{{ section.id }} .quote-container {
      margin-left: auto;
      margin-right: auto;
      max-width: 85%;
    }
  }
  
  @media screen and (max-width: 749px) {
    .icon-with-view-change-{{ section.id }} .section-title {
      font-size: 1.5rem;
    }
    
    .icon-with-view-change-{{ section.id }} .quote-container {
      padding: 2rem 1.5rem;
    }
    
    .icon-with-view-change-{{ section.id }} .quote-text {
      font-size: {{ section.settings.quote_font_size | minus: 2 }}px;
      margin: 0 1.5rem;
    }
    
    .icon-with-view-change-{{ section.id }} .magazine-logos {
      gap: 1rem;
    }
    
    .icon-with-view-change-{{ section.id }} .magazine-logo {
      max-width: {{ section.settings.logo_max_width | times: 0.8 }}px;
      max-height: {{ section.settings.logo_max_height | times: 0.8 }}px;
    }
  }
{%- endstyle -%}

<div class="color-scheme-{{ section.id }} color-{{ section.settings.color_scheme }} gradient section-{{ section.id }}-padding">
  <div class="page-width">
    <div class="icon-with-view-change-{{ section.id }}">
      {%- if section.settings.title != blank -%}
        <h2 class="section-title">{{ section.settings.title }}</h2>
      {%- endif -%}

      <div class="quote-container">
        <span class="quote-icon quote-left">&ldquo;</span>
        {% for block in section.blocks %}
          <div class="quote-text quote-{{ block.id }}" style="display: {% if forloop.first %}block{% else %}none{% endif %};">
            {{ block.settings.quote_text }}
          </div>
        {% endfor %}
        <span class="quote-icon quote-right">&rdquo;</span>
      </div>

      <div class="magazine-logos">
        {% for block in section.blocks %}
          <button 
            class="magazine-logo-btn {% if forloop.first %}active{% endif %}" 
            data-quote="quote-{{ block.id }}"
            {{ block.shopify_attributes }}
          >
            {%- if block.settings.logo_image != blank -%}
              {{ 
                block.settings.logo_image 
                | image_url: width: 200
                | image_tag: 
                  loading: 'lazy',
                  class: 'magazine-logo',
                  widths: '50, 100, 150, 200',
                  alt: block.settings.logo_image.alt | escape
              }}
            {%- elsif block.settings.fallback_image != blank -%}
              {{ 
                block.settings.fallback_image 
                | image_url: width: 200
                | image_tag: 
                  loading: 'lazy',
                  class: 'magazine-logo',
                  widths: '50, 100, 150, 200',
                  alt: block.settings.fallback_image.alt | escape
              }}
            {%- else -%}
              <span class="logo-text">{{ block.settings.logo_text }}</span>
            {%- endif -%}
          </button>
        {% endfor %}
      </div>
    </div>
  </div>
</div>

<script type="text/javascript">
  document.addEventListener('DOMContentLoaded', function() {
    var sectionId = '{{ section.id }}';
    var sectionClass = '.icon-with-view-change-' + sectionId;
    var section = document.querySelector(sectionClass);
    
    if (!section) return;
    
    var logoButtons = section.querySelectorAll('.magazine-logo-btn');
    var quoteContents = section.querySelectorAll('.quote-text');
    
    for (var i = 0; i < logoButtons.length; i++) {
      logoButtons[i].onclick = function() {
        var quoteClass = this.getAttribute('data-quote');
        
        // 隐藏所有引用
        for (var j = 0; j < quoteContents.length; j++) {
          quoteContents[j].style.display = 'none';
        }
        
        // 显示当前引用
        var activeQuote = section.querySelector('.' + quoteClass);
        if (activeQuote) {
          activeQuote.style.display = 'block';
        }
        
        // 更新按钮状态
        for (var k = 0; k < logoButtons.length; k++) {
          logoButtons[k].classList.remove('active');
        }
        this.classList.add('active');
      };
    }
  });
  
  // Shopify 编辑器支持
  if (Shopify && Shopify.designMode) {
    document.addEventListener('shopify:section:load', function(event) {
      if (event.detail.sectionId == '{{ section.id }}') {
        var sectionId = '{{ section.id }}';
        var sectionClass = '.icon-with-view-change-' + sectionId;
        var section = document.querySelector(sectionClass);
        
        if (!section) return;
        
        var logoButtons = section.querySelectorAll('.magazine-logo-btn');
        var quoteContents = section.querySelectorAll('.quote-text');
        
        for (var i = 0; i < logoButtons.length; i++) {
          logoButtons[i].onclick = function() {
            var quoteClass = this.getAttribute('data-quote');
            
            // 隐藏所有引用
            for (var j = 0; j < quoteContents.length; j++) {
              quoteContents[j].style.display = 'none';
            }
            
            // 显示当前引用
            var activeQuote = section.querySelector('.' + quoteClass);
            if (activeQuote) {
              activeQuote.style.display = 'block';
            }
            
            // 更新按钮状态
            for (var k = 0; k < logoButtons.length; k++) {
              logoButtons[k].classList.remove('active');
            }
            this.classList.add('active');
          };
        }
      }
    });
  }
</script>

{% schema %}
{
  "name": "Icon with View Change",
  "settings": [
    {
      "type": "text",
      "id": "title",
      "default": "In The Press",
      "label": "Title"
    },
    {
      "type": "color",
      "id": "title_color",
      "label": "Title color",
      "default": "#333333"
    },
    {
      "type": "color",
      "id": "title_underline_color",
      "label": "Title underline color",
      "default": "#6D388B"
    },
    {
      "type": "header",
      "content": "Quote Style"
    },
    {
      "type": "color",
      "id": "quote_background",
      "label": "Quote background color",
      "default": "#ffffff"
    },
    {
      "type": "color",
      "id": "quote_text_color",
      "label": "Quote text color",
      "default": "#333333"
    },
    {
      "type": "color",
      "id": "quote_icon_color",
      "label": "Quote icon color",
      "default": "#888888"
    },
    {
      "type": "range",
      "id": "quote_font_size",
      "min": 14,
      "max": 28,
      "step": 1,
      "unit": "px",
      "label": "Quote font size",
      "default": 18
    },
    {
      "type": "range",
      "id": "quote_border_radius",
      "min": 0,
      "max": 20,
      "step": 1,
      "unit": "px",
      "label": "Quote border radius",
      "default": 8
    },
    {
      "type": "header",
      "content": "Logo Style"
    },
    {
      "type": "range",
      "id": "logo_max_width",
      "min": 80,
      "max": 200,
      "step": 10,
      "unit": "px",
      "label": "Maximum logo width",
      "default": 120
    },
    {
      "type": "range",
      "id": "logo_max_height",
      "min": 40,
      "max": 100,
      "step": 5,
      "unit": "px",
      "label": "Maximum logo height",
      "default": 60
    },
    {
      "type": "range",
      "id": "logo_grayscale",
      "min": 0,
      "max": 100,
      "step": 10,
      "unit": "%",
      "label": "Logo grayscale",
      "default": 100,
      "info": "100% means black and white, 0% means full color"
    },
    {
      "type": "checkbox",
      "id": "logo_hover_shadow",
      "label": "Add shadow effect on hover",
      "default": true
    },
    {
      "type": "color",
      "id": "logo_text_color",
      "label": "Logo text color",
      "default": "#555555",
      "info": "For when no logo image is selected"
    },
    {
      "type": "select",
      "id": "color_scheme",
      "options": [
        {
          "value": "accent-1",
          "label": "Accent 1"
        },
        {
          "value": "accent-2",
          "label": "Accent 2"
        },
        {
          "value": "background-1",
          "label": "Background 1"
        },
        {
          "value": "background-2",
          "label": "Background 2"
        },
        {
          "value": "inverse",
          "label": "Inverse"
        }
      ],
      "default": "background-1",
      "label": "Color scheme"
    },
    {
      "type": "select",
      "id": "container_width",
      "options": [
        {
          "value": "80",
          "label": "Extra small"
        },
        {
          "value": "100",
          "label": "Small"
        },
        {
          "value": "120",
          "label": "Medium"
        },
        {
          "value": "normal",
          "label": "Normal"
        }
      ],
      "default": "normal",
      "label": "Container width"
    },
    {
      "type": "header",
      "content": "Padding"
    },
    {
      "type": "range",
      "id": "padding_top",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "Padding top",
      "default": 36
    },
    {
      "type": "range",
      "id": "padding_bottom",
      "min": 0,
      "max": 100,
      "step": 4,
      "unit": "px",
      "label": "Padding bottom",
      "default": 36
    }
  ],
  "blocks": [
    {
      "type": "magazine",
      "name": "Magazine",
      "settings": [
        {
          "type": "richtext",
          "id": "quote_text",
          "label": "Quote text",
          "default": "<p>She's as bright as the sun!! ✨ ✨ Elastic fabric and tummy control design. It gives you such a natural and comfortable fit.</p>"
        },
        {
          "type": "header",
          "content": "Logo Options"
        },
        {
          "type": "image_picker",
          "id": "logo_image",
          "label": "Primary Logo Image"
        },
        {
          "type": "image_picker",
          "id": "fallback_image",
          "label": "Fallback Image",
          "info": "Used if primary logo image is not available"
        },
        {
          "type": "text",
          "id": "logo_text",
          "label": "Text Fallback",
          "default": "Magazine Name",
          "info": "Used only if no images are selected"
        }
      ]
    }
  ],
  "presets": [
    {
      "name": "Icon with View Change",
      "blocks": [
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>She's as bright as the sun!! ✨ ✨ Elastic fabric and tummy control design. It gives you such a natural and comfortable fit.</p>",
            "logo_text": "VOGUE"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>Flaunt your curves with confidence in Women's High Waisted Bikini Sets. Designed for tummy control, these swimsuits feature a flattering high waist and a color block design that accentuates your figure.</p>",
            "logo_text": "InStyle"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>Get ready to turn heads at the beach or poolside with our Tummy Control Swimsuits. The high waisted bottoms provide extra coverage and support, while the color block pattern adds a stylish touch.</p>",
            "logo_text": "Marie Claire"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>Experience ultimate comfort and style with our Color Block Two Piece Drawstring Bathing Suit.</p>",
            "logo_text": "BAZAAR"
          }
        },
        {
          "type": "magazine",
          "settings": {
            "quote_text": "<p>The combination of a high waist and color block design creates a visually appealing look that enhances your natural curves.</p>",
            "logo_text": "ELLE"
          }
        }
      ]
    }
  ]
}
{% endschema %}

```