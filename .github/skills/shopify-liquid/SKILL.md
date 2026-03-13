---
name: shopify-liquid
description: 'Convert HTML prototypes to Shopify Liquid sections. Use for: HTML-to-Liquid translation, Liquid syntax debugging, section file creation, Shopify theme development, fixing Liquid render errors, product loops, collection grids, cart drawer logic, Section Rendering API, variant pickers, metafields.'
argument-hint: 'Describe the HTML file or Liquid task'
---

# Shopify Liquid Conversion & Development

## When to Use
- Converting standalone HTML prototypes into Shopify `.liquid` section files
- Writing or debugging Liquid template syntax
- Creating product loops, collection grids, cart drawers
- Implementing Section Rendering API for dynamic updates
- Fixing Liquid render errors or syntax issues
- Building variant pickers, add-to-cart forms

---

## Optimaxxing Project Context

### Folder Structure
```
/HTML test code/           → Standalone HTML prototypes (no Liquid)
/Liquid Code for prod use/ → Production .liquid sections + .json templates
```
- `.liquid` files deploy to `Sections/` in Shopify theme
- `.json` files deploy to `Templates/` in Shopify theme

### The 7 Protocols
| # | Collection Handle | Name |
|---|-------------------|------|
| 01 | `facemaxx-protocol` | Facemaxx |
| 02 | `sleepmaxx-protocol` | Sleepmaxx |
| 03 | `gymmaxx-protocol` | Gymmaxx |
| 04 | `framemaxx-protocol` | Framemaxx |
| 05 | `stylemaxx-protocol` | Stylemaxx |
| 06 | `mindmaxx-protocol` | Mindmaxx |
| 07 | `cleanmaxx-protocol` | Cleanmaxx |

Products are **tagged** with lowercase protocol name (`facemaxx`, `sleepmaxx`, etc.) for auto-detection.

### CSS Variables (defined in `optimaxxing-header.liquid`)
```css
--black:  #050505   /* page background */
--neon:   #C8FF00   /* primary accent — lime green */
--white:  #F5F5F5   /* body text */
--gray:   #141414   /* card backgrounds */
--mid:    #202020   /* hover states */
--dim:    #888      /* secondary text */
--border: #1e1e1e   /* dividers */
```
**Exception:** Cleanmaxx (Protocol 07) uses `#00CFFF` (cyan) instead of neon green.

### Typography
| Font | Use |
|------|-----|
| `'Bebas Neue'` | Headings, display text |
| `'Space Mono'` | Labels, nav, badges, buttons, prices |
| `'Inter'` | Body copy, descriptions |

### Layout Rules
- **2px gap** between grid cards (intentional design)
- Product grids: `grid-template-columns: repeat(auto-fill, minmax(260px, 1fr))`
- Section padding: `96px 48px` desktop / `56px 20px` mobile (768px breakpoint)

---

## Conversion Procedure: HTML → Liquid

### 1. Analyze the HTML Prototype
- Identify static content that needs dynamic Shopify data
- Map HTML elements to Liquid objects: `product`, `collection`, `cart`, `shop`
- Note inline styles that reference CSS variables

### 2. Replace Static Content with Liquid Objects

| HTML Pattern | Liquid Replacement |
|--------------|-------------------|
| Hard-coded product name | `{{ product.title }}` |
| Hard-coded price | `{{ product.price | money }}` |
| Static image `src` | `{{ product.featured_image | image_url: width: 600 }}` |
| Product list | `{% for product in collection.products %}` |
| Conditional display | `{% if product.available %}` |
| Short tagline | `{{ product.metafields.custom.short_description }}` |

### 3. Section Schema (Bottom of .liquid file)
```liquid
{% schema %}
{
  "name": "Section Name",
  "settings": [],
  "presets": [{ "name": "Section Name" }]
}
{% endschema %}
```

### 4. Asset References
```liquid
<!-- External fonts (inline in <style>) -->
@import url('https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Inter:wght@400;500;600&family=Space+Mono:wght@400;700&display=swap');

<!-- Theme assets -->
{{ 'custom.css' | asset_url | stylesheet_tag }}
{{ 'logo.png' | asset_url }}
```

---

## Core Liquid Patterns

### Product Loop (Collection Grid)
```liquid
{% for product in collection.products limit: 8 %}
  <a href="{{ product.url }}" class="product-card">
    <div class="product-image">
      <img src="{{ product.featured_image | image_url: width: 400 }}" 
           alt="{{ product.title | escape }}" loading="lazy">
    </div>
    <div class="product-info">
      <span class="product-tag">{{ collection.title | upcase }}</span>
      <h3>{{ product.title }}</h3>
      <p class="product-tagline">{{ product.metafields.custom.short_description }}</p>
      <span class="product-price">{{ product.price | money }}</span>
    </div>
  </a>
{% endfor %}
```

### Protocol Detection via Tags
```liquid
{% assign protocol_name = '' %}
{% assign protocol_color = 'var(--neon)' %}

{% for tag in product.tags %}
  {% case tag %}
    {% when 'facemaxx' %}
      {% assign protocol_name = 'FACEMAXX PROTOCOL' %}
    {% when 'sleepmaxx' %}
      {% assign protocol_name = 'SLEEPMAXX PROTOCOL' %}
    {% when 'gymmaxx' %}
      {% assign protocol_name = 'GYMMAXX PROTOCOL' %}
    {% when 'framemaxx' %}
      {% assign protocol_name = 'FRAMEMAXX PROTOCOL' %}
    {% when 'stylemaxx' %}
      {% assign protocol_name = 'STYLEMAXX PROTOCOL' %}
    {% when 'mindmaxx' %}
      {% assign protocol_name = 'MINDMAXX PROTOCOL' %}
    {% when 'cleanmaxx' %}
      {% assign protocol_name = 'CLEANMAXX PROTOCOL' %}
      {% assign protocol_color = '#00CFFF' %}
  {% endcase %}
{% endfor %}
```

### Cart Drawer with Section Rendering API
```liquid
<div id="cart-drawer" data-section-id="{{ section.id }}">
  <div class="cart-header">
    <span>YOUR PROTOCOL ({{ cart.item_count }})</span>
    <button class="cart-close" aria-label="Close cart">×</button>
  </div>
  
  {% if cart.item_count > 0 %}
    {% for item in cart.items %}
      <div class="cart-item" data-line="{{ forloop.index }}">
        <img src="{{ item.image | image_url: width: 100 }}" alt="{{ item.title | escape }}">
        <div class="cart-item-info">
          <span class="cart-item-title">{{ item.product.title }}</span>
          <span class="cart-item-variant">{{ item.variant.title }}</span>
          <span class="cart-item-price">{{ item.final_line_price | money }}</span>
        </div>
        <div class="cart-item-quantity">
          <button class="qty-btn" data-action="decrease">−</button>
          <span>{{ item.quantity }}</span>
          <button class="qty-btn" data-action="increase">+</button>
        </div>
      </div>
    {% endfor %}
    
    <div class="cart-footer">
      <div class="cart-total">
        <span>TOTAL</span>
        <span>{{ cart.total_price | money }}</span>
      </div>
      <a href="/checkout" class="checkout-btn">CHECKOUT</a>
    </div>
  {% else %}
    <p class="cart-empty">Your protocol is empty</p>
  {% endif %}
</div>
```

**JavaScript for Section Rendering API refresh:**
```javascript
async function refreshCart() {
  const res = await fetch('/?sections=optimaxxing-header');
  const data = await res.json();
  const parser = new DOMParser();
  const doc = parser.parseFromString(data['optimaxxing-header'], 'text/html');
  
  document.getElementById('cart-drawer').innerHTML = 
    doc.getElementById('cart-drawer').innerHTML;
  
  // Update cart count in nav
  const newCount = doc.querySelector('.cart-count');
  document.querySelector('.cart-count').textContent = newCount.textContent;
}
```

### Variant Picker
```liquid
<div class="variant-picker">
  {% for variant in product.variants %}
    <button 
      class="variant-btn{% if forloop.first %} active{% endif %}"
      data-variant-id="{{ variant.id }}"
      data-price="{{ variant.price | money }}"
      {% unless variant.available %}disabled{% endunless %}>
      {{ variant.title }}
      {% unless variant.available %}<span class="sold-out">SOLD OUT</span>{% endunless %}
    </button>
  {% endfor %}
</div>

<input type="hidden" name="id" value="{{ product.selected_or_first_available_variant.id }}">
```

### Add to Cart Form
```liquid
<form action="/cart/add" method="post" id="product-form">
  <input type="hidden" name="id" value="{{ product.selected_or_first_available_variant.id }}">
  <input type="hidden" name="quantity" value="1">
  
  <button type="submit" class="add-to-cart-btn" 
    {% unless product.available %}disabled{% endunless %}>
    {% if product.available %}
      ADD TO PROTOCOL — {{ product.price | money }}
    {% else %}
      SOLD OUT
    {% endif %}
  </button>
</form>
```

### Collection Tabs (Protocol Switcher)
```liquid
<div class="protocol-tabs">
  {% assign protocols = 'facemaxx-protocol,sleepmaxx-protocol,gymmaxx-protocol,framemaxx-protocol,stylemaxx-protocol,mindmaxx-protocol,cleanmaxx-protocol' | split: ',' %}
  
  {% for handle in protocols %}
    {% assign coll = collections[handle] %}
    {% if coll %}
      <button class="protocol-tab{% if forloop.first %} active{% endif %}" 
              data-collection="{{ handle }}">
        {{ coll.title | remove: ' Protocol' | upcase }}
      </button>
    {% endif %}
  {% endfor %}
</div>

<div class="protocol-grids">
  {% for handle in protocols %}
    {% assign coll = collections[handle] %}
    {% if coll %}
      <div class="protocol-grid{% if forloop.first %} active{% endif %}" 
           data-collection="{{ handle }}">
        {% for product in coll.products limit: 4 %}
          <!-- product card -->
        {% endfor %}
      </div>
    {% endif %}
  {% endfor %}
</div>
```

---

## Common Liquid Filters

| Filter | Purpose | Example |
|--------|---------|---------|
| `money` | Format currency | `{{ price | money }}` |
| `money_without_trailing_zeros` | Clean price | `{{ price | money_without_trailing_zeros }}` |
| `image_url` | Generate image URL | `{{ img | image_url: width: 800 }}` |
| `asset_url` | Theme asset path | `{{ 'app.js' | asset_url }}` |
| `handle` | URL-safe string | `{{ title | handle }}` |
| `escape` | HTML escape | `{{ text | escape }}` |
| `json` | Output as JSON | `{{ product | json }}` |
| `upcase` | Uppercase | `{{ title | upcase }}` |
| `remove` | Remove substring | `{{ title | remove: ' Protocol' }}` |
| `split` | String to array | `{{ list | split: ',' }}` |
| `t` | Translation | `{{ 'cart.title' | t }}` |

---

## JSON Page Templates

### Basic Template Structure
```json
{
  "sections": {
    "header": {
      "type": "optimaxxing-header"
    },
    "main": {
      "type": "optimaxxing-landing"
    }
  },
  "order": ["header", "main"]
}
```

### Product Template
```json
{
  "sections": {
    "header": {
      "type": "optimaxxing-header"
    },
    "main": {
      "type": "optimaxxing-product"
    }
  },
  "order": ["header", "main"]
}
```

---

## Debugging Liquid Errors

1. **Check for unclosed tags**: Every `{% if %}` needs `{% endif %}`, every `{% for %}` needs `{% endfor %}`

2. **Verify object availability**:
   ```liquid
   {% if product %}
     {{ product.title }}
   {% endif %}
   ```

3. **Inspect data with JSON filter**:
   ```liquid
   <script>console.log({{ product | json }});</script>
   ```

4. **Check handles match Shopify admin**: Collection handle `facemaxx-protocol` must match exactly

5. **Common syntax errors**:
   - Missing `%` in tags: `{ if }` vs `{% if %}`
   - Using `=` instead of `==` in comparisons
   - Forgetting `forloop.index` is 1-based

---

## Optimaxxing Section Files Reference

| File | Purpose |
|------|---------|
| `optimaxxing-header.liquid` | Nav, cart drawer, footer, global CSS vars |
| `optimaxxing-landing.liquid` | Homepage with protocol tabs + product grids |
| `optimaxxing-product.liquid` | PDP with variant picker, ATC, tabs |
| `optimaxxing-collection.liquid` | Collection listing page |
| `optimaxxing-bundles.liquid` | Bundle products page |
| `optimaxxing-quiz.liquid` | Protocol recommendation quiz |
| `optimaxxing-about.liquid` | Mission/philosophy page |

---

## Suppressing Default Shopify Header

Add to global CSS (in header section):
```css
.section-header, 
header-drawer, 
.header-wrapper { 
  display: none !important; 
}
```

---

## Judge.me Reviews (Dark Theme)

Include in product section:
```css
.jdgm-widget {
  --jdgm-primary-color: var(--neon) !important;
  --jdgm-star-color: var(--neon) !important;
  --jdgm-widget-bg: var(--gray) !important;
  --jdgm-widget-text: var(--white) !important;
}
.jdgm-rev__body,
.jdgm-rev__author {
  color: var(--white) !important;
}
```
