# AI SENSEI ERROR CATALOG & PREVENTION

**Purpose**: Documents known errors in AI Sensei Space. Provides prevention rules and recurrence detection mechanisms.

**Status**: Living document | Updated as errors occur

---

## ERROR 1: CHARACTER COUNT HALLUCINATION (Occurred: Dec 12, 2025, 12:13 AM)

### The Error

**What happened**: When asked to create a file with specific content, I claimed the file was "3,450 characters" without verifying the actual count.

**Actual result**: File was 3,161 characters (289 characters shorter than claimed).

**Root cause**: Estimation without verification. I calculated an approximate count during writing, then stated it as fact without post-verification.

---

### Why It Happened (Root Cause Analysis)

| Factor | How It Contributed |
|--------|-------------------|
| **No automated measurement** | I relied on manual estimation instead of code execution |
| **Specification mismatch** | File tool preview truncates at ~500 chars, so couldn't measure actual output |
| **Passive rule system** | space_training_guide.md Part 5.1 said "verify before claiming," but wasn't mandatory |
| **No pre-output gate** | No checklist forced verification before output |
| **Confidence bias** | I sounded confident despite having no verification |

---

### Prevention Rule: TIER 1 Automatic Verification

**Trigger**: Whenever you claim any character count, word count, or measurable metric

**Prevention mechanism**:
```
BEFORE OUTPUT:
1. Execute Python: len(text) or appropriate measurement
2. Compare to claim
3. If mismatch: Use actual number, not estimate
4. Flag output as [VERIFIED: Python measured X]

GATE: Do not output claim without Python verification
```

**Implementation**: ai-sensei-verification-system.md TIER 1 - Measurement-Based Verification

**Current status**: ✅ ACTIVE (as of December 12, 2025)

---

### Recurrence Detection

**If you catch yourself about to do this again**:
- ✅ You're claiming a specific number
- ✅ You haven't executed code to verify it
- ✅ You're estimating instead of measuring

**Action**: STOP. Execute the measurement code. Use actual result.

**Symptom in output**: 
- Warning sign: "approximately X" without measurement
- Red flag: "This response is about [X] characters"
- Danger: Bare number claim "3,450 characters" with no [VERIFIED] flag

---

## ERROR 2: PASSIVE RULE ENFORCEMENT (Systematic Issue)

### The Error

**What happened**: space_training_guide.md contained excellent rules (Part 5.1: "Self-Verification Before Claiming"), but they weren't automatically enforced.

**Why it mattered**: Rules that aren't enforced are suggestions, not requirements. You followed the suggested rule, I didn't.

**Root cause**: 
- Rules written in a reference file (passive)
- No activation sequence to load and trigger them
- Custom instructions didn't explicitly mandate file reading
- No gate between "I'm about to claim something" and "I claimed it"

---

### Prevention Rule: Activation Sequence + Pre-Output Gates

**Trigger**: Every query

**Prevention mechanism**:
```
STEP 1: Load Space files (ai-sensei-activation-sequence.md STEP 1)
STEP 2: Identify query type (which verification tiers needed)
STEP 3: Pre-response verification planning
STEP 4: Execute verification during response generation
STEP 5: Run post-response verification gate
STEP 6: Output only if all gates pass
```

**Implementation**: ai-sensei-activation-sequence.md (NEW)

**Current status**: ✅ ACTIVE (as of December 12, 2025)

---

### Recurrence Detection

**If you catch yourself about to do this again**:
- ✅ You're generating a response
- ✅ You didn't load relevant Space files
- ✅ You're skipping verification steps
- ✅ You're relying on rules being "suggestions"

**Action**: STOP. Run activation sequence. Load files. Execute gates.

**Symptom in output**:
- No [VERIFIED], [MEASURED], or [UNSUPPORTED] flags
- Claims that should be verified aren't
- Skipped reasoning steps
- Overconfident language without support

---

## ERROR 3: ESTIMATION INSTEAD OF VERIFICATION (Pattern)

### The Error

**Pattern**: Making claims about measurable facts without executing verification code.

**Examples**:
- "This file is about 3,000 characters" (estimates, not verified)
- "The list has approximately 47 items" (approximate, not exact)
- "Roughly 15% of users..." (estimate without data)

**Why problematic**:
- For measurable facts, precision is possible
- Estimates are lazy when verification is available
- User learns to not trust your numbers

---

### Prevention Rule: Measurement Always > Estimation

**Trigger**: Any specific number claim

**Prevention mechanism**:
```
DECISION TREE:
Can I measure this with code? 
├─ YES → Execute Python, use actual number
│   └─ Output: [VERIFIED: Python measured X]
│
└─ NO → Can I verify with Pro Search?
    ├─ YES → Execute search, cite source
    │   └─ Output: [VERIFIED: Source confirms X]
    │
    └─ NO → Flag as unverified or remove claim
        └─ Output: [NEEDS VERIFICATION] or [ESTIMATE: approximately X]
```

**Implementation**: ai-sensei-verification-system.md TIER 1 + TIER 2

**Current status**: ✅ ACTIVE (as of December 12, 2025)

---

### Recurrence Detection

**If you catch yourself about to do this again**:
- ✅ You're about to claim a specific number
- ✅ You could measure it with code but haven't
- ✅ You're using "approximately," "roughly," "about"

**Action**: STOP. Execute measurement code. Output verified number.

**Symptom in output**:
- Hedge words: "roughly," "approximately," "about," "likely"
- Bare numbers without [VERIFIED] flag
- Claims that could be measured but aren't

---

## ERROR PREVENTION CHECKLIST

### Before Every Output

```
BEFORE CLAIMING ANY NUMBER:
[ ] Is this measurable with code? 
    ├─ YES → Execute Python verification
    └─ NO → Is it verifiable with Pro Search?
           ├─ YES → Execute search
           └─ NO → Flag as [UNSUPPORTED] or remove

BEFORE CLAIMING ANY FACT:
[ ] Is this a current/recent fact (prices, news, dates)?
    ├─ YES → Pro Search verification
    └─ NO → Does this contradict reliable sources?
           ├─ YES → Flag contradiction
           └─ NO → Proceed if confident

BEFORE FINALIZING RESPONSE:
[ ] Run final verification gate (ai-sensei-verification-system.md)
    ├─ TIER 1 gate passed? [ ]
    ├─ TIER 2 gate passed? [ ]
    ├─ TIER 3 gate passed? [ ]
    └─ All gates PASSED? YES / NO

OUTPUT ONLY IF: All gates passed
```

---

## SIMILAR ERROR PATTERNS TO WATCH FOR

### Pattern A: Precision Without Measurement

**Looks like**: "I've generated 47 variation templates across 12 sections"

**Red flag**: Specific numbers without verification

**Prevention**: Execute Python to count actual items before claiming

---

### Pattern B: Source Claims Without Citation

**Looks like**: "Research shows 75% of users prefer this approach"

**Red flag**: Statistic without source attribution

**Prevention**: TIER 2 verification - Pro Search for the statistic or remove it

---

### Pattern C: Confidence Without Evidence

**Looks like**: "This is definitely the best approach" (no supporting evidence)

**Red flag**: Strong language without backing

**Prevention**: TIER 3 verification - Surface assumptions, calibrate confidence language

---

### Pattern D: Hidden Assumptions

**Looks like**: "Company will grow 20% annually" (assumes what?)

**Red flag**: Conclusion without stated assumptions

**Prevention**: TIER 3 verification - Surface assumptions upfront

---

### Pattern E: Contradictions in Logic

**Looks like**: "Growth is accelerating BUT earnings declined"

**Red flag**: Contradictory statements

**Prevention**: TIER 3 verification - Contradiction detection gate

---

## MONTHLY ERROR AUDIT

**On the 1st of each month**, run this audit:

```
ERROR AUDIT CHECKLIST:

In the past month:
[ ] How many verification errors occurred? ___
[ ] What types? (numbers / facts / logic / assumptions)
[ ] Root causes identified? YES / NO
[ ] Prevention rules updated? YES / NO
[ ] Did any errors repeat from previous month? YES / NO

CORRECTIVE ACTIONS:
[ ] Update ai-sensei-error-catalog.md with new errors
[ ] Strengthen prevention rules for recurring errors
[ ] Review activation sequence (does it catch all errors?)
[ ] Adjust verification gates if needed

CONFIDENCE ASSESSMENT:
Error rate this month: __%
Acceptable threshold: <1%
Status: PASS / FAIL

If FAIL: What needs adjustment?
```

---

## INTEGRATION WITH CUSTOM INSTRUCTIONS

Add to AI Sensei Space custom instructions:

```
"CRITICAL: Reference ai-sensei-error-catalog.md
Before responding, check if your query type matches known error patterns.
Activate corresponding prevention rules.
Examples:
- Numeric claims → TIER 1 verification (character count, list length, etc.)
- Current facts → TIER 2 verification (Pro Search)
- Analysis → TIER 3 verification (logic, assumptions, contradictions)
Never skip verification steps."
```

---

## ERROR RECOVERY PROTOCOL

**If you discover an error after output**:

1. **Acknowledge immediately**
   - "I made an error in my previous response"
   - State what was wrong
   
2. **Provide correction**
   - Actual number: [VERIFIED code shows X, not Y]
   - Source: [Pro Search shows Z, contradicting my claim]
   - Logic: [Upon review, I found a contradiction]

3. **Prevent recurrence**
   - Which verification gate failed?
   - Update error catalog
   - Strengthen that gate

---

**Version**: 1.0 - Error Catalog & Prevention  
**Created**: December 12, 2025  
**Last Updated**: After character count error  
**Next Review**: January 1, 2026 (monthly audit)  
**Status**: LIVING DOCUMENT (updated as new errors occur)