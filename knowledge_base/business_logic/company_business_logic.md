# ACME SaaS Business Logic and Customer Lifecycle

## Customer Classification System

This document provides high-level examples of customer segmentation for a typical SaaS company. Customize these categories based on your business model.

## Customer Types

### 1. Enterprise Customers
**Definition**: Large organizations with formal contracts and dedicated support.

**Characteristics**:
- Annual contracts $50K+
- Dedicated account teams
- Custom integrations and features
- Enterprise SLA requirements

**Business Indicators**:
- `sf_account_type_custom` = 'Enterprise Customer'
- Active in `agreement_f` table
- High monthly consumption values

### 2. Self-Service Customers
**Definition**: Customers using product through self-service signup who have converted to paid plans.

**Characteristics**:
- Started with free trial or freemium
- Monthly subscription payments
- Standard product features
- Self-service support primarily

**Business Indicators**:
- `sf_account_type_custom` = 'Self-Service Customer' 
- Regular billing in `billing_event_f`
- Standard pricing plans

### 3. Trial Users
**Definition**: Prospects evaluating the product through free trials or freemium plans.

**Characteristics**:
- Limited feature access
- Time-bound or usage-bound trials
- No payment method required initially
- Sales qualification in progress

**Business Indicators**:
- Trial status in user tables
- Limited usage patterns
- No billing events yet

## Customer Journey

```
Trial User → Self-Service Customer → Enterprise Customer
     ↓              ↓                      ↓
Free Trial → Monthly Plans → Annual Contracts
```

## Revenue Classification

- **Primary Revenue**: Enterprise Customer contracts and expansions
- **Growth Revenue**: Self-Service Customer monthly recurring revenue  
- **Pipeline Revenue**: Trial User conversion opportunities

## Example Customer Health Scoring

### Enterprise Customers
- Contract utilization rates
- Support ticket trends
- Renewal risk indicators
- Expansion opportunities

### Self-Service Customers
- Monthly usage consistency
- Feature adoption rates  
- Upgrade readiness signals
- Churn risk factors

### Trial Users
- Trial engagement levels
- Feature exploration depth
- Time to first value
- Sales qualification status

## Common Analysis Patterns

Always segment analysis by customer type as each has different:
- **Revenue patterns** (annual vs monthly)
- **Usage behaviors** (enterprise vs individual)
- **Support needs** (dedicated vs self-service)
- **Success metrics** (retention vs conversion)

This segmentation ensures accurate benchmarking and appropriate business recommendations for each customer type.