# Design Document: Inventory Risk Predictor AI

## Overview

The Inventory Risk Predictor AI is a Next.js 14 application that combines Monte Carlo simulation with Google Gemini AI to provide inventory risk analysis. The system follows a modern, boutique Fintech aesthetic with glassmorphism design patterns, smooth animations, and professional data visualization.

The architecture separates concerns into distinct layers:
- **Presentation Layer**: Next.js App Router pages with Tailwind CSS and Framer Motion
- **Business Logic Layer**: Monte Carlo simulation engine and risk calculation algorithms
- **Data Layer**: Supabase for authentication and PostgreSQL storage
- **AI Integration Layer**: Google Gemini API for natural language insights

The application is optimized for hackathon demonstration, prioritizing visual polish, instant performance, and clear value proposition over enterprise features like multi-SKU analysis or historical data import.

## Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Next.js 14 App Router                    │
├─────────────────────────────────────────────────────────────┤
│  Authentication Pages    │         Dashboard Page            │
│  - /login               │  - /dashboard                      │
│  - /register            │                                    │
└─────────────────────────────────────────────────────────────┘
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
        ▼                 ▼                 ▼
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│   Supabase   │  │ Risk Engine  │  │  Gemini API  │
│     Auth     │  │  (Monte      │  │  (AI Insight)│
│              │  │   Carlo)     │  │              │
└──────────────┘  └──────────────┘  └──────────────┘
        │
        ▼
┌──────────────┐
│  PostgreSQL  │
│   Database   │
└──────────────┘
```

### Component Architecture

```
app/
├── (auth)/
│   ├── login/
│   │   └── page.tsx          # Login page with glassmorphism form
│   └── register/
│       └── page.tsx          # Registration page with glassmorphism form
├── dashboard/
│   └── page.tsx              # Protected dashboard route
├── api/
│   ├── simulate/
│   │   └── route.ts          # Monte Carlo simulation endpoint
│   └── ai-insight/
│       └── route.ts          # Gemini API integration endpoint
└── layout.tsx                # Root layout with global styles

components/
├── auth/
│   ├── LoginForm.tsx         # Login form with validation
│   ├── RegisterForm.tsx      # Registration form with validation
│   └── AuthLayout.tsx        # Split layout wrapper for auth pages
├── dashboard/
│   ├── InputPanel.tsx        # Inventory parameter inputs
│   ├── MagicFillButton.tsx   # Demo data generator
│   ├── RiskChart.tsx         # Recharts area chart visualization
│   └── AIInsightCard.tsx     # Typing animation for AI recommendations
└── ui/
    ├── GlassCard.tsx         # Reusable glassmorphism card
    ├── GlassInput.tsx        # Styled input with focus states
    └── LoadingSpinner.tsx    # Loading state indicator

lib/
├── simulation/
│   ├── monteCarlo.ts         # Core Monte Carlo simulation logic
│   └── riskCalculator.ts     # Risk probability calculations
├── ai/
│   └── geminiClient.ts       # Gemini API client wrapper
├── supabase/
│   ├── client.ts             # Supabase client initialization
│   └── auth.ts               # Authentication helpers
└── utils/
    ├── validators.ts         # Input validation functions
    └── demoData.ts           # Magic Fill data generator
```

### Technology Stack

- **Frontend Framework**: Next.js 14 with App Router and TypeScript
- **Styling**: Tailwind CSS with custom glassmorphism utilities
- **Animation**: Framer Motion for component animations
- **Charts**: Recharts for data visualization
- **Backend**: Next.js API Routes
- **Database**: Supabase (PostgreSQL + Auth)
- **AI**: Google Gemini API (gemini-pro model)
- **Fonts**: Inter (UI), Space Grotesk (Headings/Numbers)

## Components and Interfaces

### Authentication Components

#### LoginForm Component
```typescript
interface LoginFormProps {
  onSuccess: () => void;
}

interface LoginFormState {
  email: string;
  password: string;
  error: string | null;
  isLoading: boolean;
}

// Handles user login with Supabase Auth
// Validates email format and password requirements
// Displays inline error messages for validation failures
// Redirects to dashboard on successful authentication
```

#### RegisterForm Component
```typescript
interface RegisterFormProps {
  onSuccess: () => void;
}

interface RegisterFormState {
  email: string;
  password: string;
  confirmPassword: string;
  error: string | null;
  isLoading: boolean;
}

// Handles user registration with Supabase Auth
// Validates email format, password strength, and password confirmation
// Creates new user account and automatically logs in
// Redirects to dashboard on successful registration
```

#### AuthLayout Component
```typescript
interface AuthLayoutProps {
  children: React.ReactNode;
  visualType: 'mesh' | 'gradient' | 'particles';
}

// Provides split layout for authentication pages
// Left side: Glass-themed form container
// Right side: Abstract 3D/mesh visual background
// Responsive design that stacks on mobile
```

### Dashboard Components

#### InputPanel Component
```typescript
interface InputPanelProps {
  onSimulate: (params: InventoryParams) => void;
  isSimulating: boolean;
}

interface InventoryParams {
  currentStock: number;
  avgDailySales: number;
  leadTime: number;
  reorderQuantity: number;
}

interface InputPanelState {
  params: InventoryParams;
  errors: Partial<Record<keyof InventoryParams, string>>;
  touched: Partial<Record<keyof InventoryParams, boolean>>;
}

// Renders four input fields for inventory parameters
// Validates inputs in real-time (positive numbers, reasonable ranges)
// Displays inline validation errors
// Enables simulate button only when all inputs are valid
// Includes Magic Fill button for demo data
```

#### MagicFillButton Component
```typescript
interface MagicFillButtonProps {
  onFill: (params: InventoryParams) => void;
}

// Generates realistic synthetic inventory data
// Uses randomization within business-realistic ranges
// Animates button with sparkle effect on click
// Immediately populates all input fields
```

#### RiskChart Component
```typescript
interface RiskChartProps {
  simulationData: SimulationResult;
  isAnimating: boolean;
}

interface SimulationResult {
  days: number[];
  inventoryLevels: number[][];  // Array of trajectories
  medianTrajectory: number[];
  percentile25: number[];
  percentile75: number[];
  stockoutProbability: number;
  overstockProbability: number;
}

// Renders area chart using Recharts
// Displays median inventory trajectory as primary line
// Shows 25th-75th percentile range as shaded area (Cone of Uncertainty)
// Animates chart entrance with Framer Motion
// Uses gradient fills matching glassmorphism theme
// Responsive to container width
```

#### AIInsightCard Component
```typescript
interface AIInsightCardProps {
  insight: string;
  isLoading: boolean;
}

interface AIInsightCardState {
  displayedText: string;
  isTyping: boolean;
}

// Displays AI-generated recommendation from Gemini
// Implements typing animation effect (character-by-character reveal)
// Shows loading spinner while waiting for AI response
// Displays fallback message if AI is unavailable
// Glassmorphism card styling with subtle glow effect
```

### Core Business Logic

#### Monte Carlo Simulation Engine
```typescript
interface SimulationConfig {
  currentStock: number;
  avgDailySales: number;
  leadTime: number;
  reorderQuantity: number;
  numDays: number;           // Fixed at 1000
  numSimulations: number;    // Fixed at 100 trajectories
}

interface DailySimulationState {
  day: number;
  inventory: number;
  sales: number;
  reorderTriggered: boolean;
}

interface SimulationResult {
  trajectories: number[][];
  stockoutCount: number;
  overstockCount: number;
  medianTrajectory: number[];
  percentile25: number[];
  percentile75: number[];
  stockoutProbability: number;
  overstockProbability: number;
}

function runMonteCarloSimulation(config: SimulationConfig): SimulationResult {
  // For each of 100 simulation runs:
  //   1. Initialize inventory at currentStock
  //   2. For each of 1000 days:
  //      a. Generate daily sales using normal distribution (mean=avgDailySales, stdDev=avgDailySales*0.3)
  //      b. Subtract sales from inventory
  //      c. Check if inventory <= reorder point (avgDailySales * leadTime)
  //      d. If reorder triggered, add reorderQuantity after leadTime days
  //      e. Track if inventory hits zero (stockout) or exceeds 2x avgDailySales*leadTime (overstock)
  //   3. Store complete trajectory
  // 
  // Calculate aggregate statistics:
  //   - Median trajectory across all runs
  //   - 25th and 75th percentile trajectories
  //   - Stockout probability = (runs with stockout) / total runs
  //   - Overstock probability = (runs with overstock) / total runs
  //
  // Return complete simulation result
}
```

#### Risk Calculator
```typescript
interface RiskMetrics {
  stockoutProbability: number;
  overstockProbability: number;
  riskLevel: 'low' | 'medium' | 'high' | 'critical';
  recommendation: string;
}

function calculateRiskMetrics(result: SimulationResult): RiskMetrics {
  // Determine risk level based on probabilities:
  //   - Critical: stockout > 30% OR overstock > 50%
  //   - High: stockout > 15% OR overstock > 30%
  //   - Medium: stockout > 5% OR overstock > 15%
  //   - Low: otherwise
  //
  // Generate basic recommendation based on dominant risk
}
```

### AI Integration

#### Gemini Client
```typescript
interface GeminiRequest {
  simulationResult: SimulationResult;
  inventoryParams: InventoryParams;
}

interface GeminiResponse {
  insight: string;
  confidence: number;
}

async function generateAIInsight(request: GeminiRequest): Promise<GeminiResponse> {
  // Construct prompt for Gemini API:
  //   "You are an inventory management expert. Based on the following simulation:
  //    - Stockout probability: X%
  //    - Overstock probability: Y%
  //    - Current stock: Z units
  //    - Avg daily sales: W units
  //    - Lead time: L days
  //    - Reorder quantity: R units
  //    
  //    Provide a 2-sentence executive recommendation focusing on the highest risk.
  //    Be specific with numbers and actionable steps."
  //
  // Call Gemini API with gemini-pro model
  // Parse response and extract recommendation text
  // Return insight with confidence score
}
```

### Database Schema

#### Users Table (Supabase Auth)
```sql
-- Managed by Supabase Auth
-- Standard user authentication fields
-- No custom fields needed for hackathon scope
```

#### Simulations Table (Optional - Not Required for Hackathon)
```sql
-- Future enhancement for persisting simulation history
-- Not implemented in hackathon version
-- Session-based storage is sufficient for demo
```

## Data Models

### InventoryParams
```typescript
interface InventoryParams {
  currentStock: number;        // Current inventory level (units)
  avgDailySales: number;       // Average daily sales rate (units/day)
  leadTime: number;            // Supplier lead time (days)
  reorderQuantity: number;     // Quantity to order when reorder point hit (units)
}

// Validation rules:
// - All values must be positive numbers
// - currentStock: 1 - 100,000
// - avgDailySales: 0.1 - 10,000
// - leadTime: 1 - 365
// - reorderQuantity: 1 - 100,000
```

### SimulationResult
```typescript
interface SimulationResult {
  trajectories: number[][];           // [simulation][day] = inventory level
  stockoutCount: number;              // Number of simulations with stockout
  overstockCount: number;             // Number of simulations with overstock
  medianTrajectory: number[];         // Median inventory level per day
  percentile25: number[];             // 25th percentile per day
  percentile75: number[];             // 75th percentile per day
  stockoutProbability: number;        // Percentage (0-100)
  overstockProbability: number;       // Percentage (0-100)
  executionTimeMs: number;            // Performance metric
}
```

### AIInsight
```typescript
interface AIInsight {
  text: string;                       // 2-sentence recommendation from Gemini
  timestamp: Date;                    // When insight was generated
  confidence: number;                 // AI confidence score (0-1)
  fallback: boolean;                  // True if using fallback message
}
```

### DemoData
```typescript
interface DemoData {
  scenarios: {
    balanced: InventoryParams;        // Low risk scenario
    stockoutRisk: InventoryParams;    // High stockout risk
    overstockRisk: InventoryParams;   // High overstock risk
  };
}

// Predefined scenarios for Magic Fill:
// - Balanced: currentStock=1000, avgDailySales=50, leadTime=14, reorderQuantity=700
// - Stockout Risk: currentStock=200, avgDailySales=80, leadTime=20, reorderQuantity=300
// - Overstock Risk: currentStock=5000, avgDailySales=30, leadTime=10, reorderQuantity=2000
```


## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Input Validation Accepts Valid Values

*For any* positive number provided as Current Stock, Average Daily Sales, Lead Time, or Reorder Quantity, the Dashboard input validation should accept the value and not display an error message.

**Validates: Requirements 2.1, 2.2, 2.3, 2.4**

### Property 2: Input Validation Rejects Invalid Values

*For any* non-positive number, non-numeric value, or null value provided as an inventory parameter input, the Dashboard validation should reject the value and display an inline error message.

**Validates: Requirements 2.5**

### Property 3: Valid Inputs Enable Simulation

*For any* complete set of valid inventory parameters (all four fields with positive values), the Dashboard should enable the simulation trigger button.

**Validates: Requirements 2.6**

### Property 4: Magic Fill Generates Values in Range

*For any* invocation of the Magic Fill function, all generated values should fall within their specified ranges: Current Stock (100-5000), Average Daily Sales (10-200), Lead Time (5-30), and Reorder Quantity (100-2000).

**Validates: Requirements 3.2, 3.3, 3.4, 3.5**

### Property 5: Simulation Produces 1000-Day Results

*For any* valid inventory parameters, the Monte Carlo simulation should produce results containing exactly 1,000 daily data points for inventory levels.

**Validates: Requirements 4.1, 4.3**

### Property 6: Sales Volatility Has Statistical Properties

*For any* simulation run, the generated daily sales values should have a mean approximately equal to the input avgDailySales (within 10% tolerance) and a standard deviation approximately equal to avgDailySales * 0.3 (within 20% tolerance).

**Validates: Requirements 4.2**

### Property 7: Probabilities Are Valid Percentages

*For any* simulation result, both stockout probability and overstock probability should be numbers in the range [0, 100].

**Validates: Requirements 4.4, 4.5**

### Property 8: Simulation Result Structure Is Complete

*For any* valid simulation execution, the returned result should contain all required fields: trajectories, stockoutCount, overstockCount, medianTrajectory, percentile25, percentile75, stockoutProbability, and overstockProbability.

**Validates: Requirements 4.7**

### Property 9: Error Handling Displays Messages

*For any* error condition (API failure, validation failure, network error), the System should display a user-friendly error message and log the error to the console.

**Validates: Requirements 10.1, 10.2, 10.5**

## Error Handling

### Input Validation Errors
- **Invalid Number Format**: Display "Please enter a valid number" inline with the field
- **Negative Values**: Display "Value must be positive" inline with the field
- **Empty Fields**: Display "This field is required" inline with the field
- **Out of Range**: Display "Value must be between X and Y" inline with the field

### Simulation Errors
- **Execution Failure**: Display toast notification "Simulation failed. Please try again."
- **Timeout**: Display toast notification "Simulation is taking longer than expected. Please refresh and try again."
- **Invalid Configuration**: Display inline errors on problematic input fields

### AI Integration Errors
- **Gemini API Unavailable**: Display fallback message in AI Insight Card: "AI insights are temporarily unavailable. Your simulation results are still valid."
- **API Rate Limit**: Display fallback message: "AI service is busy. Please try again in a moment."
- **Invalid Response**: Display fallback message: "Unable to generate AI insights at this time."
- **Network Error**: Display fallback message: "Connection issue. AI insights unavailable."

### Authentication Errors
- **Invalid Credentials**: Display "Invalid email or password" below login form
- **Email Already Exists**: Display "An account with this email already exists" below registration form
- **Weak Password**: Display "Password must be at least 8 characters" below password field
- **Network Error**: Display "Unable to connect. Please check your internet connection."
- **Session Expired**: Redirect to login page with message "Your session has expired. Please log in again."

### Error Recovery
- All error messages should be dismissible
- Form state should be preserved when errors occur
- Users should be able to retry failed operations without losing data
- Critical errors should provide actionable next steps

### Error Logging
- All errors logged to browser console with stack traces
- Include context: user action, input values (sanitized), timestamp
- Format: `[ERROR] [Component] [Action]: Message`
- Example: `[ERROR] [MonteCarloSimulation] [runSimulation]: Invalid avgDailySales value: -5`

## Testing Strategy

### Dual Testing Approach

This project requires both unit testing and property-based testing to ensure comprehensive coverage:

- **Unit Tests**: Verify specific examples, edge cases, error conditions, and integration points
- **Property Tests**: Verify universal properties across all inputs through randomization

Both approaches are complementary and necessary. Unit tests catch concrete bugs in specific scenarios, while property tests verify general correctness across a wide input space.

### Property-Based Testing Configuration

**Library Selection**: Use `fast-check` for TypeScript/JavaScript property-based testing

**Configuration Requirements**:
- Minimum 100 iterations per property test (due to randomization)
- Each property test must reference its design document property
- Tag format: `// Feature: inventory-risk-predictor, Property {number}: {property_text}`
- Each correctness property must be implemented by a SINGLE property-based test

**Example Property Test Structure**:
```typescript
import fc from 'fast-check';

// Feature: inventory-risk-predictor, Property 1: Input Validation Accepts Valid Values
test('Dashboard accepts all positive numbers as valid inputs', () => {
  fc.assert(
    fc.property(
      fc.integer({ min: 1, max: 100000 }),
      fc.float({ min: 0.1, max: 10000 }),
      fc.integer({ min: 1, max: 365 }),
      fc.integer({ min: 1, max: 100000 }),
      (currentStock, avgDailySales, leadTime, reorderQuantity) => {
        const result = validateInventoryParams({
          currentStock,
          avgDailySales,
          leadTime,
          reorderQuantity
        });
        expect(result.isValid).toBe(true);
        expect(result.errors).toEqual({});
      }
    ),
    { numRuns: 100 }
  );
});
```

### Unit Testing Strategy

**Focus Areas for Unit Tests**:
1. **Authentication Flow Examples**:
   - Successful login with valid credentials
   - Failed login with invalid credentials
   - Successful registration with new email
   - Failed registration with existing email
   - Route protection for unauthenticated users

2. **Magic Fill Examples**:
   - Button click populates all fields
   - Generated values enable simulation button
   - Multiple clicks generate different values

3. **Simulation Edge Cases**:
   - Zero initial stock
   - Very high sales volatility
   - Very long lead times
   - Reorder quantity larger than initial stock

4. **AI Integration Examples**:
   - Successful Gemini API response
   - Failed API call with fallback message
   - Typing animation displays full text

5. **Session Persistence Examples**:
   - Simulation results persist during session
   - Results cleared on logout
   - Results not available in new session

6. **Error Handling Examples**:
   - API failure displays error message
   - Network error displays connection message
   - Validation error highlights field

**Unit Test Organization**:
```
__tests__/
├── auth/
│   ├── LoginForm.test.tsx
│   ├── RegisterForm.test.tsx
│   └── routeProtection.test.ts
├── dashboard/
│   ├── InputPanel.test.tsx
│   ├── MagicFillButton.test.tsx
│   ├── RiskChart.test.tsx
│   └── AIInsightCard.test.tsx
├── simulation/
│   ├── monteCarlo.test.ts
│   ├── riskCalculator.test.ts
│   └── monteCarlo.properties.test.ts  # Property-based tests
├── ai/
│   └── geminiClient.test.ts
└── utils/
    ├── validators.test.ts
    ├── validators.properties.test.ts  # Property-based tests
    └── demoData.test.ts
```

### Testing Tools
- **Test Runner**: Jest with React Testing Library
- **Property Testing**: fast-check
- **Mocking**: Jest mocks for Supabase and Gemini API
- **Coverage Target**: 80% code coverage minimum
- **Performance Testing**: Manual verification that simulation completes < 1 second

### Integration Testing
- Test complete flow: Login → Input → Simulate → View Results → AI Insight
- Test error recovery: Failed API → Retry → Success
- Test session management: Simulate → Logout → Login → No Results

### Visual Testing (Manual for Hackathon)
- Verify glassmorphism styling on all components
- Verify animations are smooth (60fps)
- Verify responsive layout on mobile/tablet/desktop
- Verify color gradients and mesh background
- Verify typing animation in AI Insight Card
