# 🎯 Optimization Study Guide

## Core Framework

### 1. Setup the Question
**Template:** *"How to [action] to [optimize goal] while [staying within limits]?"*

**Examples:**
- "How to allocate budget to **maximize ROI** while **spending ≤ $100k**?"
- "How to schedule staff to **minimize cost** while **meeting coverage**?"

### 2. Variables
**What you can control/decide**

**Ask:** *"What decisions am I making?"*

**Examples:**
- Budget allocation: `[Social, TV, Radio, Online]`
- Staff scheduling: `[Morning, Afternoon, Night]`
- Retention: `[Winback_Month2, Winback_Month3, ...]`

### 3. Objective
**What you want to optimize (minimize or maximize)**

**Ask:** *"What's my main goal?"*

**Examples:**
- `minimize(total_cost)`
- `maximize(retention_rate)` → `minimize(-retention_rate)`
- `minimize(risk)`

### 4. Constraints
**Rules you must follow**

**Ask:** *"What are my limits and requirements?"*

**Types:**
- **Budget:** `total_spend ≤ 100000`
- **Requirements:** `retention ≥ 0.85`
- **Capacity:** `staff_per_shift ≤ max_capacity`
- **Balance:** `sum(allocations) = total_budget`

---

## Key Insights

### Objective vs Constraints
- **Objective Function:** Says the **direction** you want to go (minimize cost, maximize profit)
- **Constraints:** Set the **boundaries** and **requirements**

### The Right Question is Everything
> **"Optimization doesn't solve business problems. It solves the mathematical version of how you frame your business problem."**

**Same data, different questions = completely different strategies**

### Variables ≠ Constraints
- You don't need equal numbers of variables and constraints
- Constraints depend on business rules, not variable count

---

## Example 1: Marketing Budget Allocation

**Question:** How to allocate $10,000 across 4 marketing channels to maximize ROI while ensuring minimum spend in each?

**Setup:**
- **Variables:** [Social_Media, TV_Ads, Radio, Online]
- **Objective:** Maximize total ROI
- **Constraints:** Min $1000 per channel, total budget = $10,000

```python
import numpy as np
from scipy.optimize import minimize

def marketing_roi(budget):
    """Calculate negative ROI to maximize (since minimize() minimizes)"""
    roi_rates = [0.15, 0.08, 0.12, 0.20]  # 15%, 8%, 12%, 20% returns
    total_roi = sum(amount * roi_rates[i] for i, amount in enumerate(budget))
    return -total_roi  # Return negative (to maximize with minimize())

def budget_constraint(budget):
    """Total budget must equal exactly $10,000"""
    return sum(budget) - 10000  # Must equal 0 for 'eq' constraint

def min_spend_constraint(budget):
    """Each channel must get at least $1000"""
    return min(budget) - 1000  # Minimum value must be >= 1000

# Setup
initial_guess = [2500, 2500, 2500, 2500]
bounds = [(1000, 8000) for _ in range(4)]
constraints = [
    {'type': 'eq', 'fun': budget_constraint},
    {'type': 'ineq', 'fun': min_spend_constraint}
]

# Optimize
result = minimize(marketing_roi, initial_guess, method='SLSQP', 
                 bounds=bounds, constraints=constraints)
```

---

## Example 2: Customer Retention Program

**Question:** How to minimize budget on retention program while achieving 85% retention?

**Setup:**
- **Variables:** [Winback_contacts_month2, ..., Winback_contacts_month13]
- **Objective:** Minimize total budget
- **Constraints:** 85% retention, contact limits, 95% success rate

```python
def total_budget(winback_contacts):
    """Minimize total budget for retention program"""
    cost_per_contact = 30
    return sum(winback_contacts) * cost_per_contact

def simulate_members(winback_contacts):
    """Simulate member count month by month"""
    members = [1000]  # Start with 1000 members
    retention_rates = [1.0, 0.90, 0.88, 0.86, 0.84, 0.82, 0.80, 0.78, 0.76, 0.74, 0.72, 0.70, 0.68]
    
    for month in range(1, 13):
        retained = members[month-1] * retention_rates[month]
        winback_success = winback_contacts[month-1] * 0.95
        members.append(retained + winback_success)
    
    return members

def retention_goal_constraint(winback_contacts):
    """13th month retention rate must be >= 85%"""
    members = simulate_members(winback_contacts)
    final_retention_rate = members[12] / 1000
    return final_retention_rate - 0.85

def contact_capacity_constraints(winback_contacts):
    """Can't contact more than customers lost each month"""
    members = [1000]
    retention_rates = [1.0, 0.90, 0.88, 0.86, 0.84, 0.82, 0.80, 0.78, 0.76, 0.74, 0.72, 0.70, 0.68]
    
    constraints = []
    for month in range(1, 13):
        retained = members[month-1] * retention_rates[month]
        lost_customers = members[month-1] - retained
        constraint = lost_customers - winback_contacts[month-1]
        constraints.append(constraint)
        
        winback_success = winback_contacts[month-1] * 0.95
        members.append(retained + winback_success)
    
    return min(constraints)

# Setup
initial_guess = [30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85]
bounds = [(0, 300) for _ in range(12)]
constraints = [
    {'type': 'ineq', 'fun': retention_goal_constraint},
    {'type': 'ineq', 'fun': contact_capacity_constraints}
]

# Optimize
result = minimize(total_budget, initial_guess, method='SLSQP',
                 bounds=bounds, constraints=constraints)
```

---

## Business Context Changes

### Scenario 1: No Budget Limit
**Question:** *"What's the cheapest way to achieve 85% retention?"*
- **Objective:** Minimize cost
- **Constraint:** Retention ≥ 85%

### Scenario 2: $100k Budget Limit
**Question:** *"What's the best retention we can achieve with $100k?"*
- **Objective:** Maximize retention
- **Constraint:** Budget ≤ $100k

```python
# Change objective function for budget-constrained scenario
def negative_final_members(winback_contacts):
    """Maximize final member count (minimize negative)"""
    members = simulate_members(winback_contacts)
    return -members[12]

def budget_constraint(winback_contacts):
    """Total budget must not exceed $100,000"""
    cost_per_contact = 30
    total_cost = sum(winback_contacts) * cost_per_contact
    return 100000 - total_cost

# New constraints for budget scenario
constraints = [
    {'type': 'ineq', 'fun': budget_constraint},
    {'type': 'ineq', 'fun': contact_capacity_constraints}
]

# Optimize for maximum retention within budget
result = minimize(negative_final_members, initial_guess, method='SLSQP',
                 bounds=bounds, constraints=constraints)
```

---

## Quick Reference

### Standard Template
```python
from scipy.optimize import minimize

# 1. Define objective function
def objective_function(variables):
    # Calculate what you want to minimize/maximize
    return result

# 2. Define constraints
def constraint_function(variables):
    # Return >= 0 for 'ineq' constraints
    # Return = 0 for 'eq' constraints
    return constraint_value

# 3. Setup optimization
initial_guess = [...]  # Starting values
bounds = [(min, max), ...]  # Variable limits
constraints = [
    {'type': 'ineq', 'fun': constraint_function},
    {'type': 'eq', 'fun': another_constraint}
]

# 4. Run optimization
result = minimize(objective_function, initial_guess, 
                 method='SLSQP', bounds=bounds, constraints=constraints)
```

### Constraint Types
- **'eq':** Equality constraint (function must return 0)
- **'ineq':** Inequality constraint (function must return ≥ 0)

### Common Patterns
- **Budget constraints:** `budget_limit - total_spent`
- **Minimum requirements:** `actual_value - minimum_required`
- **Capacity limits:** `capacity_limit - usage`
- **Balance equations:** `total_in - total_out`

---

## Remember
1. **Question framing is critical** - same data, different questions = different solutions
2. **Constraints encode business rules** - they're as important as the objective
3. **Variables are your decisions** - what you can control
4. **Objective is your goal** - what you want to optimize