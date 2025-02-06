# E-Commerce-Fraud-Analysis

## Overview
ABC Company operates an e-commerce platform and processes thousands of orders daily. To deliver these orders, ABC has partnered with several courier companies in India, which charge fees based on the weight of the products and the distance between the warehouse and the customer's delivery address. ABC wants to verify whether the fees charged by courier companies for each order are correct.

## Data Sources
ABC's internal data is split across three reports:
1. **Website Order Report** - Includes order IDs and SKUs for each order.
2. **Master SKU** - Provides the gross weight of each product, which is necessary to calculate the total weight of each order.
3. **Warehouse PIN to All India Pincode Mapping** - Maps warehouse PINs to delivery PINs to determine delivery areas.

Additionally, ABC receives **billing data from courier companies**, which includes:
- AWB Number
- Order ID
- Shipment Weight
- Warehouse Pickup PIN
- Customer Delivery PIN
- Delivery Area
- Charge per Shipment
- Type of Shipment

## Objective
ABC aims to:
1. Compare the total weight of each order calculated using the SKU master with the weight stated by the courier company in their invoice.
2. Ensure that weight is rounded up to the nearest multiple of 0.5 kg.
3. Verify the delivery area using warehouse PIN mappings.
4. Calculate expected charges based on weight slabs, delivery area, and type of shipment.
5. Compare expected charges with the courier company's invoiced charges to identify discrepancies.

## Data Processing Steps

### Step 1: Extract Unique Customer Pincodes
- Extract unique customer PIN codes from the **pincode mapping dataset** and store them in the `abc_courier` DataFrame.
- This ensures that each PIN code is unique and correctly associated with the respective delivery area.

### Step 2: Filter and Select Relevant Data
- From the **courier invoice dataset**, select the following columns:
  - `Order ID`
  - `Customer Pincode`
  - `Type of Shipment`
- Store this subset in the `courier_abc` DataFrame.

### Step 3: Merge Data
- Merge `courier_abc` with `abc_courier` on `Customer Pincode` to link customer PIN codes with their respective orders and shipping details.
- Store the merged dataset in `pincodes` DataFrame.

### Step 4: Calculate Weight Slab
- Define the `weight_slab()` function to determine the applicable weight slab:
  - If the weight is a multiple of 1 kg, return it as is.
  - If the remainder is greater than 0.5, round up to the next full kg.
  - If the remainder is less than or equal to 0.5, round up to the nearest 0.5 kg.
- Apply this function to:
  - `Weights (Kgs)` column in `merged2`
  - `Charged Weight` column in `courier_invoice`

### Step 5: Calculate Expected Charges
- Extract courier rate card details for each `Delivery Zone As Per ABC`:
  - Fixed charge (`_fixed`)
  - Additional charge per weight (`_additional`)
- Apply charge calculation logic:
  - If **Forward Charges**, apply additional charge for weight exceeding 0.5 kg.
  - If **Forward and RTO Charges**, add RTO costs accordingly.
- Store the computed expected charge in the `Expected Charge as per ABC` column in `merged2`.

### Step 6: Identify Fraud or Discrepancies
- Compare `Expected Charge as per ABC` with `Charged Amount` in the courier invoices.
- Flag discrepancies where the charged amount significantly deviates from the expected charge.
- Use these insights to validate invoices and identify potential fraud cases.

## Conclusion
This process enables ABC Company to:
- Validate courier invoices based on actual order weight and delivery zones.
- Detect discrepancies and potential overcharges by courier companies.
- Ensure fair and accurate courier billing practices for cost efficiency and fraud prevention.

