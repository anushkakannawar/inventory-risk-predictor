# Requirements Document: Inventory Risk Predictor AI

## Introduction

The Inventory Risk Predictor AI is a React-based inventory management application that uses Monte Carlo simulation to predict and analyze inventory risks. The system helps businesses optimize their inventory levels by identifying overstock, understock, and dead inventory risks through statistical simulation and financial impact analysis. The application provides multi-SKU support, scenario planning capabilities, and data persistence through Supabase authentication and storage.

## Glossary

- **System**: The Inventory Risk Predictor AI application
- **Monte_Carlo_Engine**: The simulation component that runs stochastic demand and lead time simulations
- **Risk_Analyzer**: The component that calculates risk metrics from simulation results
- **Financial_Calculator**: The component that computes carrying costs and stockout losses
- **SKU**: Stock Keeping Unit - a unique identifier for each product
- **Reorder_Point**: The inventory level at which a new order should be placed
- **Lead_Time**: The time between placing an order and receiving inventory
- **Carrying_Cost**: The cost of holding inventory over time
- **Stockout**: A situation where inventory is depleted and demand cannot be met
- **Overstock**: Excess inventory beyond optimal levels
- **Simulation_Trajectory**: A single path through the Monte Carlo simulation
- **Risk_Profile**: The collection of risk metrics for a SKU or portfolio
- **CSV_Importer**: The component that parses and validates CSV inventory data
- **Auth_System**: The Supabase-based authentication and user management system
- **Dashboard**: The main interface showing portfolio-level risk metrics
- **Scenario_Analyzer**: The component that enables what-if analysis with parameter variations

## Requirements

### Requirement 1: Monte Carlo Simulation

**User Story:** As an inventory manager, I want to run statistical simulations of inventory levels, so that I can understand the range of possible outcomes and associated risks.

#### Acceptance Criteria

1. WHEN a simulation is requested, THE Monte_Carlo_Engine SHALL execute exactly 100 independent simulation trajectories
2. WHEN simulating demand, THE Monte_Carlo_Engine SHALL generate stochastic demand values using a normal distribution with the specified mean and standard deviation
3. WHEN simulating lead time, THE Monte_Carlo_Engine SHALL vary lead times within ±2 days of the specified mean lead time
4. WHEN inventory reaches the reorder point, THE Monte_Carlo_Engine SHALL place an order that arrives after the simulated lead time
5. WHEN calculating risk metrics, THE Monte_Carlo_Engine SHALL compute the 25th, 50th, and 75th percentiles of inventory levels across all trajectories
6. THE Monte_Carlo_Engine SHALL simulate inventory levels for exactly 365 days per trajectory
7. WHEN demand exceeds available inventory, THE Monte_Carlo_Engine SHALL record a stockout event and set inventory to zero

### Requirement 2: Risk Classification and Analysis

**User Story:** As an inventory manager, I want to see clear risk classifications for each product, so that I can prioritize which items need attention.

#### Acceptance Criteria

1. WHEN calculating overstock probability, THE Risk_Analyzer SHALL compute the percentage of simulation days where inventory exceeded 150% of the reorder point
2. WHEN calculating understock probability, THE Risk_Analyzer SHALL compute the percentage of simulation days where inventory fell below 50% of the reorder point
3. WHEN calculating stockout probability, THE Risk_Analyzer SHALL compute the percentage of simulation days where inventory reached zero
4. WHEN classifying risk level, THE Risk_Analyzer SHALL assign "Low" for probabilities ≤20%, "Medium" for probabilities 21-50%, and "High" for probabilities >50%
5. THE Risk_Analyzer SHALL calculate risk metrics independently for each SKU in the portfolio
6. WHEN aggregating portfolio metrics, THE Risk_Analyzer SHALL compute weighted averages based on SKU value

### Requirement 3: Financial Impact Calculation

**User Story:** As a financial analyst, I want to quantify the financial impact of inventory risks, so that I can justify inventory optimization investments.

#### Acceptance Criteria

1. WHEN calculating carrying costs, THE Financial_Calculator SHALL apply a 20% annual rate to the average inventory value
2. WHEN calculating stockout losses, THE Financial_Calculator SHALL multiply the number of stockout days by the daily revenue rate
3. WHEN computing net risk value, THE Financial_Calculator SHALL sum carrying costs and stockout losses
4. THE Financial_Calculator SHALL calculate financial impacts for each SKU independently
5. WHEN aggregating portfolio financials, THE Financial_Calculator SHALL sum individual SKU impacts

### Requirement 4: Data Import and Validation

**User Story:** As an inventory manager, I want to import inventory data from CSV files, so that I can quickly analyze my existing inventory without manual data entry.

#### Acceptance Criteria

1. WHEN a CSV file is uploaded, THE CSV_Importer SHALL parse the file and extract SKU data
2. WHEN parsing CSV data, THE CSV_Importer SHALL validate that required fields (SKU name, current stock, reorder point, lead time, daily demand mean, daily demand std dev, unit cost, selling price) are present
3. WHEN invalid data is encountered, THE CSV_Importer SHALL return descriptive error messages indicating which fields are invalid
4. WHEN valid CSV data is parsed, THE CSV_Importer SHALL convert string values to appropriate numeric types
5. THE CSV_Importer SHALL handle CSV files with headers in the first row

### Requirement 5: Scenario Analysis

**User Story:** As an inventory planner, I want to test different inventory parameters, so that I can find optimal reorder points and order quantities.

#### Acceptance Criteria

1. WHEN a user modifies simulation parameters, THE Scenario_Analyzer SHALL re-run the Monte Carlo simulation with the new parameters
2. WHEN displaying scenario results, THE System SHALL show side-by-side comparisons of risk metrics before and after parameter changes
3. WHEN a user adjusts reorder point, THE Scenario_Analyzer SHALL immediately recalculate risk probabilities
4. WHEN a user adjusts order quantity, THE Scenario_Analyzer SHALL immediately recalculate financial impacts
5. THE Scenario_Analyzer SHALL preserve original SKU data while testing scenarios

### Requirement 6: Portfolio Dashboard Visualization

**User Story:** As an executive, I want to see a high-level overview of inventory health across all products, so that I can quickly assess overall risk exposure.

#### Acceptance Criteria

1. WHEN displaying the dashboard, THE System SHALL show aggregate metrics for total portfolio value, average risk level, and total financial impact
2. WHEN rendering risk gauges, THE System SHALL use color coding (green for low, yellow for medium, red for high risk)
3. WHEN displaying inventory distribution charts, THE System SHALL show the 25th, 50th, and 75th percentile trajectories
4. WHEN a user selects a SKU, THE System SHALL navigate to the detailed SKU analysis page
5. THE Dashboard SHALL update metrics in real-time when simulation parameters change

### Requirement 7: SKU Detail Analysis

**User Story:** As an inventory analyst, I want to see detailed analysis for individual products, so that I can understand specific risk drivers and optimization opportunities.

#### Acceptance Criteria

1. WHEN displaying SKU details, THE System SHALL show all simulation trajectories overlaid on a single chart
2. WHEN rendering the detail page, THE System SHALL display current parameters (reorder point, lead time, demand statistics)
3. WHEN showing risk breakdown, THE System SHALL separately display overstock, understock, and stockout probabilities
4. WHEN calculating recommendations, THE System SHALL suggest optimal reorder points based on simulation results
5. THE System SHALL display financial impact breakdown (carrying costs vs stockout losses)

### Requirement 8: Authentication and Data Persistence

**User Story:** As a user, I want to securely log in and have my inventory data saved, so that I can access my analysis across sessions and devices.

#### Acceptance Criteria

1. WHEN a user signs up, THE Auth_System SHALL create a new user account with email and password
2. WHEN a user logs in, THE Auth_System SHALL validate credentials and establish an authenticated session
3. WHEN a user saves inventory data, THE System SHALL persist SKU data to the user's Supabase storage
4. WHEN a user logs in, THE System SHALL retrieve previously saved inventory data
5. WHEN a user logs out, THE Auth_System SHALL terminate the session and clear local data
6. THE Auth_System SHALL prevent unauthorized access to other users' data

### Requirement 9: Multi-Currency Support

**User Story:** As an international business user, I want to view financial metrics in my preferred currency, so that I can make decisions in familiar monetary units.

#### Acceptance Criteria

1. WHEN a user selects a currency, THE System SHALL display all financial values in the selected currency
2. THE System SHALL support at least USD, EUR, GBP, and JPY currencies
3. WHEN currency is changed, THE System SHALL update all displayed financial metrics immediately
4. THE System SHALL persist the user's currency preference across sessions
5. WHEN calculating financial impacts, THE System SHALL use the selected currency for all computations

### Requirement 10: Reorder Point Optimization

**User Story:** As an inventory optimizer, I want the system to recommend optimal reorder points, so that I can minimize total inventory costs while maintaining service levels.

#### Acceptance Criteria

1. WHEN analyzing simulation results, THE System SHALL identify reorder points that minimize the sum of carrying costs and stockout losses
2. WHEN displaying recommendations, THE System SHALL show the expected financial impact of implementing the recommended reorder point
3. WHEN comparing reorder points, THE System SHALL calculate the trade-off between overstock risk and stockout risk
4. THE System SHALL recommend reorder points that achieve at least 95% service level (maximum 5% stockout probability)
5. WHEN multiple SKUs are analyzed, THE System SHALL prioritize recommendations by potential financial impact

### Requirement 11: Normal Distribution Generation

**User Story:** As a system developer, I want accurate random number generation for demand simulation, so that the Monte Carlo results reflect realistic demand variability.

#### Acceptance Criteria

1. WHEN generating stochastic demand, THE Monte_Carlo_Engine SHALL use the Box-Muller transform to convert uniform random numbers to normally distributed values
2. WHEN simulating demand with mean μ and standard deviation σ, THE Monte_Carlo_Engine SHALL generate values that converge to the specified distribution parameters
3. THE Monte_Carlo_Engine SHALL use independent random samples for each simulation day and trajectory
4. WHEN demand is negative due to random variation, THE Monte_Carlo_Engine SHALL treat demand as zero
5. THE Monte_Carlo_Engine SHALL generate reproducible results when provided with the same random seed

### Requirement 12: Data Export

**User Story:** As an inventory manager, I want to export simulation results and risk analysis, so that I can share findings with stakeholders and integrate with other systems.

#### Acceptance Criteria

1. WHEN a user requests data export, THE System SHALL generate a CSV file containing all SKU data and risk metrics
2. WHEN exporting simulation results, THE System SHALL include columns for SKU name, current stock, reorder point, risk probabilities, and financial impacts
3. THE System SHALL format exported data with headers in the first row
4. WHEN export is complete, THE System SHALL trigger a file download in the user's browser
5. THE System SHALL export data in a format compatible with the CSV import functionality

