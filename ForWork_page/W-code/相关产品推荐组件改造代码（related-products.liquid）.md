```liquid
<div>
  
  <product-recommendations

    class="related-products page-width section-{{ section.id }}-padding isolate"

    {% unless section.settings.use_fixed_products %}

      data-url="{{ routes.product_recommendations_url }}?section_id={{ section.id }}&product_id={{ product.id }}&limit={{ section.settings.products_to_show }}"

    {% endunless %}

  >

    {% if section.settings.use_fixed_products %}

      {%- comment -%} 使用固定推荐产品 {%- endcomment -%}

      <h2

        class="related-products__heading {{ section.settings.heading_size }} title-with-highlight animate-item animate-item--child index-0"

        style="--hightlight-color:{{ section.settings.title_highlight_color }}"

      >

        {{ section.settings.title | default: 'You may also like' }}

      </h2>

      <ul

        class="grid product-grid grid--{{ section.settings.columns_desktop }}-col-desktop grid--{{ section.settings.columns_mobile }}-col-tablet-down{% if section.settings.stretch_cards %} grid--stretch{% endif %}"

        role="list"

      >

        {% assign product_index = 0 %}

  

        {% if section.settings.fixed_product_1.id and section.settings.fixed_product_1.id != product.id %}

          <li class="grid__item animate-item animate-item--child" style="--index:{{ product_index }};">

            {% render 'card-product',

              card_product: section.settings.fixed_product_1,

              media_aspect_ratio: section.settings.image_ratio,

              show_secondary_image: section.settings.show_secondary_image,

              show_vendor: section.settings.show_vendor,

              show_quick_add: section.settings.enable_quick_add,

              show_rating: section.settings.show_rating

            %}

          </li>

          {% assign product_index = product_index | plus: 1 %}

        {% endif %}

  

        {% if section.settings.fixed_product_2.id

          and section.settings.fixed_product_2.id != product.id

          and product_index < section.settings.products_to_show

        %}

          <li class="grid__item animate-item animate-item--child" style="--index:{{ product_index }};">

            {% render 'card-product',

              card_product: section.settings.fixed_product_2,

              media_aspect_ratio: section.settings.image_ratio,

              show_secondary_image: section.settings.show_secondary_image,

              show_vendor: section.settings.show_vendor,

              show_quick_add: section.settings.enable_quick_add,

              show_rating: section.settings.show_rating

            %}

          </li>

          {% assign product_index = product_index | plus: 1 %}

        {% endif %}

  

        {% if section.settings.fixed_product_3.id

          and section.settings.fixed_product_3.id != product.id

          and product_index < section.settings.products_to_show

        %}

          <li class="grid__item animate-item animate-item--child" style="--index:{{ product_index }};">

            {% render 'card-product',

              card_product: section.settings.fixed_product_3,

              media_aspect_ratio: section.settings.image_ratio,

              show_secondary_image: section.settings.show_secondary_image,

              show_vendor: section.settings.show_vendor,

              show_quick_add: section.settings.enable_quick_add,

              show_rating: section.settings.show_rating

            %}

          </li>

          {% assign product_index = product_index | plus: 1 %}

        {% endif %}

  

        {% if section.settings.fixed_product_4.id

          and section.settings.fixed_product_4.id != product.id

          and product_index < section.settings.products_to_show

        %}

          <li class="grid__item animate-item animate-item--child" style="--index:{{ product_index }};">

            {% render 'card-product',

              card_product: section.settings.fixed_product_4,

              media_aspect_ratio: section.settings.image_ratio,

              show_secondary_image: section.settings.show_secondary_image,

              show_vendor: section.settings.show_vendor,

              show_quick_add: section.settings.enable_quick_add,

              show_rating: section.settings.show_rating

            %}

          </li>

        {% endif %}

      </ul>

    {% else %}

      {%- comment -%} 使用算法推荐产品（原逻辑） {%- endcomment -%}

      {% if recommendations.performed and recommendations.products_count > 0 %}

        <h2

          class="related-products__heading {{ section.settings.heading_size }} title-with-highlight animate-item animate-item--child index-0"

          style="--hightlight-color:{{ section.settings.title_highlight_color }}"

        >

          {{ section.settings.title }}

        </h2>

        <ul

          class="grid product-grid grid--{{ section.settings.columns_desktop }}-col-desktop grid--{{ section.settings.columns_mobile }}-col-tablet-down{% if section.settings.stretch_cards %} grid--stretch{% endif %}"

          role="list"

        >

          {% for recommendation in recommendations.products %}

            {% unless recommendation.tags contains 'collection-hidden' %}

              <li class="grid__item animate-item animate-item--child" style="--index:{{ forloop.index0 }};">

                {% render 'card-product',

                  card_product: recommendation,

                  media_aspect_ratio: section.settings.image_ratio,

                  show_secondary_image: section.settings.show_secondary_image,

                  show_vendor: section.settings.show_vendor,

                  show_quick_add: section.settings.enable_quick_add,

                  show_rating: section.settings.show_rating

                %}

              </li>

            {% endunless %}

          {% endfor %}

        </ul>

      {% endif %}

    {% endif %}

  </product-recommendations>
  </div>
  
  {% schema %}

    {

    "type": "header",

    "content": "固定推荐产品设置"

    },

    {

    "type": "checkbox",

    "id": "use_fixed_products",

    "label": "使用固定推荐产品",

    "info": "开启后将显示手动选择的产品，而非算法推荐产品",

    "default": false

    },

    {

    "type": "product",

    "id": "fixed_product_1",

    "label": "固定推荐产品 1"

    },

    {

    "type": "product",

    "id": "fixed_product_2",

    "label": "固定推荐产品 2"

    },

    {

    "type": "product",

    "id": "fixed_product_3",

    "label": "固定推荐产品 3"

    },

    {

    "type": "product",

    "id": "fixed_product_4",

    "label": "固定推荐产品 4"

    },
{% endschema %}
```