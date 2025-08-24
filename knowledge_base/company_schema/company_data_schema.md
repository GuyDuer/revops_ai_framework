# Company Data Warehouse Schema Knowledge

This document provides example schema structure for a typical SaaS company's data warehouse. Customize table and field names based on your specific data model.

## Core Revenue and Customer Tables

### salesforce_account_d
Account dimension table with all Salesforce account data:
- **sf_account_id**: Primary key - Salesforce account ID
- **sf_account_name**: Name of the account
- **organization_id**: Foreign key to organization_d.organization_id
- **sf_account_type_custom**: Custom account type (e.g., 'Enterprise Customer', 'Self-Service Customer')
- **account_region**: Geographic region of the account
- **sf_industry**: Industry of the account
- **sf_owner_id**: Foreign key to employee_d.sf_user_id
- **created_at_ts**: Timestamp when account was created

### billing_event_f
Billing events fact table with all revenue data:
- **organization_id**: Foreign key to organization_d.organization_id
- **billing_date_ts**: Date when billing occurred
- **amount**: Revenue amount in USD
- **billing_type**: Type of billing event (subscription, usage, etc.)
- **customer_name**: Customer name in billing system

### organization_d
Organization dimension table with all customer organizations:
- **organization_id**: Primary key - unique organization identifier
- **organization_name**: Name of the organization
- **company_name**: Legal company name
- **is_internal**: Boolean indicating if this is an internal company organization
- **is_verified**: Boolean indicating if the organization is verified
- **verified_at**: Timestamp when the organization was verified

## Sales Calls and Engagement

### gong_call_f
Gong sales calls fact table with call data and AI-generated summaries:
- **gong_call_id**: Primary key - Gong's internal call ID
- **gong_call_name**: Name/title of the call
- **sf_owner_id**: Foreign key to employee_d.sf_user_id (call owner)
- **gong_call_start_ts**: Timestamp when the call started
- **gong_call_duration**: Duration of the call
- **gong_call_brief**: AI-generated brief summary of the call content
- **gong_call_key_points**: AI-generated key points and takeaways from the call
- **gong_primary_account**: Foreign key to salesforce_account_d.sf_account_id
- **gong_related_opportunity**: Foreign key to opportunity_d.opportunity_id

## Opportunities and Deals

### opportunity_d
Opportunity dimension table with all Salesforce opportunity data:
- **opportunity_id**: Primary key - Salesforce opportunity ID
- **opportunity_name**: Name of the opportunity
- **sf_account_id**: Foreign key to salesforce_account_d.sf_account_id
- **stage_name**: Current stage of opportunity (e.g., 'Closed Won', 'Negotiate')
- **amount**: Total contract value in USD
- **probability**: Probability of closing (percentage)
- **close_date**: Expected or actual close date
- **owner_id**: Foreign key to employee_d.sf_user_id

### contact_d
Contact dimension table with customer contact information:
- **contact_id**: Primary key - unique contact identifier
- **contact_name**: Full name of the contact
- **contact_email**: Email address of the contact
- **contact_title**: Job title of the contact
- **sf_account_id**: Foreign key to salesforce_account_d.sf_account_id

### lead_d
Lead dimension table with prospect lead information:
- **lead_id**: Primary key - unique lead identifier
- **lead_name**: Full name of the lead
- **lead_email**: Email address of the lead
- **lead_company**: Company name of the lead
- **lead_status**: Current status of the lead
- **lead_source**: Source of the lead
- **created_date_ts**: Timestamp when the lead was created

## Usage and Consumption

### usage_event_f
Usage events fact table tracking product usage:
- **activity_date**: Date of the activity
- **organization_id**: Foreign key to organization_d.organization_id
- **usage_type**: Type of usage (queries, storage, API calls, etc.)
- **usage_amount**: Amount of usage consumed
- **usage_cost_usd**: Cost of usage in USD

### employee_d
Employee dimension table:
- **sf_user_id**: Primary key - Salesforce user ID
- **first_name**: First name of the employee
- **last_name**: Last name of the employee
- **user_email**: Email of the employee

## Common Query Patterns

### Monthly Revenue Analysis
```sql
SELECT 
    date_trunc('month', billing_date_ts) AS month,
    sf_account_type_custom,
    SUM(amount) AS total_revenue
FROM billing_event_f
JOIN salesforce_account_d ON billing_event_f.organization_id = salesforce_account_d.organization_id
WHERE billing_date_ts >= '2024-01-01'
GROUP BY month, sf_account_type_custom
ORDER BY month DESC;
```

### Opportunity Analysis  
```sql
SELECT
    sa.sf_account_name,
    o.opportunity_name,
    o.stage_name,
    o.amount,
    o.probability,
    emp.first_name || ' ' || emp.last_name AS owner_name
FROM opportunity_d o
JOIN salesforce_account_d sa ON o.sf_account_id = sa.sf_account_id
JOIN employee_d emp ON o.owner_id = emp.sf_user_id
WHERE o.stage_name NOT IN ('Closed Lost')
ORDER BY o.amount DESC;
```

### Recent Sales Calls
```sql
SELECT 
    gc.gong_call_name,
    gc.gong_call_start_ts,
    gc.gong_call_brief,
    sa.sf_account_name,
    emp.first_name || ' ' || emp.last_name AS call_owner
FROM gong_call_f gc
LEFT JOIN salesforce_account_d sa ON gc.gong_primary_account = sa.sf_account_id
LEFT JOIN employee_d emp ON gc.sf_owner_id = emp.sf_user_id
WHERE gc.gong_call_start_ts >= CURRENT_DATE - INTERVAL 30 DAY
ORDER BY gc.gong_call_start_ts DESC;
```

## Best Practices

- Always join with employee_d to get human-readable owner names
- Segment analysis by customer type (sf_account_type_custom)  
- Use appropriate date filters for performance
- Include data quality checks in queries
- Consider time zones when analyzing timestamp data

This schema provides a foundation for typical SaaS RevOps analysis. Customize field names, table structures, and relationships based on your specific data model.