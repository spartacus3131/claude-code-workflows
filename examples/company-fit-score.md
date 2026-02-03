# Example: "Create me a company fit score"

A real walkthrough showing how to turn a vague feature request into working code using Claude Code workflows.

---

## The Request

> "Create me a company fit score"

That's it. That's what the stakeholder said. No spec, no requirements, no mockups.

---

## Step 1: Generate a Spec with `/vibe-pm`

Instead of asking 15 clarifying questions, use the vibe-pm skill to generate a spec with smart inferences.

```
> Create me a company fit score
> /vibe-pm
```

**Claude generates:**

```markdown
## Company Fit Score

**Outcome**: Users can see at a glance how well a company matches their Ideal Customer Profile (ICP).

**Job**: When I'm reviewing a list of prospects, I want to quickly identify which companies are worth pursuing, so I can prioritize my outreach and not waste time on poor fits.

---

### Requirements

**Happy Path**:
```gherkin
Given I'm viewing a company record
When the page loads
Then I see a fit score from 0-100 with a label (Poor/Fair/Good/Excellent)
And I see which ICP criteria matched and which didn't
```

**Scoring Logic**:
| Criteria | Weight | Example |
|----------|--------|---------|
| Industry match | 25 pts | Tech, SaaS, Fintech → full points |
| Employee count in range | 20 pts | 50-500 employees → full points |
| Revenue in range | 20 pts | $5M-$50M ARR → full points |
| Tech stack match | 15 pts | Uses Salesforce, HubSpot → full points |
| Geography match | 10 pts | US, Canada, UK → full points |
| Growth signals | 10 pts | Recent funding, hiring → full points |

**Score Labels**:
| Score | Label | Color |
|-------|-------|-------|
| 0-25 | Poor Fit | Red |
| 26-50 | Fair Fit | Orange |
| 51-75 | Good Fit | Yellow |
| 76-100 | Excellent Fit | Green |

**Edge Cases**:
| Condition | Handling |
|-----------|----------|
| Missing data for a criterion | Skip that criterion, adjust max possible score |
| No ICP defined | Show "Set up your ICP to see fit scores" |
| All criteria missing | Show "Insufficient data" instead of 0 |

---

### Not Building
- NOT: Editable weights (hardcoded for v1)
- NOT: ML-based scoring (rule-based only)
- NOT: Historical score tracking
- NOT: Batch scoring API

### Assumptions Made
- ICP criteria stored in `user_settings.icp_criteria` (JSON field)
- Company data in `companies` table with standard fields
- Using existing Badge component for score display
- Score calculated on read, not stored

---

### Done When
- [ ] Fit score displays on company detail page
- [ ] Score breakdown shows which criteria matched
- [ ] Missing data handled gracefully
- [ ] Works for companies with partial data
```

**Me**: "Good, but we also need this on the company list view, not just detail. And add a sorting option."

**Claude updates the spec** with list view requirements.

---

## Step 2: Build It

With an unambiguous spec, Claude builds without asking questions:

```
> Build this. Start with the scoring function, then the UI components.
```

Claude creates:
- `src/lib/scoring/companyFitScore.ts` - Pure scoring function
- `src/components/FitScoreBadge.tsx` - Display component
- `src/components/FitScoreBreakdown.tsx` - Detailed breakdown
- Updates to `CompanyDetail.tsx` and `CompanyList.tsx`

---

## Step 3: Mid-Build Course Correction

Halfway through, I realize the scoring logic is getting complex. The code works but it's messy.

```
> This scoring function is getting ugly. /elegant-redo
```

**Claude analyzes:**

```markdown
## Current Implementation Analysis

**Core problem**: Calculate weighted score across 6 criteria with partial data handling

**What's wrong**:
- 6 nested if/else blocks for each criterion
- Duplicated "missing data" logic in each block
- Magic numbers scattered throughout
- Hard to add new criteria

**The insight**: This is a reducer pattern. Each criterion is a scoring rule that can be applied independently.

## Elegant Approach

**Key insight**: Define criteria as data, not code. Each criterion is an object with a `score()` function.

**New structure**:
```typescript
const CRITERIA: Criterion[] = [
  {
    name: 'Industry',
    weight: 25,
    score: (company, icp) => icp.industries.includes(company.industry) ? 1 : 0
  },
  // ... more criteria
];

const calculateFitScore = (company, icp) =>
  CRITERIA.reduce((acc, criterion) => {
    const value = criterion.score(company, icp);
    if (value === null) return acc; // Skip missing data
    return {
      score: acc.score + (value * criterion.weight),
      maxPossible: acc.maxPossible + criterion.weight,
      breakdown: [...acc.breakdown, { name: criterion.name, matched: value > 0 }]
    };
  }, { score: 0, maxPossible: 0, breakdown: [] });
```

This replaces 80 lines with 25 lines and makes adding criteria trivial.

Should I proceed?
```

**Me**: "Yes"

Claude rewrites cleanly.

---

## Step 4: Pre-PR Quiz with `/grill`

Before opening a PR, I want to make sure I understand what I built.

```
> /grill
```

**Claude**:

```markdown
## Grill Session

I've reviewed your changes. 4 files changed, 312 additions.

**Question 1 of 5**

The scoring function handles missing data by excluding that criterion from the max possible score. Walk me through what happens if a company has NO data for ANY criterion.

---
Take your time.
```

**Me**: "It would return a score of 0 out of 0 possible, which... oh, that's a division by zero when we calculate the percentage."

**Claude**:

```markdown
Good catch. That's a bug. The edge case table said "Show 'Insufficient data' instead of 0" but the implementation doesn't handle it.

**Question 2 of 5**

You're storing the ICP criteria in `user_settings.icp_criteria` as JSON. What happens if a user hasn't set up their ICP yet?
```

**Me**: "We show the 'Set up your ICP' message... actually let me check the code."

*Finds that the null check is missing*

**Claude**:

```markdown
Right - that's another gap. Let's fix both before continuing.

[After fixes...]

**Question 5 of 5**

The scoring weights are hardcoded. The spec says "NOT building editable weights" - but someone's going to ask for it. Where would you add that capability if asked?

---
```

**Me**: "The weights are in the CRITERIA array. I'd add a user_settings.scoring_weights field and merge it with defaults at runtime."

**Claude**:

```markdown
## Grill Complete

**Result**: READY TO SHIP (after the two fixes)

### Strengths
- Clean separation of scoring logic from display
- Reducer pattern makes criteria easy to add
- Good understanding of extension points

### Areas Fixed During Session
- Division by zero with no valid criteria
- Missing ICP null check

### Recommendation
Make those two fixes, then ship it.
```

---

## Step 5: Capture the Lesson with `/meta-rule`

I made the same "missing null check" mistake twice this week.

```
> /meta-rule
```

**Claude**:

```markdown
I notice you've had null check issues twice now. Want me to add a rule?

Added to CLAUDE.md:

### Code Style

- Always handle the "not configured" state for user settings
  - Context: Any feature that depends on user preferences/settings
  - Check: Before accessing `user_settings.X`, verify it exists and show setup prompt if not
  - Example: `if (!user.settings?.icp_criteria) return <SetupICPPrompt />`

This will apply to future sessions.
```

---

## Final Result

**What I said**: "Create me a company fit score"

**What I shipped** (in ~45 minutes):
- Clean scoring engine with 6 weighted criteria
- FitScoreBadge component with color-coded labels
- FitScoreBreakdown showing matched/unmatched criteria
- List view with sort-by-fit-score
- Proper handling of missing data and unconfigured ICP
- A new rule in CLAUDE.md to prevent future null check bugs

**Skills used**:
1. `/vibe-pm` - Generated unambiguous spec from vague request
2. `/elegant-redo` - Cleaned up messy scoring logic mid-build
3. `/grill` - Caught two bugs before PR
4. `/meta-rule` - Captured lesson for future sessions

---

## The Code

### `src/lib/scoring/companyFitScore.ts`

```typescript
export interface Criterion {
  name: string;
  weight: number;
  score: (company: Company, icp: ICPCriteria) => number | null;
}

export const CRITERIA: Criterion[] = [
  {
    name: 'Industry',
    weight: 25,
    score: (company, icp) => {
      if (!company.industry || !icp.industries?.length) return null;
      return icp.industries.includes(company.industry) ? 1 : 0;
    }
  },
  {
    name: 'Employee Count',
    weight: 20,
    score: (company, icp) => {
      if (!company.employeeCount || !icp.employeeRange) return null;
      const { min, max } = icp.employeeRange;
      return company.employeeCount >= min && company.employeeCount <= max ? 1 : 0;
    }
  },
  {
    name: 'Revenue',
    weight: 20,
    score: (company, icp) => {
      if (!company.revenue || !icp.revenueRange) return null;
      const { min, max } = icp.revenueRange;
      return company.revenue >= min && company.revenue <= max ? 1 : 0;
    }
  },
  {
    name: 'Tech Stack',
    weight: 15,
    score: (company, icp) => {
      if (!company.technologies?.length || !icp.technologies?.length) return null;
      const matches = company.technologies.filter(t => icp.technologies.includes(t));
      return matches.length / icp.technologies.length;
    }
  },
  {
    name: 'Geography',
    weight: 10,
    score: (company, icp) => {
      if (!company.country || !icp.countries?.length) return null;
      return icp.countries.includes(company.country) ? 1 : 0;
    }
  },
  {
    name: 'Growth Signals',
    weight: 10,
    score: (company, icp) => {
      if (!icp.requireGrowthSignals) return null;
      const signals = [company.recentFunding, company.hiringGrowth].filter(Boolean);
      return signals.length > 0 ? 1 : 0;
    }
  }
];

export interface FitScoreResult {
  score: number;
  maxPossible: number;
  percentage: number;
  label: 'Poor Fit' | 'Fair Fit' | 'Good Fit' | 'Excellent Fit' | 'Insufficient Data';
  color: 'red' | 'orange' | 'yellow' | 'green' | 'gray';
  breakdown: { name: string; matched: boolean | null; weight: number }[];
}

export function calculateFitScore(company: Company, icp: ICPCriteria | null): FitScoreResult {
  if (!icp) {
    return {
      score: 0,
      maxPossible: 0,
      percentage: 0,
      label: 'Insufficient Data',
      color: 'gray',
      breakdown: []
    };
  }

  const result = CRITERIA.reduce(
    (acc, criterion) => {
      const value = criterion.score(company, icp);

      if (value === null) {
        return {
          ...acc,
          breakdown: [...acc.breakdown, { name: criterion.name, matched: null, weight: criterion.weight }]
        };
      }

      return {
        score: acc.score + (value * criterion.weight),
        maxPossible: acc.maxPossible + criterion.weight,
        breakdown: [...acc.breakdown, { name: criterion.name, matched: value > 0.5, weight: criterion.weight }]
      };
    },
    { score: 0, maxPossible: 0, breakdown: [] as FitScoreResult['breakdown'] }
  );

  // Handle case where no criteria could be evaluated
  if (result.maxPossible === 0) {
    return {
      ...result,
      percentage: 0,
      label: 'Insufficient Data',
      color: 'gray'
    };
  }

  const percentage = Math.round((result.score / result.maxPossible) * 100);

  const { label, color } = getScoreLabel(percentage);

  return {
    ...result,
    percentage,
    label,
    color
  };
}

function getScoreLabel(percentage: number): { label: FitScoreResult['label']; color: FitScoreResult['color'] } {
  if (percentage >= 76) return { label: 'Excellent Fit', color: 'green' };
  if (percentage >= 51) return { label: 'Good Fit', color: 'yellow' };
  if (percentage >= 26) return { label: 'Fair Fit', color: 'orange' };
  return { label: 'Poor Fit', color: 'red' };
}
```

### `src/components/FitScoreBadge.tsx`

```typescript
import { FitScoreResult } from '@/lib/scoring/companyFitScore';

const colorClasses = {
  red: 'bg-red-100 text-red-800 border-red-200',
  orange: 'bg-orange-100 text-orange-800 border-orange-200',
  yellow: 'bg-yellow-100 text-yellow-800 border-yellow-200',
  green: 'bg-green-100 text-green-800 border-green-200',
  gray: 'bg-gray-100 text-gray-600 border-gray-200'
};

interface FitScoreBadgeProps {
  result: FitScoreResult;
  showPercentage?: boolean;
}

export function FitScoreBadge({ result, showPercentage = true }: FitScoreBadgeProps) {
  return (
    <span className={`inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium border ${colorClasses[result.color]}`}>
      {showPercentage && result.label !== 'Insufficient Data' && (
        <span className="mr-1 font-bold">{result.percentage}</span>
      )}
      {result.label}
    </span>
  );
}
```
