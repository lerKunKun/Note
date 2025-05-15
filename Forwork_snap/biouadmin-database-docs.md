# BIOU Admin Database Documentation

## Overview

This documentation describes the database schema for the BIOU Admin system. The database is built on MySQL 8.0.36 and contains tables for managing various aspects of e-commerce operations including order management, product inventory, advertising, shipping, reporting, and system administration.

## Table Categories

The database consists of several categories of tables:

1. **Advertising Management** - Tables prefixed with `qu_adv_` or `qu_advertising_`
2. **Order Management** - Tables prefixed with `qu_order_`
3. **Product Management** - Tables prefixed with `qu_product_` and `qu_listing_`
4. **Shipping & Logistics** - Tables prefixed with `qu_checkbill_`
5. **Reporting** - Tables prefixed with `qu_report_`
6. **System Management** - Tables prefixed with `qu_system_`
7. **Miscellaneous** - Other supporting tables

## Advertising Management Tables

### qu_adv_accountopening
Stores information about advertising account platforms.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| title | varchar(50) | Platform name |
| status | int | Status indicator |

### qu_adv_proxyownership
Tracks proxy ownership information.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| title | varchar(50) | Platform name |
| status | int | Status indicator |

### qu_advaccount
Stores advertising account details.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| platform_id | int | Advertising platform ID |
| advertiser_id | varchar(255) | Account ID |
| advertiser_name | varchar(255) | Account owner/entity name |
| status | int | Account status (1=active, 0=expired, 2=processed) |
| ProxyOwnership | varchar(255) | Proxy ownership information |
| *Additional columns* | *Various* | Account metadata and status fields |

### qu_advaccount_admin
Associates advertising accounts with admin/operating users.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| advertiser_id | varchar(255) | Advertising account ID |
| admin_id | int | Primary operator ID |
| shop_id | int | Site/shop ID |
| status | int | Status (1=active, 0=expired) |
| *Additional columns* | *Various* | Time periods and affiliations |

### qu_advertising
Records advertising expense details.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| admin_id | int | Operator ID |
| shop_id | int | Site ID |
| advertiser_id | varchar(255) | Advertising account ID |
| spend | decimal(20,2) | Advertising cost |
| platform_id | int | Advertising platform ID |
| *Additional columns* | *Various* | Tracking, verification and time data |

### qu_advplatform
Lists supported advertising platforms.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| title | varchar(50) | Platform name |
| status | int | Status indicator |

## Order Management Tables

### qu_order
Main order table storing order details from e-commerce platforms.

| Column | Type | Description |
|--------|------|-------------|
| id | int | Primary key |
| order_id | varchar(255) | Order ID (unique) |
| shop_id | int | Store ID |
| name | varchar(255) | Order name |
| number | varchar(255) | Order number |
| total_price | decimal(20,2) | Original sale amount (before refunds) |
| currency | varchar(255) | Currency code |
| financial_status | varchar(255) | Payment status |
| fulfillment_status | varchar(255) | Shipping status |
| tracking_number | varchar(255) | Shipping tracking number |
| *Additional columns* | *Various* | Timestamps, country codes, dispute info |

### qu_order_achievement
Tracks order performance metrics and costs.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| order_id | varchar(255) | Order ID |
| shop_id | int | Store ID |
| freight | decimal(10,3) | Shipping cost (RMB) |
| material | decimal(10,3) | Procurement cost (RMB) |
| dispute | decimal(10,3) | Dispute cost (RMB) |
| total_price | decimal(20,2) | Final amount |
| skuquantity | varchar(50) | Number of items |
| *Additional columns* | *Various* | Financial metrics and status flags |

### qu_order_customer
Stores customer information for orders.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| order_id | varchar(255) | Order ID |
| customer_id | varchar(255) | Customer ID |
| shop_id | int | Store ID |
| email | varchar(255) | Customer email |
| phone | varchar(255) | Customer phone |
| *Additional columns* | *Various* | Name, status, and marketing preferences |

### qu_order_billing
Contains order billing address information.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| order_id | varchar(255) | Order ID |
| shop_id | int | Store ID |
| name | varchar(255) | Customer name |
| country | varchar(255) | Country name |
| country_code | varchar(255) | Country code |
| *Additional columns* | *Various* | Address details, province, city, zip |

### qu_order_fulfillments
Tracks shipping and fulfillment status of orders.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| order_id | varchar(255) | Order ID |
| shop_id | int | Store ID |
| status | varchar(255) | Shipping status |
| tracking_company | varchar(255) | Shipping company |
| tracking_number | varchar(255) | Tracking number |
| *Additional columns* | *Various* | Timestamps and tracking URL |

### qu_order_skuinfo
Contains product SKU information related to orders.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| order_id | varchar(255) | Order ID |
| shop_id | int | Store ID |
| sku | varchar(255) | Product SKU |
| quantity | int | Quantity ordered |
| *Additional columns* | *Various* | Product properties and pricing |

### qu_order_dispute_*
Various tables that track payment disputes (paypal, stripe, etc.).

| Table | Purpose |
|-------|---------|
| qu_order_dispute_api | Generic dispute tracking |
| qu_order_dispute_paypal | PayPal-specific disputes |
| qu_order_dispute_stripe | Stripe-specific disputes |
| qu_order_dispute_afterpay | Afterpay-specific disputes |
| qu_order_dispute_airwallex | Airwallex-specific disputes |

## Product Management Tables

### qu_product
Primary product information table.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| spu | varchar(255) | Stock Keeping Unit |
| title | varchar(255) | Product title |
| *Additional columns* | *Various* | Product details, pricing, status |

### qu_product_inventory
Tracks product inventory levels.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| spu | varchar(255) | Stock Keeping Unit |
| warehouse_id | int | Warehouse identifier |
| quantity | int | Inventory quantity |
| *Additional columns* | *Various* | Inventory tracking data |

### qu_listing_product
Stores product information for marketplace listings.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| spu | varchar(100) | Product SPU |
| product_name | varchar(255) | Product name |
| status | int | Status (0=pending, 1=approved, 2=rejected, 3=deleted) |
| *Additional columns* | *Various* | Product descriptions, images, attributes |

## Shipping & Logistics Tables

### qu_checkbill_*
Set of tables for reconciling shipping charges with carriers.

| Table | Carrier |
|-------|---------|
| qu_checkbill_4px | 4PX Express |
| qu_checkbill_sf | SF Express |
| qu_checkbill_sdh | 闪电猴 (Lightning Monkey) |
| qu_checkbill_qqwy | 全球无忧 (Global Worry-free) |

Each table generally contains:
- Tracking/waybill numbers
- Weight information
- Shipping costs
- Reconciliation status

## Reporting Tables

### qu_report_day_*
Daily reporting tables for various metrics.

| Table | Metrics |
|-------|---------|
| qu_report_day | General daily metrics |
| qu_report_day_cost | Cost analysis |
| qu_report_day_profit | Profit analysis |
| qu_report_day_personal | Personal performance metrics |

### qu_report_monthly_*
Monthly aggregated reporting tables.

| Table | Metrics |
|-------|---------|
| qu_report_monthly_dispute | Monthly dispute summaries |
| qu_report_monthly_profit | Monthly profit summaries |
| qu_report_monthly_personal | Monthly personal performance |

## System Management Tables

### qu_system_admin
Stores system administrator accounts.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| username | varchar(255) | Admin username |
| password | varchar(255) | Admin password (hashed) |
| status | int | Account status |
| *Additional columns* | *Various* | Permissions and metadata |

### qu_system_menu
Defines system menu structure.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| title | varchar(255) | Menu item title |
| url | varchar(255) | Menu URL/path |
| *Additional columns* | *Various* | Menu hierarchy and permissions |

### qu_system_log
Records system activity logs.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| admin_id | int | Admin user ID |
| content | text | Log content |
| createtime | int | Log timestamp |
| *Additional columns* | *Various* | IP address, action type, etc. |

## E-commerce Integration Tables

### qu_shops
Contains information about connected e-commerce stores.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| name | varchar(255) | Shop name |
| platform | varchar(255) | Platform (e.g., Shopify) |
| domain | varchar(255) | Shop domain |
| *Additional columns* | *Various* | API credentials and status |

### qu_shops_admin
Associates admin users with specific shops.

| Column | Type | Description |
|--------|------|-------------|
| id | int UNSIGNED | Primary key |
| shop_id | int | Shop ID |
| admin_id | int | Admin user ID |
| status | int | Association status |
| *Additional columns* | *Various* | Permission levels and timestamps |

## Social Media Integration Tables

The database includes tables for various social media/ad platform integrations:
- `qu_fb_token` - Facebook API tokens
- `qu_snap_token` - Snapchat API tokens
- `qu_pinterest_token` - Pinterest API tokens
- `qu_jinyi_token` - Jinyi platform tokens

## Conclusion

This database schema supports a comprehensive e-commerce management system with capabilities for order processing, inventory tracking, advertising management, shipping logistics, financial reporting, and customer service. The tables are designed with appropriate relationships to maintain data integrity while supporting the operational needs of online retail. 