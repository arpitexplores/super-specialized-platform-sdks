## Source: references/skills/shopify-development/SKILL.md

---
name: shopify-development
description: Build Shopify apps, extensions, themes using GraphQL Admin API, Shopify CLI, Polaris UI, and Liquid.
risk: unknown
source: community
date_added: '2026-02-27'
---

# Shopify Development Skill

Use this skill when the user asks about:

- Building Shopify apps or extensions
- Creating checkout/admin/POS UI customizations
- Developing themes with Liquid templating
- Integrating with Shopify GraphQL or REST APIs
- Implementing webhooks or billing
- Working with metafields or Shopify Functions

---

## ROUTING: What to Build

**IF user wants to integrate external services OR build merchant tools OR charge for features:**
→ Build an **App** (see `references/app-development.md`)

**IF user wants to customize checkout OR add admin UI OR create POS actions OR implement discount rules:**
→ Build an **Extension** (see `references/extensions.md`)

**IF user wants to customize storefront design OR modify product/collection pages:**
→ Build a **Theme** (see `references/themes.md`)

**IF user needs both backend logic AND storefront UI:**
→ Build **App + Theme Extension** combination

---

## Shopify CLI Commands

Install CLI:

```bash
npm install -g @shopify/cli@latest
```

Create and run app:

```bash
shopify app init          # Create new app
shopify app dev           # Start dev server with tunnel
shopify app deploy        # Build and upload to Shopify
```

Generate extension:

```bash
shopify app generate extension --type checkout_ui_extension
shopify app generate extension --type admin_action
shopify app generate extension --type admin_block
shopify app generate extension --type pos_ui_extension
shopify app generate extension --type function
```

Theme development:

```bash
shopify theme init        # Create new theme
shopify theme dev         # Start local preview at localhost:9292
shopify theme pull --live # Pull live theme
shopify theme push --development  # Push to dev theme
```

---

## Access Scopes

Configure in `shopify.app.toml`:

```toml
[access_scopes]
scopes = "read_products,write_products,read_orders,write_orders,read_customers"
```

Common scopes:

- `read_products`, `write_products` - Product catalog access
- `read_orders`, `write_orders` - Order management
- `read_customers`, `write_customers` - Customer data
- `read_inventory`, `write_inventory` - Stock levels
- `read_fulfillments`, `write_fulfillments` - Order fulfillment

---

## GraphQL Patterns (Validated against API 2026-01)

### Query Products

```graphql
query GetProducts($first: Int!, $query: String) {
  products(first: $first, query: $query) {
    edges {
      node {
        id
        title
        handle
        status
        variants(first: 5) {
          edges {
            node {
              id
              price
              inventoryQuantity
            }
          }
        }
      }
    }
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

### Query Orders

```graphql
query GetOrders($first: Int!) {
  orders(first: $first) {
    edges {
      node {
        id
        name
        createdAt
        displayFinancialStatus
        totalPriceSet {
          shopMoney {
            amount
            currencyCode
          }
        }
      }
    }
  }
}
```

### Set Metafields

```graphql
mutation SetMetafields($metafields: [MetafieldsSetInput!]!) {
  metafieldsSet(metafields: $metafields) {
    metafields {
      id
      namespace
      key
      value
    }
    userErrors {
      field
      message
    }
  }
}
```

Variables example:

```json
{
  "metafields": [
    {
      "ownerId": "gid://shopify/Product/123",
      "namespace": "custom",
      "key": "care_instructions",
      "value": "Handle with care",
      "type": "single_line_text_field"
    }
  ]
}
```

---

## Checkout Extension Example

```tsx
import {
  reactExtension,
  BlockStack,
  TextField,
  Checkbox,
  useApplyAttributeChange,
} from "@shopify/ui-extensions-react/checkout";

export default reactExtension("purchase.checkout.block.render", () => (
  <GiftMessage />
));

function GiftMessage() {
  const [isGift, setIsGift] = useState(false);
  const [message, setMessage] = useState("");
  const applyAttributeChange = useApplyAttributeChange();

  useEffect(() => {
    if (isGift && message) {
      applyAttributeChange({
        type: "updateAttribute",
        key: "gift_message",
        value: message,
      });
    }
  }, [isGift, message]);

  return (
    <BlockStack spacing="loose">
      <Checkbox checked={isGift} onChange={setIsGift}>
        This is a gift
      </Checkbox>
      {isGift && (
        <TextField
          label="Gift Message"
          value={message}
          onChange={setMessage}
          multiline={3}
        />
      )}
    </BlockStack>
  );
}
```

---

## Liquid Template Example

```liquid
{% comment %} Product Card Snippet {% endcomment %}
<div class="product-card">
  <a href="{{ product.url }}">
    {% if product.featured_image %}
      <img
        src="{{ product.featured_image | img_url: 'medium' }}"
        alt="{{ product.title | escape }}"
        loading="lazy"
      >
    {% endif %}
    <h3>{{ product.title }}</h3>
    <p class="price">{{ product.price | money }}</p>
    {% if product.compare_at_price > product.price %}
      <p class="sale-badge">Sale</p>
    {% endif %}
  </a>
</div>
```

---

## Webhook Configuration

In `shopify.app.toml`:

```toml
[webhooks]
api_version = "2026-01"

[[webhooks.subscriptions]]
topics = ["orders/create", "orders/updated"]
uri = "/webhooks/orders"

[[webhooks.subscriptions]]
topics = ["products/update"]
uri = "/webhooks/products"

# GDPR mandatory webhooks (required for app approval)
[webhooks.privacy_compliance]
customer_data_request_url = "/webhooks/gdpr/data-request"
customer_deletion_url = "/webhooks/gdpr/customer-deletion"
shop_deletion_url = "/webhooks/gdpr/shop-deletion"
```

---

## Best Practices

### API Usage

- Use GraphQL over REST for new development
- Request only fields you need (reduces query cost)
- Implement cursor-based pagination with `pageInfo.endCursor`
- Use bulk operations for processing more than 250 items
- Handle rate limits with exponential backoff

### Security

- Store API credentials in environment variables
- Always verify webhook HMAC signatures before processing
- Validate OAuth state parameter to prevent CSRF
- Request minimal access scopes
- Use session tokens for embedded apps

### Performance

- Cache API responses when data doesn't change frequently
- Use lazy loading in extensions
- Optimize images in themes using `img_url` filter
- Monitor GraphQL query costs via response headers

---

## Troubleshooting

**IF you see rate limit errors:**
→ Implement exponential backoff retry logic
→ Switch to bulk operations for large datasets
→ Monitor `X-Shopify-Shop-Api-Call-Limit` header

**IF authentication fails:**
→ Verify the access token is still valid
→ Check that all required scopes were granted
→ Ensure OAuth flow completed successfully

**IF extension is not appearing:**
→ Verify the extension target is correct
→ Check that extension is published via `shopify app deploy`
→ Confirm the app is installed on the test store

**IF webhook is not receiving events:**
→ Verify the webhook URL is publicly accessible
→ Check HMAC signature validation logic
→ Review webhook logs in Partner Dashboard

**IF GraphQL query fails:**
→ Validate query against schema (use GraphiQL explorer)
→ Check for deprecated fields in error message
→ Verify you have required access scopes

---

## Reference Files

For detailed implementation guides, read these files:

- `references/app-development.md` - OAuth authentication flow, GraphQL mutations for products/orders/billing, webhook handlers, billing API integration
- `references/extensions.md` - Checkout UI components, Admin UI extensions, POS extensions, Shopify Functions for discounts/payment/delivery
- `references/themes.md` - Liquid syntax reference, theme directory structure, sections and snippets, common patterns

---

## Scripts

- `scripts/shopify_init.py` - Interactive project scaffolding. Run: `python scripts/shopify_init.py`
- `scripts/shopify_graphql.py` - GraphQL utilities with query templates, pagination, rate limiting. Import: `from shopify_graphql import ShopifyGraphQL`

---

## Official Documentation Links

- Shopify Developer Docs: https://shopify.dev/docs
- GraphQL Admin API Reference: https://shopify.dev/docs/api/admin-graphql
- Shopify CLI Reference: https://shopify.dev/docs/api/shopify-cli
- Polaris Design System: https://polaris.shopify.com

API Version: 2026-01 (quarterly releases, 12-month deprecation window)

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Merged Reference (legacy variant)

---
name: odoo-shopify-integration
description: "Connect Odoo with Shopify: sync products, inventory, orders, and customers using the Shopify API and Odoo's external API or connector modules."
---

# Odoo ↔ Shopify Integration

## Overview

This skill guides you through integrating Odoo with Shopify — syncing your product catalog, real-time inventory levels, incoming orders, and customer data. It covers both using the official Odoo Shopify connector (Enterprise) and building a custom integration via Shopify REST + Odoo XMLRPC APIs.

## When to Use This Skill

- Selling on Shopify while managing inventory in Odoo.
- Automatically creating Odoo sales orders from Shopify purchases.
- Keeping Odoo stock levels in sync with Shopify product availability.
- Mapping Shopify product variants to Odoo product templates.

## How It Works

1. **Activate**: Mention `@odoo-shopify-integration` and describe your sync scenario.
2. **Design**: Receive the data flow architecture and field mapping.
3. **Build**: Get code snippets for the Shopify webhook receiver and Odoo API caller.

## Data Flow Architecture

```
SHOPIFY                          ODOO
--------                         ----
Product Catalog <──────sync──────  Product Templates + Variants
Inventory Level <──────sync──────  Stock Quants (real-time)
New Order       ───────push──────> Sale Order (auto-confirmed)
Customer        ───────push──────> res.partner (created if new)
Fulfillment     <──────push──────  Delivery Order validated
```

## Examples

### Example 1: Push an Odoo Sale Order for a Shopify Order (Python)

```python
import xmlrpc.client, requests

# Odoo connection
odoo_url = "https://myodoo.example.com"
db, uid, pwd = "my_db", 2, "api_key"
models = xmlrpc.client.ServerProxy(f"{odoo_url}/xmlrpc/2/object")

def create_odoo_order_from_shopify(shopify_order):
    # Find or create customer
    partner = models.execute_kw(db, uid, pwd, 'res.partner', 'search_read',
        [[['email', '=', shopify_order['customer']['email']]]],
        {'fields': ['id'], 'limit': 1}
    )
    partner_id = partner[0]['id'] if partner else models.execute_kw(
        db, uid, pwd, 'res.partner', 'create', [{
            'name': shopify_order['customer']['first_name'] + ' ' + shopify_order['customer']['last_name'],
            'email': shopify_order['customer']['email'],
        }]
    )

    # Create Sale Order
    order_id = models.execute_kw(db, uid, pwd, 'sale.order', 'create', [{
        'partner_id': partner_id,
        'client_order_ref': f"Shopify #{shopify_order['order_number']}",
        'order_line': [(0, 0, {
            'product_id': get_odoo_product_id(line['sku']),
            'product_uom_qty': line['quantity'],
            'price_unit': float(line['price']),
        }) for line in shopify_order['line_items']],
    }])
    return order_id

def get_odoo_product_id(sku):
    result = models.execute_kw(db, uid, pwd, 'product.product', 'search_read',
        [[['default_code', '=', sku]]], {'fields': ['id'], 'limit': 1})
    return result[0]['id'] if result else False
```

### Example 2: Shopify Webhook for Real-Time Orders

```python
from flask import Flask, request
app = Flask(__name__)

@app.route('/webhook/shopify/orders', methods=['POST'])
def shopify_order_webhook():
    shopify_order = request.json
    order_id = create_odoo_order_from_shopify(shopify_order)
    return {"odoo_order_id": order_id}, 200
```

## Best Practices

- ✅ **Do:** Use Shopify's **webhook system** for real-time order sync instead of polling.
- ✅ **Do:** Match products using **SKU / Internal Reference** as the unique key between both systems.
- ✅ **Do:** Validate Shopify webhook HMAC signatures before processing any payload.
- ❌ **Don't:** Sync inventory from both systems simultaneously without a "master system" — pick one as the source of truth.
- ❌ **Don't:** Use Shopify product IDs as the key — use SKUs which are stable across platforms.

---

## Merged Reference (legacy variant)

---
name: odoo-woocommerce-bridge
description: "Sync Odoo with WooCommerce: products, inventory, orders, and customers via WooCommerce REST API and Odoo external API."
---

# Odoo ↔ WooCommerce Bridge

## Overview

This skill guides you through building a reliable sync bridge between Odoo (the back-office ERP) and WooCommerce (the WordPress online store). It covers product catalog sync, real-time inventory updates, order import, and customer record management.

## When to Use This Skill

- Running a WooCommerce store with Odoo for inventory and fulfillment.
- Automatically pulling WooCommerce orders into Odoo as sale orders.
- Keeping WooCommerce product stock in sync with Odoo's warehouse.
- Mapping WooCommerce order statuses to Odoo delivery states.

## How It Works

1. **Activate**: Mention `@odoo-woocommerce-bridge` and describe your sync requirements.
2. **Design**: Get the field mapping table between WooCommerce and Odoo objects.
3. **Build**: Receive Python integration scripts using the WooCommerce REST API.

## Field Mapping: WooCommerce → Odoo

| WooCommerce | Odoo |
|---|---|
| `products` | `product.template` + `product.product` |
| `orders` | `sale.order` + `sale.order.line` |
| `customers` | `res.partner` |
| `stock_quantity` | `stock.quant` |
| `sku` | `product.product.default_code` |
| `order status: processing` | Sale Order: `sale` (confirmed) |
| `order status: completed` | Delivery: `done` |

## Examples

### Example 1: Pull WooCommerce Orders into Odoo (Python)

```python
from woocommerce import API
import xmlrpc.client

# WooCommerce client
wcapi = API(
    url="https://mystore.com",
    consumer_key="ck_xxxxxxxxxxxxx",
    consumer_secret="cs_xxxxxxxxxxxxx",
    version="wc/v3"
)

# Odoo client
odoo_url = "https://myodoo.example.com"
db, uid, pwd = "my_db", 2, "api_key"
models = xmlrpc.client.ServerProxy(f"{odoo_url}/xmlrpc/2/object")

def sync_orders():
    # Get unprocessed WooCommerce orders
    orders = wcapi.get("orders", params={"status": "processing", "per_page": 50}).json()

    for wc_order in orders:
        # Find or create Odoo partner
        email = wc_order['billing']['email']
        partner = models.execute_kw(db, uid, pwd, 'res.partner', 'search',
            [[['email', '=', email]]])
        if not partner:
            partner_id = models.execute_kw(db, uid, pwd, 'res.partner', 'create', [{
                'name': f"{wc_order['billing']['first_name']} {wc_order['billing']['last_name']}",
                'email': email,
                'phone': wc_order['billing']['phone'],
                'street': wc_order['billing']['address_1'],
                'city': wc_order['billing']['city'],
            }])
        else:
            partner_id = partner[0]

        # Create Sale Order in Odoo
        order_lines = []
        for item in wc_order['line_items']:
            product = models.execute_kw(db, uid, pwd, 'product.product', 'search',
                [[['default_code', '=', item['sku']]]])
            if product:
                order_lines.append((0, 0, {
                    'product_id': product[0],
                    'product_uom_qty': item['quantity'],
                    'price_unit': float(item['price']),
                }))

        models.execute_kw(db, uid, pwd, 'sale.order', 'create', [{
            'partner_id': partner_id,
            'client_order_ref': f"WC-{wc_order['number']}",
            'order_line': order_lines,
        }])

        # Mark WooCommerce order as on-hold (processed by Odoo)
        wcapi.put(f"orders/{wc_order['id']}", {"status": "on-hold"})
```

### Example 2: Push Odoo Stock to WooCommerce

```python
def sync_inventory_to_woocommerce():
    # Get all products with a SKU from Odoo
    products = models.execute_kw(db, uid, pwd, 'product.product', 'search_read',
        [[['default_code', '!=', False], ['type', '=', 'product']]],
        {'fields': ['default_code', 'qty_available']}
    )

    for product in products:
        sku = product['default_code']
        qty = int(product['qty_available'])

        # Update WooCommerce by SKU
        wc_products = wcapi.get("products", params={"sku": sku}).json()
        if wc_products:
            wcapi.put(f"products/{wc_products[0]['id']}", {
                "stock_quantity": qty,
                "manage_stock": True,
            })
```

## Best Practices

- ✅ **Do:** Use **SKU** as the unique identifier linking WooCommerce products to Odoo products.
- ✅ **Do:** Run inventory sync on a **schedule** (every 15-30 min) rather than real-time to avoid rate limits.
- ✅ **Do:** Log all API calls and errors to a database table for debugging.
- ❌ **Don't:** Process the same WooCommerce order twice — flag it as processed immediately after import.
- ❌ **Don't:** Sync draft or cancelled WooCommerce orders to Odoo — filter by `status = processing` or `completed`.

---

## Merged Reference (legacy variant)

---
name: shopify-apps
description: "Expert patterns for Shopify app development including Remix/React Router apps, embedded apps with App Bridge, webhook handling, GraphQL Admin API, Polaris components, billing, and app extensions. U..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Shopify Apps

## Patterns

### React Router App Setup

Modern Shopify app template with React Router

### Embedded App with App Bridge

Render app embedded in Shopify Admin

### Webhook Handling

Secure webhook processing with HMAC verification

## Anti-Patterns

### ❌ REST API for New Apps

### ❌ Webhook Processing Before Response

### ❌ Polling Instead of Webhooks

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | high | ## Respond immediately, process asynchronously |
| Issue | high | ## Check rate limit headers |
| Issue | high | ## Request protected customer data access |
| Issue | medium | ## Use TOML only (recommended) |
| Issue | medium | ## Handle both URL formats |
| Issue | high | ## Use GraphQL for all new code |
| Issue | high | ## Use latest App Bridge via script tag |
| Issue | high | ## Implement all GDPR handlers |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Merged Reference (legacy variant)

---
name: wordpress-woocommerce-development
description: "WooCommerce store development workflow covering store setup, payment integration, shipping configuration, and customization."
category: granular-workflow-bundle
risk: safe
source: personal
date_added: "2026-02-27"
---

# WordPress WooCommerce Development Workflow

## Overview

Specialized workflow for building WooCommerce stores including setup, payment gateway integration, shipping configuration, custom product types, and store optimization.

## When to Use This Workflow

Use this workflow when:
- Setting up WooCommerce stores
- Integrating payment gateways
- Configuring shipping methods
- Creating custom product types
- Building subscription products

## Workflow Phases

### Phase 1: Store Setup

#### Skills to Invoke
- `app-builder` - Project scaffolding
- `wordpress-penetration-testing` - WordPress patterns

#### Actions
1. Install WooCommerce
2. Run setup wizard
3. Configure store settings
4. Set up tax rules
5. Configure currency

#### Copy-Paste Prompts
```
Use @app-builder to set up WooCommerce store
```

### Phase 2: Product Configuration

#### Skills to Invoke
- `wordpress-penetration-testing` - WooCommerce patterns

#### Actions
1. Create product categories
2. Add product attributes
3. Configure product types
4. Set up variable products
5. Add product images

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to configure WooCommerce products
```

### Phase 3: Payment Integration

#### Skills to Invoke
- `payment-integration` - Payment processing
- `stripe-integration` - Stripe
- `paypal-integration` - PayPal

#### Actions
1. Choose payment gateways
2. Configure Stripe
3. Set up PayPal
4. Add offline payments
5. Test payment flows

#### Copy-Paste Prompts
```
Use @stripe-integration to integrate Stripe payments
```

```
Use @paypal-integration to integrate PayPal
```

### Phase 4: Shipping Configuration

#### Skills to Invoke
- `wordpress-penetration-testing` - WooCommerce shipping

#### Actions
1. Set up shipping zones
2. Configure shipping methods
3. Add flat rate shipping
4. Set up free shipping
5. Integrate carriers

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to configure shipping
```

### Phase 5: Store Customization

#### Skills to Invoke
- `frontend-developer` - Store customization
- `frontend-design` - Store design

#### Actions
1. Customize product pages
2. Modify cart page
3. Style checkout flow
4. Create custom templates
5. Add custom fields

#### Copy-Paste Prompts
```
Use @frontend-developer to customize WooCommerce templates
```

### Phase 6: Extensions

#### Skills to Invoke
- `wordpress-penetration-testing` - WooCommerce extensions

#### Actions
1. Install required extensions
2. Configure subscriptions
3. Set up bookings
4. Add memberships
5. Integrate marketplace

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to configure WooCommerce extensions
```

### Phase 7: Optimization

#### Skills to Invoke
- `web-performance-optimization` - Performance
- `database-optimizer` - Database optimization

#### Actions
1. Optimize product images
2. Enable caching
3. Optimize database
4. Configure CDN
5. Set up lazy loading

#### Copy-Paste Prompts
```
Use @web-performance-optimization to optimize WooCommerce store
```

### Phase 8: Testing

#### Skills to Invoke
- `playwright-skill` - E2E testing
- `test-automator` - Test automation

#### Actions
1. Test checkout flow
2. Verify payment processing
3. Test email notifications
4. Check mobile experience
5. Performance testing

#### Copy-Paste Prompts
```
Use @playwright-skill to test WooCommerce checkout flow
```

## Quality Gates

- [ ] Products displaying correctly
- [ ] Checkout flow working
- [ ] Payments processing
- [ ] Shipping calculating
- [ ] Emails sending
- [ ] Mobile responsive

## Related Workflow Bundles

- `wordpress` - WordPress development
- `wordpress-theme-development` - Theme development
- `wordpress-plugin-development` - Plugin development
- `payment-integration` - Payment processing

## Source: references/skills/shopify-development/references/legacy/odoo-shopify-integration/SKILL.md

---
name: odoo-shopify-integration
description: "Connect Odoo with Shopify: sync products, inventory, orders, and customers using the Shopify API and Odoo's external API or connector modules."
---

# Odoo ↔ Shopify Integration

## Overview

This skill guides you through integrating Odoo with Shopify — syncing your product catalog, real-time inventory levels, incoming orders, and customer data. It covers both using the official Odoo Shopify connector (Enterprise) and building a custom integration via Shopify REST + Odoo XMLRPC APIs.

## When to Use This Skill

- Selling on Shopify while managing inventory in Odoo.
- Automatically creating Odoo sales orders from Shopify purchases.
- Keeping Odoo stock levels in sync with Shopify product availability.
- Mapping Shopify product variants to Odoo product templates.

## How It Works

1. **Activate**: Mention `@odoo-shopify-integration` and describe your sync scenario.
2. **Design**: Receive the data flow architecture and field mapping.
3. **Build**: Get code snippets for the Shopify webhook receiver and Odoo API caller.

## Data Flow Architecture

```
SHOPIFY                          ODOO
--------                         ----
Product Catalog <──────sync──────  Product Templates + Variants
Inventory Level <──────sync──────  Stock Quants (real-time)
New Order       ───────push──────> Sale Order (auto-confirmed)
Customer        ───────push──────> res.partner (created if new)
Fulfillment     <──────push──────  Delivery Order validated
```

## Examples

### Example 1: Push an Odoo Sale Order for a Shopify Order (Python)

```python
import xmlrpc.client, requests

# Odoo connection
odoo_url = "https://myodoo.example.com"
db, uid, pwd = "my_db", 2, "api_key"
models = xmlrpc.client.ServerProxy(f"{odoo_url}/xmlrpc/2/object")

def create_odoo_order_from_shopify(shopify_order):
    # Find or create customer
    partner = models.execute_kw(db, uid, pwd, 'res.partner', 'search_read',
        [[['email', '=', shopify_order['customer']['email']]]],
        {'fields': ['id'], 'limit': 1}
    )
    partner_id = partner[0]['id'] if partner else models.execute_kw(
        db, uid, pwd, 'res.partner', 'create', [{
            'name': shopify_order['customer']['first_name'] + ' ' + shopify_order['customer']['last_name'],
            'email': shopify_order['customer']['email'],
        }]
    )

    # Create Sale Order
    order_id = models.execute_kw(db, uid, pwd, 'sale.order', 'create', [{
        'partner_id': partner_id,
        'client_order_ref': f"Shopify #{shopify_order['order_number']}",
        'order_line': [(0, 0, {
            'product_id': get_odoo_product_id(line['sku']),
            'product_uom_qty': line['quantity'],
            'price_unit': float(line['price']),
        }) for line in shopify_order['line_items']],
    }])
    return order_id

def get_odoo_product_id(sku):
    result = models.execute_kw(db, uid, pwd, 'product.product', 'search_read',
        [[['default_code', '=', sku]]], {'fields': ['id'], 'limit': 1})
    return result[0]['id'] if result else False
```

### Example 2: Shopify Webhook for Real-Time Orders

```python
from flask import Flask, request
app = Flask(__name__)

@app.route('/webhook/shopify/orders', methods=['POST'])
def shopify_order_webhook():
    shopify_order = request.json
    order_id = create_odoo_order_from_shopify(shopify_order)
    return {"odoo_order_id": order_id}, 200
```

## Best Practices

- ✅ **Do:** Use Shopify's **webhook system** for real-time order sync instead of polling.
- ✅ **Do:** Match products using **SKU / Internal Reference** as the unique key between both systems.
- ✅ **Do:** Validate Shopify webhook HMAC signatures before processing any payload.
- ❌ **Don't:** Sync inventory from both systems simultaneously without a "master system" — pick one as the source of truth.
- ❌ **Don't:** Use Shopify product IDs as the key — use SKUs which are stable across platforms.

## Source: references/skills/shopify-development/references/legacy/odoo-woocommerce-bridge/SKILL.md

---
name: odoo-woocommerce-bridge
description: "Sync Odoo with WooCommerce: products, inventory, orders, and customers via WooCommerce REST API and Odoo external API."
---

# Odoo ↔ WooCommerce Bridge

## Overview

This skill guides you through building a reliable sync bridge between Odoo (the back-office ERP) and WooCommerce (the WordPress online store). It covers product catalog sync, real-time inventory updates, order import, and customer record management.

## When to Use This Skill

- Running a WooCommerce store with Odoo for inventory and fulfillment.
- Automatically pulling WooCommerce orders into Odoo as sale orders.
- Keeping WooCommerce product stock in sync with Odoo's warehouse.
- Mapping WooCommerce order statuses to Odoo delivery states.

## How It Works

1. **Activate**: Mention `@odoo-woocommerce-bridge` and describe your sync requirements.
2. **Design**: Get the field mapping table between WooCommerce and Odoo objects.
3. **Build**: Receive Python integration scripts using the WooCommerce REST API.

## Field Mapping: WooCommerce → Odoo

| WooCommerce | Odoo |
|---|---|
| `products` | `product.template` + `product.product` |
| `orders` | `sale.order` + `sale.order.line` |
| `customers` | `res.partner` |
| `stock_quantity` | `stock.quant` |
| `sku` | `product.product.default_code` |
| `order status: processing` | Sale Order: `sale` (confirmed) |
| `order status: completed` | Delivery: `done` |

## Examples

### Example 1: Pull WooCommerce Orders into Odoo (Python)

```python
from woocommerce import API
import xmlrpc.client

# WooCommerce client
wcapi = API(
    url="https://mystore.com",
    consumer_key="ck_xxxxxxxxxxxxx",
    consumer_secret="cs_xxxxxxxxxxxxx",
    version="wc/v3"
)

# Odoo client
odoo_url = "https://myodoo.example.com"
db, uid, pwd = "my_db", 2, "api_key"
models = xmlrpc.client.ServerProxy(f"{odoo_url}/xmlrpc/2/object")

def sync_orders():
    # Get unprocessed WooCommerce orders
    orders = wcapi.get("orders", params={"status": "processing", "per_page": 50}).json()

    for wc_order in orders:
        # Find or create Odoo partner
        email = wc_order['billing']['email']
        partner = models.execute_kw(db, uid, pwd, 'res.partner', 'search',
            [[['email', '=', email]]])
        if not partner:
            partner_id = models.execute_kw(db, uid, pwd, 'res.partner', 'create', [{
                'name': f"{wc_order['billing']['first_name']} {wc_order['billing']['last_name']}",
                'email': email,
                'phone': wc_order['billing']['phone'],
                'street': wc_order['billing']['address_1'],
                'city': wc_order['billing']['city'],
            }])
        else:
            partner_id = partner[0]

        # Create Sale Order in Odoo
        order_lines = []
        for item in wc_order['line_items']:
            product = models.execute_kw(db, uid, pwd, 'product.product', 'search',
                [[['default_code', '=', item['sku']]]])
            if product:
                order_lines.append((0, 0, {
                    'product_id': product[0],
                    'product_uom_qty': item['quantity'],
                    'price_unit': float(item['price']),
                }))

        models.execute_kw(db, uid, pwd, 'sale.order', 'create', [{
            'partner_id': partner_id,
            'client_order_ref': f"WC-{wc_order['number']}",
            'order_line': order_lines,
        }])

        # Mark WooCommerce order as on-hold (processed by Odoo)
        wcapi.put(f"orders/{wc_order['id']}", {"status": "on-hold"})
```

### Example 2: Push Odoo Stock to WooCommerce

```python
def sync_inventory_to_woocommerce():
    # Get all products with a SKU from Odoo
    products = models.execute_kw(db, uid, pwd, 'product.product', 'search_read',
        [[['default_code', '!=', False], ['type', '=', 'product']]],
        {'fields': ['default_code', 'qty_available']}
    )

    for product in products:
        sku = product['default_code']
        qty = int(product['qty_available'])

        # Update WooCommerce by SKU
        wc_products = wcapi.get("products", params={"sku": sku}).json()
        if wc_products:
            wcapi.put(f"products/{wc_products[0]['id']}", {
                "stock_quantity": qty,
                "manage_stock": True,
            })
```

## Best Practices

- ✅ **Do:** Use **SKU** as the unique identifier linking WooCommerce products to Odoo products.
- ✅ **Do:** Run inventory sync on a **schedule** (every 15-30 min) rather than real-time to avoid rate limits.
- ✅ **Do:** Log all API calls and errors to a database table for debugging.
- ❌ **Don't:** Process the same WooCommerce order twice — flag it as processed immediately after import.
- ❌ **Don't:** Sync draft or cancelled WooCommerce orders to Odoo — filter by `status = processing` or `completed`.

## Source: references/skills/shopify-development/references/legacy/shopify-apps/SKILL.md

---
name: shopify-apps
description: "Expert patterns for Shopify app development including Remix/React Router apps, embedded apps with App Bridge, webhook handling, GraphQL Admin API, Polaris components, billing, and app extensions. U..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Shopify Apps

## Patterns

### React Router App Setup

Modern Shopify app template with React Router

### Embedded App with App Bridge

Render app embedded in Shopify Admin

### Webhook Handling

Secure webhook processing with HMAC verification

## Anti-Patterns

### ❌ REST API for New Apps

### ❌ Webhook Processing Before Response

### ❌ Polling Instead of Webhooks

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | high | ## Respond immediately, process asynchronously |
| Issue | high | ## Check rate limit headers |
| Issue | high | ## Request protected customer data access |
| Issue | medium | ## Use TOML only (recommended) |
| Issue | medium | ## Handle both URL formats |
| Issue | high | ## Use GraphQL for all new code |
| Issue | high | ## Use latest App Bridge via script tag |
| Issue | high | ## Implement all GDPR handlers |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Source: references/skills/shopify-development/references/legacy/wordpress-woocommerce-development/SKILL.md

---
name: wordpress-woocommerce-development
description: "WooCommerce store development workflow covering store setup, payment integration, shipping configuration, and customization."
category: granular-workflow-bundle
risk: safe
source: personal
date_added: "2026-02-27"
---

# WordPress WooCommerce Development Workflow

## Overview

Specialized workflow for building WooCommerce stores including setup, payment gateway integration, shipping configuration, custom product types, and store optimization.

## When to Use This Workflow

Use this workflow when:
- Setting up WooCommerce stores
- Integrating payment gateways
- Configuring shipping methods
- Creating custom product types
- Building subscription products

## Workflow Phases

### Phase 1: Store Setup

#### Skills to Invoke
- `app-builder` - Project scaffolding
- `wordpress-penetration-testing` - WordPress patterns

#### Actions
1. Install WooCommerce
2. Run setup wizard
3. Configure store settings
4. Set up tax rules
5. Configure currency

#### Copy-Paste Prompts
```
Use @app-builder to set up WooCommerce store
```

### Phase 2: Product Configuration

#### Skills to Invoke
- `wordpress-penetration-testing` - WooCommerce patterns

#### Actions
1. Create product categories
2. Add product attributes
3. Configure product types
4. Set up variable products
5. Add product images

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to configure WooCommerce products
```

### Phase 3: Payment Integration

#### Skills to Invoke
- `payment-integration` - Payment processing
- `stripe-integration` - Stripe
- `paypal-integration` - PayPal

#### Actions
1. Choose payment gateways
2. Configure Stripe
3. Set up PayPal
4. Add offline payments
5. Test payment flows

#### Copy-Paste Prompts
```
Use @stripe-integration to integrate Stripe payments
```

```
Use @paypal-integration to integrate PayPal
```

### Phase 4: Shipping Configuration

#### Skills to Invoke
- `wordpress-penetration-testing` - WooCommerce shipping

#### Actions
1. Set up shipping zones
2. Configure shipping methods
3. Add flat rate shipping
4. Set up free shipping
5. Integrate carriers

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to configure shipping
```

### Phase 5: Store Customization

#### Skills to Invoke
- `frontend-developer` - Store customization
- `frontend-design` - Store design

#### Actions
1. Customize product pages
2. Modify cart page
3. Style checkout flow
4. Create custom templates
5. Add custom fields

#### Copy-Paste Prompts
```
Use @frontend-developer to customize WooCommerce templates
```

### Phase 6: Extensions

#### Skills to Invoke
- `wordpress-penetration-testing` - WooCommerce extensions

#### Actions
1. Install required extensions
2. Configure subscriptions
3. Set up bookings
4. Add memberships
5. Integrate marketplace

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to configure WooCommerce extensions
```

### Phase 7: Optimization

#### Skills to Invoke
- `web-performance-optimization` - Performance
- `database-optimizer` - Database optimization

#### Actions
1. Optimize product images
2. Enable caching
3. Optimize database
4. Configure CDN
5. Set up lazy loading

#### Copy-Paste Prompts
```
Use @web-performance-optimization to optimize WooCommerce store
```

### Phase 8: Testing

#### Skills to Invoke
- `playwright-skill` - E2E testing
- `test-automator` - Test automation

#### Actions
1. Test checkout flow
2. Verify payment processing
3. Test email notifications
4. Check mobile experience
5. Performance testing

#### Copy-Paste Prompts
```
Use @playwright-skill to test WooCommerce checkout flow
```

## Quality Gates

- [ ] Products displaying correctly
- [ ] Checkout flow working
- [ ] Payments processing
- [ ] Shipping calculating
- [ ] Emails sending
- [ ] Mobile responsive

## Related Workflow Bundles

- `wordpress` - WordPress development
- `wordpress-theme-development` - Theme development
- `wordpress-plugin-development` - Plugin development
- `payment-integration` - Payment processing

