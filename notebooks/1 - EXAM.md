# Exam Project - ViDA (VAT in Digital Age) E-Invoicing Data Quality Gap Analysis
For course BI-related programming language | Nackademin - BI25

## A few things before exam project introduction

**The data is fictive.** All company names, VAT numbers, IBANs, addresses and transactions are completely synthetic. Any resemblance to real entities is coincidental. Data provided are simplified and do not reflect actual transaction data. They are modeled in this project for learning purpose.

**ViDA (EU Directive) is real.** VAT in the Digital Age is an ongoing EU legislative initiative. We use it as a business context to practise data analysis — not as a compliance reference. We use some of the known and possible technical requirements from ViDA. These requirements are simplified and modified to form our learning and exam project.

If you want to read more about ViDA,  [European Commission - ViDA implementation strategy](https://taxation-customs.ec.europa.eu/news/vat-digital-age-implementation-strategy-support-businesses-and-member-states-2025-09-24_en)

> You do not need to become a tax nor ViDA expert. What you will need to work with the exam project will be provided to you. 

**The analysis and functions you write are for this exam only.** They are not suggested production solutions.

## The Business Case

**TechDist GmbH** is a fictional German electronics wholesaler distributing to business customers across the EU.

The company is preparing for ViDA cross-border B2B e-invoicing. A cross-functional team has been set up to analyse the gap between their current transaction data and what compliant e-invoicing will require. You are part of that team.

## What does ViDA e-invoice require 

Cross-border B2B e-invoices must contain certain obligatory fields to be accepted by tax authorities. Missing or incorrect fields mean the invoice may be rejected.

The **requirements** our business transaction data will be analysed against include:

**The invoice data requirement**

- invoice_no: invoice unique identifying number(invoice_id/invoice_line)
- invoice_line_no: identifying number for line number.(invoice_line_id/invoice_line)
- invoice_date: date when invoice is issued(invoice_date/invoice_line)
- invoice_type: invoice or credit note (corrective invoice)(invoice_type/invoice_line)
> - seller_company_name: TechDist GmbH
- seller_country: country of the seller — always DE for TechDist GmbH
> - seller_vat_reg_no: seller's VAT registration number
> - seller_iban: Bank account reference
- buyer_company_name: name of the buying company(name/customers)
- buyer_country: country where the buying company is registered(country/customers)
- buyer_vat_reg_no: buyer's VAT registration number(Registration no/customers(here are two options but the VAT no has two missing fields and seems uncompleted information))
- quantity: item quantity invoiced (quantity/order_line? and quantity/invoice_line)
- unit_price: item unit price(unit_price/order_line? and unit_price/invoice_line)
- invoiced_amount_net: net amount invoiced (without VAT)(line_amount/invoice_line)
- invoice_currency: EUR()
- vat_rate: rate applicable for sales item(vat_rate/invoice_line)
- vat_amount: calculated VAT(vat_amount)
- vat_currency: EUR (currency/invoice_line)
- invoiced_amount_total: total amount invoice(total_amount/invoice_line)
- reverse_charge: VAT to be accounted for by the cross-border buyer if buyer has VAT registration(vat_category_code)
- corrective_invoice_ref: for example if invoice is a credit note, it has been issued to correct earlier issued invoice. The original invoice_no is required here (invoice_ref, refunded)
- invoice_status: whether the invoice has been issued or cancelled(invoice_status/ invoice_line)

**The 10-day rule**

From the date the customer order has been delivered, the invoice must be issued within 10 days since the delivery date. (invoice_date(invoice_line) - delivered_date(delivery_confirm_date/deliveries) <= 10)

## What does your gap analysis will work on

**Exam part 1 & 2**
1. Is the current `invoice_line` data ViDA Compliant?
2. What data is missing in `invoice_line` provided to you
3. What about the 10-day rule? Are invoice_date ViDA compliant?

**Exam part 3**
- Instruction to be published in week 16
- Part 3 data will be larger than the sample data but in same table construct
- We will focus on your Python skills in exam part 3

## The Data

Five datasets (samples for now) from TechDist GmbH's ERP system are in your `Exam Project/data/raw sample` folder.

1. `customers.csv` contains one row per customer (buyer)
2. `delivery_addresses.csv` contains delivery locations per customer — one customer can have several
3. `order_line.csv` contains one row per product line ordered
4. `deliveries.csv` contains one confirmed delivery record per order
5. `invoice_line.csv` contains one row per invoice line

The simple operations flow in table names looks like this:

```
customers ──────────────────── delivery_addresses
    │                                  │
    └──────────── order_line ──────────┘
                      │
                  deliveries
                      │
                 invoice_line
```

> ✅ Start by opening the 🗂️ folder and having a look at the datasets. They are sample data, small and readable.

### 1. `customers.csv`

This dataset contains following columns:

| Header | Info |
|--------|------|
| Customer ID | customer ID in internal system |
| Name | customer company name |
| Customer type | business, private customer or others | 
| Registration no | customer company national registration number |
| VAT no | customer company national VAT number|
| City | customer registered address city |
| Country | customer registered country |
| Registration date | customer registration date in internal system |
| Updated date | customer info last update date in internal system |

### 2. `delivery_addresses.csv`

You do not need to look for data quality issues in this dataset. The customer country and delivery country mismatch is not an error. This is intentional and indicates that one customer based in a country may have several delivery address in another country or other countries. Because our datasets are all fictive, the odd street names with seemingly mismatching country is not an error. It is to avoid coincidence to real addresses. 

This dataset contains following columns:

| Header | Info |
|--------|------|
| Customer ID | customer ID in internal system |
| Delivery place id | delivery place ID in interal system |
| Delivery place name | *customer company name used for data simulation purpose* |
| Delivery place address | delivery address |
| Delivery city | delivery address city |
| Delivery country | delivery address country |
| Registration date | delivery address registration date in internal system |
| Updated date | delivery address updated date in internal system |

### 3. `order_line.csv` 

This dataset contains following columns:

| Header | Info |
|--------|------|
| order_line_id | order line id |
| order_id | order head id |
| order_date | date the order is placed |
| customer_id | customer ID in internal system |
| delivery_place_id | delivery place ID in interal system |
| product_id | product id in internal system |
| product_description | product name in internal system|
| product_type | product or service ordered |
| quantity | quantity ordered |
| uom | unit of measure |
| unit_price | price per unit |
| currency | price currency |

### 4. `deliveries.csv`

This dataset contains following columns:

| Header | Info |
|--------|------|
| delivery_confirm_id | delivery confirmation id in internal system |
| order_id | order head id  |
| delivery_confirm_date | delivery confirmed date (HINT: after a delivery confirmation is received by internal system, an e-invoice for the fulfilled order can be generated) |
| delivery_country | delivery country |

### 5. `invoice_line.csv`

Please note that **vat_rate** here can be 0 for *reverse charge* transactions. For the transactions with 0 vat, you can also see **vat_category_code** is AE which indicates VATEX code for reverse charge.

This dataset contains following columns:

| Header | Info |
|--------|------|
| invoice_line_id | invoice line id |
| invoice_id | invoice header id |
| invoice_date | the date invoice is issued |
| invoice_type | invoice, credit note |
| order_id | order head id |
| product_id | product id in internal system |
| product_description | product name in internal system |
| quantity | quantity ordered |
| uom | unit of measure |
| unit_price | price per unit |
| currency | price currency |
| line_amount | invoiced amount excl. vat |
| vat_rate | vat % |
| vat_amount | calculated VAT amount |
| total_amount | invoiced amount including VAT |
| invoice_status | issued, cancelled |
| invoice_ref | corrective invoice ref |
| vat_category_code | AE (reverse charge), S (standard rate) |

## Exam Format

You must pass all 3 parts to pass the course.

| Part | Format | Grade | Instruction released |
|---|---|---|---|
| Part 1 | Individual written hand-in | Pass only | Today |
| Part 2 | Group presentation + Notebook hand-in | Pass only | Today |
| Part 3 | Individual code hand-in | Pass or Pass with Distinction | After group presentation |


## Exame Part 1 — Your Analysis Plan

**Individual hand-in**<br>
**Format**: Markdown file or Jupyter Notebook.<br>
**How to hand in**: please email your individual hand-in as an attachment to **jiani.li@nackademin.se**. You will receive a confirmation that your hand-in is received. <br>
**Deadline**: Tue 7 April 2026, till the end of day <br>
**Grade:** Pass / Not yet passed

### Your role

You are a newly hired analyst supporting the accounting department — the bridge between accountants and IT as TechDist GmbH prepares for ViDA.

Your first task is to write a **gap analysis plan**: a short structured document describing how you will use the provided data to identify compliance risks.

> ✅ No code needed. You may refer to the data and describe what you would look for — without showing results.

### Use below template if you'd like to

>The questions under each header are hints. You do not need to answer each one literally — use them to guide your thinking.

---

**Title:** Gap Analysis Plan — TechDist GmbH ViDA Preparation  
**Author:** *(your name)*  
**Date:** *(hand-in date)*


#### 1. The Requirement *(provided - see above)*

Under ViDA Pillar 1, cross-border B2B e-invoices must contain certain obligatory fields. 

*add your own understanding ...*

#### 2. Our Data *(provided - see above)*

We now have retrieved 5 datasets from ERP covering the transaction flow — from customer registration to invoicing. 

*add your own understanding ...*

#### 3. The Gap *(your analysis)*

> Which tables are most relevant to check?  
> Which required fields are missing or look problematic in our data?  
> Could any fields be inconsistent across tables?<br>
> ...

#### 4. The Impact *(your analysis)*

> Which customers or transactions are most at risk?  
> What would happen if we send e-invoices today?<br>
> ...


#### 5. Next Steps *(your analysis)*

> What could we do to fix — and why?  
> Who in the business needs to be involved?  
> What would the data look like after the fixes? <br>
> ...

---

## Exam Part 2 — Group Presentation & Notebook

**Group of 3–4 people (minimum 2)**<br>
**Format**: Live presentation of role pay (15 min) and a group Notebook hand-in<br>
**How to hand in**: to be updated <br>
**Presentation date**: Wed 15 April 2026 <br>
**Grade:** Pass / Not yet passed

### About your group

As a group, you assume roles within TechDist GmbH's ViDA preparation project and present a short **data-driven internal meeting** — showing how different departments see the same data quality issues from different angles.

### Roles to choose from

| Role | Perspective |
|---|---|
| **Accountanting Manager** | Raises compliance concerns — what does this mean in practice? |
| **Data Analyst** | Identifies and presents the data quality gaps with evidence |
| **Data Engineer** | How does data flow between systems? Where can transformations help? |
| **ERP Engineer** | Where does the data come from? Would it be realistic to fix at source? |

Select **at least 2 roles**. Show a constructive dialog — not just a report read aloud.

### Presentation guidelines

- Show data evidence during the presentation — in your Notebook or PowerPoint using Python as your data processing language
- Address at least one concrete data quality issue found in the datasets
- Everyone is expected to have contributed, even if not everyone presents

### What to hand in

A Jupyter Notebook with your group's data-driven analysis that serves as the foundation of your cross-functional dialog. Include at the top:

- Notebook title
- Group members
- Roles represented

Your Notebook should show which data quality issues you focused on, the code and results that support your findings, and a short note on how your group arrived at the analysis.

## Exam Part 3 — Your Function Hand-in

**Individual Hand-in**<br>
**Format**: Notebook and/or Python script hand-in <br>
**How to hand in**: to be updated <br>
**Deadline**: Sun 19 April 2026, till the end of day<br>
**Grade:** Pass with distinction / Pass / Not yet passed

*Exam data with instruction for Part 3 will be released after the group presentation.*

---
*All data in this project is synthetic and created for educational purposes only.*